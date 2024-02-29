---
description: Twine data structures
---

# Data Structures

Twine uses [IPLD](https://ipld.io/docs/schemas/) to encode its data. There are two data structures: Pulses, and Chain metadata.

Pulses are logically grouped as chains, which act as proxies for ownership. Generally, a chain is produced for a single purpose or use case by a single process and owner. Every pulse of every chain is also linked (via hash-linking) to pulses of other chains, thus forming the larger Tapestry. Chain metadata stores meta information about the construction of the chain, including the public key for provenance verification.&#x20;

Hash-linking of the data structures is facilitated by the process of [content addressing and CIDs](https://docs.ipfs.tech/concepts/content-addressing). With content addressing, data is referenced by its hash instead of its location using a special identifier called a Content ID (CID). A CID is constructed using the hash of serialized data, meta-information about the serialization method, hash algorithm, format, and version of the CID itself. This self-descriptive CID is used as both an identifier for retrieval of the original data, as well as a checksum to verify its integrity. Since CIDs contain a hash of the data, they can be used for the purposes of hash-linking.&#x20;

{% hint style="info" %}
Content addressing and CIDs were established as part of the distributed peer-to-peer filesystem IPFS, however IPFS is not necessary for Twine data.&#x20;
{% endhint %}

Both Pulses and Chain metadata follow a similar outer structure. What distinguishes them is the type of their \`content\` property.

```
type TwinePulse struct {
    content PulseContent
    signature String
} representation map

type TwineChain struct {
    content ChainContent
    signature String
} representation map
```

### Signatures

Signatures are represented as a string type which encodes a [Compact Javascript Web Signature JWS](https://www.rfc-editor.org/rfc/rfc7515#section-3.1).&#x20;

<pre class="language-json" data-title="Example Compact JWS" data-overflow="wrap"><code class="lang-json"><strong>"eyJhbGciOiJFUzI1NiJ9.FECjYMLfBcaarQShFsVVcV3h12vG9Aw2JN5yNptUodLAQV3mfxSgwqdO-VQStLeTRdZDon4YXVGtcgZ_aLzbEqw1.3xZ-2BxejyxinNugtmmm5bo4LuuymNk0KRILsNMluYXjgKWLJRI9EO_MbUnBGiLjAOg16PV6CGrjYDNwAzzbkQ"
</strong></code></pre>

### Mixins

Twine structures make use of a `Mixin` type which is used to store connections between chains.

```
type Mixin struct {
    chain Link
    value Link
} representation map
```

### Pulses

Pulses are the data structures that chain together to create the tapestry. They are the structures that carry payloads and are used to append updates.

{% tabs %}
{% tab title="Schema" %}
```
type PulseContent struct {
  specification String
  chain Link
  index Integer
  links [Link]
  mixins [Mixin]
  source String
} representation map
```
{% endtab %}

{% tab title="Example JSON" %}
<pre class="language-json"><code class="lang-json"><strong>// Represented as DAG-JSON
</strong><strong>{
</strong>  "content": {
    "chain": {
      "/": "bafyriqeoead7csc563ich2z5r6zk4775kopw6fdi2b4xd2jakect2frugslusvva7boq273kzc5uwgm3gyrtfimonjd5xlv5cjt3vibp36u6m"
    },
    "index": 22217,
    "links": [
      {
        "/": "bafyriqelifnu5iq5pgpzqayswpwlq3n6mbq44jcnnhe7afgyupj5hobuquzsls7n2misixjao6wn24mp2gycnjhgokhznetehc4z76jpxi35u"
      }
    ],
    "mixins": [
      {
        "chain": {
          "/": "bafyriqgpmci2rgrxbirme3rsxwqlfocl6h7hjrcm5qytgiiujr6whp26xi7ofomwldjxsdbv54ahuomvpufgh5gdwi5h3pmeozop6fpnpwfzg"
        },
        "value": {
          "/": "bafyriqamkiqv7rf64ohyb6zhf3y4twnwhgopgh2swyu3ytfnzqjhani5thv4bh2dhaa5yatqr6lubm4adwfw5boieg7jjaz2a7bvwkpmxvvcg"
        }
      }
    ],
    "payload": {
      "timestamp": "2022-03-25T20:11:44.207Z"
    },
    "source": "random.colorado.edu"
  },
  "signature": "eyJhbGciOiJSUzI1NiJ9.FEDk6G9L64PqhDw5OE3o5AWwpGptF_t9_myMew2W9_ZBc5YEWZ0KVKE0xmAVb-vgQnJqV1W7iZVzxWzE6RaW0tdR.dpCCKYWMjo0iy_zJyb8ZSuqP-ShWJdJJHWGky4WblAYfpMMudyPD7ZOIvJY8ysGIt0_wSFjxZnlA62yL_sz0sCLC2xj93GrCwuBDafikbyzHf2zzOxYpqkXLGorq_02DHg_RrySU1CQqNrKMwTHbsOTz0VyoXii2H8wTRZyYeCoJ5Ju0W6cSDJJcoxm6ozhZnqcjjkohBUAbu0wX4nj_rdWPwmIFzYAnPXdN9id_Eo3s5PIO4jO5NzQQ3VzkFx1N9W3NbMeEuqkz-nwe4DGmgSr-EqFmhvLjPVmB1kYQ4bYovMykdQJyKETQ6r0XXKgjJ8KegDrSGU1o2N2dkpYdBQ"
}
</code></pre>
{% endtab %}
{% endtabs %}

### Chain Metadata

Chain Metadata structures store information about the chain as a whole. They must be retrieved in order to verify the signatures of Twine data structures.

{% tabs %}
{% tab title="Schema" %}
```
type ChainContent struct {
  specification String
  key JWK # https://www.rfc-editor.org/rfc/rfc7517
  links_radix Integer
  meta Map
  mixins [Mixin]
  source String
} representation map
```
{% endtab %}

{% tab title="Example JSON" %}
<pre class="language-json"><code class="lang-json"><strong>// Represented as DAG-JSON
</strong>{
  "content": {
    "key": {
      "alg": "ES256",
      "crv": "P-256",
      "kty": "EC",
      "x": "yiQcZrPJGV11Z6o0r4Oj-iKrGbYYqSp7vLgUDUtQDUA",
      "y": "gUyDV_gyzU2SMfPmIB2uyOtclxR6v6Ic-JTmJDq4Tq0"
    },
    "links_radix": 32,
    "meta": {
      "description": "Weather chain showing boulder weather every 5 minutes."
    },
    "mixins": [
      
    ],
    "source": "entwine.me",
    "specification": "twine/1.0.x"
  },
  "signature": "eyJhbGciOiJFUzI1NiJ9.FECjYMLfBcaarQShFsVVcV3h12vG9Aw2JN5yNptUodLAQV3mfxSgwqdO-VQStLeTRdZDon4YXVGtcgZ_aLzbEqw1.3xZ-2BxejyxinNugtmmm5bo4LuuymNk0KRILsNMluYXjgKWLJRI9EO_MbUnBGiLjAOg16PV6CGrjYDNwAzzbkQ"
}
</code></pre>
{% endtab %}
{% endtabs %}

