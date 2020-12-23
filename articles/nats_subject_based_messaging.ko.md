title: [NATS] 주제(subject) 기반 메시징
author: NATS 공식문서
source: https://docs.nats.io/nats-concepts/subjects 

# 주제(Subject) 기반 메시징

기본적으로 NATS는 메시지를 게시하고 수신하기 위한 것이다. 이 두가지 모두 메시지를 스트림 또는 토픽으로 범위를 지정하는 _Subjects_에 강하게 의존한다. 간단히 말하면 하나의 주제(subject)는 단지 게시자와 구독자가 서로를 찾는데 사용할 수 있는 이름을 나타내는 문자열이다. 

![](https://gblobscdn.gitbook.com/assets%2F-LqMYcZML1bsXrN3Ezg0%2F-LqMZac7AGFpQY7ewbGi%2F-LqMZftZF2C39fSwp6zq%2Fsubjects1.svg?alt=media)

NATS 서버는 몇개의 문자를 특수문자로 지정하고 있고, 정해진 사양에 따라 영-숫자 문자와 "." 만이 주제(subject) 이름에 사용되어야 한다. 주제는 대소문자를 구별하고, 공백을 포함해선 안된다. 클라이언트 간에 안전하려면 ASCII 문자를 사용해야하지만 이는 나중에 변경될 수 있다.

## 주제(Subject)의 계층

주제 계층을 생성하는데 `.`문자가 사용된다. 예를들어, 세계 시계 어플리케이션은 주제를 논리적으로 그룹화하기 위해 다음과 같이 정의할 수 있다.

```markup
time.us
time.us.east
time.us.east.atlanta
time.eu.east
time.eu.warsaw
```

## 와일드카드

NATS는 점(.)으로 구분된 주제에서 하나 이상의 요소를 대신할 수 있는 두개의 _wildcards_를 제공한다. 구독자는 이러한 와일드 카드를 사용하여 단일 구독으로 여러 주제를 수신할 수 있지만 게시자는 항상 와일드카드 없이 지정된 주제만 사용해야한다.
 
### 단일 토큰 매칭

첫번째 와일드카드는 단일 토큰과 일치하는 `*`이다. 예를들어, 응용프로그램이 동부 타임존을 수신하려는 경우 `time.us.east`와 `time.eu.east`에 일치하는 `time.*.east`에 구독할 수 있다.

![](https://gblobscdn.gitbook.com/assets%2F-LqMYcZML1bsXrN3Ezg0%2F-LqMZac7AGFpQY7ewbGi%2F-LqMZftc5Yt_LEI224RE%2Fsubjects2.svg?alt=media)

### 다중 토큰 매칭

두번째 와일드카드는 하나 이상의 토큰과 일치하는 `>`이며, 주제의 끝 부분에만 사용할 수 있다. 예를들어 time.us.*은 둘 이상의 토큰과 매치될 수 없기 때문에 time.us.east에만 일치하는 반면 `time.us.>`는 time.us.east와 time.us.east.atlanta와 일치한다.

![](https://gblobscdn.gitbook.com/assets%2F-LqMYcZML1bsXrN3Ezg0%2F-LqMZac7AGFpQY7ewbGi%2F-LqMZfteO4zWGmIbkFHf%2Fsubjects3.svg?alt=media)

### 모니터링 및 와이어탭

보안 구성에 따라 _wire tab_이라는 것을 생성하여 모니터링에 와일드카드를 사용할 수 있다. 가장 간단하게 `>`에 대한 구독자를 생성할 수 있다. 이 응용프로그램은 (보안 구성에 따라) NATS 클러스터에서 전송된 모든 메시지를 수신할 것이다.

### 혼합 와일드카드

`*` 와일드카드는 동일한 주제에 여러번 나타날 수 있다. 두가지 유형을 함께 사용할 수도 있다. 예를들어, `*.*.east.>`는 `time.us.east.atlanta`를 수신한다.
