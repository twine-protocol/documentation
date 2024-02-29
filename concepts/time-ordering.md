---
description: Understand how a stitch in Twine saves time
---

# Time Ordering

Several entwined Twine chains are called a Tapestry. The Tapestry is a [DAG](https://en.wikipedia.org/wiki/Directed\_acyclic\_graph) and therefore has a partial ordering. If a pulse "A" contains the CID (hash) of another pulse "B", then pulse "B", must have been produced _after_ pulse "A" because a hash value can not be known prior to creation. By traversing the graph one can answer the question "did pulse A come before pulse B?". The answer may be "yes", "no", or "unknown".

To see why this is, consider the tapestry presented in [this interactive demo](https://bewildered-old-narrow-pixie.fission.app/#/embed/TimeBoundingGrid). In this demo time flows from left to right and you can drag each pulse to see the flexibility in its ordering. Each pulse is labeled with the time it was actually emitted.

The pulses may declare whatever timestamp they want, but the ordering is bound by the connections between the pulses.

[Now consider this demonstration](https://bewildered-old-narrow-pixie.fission.app/#/embed/TimeBoundingGrid?expanded=3\&expanded=12\&isFixed=gold). Suppose you control the yellow chain, and you know for certain when each pulse was created but you want to deduce. This means the yellow pulses are fixed in place representing our certainty about the timing. Suppose you want to compare a pulse on the red chain to a pulse on your yellow chain. Try draggin the other pulses to see if you can modify the timing to make it seem like the red pulse came before the yellow one. You should see that this isn't possible because of the configuration.

In general, if a path can be found between two pulses then their ordering is unambiguous and one can be proved to have happened before another.

However, comparing two other pulses you can see that, at least [in this example](https://bewildered-old-narrow-pixie.fission.app/#/embed/TimeBoundingGrid?expanded=2\&expanded=12\&isFixed=gold), there is ambiguity about the ordering.
