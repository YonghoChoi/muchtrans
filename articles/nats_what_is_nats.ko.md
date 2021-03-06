title: [NATS] NATS란?
author: NATS 공식문서
source: https://docs.nats.io/nats-concepts/intro 

# NATS란?

NATS 메시징을 사용하면 컴퓨터 응용프로그램과 서비스 간에 메시지로 분할된 데이터를 교환할 수 있다. 이 메시지들은 주제별로 주소가 지정되며 네트워크 위치에 의존하지 않는다. 이는 응용프로그램 또는 서비스와 기본 물리적인 네트워크 사이에 추상화된 계층을 제공한다.  데이터는 메시지로 인코딩 되고, 프레임화되어 게시자(publisher)에 의해 전송된다. 전송된 메시지는 하나 또는 다수의 구독자(subscriber)들에게 수신되어 디코딩되고, 처리된다. 

NATS를 사용하면 프로그램이 다양한 환경과 언어, 클라우드 공급자, 온프레미스 시스템 사이에서 쉽게 통신할 수 있다. 클라이트는 일반적으로 단일 URL을 사용하여 NATS 시스템에 연결하고나서 메시지를 구독(subscribe) 하거나 주제(subject)에 게시(publish) 한다. 이러한 간단한 설계로 인해 NATS는 프로그램이 공통 메시지 처리 코드를 공유하고, 리소스와 상호 종속성을 격리하며, 서비스 요청이든 스트림 데이터든 메시지 볼륨의 증가를 쉽게 처리하여 확장할 수 있다.  

![](https://gblobscdn.gitbook.com/assets%2F-LqMYcZML1bsXrN3Ezg0%2F-LqMZac7AGFpQY7ewbGi%2F-LqMZghqzxWjKe_dGHGQ%2Fintro.svg?alt=media)

NATS 코어는 **최대 한번**의 서비스 품질을 제공한다. 구독자가 주제에 구독하고 있지 않거나(일치하는 주제가 없음), 메시지를 보낼 때 활성화 되지 않은 경우 메시지는 수신되지 않는다. 이는 TCP/IP가 제공하는 것과 동일한 수준의 보증이다. NATS의 Core는 메모리에서만 메시지를 들고 있고, 디스크에 직접 메시지를 쓰지 않는 화재 발생 방지(fire-and-forget) 메시징 시스템이다. 더 높은 수준의 서비스 보증이 필요하다면 [NATS Streaming](../nats-streaming-concepts/intro.md)을 사용하거나 [acks](acks.md)와 [sequence numbers](seq_num.md) 같이 입증되고 확장 가능한 참조 설계를 통해 클라이언트 응용프로그램에 추가 안정성을 구축할 수 있다.