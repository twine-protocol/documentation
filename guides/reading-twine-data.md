---
description: Use the javascript library to access twine data from an HTTP api
---

# Reading Twine Data

We are going to walk through a basic way of interacting with a Twine HTTP api. For this example we'll use the api hosted by the [CURBy project](https://random.colorado.edu).

### Prerequisites

You need a javascript runtime such as [node.js](https://nodejs.org/en/learn/getting-started/how-to-install-nodejs), or [bun](https://bun.sh/) to begin.

### Create a project

Follow your package manager's instructions for creating a project. For node.js this looks like:

```sh
mkdir hello-twine && cd hello-twine
npm init
```

This tutorial will use ES Module syntax. To enable it we need to edit the `package.json` file and set the module type as shown on line 6:

<pre class="language-json" data-title="package.json" data-line-numbers><code class="lang-json">{
  "name": "hello-twine",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  <a data-footnote-ref href="#user-content-fn-1">"type": "module",</a>
  "scripts": {
    "test": "echo \"Error: no test specified\" &#x26;&#x26; exit 1"
  },
  "author": "",
  "license": "ISC"
}
</code></pre>

### Install twine libraries

To read twine data we need the twine-core package and to interact with an HTTP api we need the twine-http-store package.

```sh
npm install @twine-protocol/twine-core \
    @twine-protocol/twine-http-store
```

### Code

Open up the `index.js` file in a code editor and input the following:

{% code title="index.js" lineNumbers="true" %}
```javascript
import { HttpStore } from '@twine-protocol/twine-http-store'

const API_URL = 'https://random.colorado.edu/api'
const store = new HttpStore(API_URL)

async function listChains(){
  console.log(`Found the following twine chains at ${API_URL}`)
  for await (const chain of store.chains()){
    console.log(`  > ${chain.cid}`)
  }
}

listChains()
```
{% endcode %}

Explanation:

* Line 1: imports the HttpStore&#x20;
* Line 5: Creates an instance of the store and points it to the API
* Line 8: Loops through the [AsyncIterator](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global\_Objects/AsyncIterator) returned by the call to `.chains()`&#x20;
* Line 9: Prints the CID of each chain

By default, all twine data is verified upon retrieval. So if this succeeds without error, then both the hashes and signatures are valid.

Now you can run this script and see the output.

```sh
node index.js
```

Let's now modify the code to fetch the last 10 pulses on a specific chain. We'll use node.js commandline arguments to specify the chain.

We create a new function to fetch the last n pulses from a specified chain:

<pre class="language-javascript" data-line-numbers><code class="lang-javascript"><strong>async function fetchPulses(chainCid, count = 10){
</strong>  console.log(`Fetching the last ${count} pulses from chain ${chainCid}`)
  for await (const pulse of store.pulses(chainCid)){
    console.log(`\npulse ${pulse.cid}:`)
    console.log(JSON.stringify(pulse.value.content.payload, null, 2))
    count--
    if(count &#x3C;= 0) break
  }
}
</code></pre>

Explanation:

* Line 1: declares a function to fetch a specified number of pulses from a chain. With a default of 10 pulses
* Line 3: Loops through the AsyncIterable of pulses on the chain
* Line 6 & 7: keep track of the number of listed pulses and breaks out of the loop

Notice that on line 5 we access the `.value.content.payload` property. The `.value` property holds the twine data and the `.content.payload` is the pulse payload as a javascript object.

All that is left is to detect when a command line argument is present:

```javascript
if (process.argv.length === 2){
  listChains()
} else if (process.argv.length === 3){
  fetchPulses(process.argv[2])
}
```

Now we can again run our script:

```sh
node index.js "<PUT CHAIN CID HERE>"
```

And you should see a list of pulse CIDs with their payloads.

### Whole script

```javascript
import { HttpStore } from '@twine-protocol/twine-http-store'

const API_URL = 'http://localhost:3000'
const store = new HttpStore(API_URL)

async function listChains(){
  console.log(`Found the following twine chains at ${API_URL}`)
  for await (const chain of store.chains()){
    console.log(`  > ${chain.cid}`)
  }
}

async function fetchPulses(chainCid, count = 10){
  console.log(`Fetching the last ${count} pulses from chain ${chainCid}`)
  for await (const pulse of store.pulses(chainCid)){
    console.log(`\npulse ${pulse.cid}:`)
    console.log(JSON.stringify(pulse.value.content.payload, null, 2))
    count--
    if(count <= 0) break
  }
}

if (process.argv.length === 2){
  listChains()
} else if (process.argv.length === 3){
  fetchPulses(process.argv[2])
}
```

[^1]: insert this line
