# 발행-구독

NATS는 일대다 통신을 위한 발행-구독 메시지 분산 모델을 구현한다. 게시자는 주제로 메시지를 보내고 주제를 수신하고 있는 모든 활성 구독자들은 메시지를 받는다. 구독자는 정규표현식과 같은 방식으로 와일드 카드 주제를 수신 할 수도 있다. 이러한 일대다 패턴을 팬아웃(fan-out)이라고도 한다.

![](https://gblobscdn.gitbook.com/assets%2F-LqMYcZML1bsXrN3Ezg0%2F-LqMZac7AGFpQY7ewbGi%2F-LqMZgHxXJwFMQlDdoqN%2Fpubsub.svg?alt=media)

[pub-sub tutorial](https://docs.nats.io/developing-with-nats/tutorials/pubsub)을 따라 운영 서버에서 NATS 발행-구독을 직접 시도해보자. 
