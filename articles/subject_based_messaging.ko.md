title: 주제(subject) 기반 메시징
author: NATS 공식문서
source: https://docs.nats.io/nats-concepts/subjects 

# 주제(Subject) 기반 메시징

기본적으로 NATS는 메시지를 게시하고 수신하기 위한 것이다. 이 두가지 모두 메시지를 스트림 또는 토픽으로 범위를 지정하는 _Subjects_에 강하게 의존한다. 간단히 말하면 하나의 주제(subject)는 단지 게시자와 구독자가 서로를 찾는데 사용할 수 있는 이름을 나타내는 문자열이다. 

![](https://gblobscdn.gitbook.com/assets%2F-LqMYcZML1bsXrN3Ezg0%2F-LqMZac7AGFpQY7ewbGi%2F-LqMZftZF2C39fSwp6zq%2Fsubjects1.svg?alt=media)

NATS 서버는 몇개의 문자를 특수문자로 지정하고 있고, 정해진 사양에 따라 영-숫자 문자와 "." 만이 주제(subject) 이름에 사용되어야 한다. 주제는 대소문자를 구별하고, 공백을 포함해선 안된다. 클라이언트 간에 안전하려면 ASCII 문자를 사용해야하지만 이는 나중에 변경될 수 있다.

## Subject Hierarchies

The `.` character is used to create a subject hierarchy. For example, a world clock application might define the following to logically group related subjects:

```markup
time.us
time.us.east
time.us.east.atlanta
time.eu.east
time.eu.warsaw
```

## Wildcards

NATS provides two _wildcards_ that can take the place of one or more elements in a dot-separated subject. Subscribers can use these wildcards to listen to multiple subjects with a single subscription but Publishers will always use a fully specified subject, without the wildcard.

### Matching A Single Token

The first wildcard is `*` which will match a single token. For example, if an application wanted to listen for eastern time zones, they could subscribe to `time.*.east`, which would match `time.us.east` and `time.eu.east`.

![](../.gitbook/assets/subjects2.svg)

### Matching Multiple Tokens

The second wildcard is `>` which will match one or more tokens, and can only appear at the end of the subject. For example, `time.us.>` will match `time.us.east` and `time.us.east.atlanta`, while `time.us.*` would only match `time.us.east` since it can't match more than one token.

![](../.gitbook/assets/subjects3.svg)

### Monitoring and Wire Taps

Subject to your security configuration, wildcards can be used for monitoring by creating something sometimes called a _wire tap_. In the simplest case you can create a subscriber for `>`. This application will receive all messages -- again, subject to security settings -- sent on your NATS cluster.

### Mix Wildcards

The wildcard `*` can appear multiple times in the same subject. Both types can be used as well. For example, `*.*.east.>` will receive `time.us.east.atlanta`.
