title: [NATS] Publish-Subscribe
author: NATS Official Reference
source: https://docs.nats.io/nats-concepts/pubsub

# Publish-Subscribe

NATS implements a publish-subscribe message distribution model for one-to-many communication. A publisher sends a message on a subject and any active subscriber listening on that subject receives the message. Subscribers can also register interest in wildcard subjects that work a bit like a regular expression \(but only a bit\). This one-to-many pattern is sometimes called fan-out.

![](https://gblobscdn.gitbook.com/assets%2F-LqMYcZML1bsXrN3Ezg0%2F-LqMZac7AGFpQY7ewbGi%2F-LqMZgHxXJwFMQlDdoqN%2Fpubsub.svg?alt=media)

Try NATS publish subscribe on your own, using a live server by walking through the [pub-sub tutorial](https://docs.nats.io/developing-with-nats/tutorials/pubsub).
