# pubsub-swarm

> Form a p2p swarm of nodes around a topic and exchange messages.

## Background

This module provides a simple pubsub interface on top of an unstructured swarm
of peers interested in a common topic string. Distributed pubsub! It's meant to
be used as a primitive to build server-less applications without needing to have
a deeper understanding of distributed systems.

This module doesn't make guarantees about latencies, nor does it guarantee
delivery. Gossip isn't as fast as flooding, but it's a very robust means of
propogating data over an unstructured network. If the network is stable message
should reach eveyr node, but partitions can occur in the network and bridging
peers can drop out at any time.

Messages are cryptographically signed by their creator and given a unique
sequence number. This means that a. each incoming message can be verified that
it came from its claimed author, and that b. peers will not report duplicate
messages (e.g. if two different peers deliver them).


## Example

```js
var pubsub = require('pubsub-swarm')

var swarm = pubsub('foobar')

swarm.on('message', function (msg) {
  console.log('message', msg)
})

swarm.on('connected', function () {
  swarm.publish({ data: 'hello warld from ' + this.id + '!'})
})
```

Running this in two terminals shows the message exchange:

```
$ node example.js
found + connected to peer
message { data: 'hello warld from
ARIwGee//eZNsQ5JfpV/kHQPpNyfO77dW7BdssCaxIw=.ed25519!' }
^C
```

```
$ node example.js
found + connected to peer
message { data: 'hello warld from
X5dzhP51NbZdF9pc05ImtIzritqYlStPKOYowWOsmvs=.ed25519!' }
^C
```

## API

```js
var pubsub = require('pubsub-swarm')
```

### var swarm = pubsub(topic)

Returns a new pubsub swarm interested in the topic `topic` (string). It will
begin searching for other interested peers  using the BitTorrent DHT and
multicast DNS.

### swarm.on('message', function (msg) {})

Emits when a new message has been received by a peer.

### swarm.publish(msg)

Publish a message to the rest of the swarm. It will gradually propogate through
the swarm using [periodic gossip](https://github.com/noffle/secure-gossip).

## install

With [npm](https://npmjs.org/) installed, run

```
$ npm install pubsub-swarm
```

## license

ISC