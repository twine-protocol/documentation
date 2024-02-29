# Javascript (twine-js)

[Twine-js](https://github.com/twine-protocol/twine-js) is a set of libraries for interacting with twine data.

There are several packages for different purposes:

* [`@twine-protocol/twine-core`](https://github.com/twine-protocol/twine-js/tree/master/packages/twine-core) The core library for reading and verifying twine data
* [`@twine-protocol/twine-builder`](https://github.com/twine-protocol/twine-js/tree/master/packages/twine-builder) The core library for creating twine data
* [`@twine-protocol/twine-cli`](https://github.com/twine-protocol/twine-js/tree/master/packages/twine-cli) Command line tools for interacting with twine data

There are additional packages catered towards storage of twine data:

* [`@twine-protocol/twine-car-utils`](https://github.com/twine-protocol/twine-js/tree/master/packages/twine-car-utils) Utility functions for storing twine in [CAR format](https://ipld.io/specs/transport/car/carv2/)
* [`@twine-protocol/twine-blockstore-store`](https://github.com/twine-protocol/twine-js/tree/master/packages/twine-blockstore-store) Storage using anything that implements the [Blockstore Interface](https://github.com/ipfs/js-stores)
* [`@twine-protocol/twine-http-store`](https://github.com/twine-protocol/twine-js/tree/master/packages/twine-http-store) Client library to store twine data on an HTTP API&#x20;
