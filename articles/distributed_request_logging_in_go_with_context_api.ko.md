title: Go 언어로 Contest API를 사용하여 분산 Request 로깅하기
author: Oliver Seitz
source: https://medium.com/swlh/distributed-request-logging-in-go-with-context-api-16040688ed36

# Go 언어로 Contest API를 사용하여 분산 Request 로깅하기

최근에는 마이크로서비스 아키텍처가 아주 흔해졌고, 서버 어플리케이션을 개발하는 용도로 인기를 끌고 있다. 현재 개발 중인 도메인의 클래스들로 로거 클래스를 주입하는 방식으로 매우 쉽게 로깅을 할 수 있고, unique id를 발급하여 요청 파이프라인 내 어디서나 로거를 주입하여 요청을 트레이싱 할 수 있다. 마이크로서비스 아키텍처에서 여기에서 소개하는 방법을 따라하는 것만으로 이를 수행할 수 있다. 하지만 여러 서비스들에 분산되어 있는 로그 메시지들이 서로 불일치 할 수 있다.  이 문서에서는 고언어로 작성 된 모든 서비스들에서 같은 request id를 공유하는 하나의 간단한 HTTP 파이프라인을 설명한다. 

서비스 전반에 걸쳐서 일관성 있는 로그를 남기기 위해서는 각 서비스들 간에 같은 식별자를 공유해야한다. HTTP API들을 예로 들면 이러한 식별자를 Request 헤더로 전송할 수 있다. 이 방법을 사용하면 비즈니스와 관련된 데이터를 포함하는 Request의 바디나 쿼리 파라미터의 수정이 필요없고, 도메인 로직에서 신경쓸 필요가 없게 된다. 헤더 안에 식별자를 포함시키기 위해서는 다른 서비스들과 통신하는 프로그램의 어느 위치에서나 식별자에 엑세스 할 수 있어야 한다.

## 컨텍스트

고언어에서 context API는 서로 다른 메소드 간 컨텍스트 관련(context-releated) 데이터들을 전송하는데 권장되는 방법이다.  컨텍스트 관련 데이터는 도메인 로직 안에 포함되지 않은 데이터를 의미한다. 예를들어, 하나의 기사 번호는 도메인 관련 데이터이지만 발신의 IP 주소는 비즈니스 사례에 따라 다를 수 있다. 컨텍스트는 현재의 컨텍스트를 복제하여 새로운 컨텍스트로 생성하고, 키-값 쌍으로 확장될 수 있는 하나의 불변 구조체이다. 모든 http.Request는 데이터를 추가하여 확장될 수 있는 컨텍스트를 포함하고 있다. 

컨텍스트는 API 호출을 위한 메소드에 할당되어야 한다. 관례적으로 컨텍스트는 첫번째 파라미터에 ctx라는 변수명을 사용한다.
```
func DoCall(ctx context.Context, url string) error {
  ...
}
```



## HTTP 파이프라인

컨텍스트에 request id를 입력하려면 모든 request 요청을 가로채는 HTTP 핸들러를 구현해야 한다. 아래 예제 코드에서 볼 수 있듯이 고 언어에서는 간단하게 구현할 수 있다. UseRequestId 핸들러는 ReqIdHeaderName이라는 HTTP 헤더로부터 request id를 얻어내어 request 컨텍스트에 추가한다. request id가 존재하지 않는다면 새로운 request id를 생성한다. 이 과정은 유입되는 모든 request에서 발생한다.
```go
func UseRequestId(next http.HandlerFunc) http.HandlerFunc {
   fn := func(w http.ResponseWriter, r *http.Request) {
      var reqId string
      if reqId = r.Header.Get(ReqIdHeaderName); reqId == "" {
         // 첫 request 발생
         reqId = uuid.New().String()
      }
      // 현재의 컨텍스트를 복제하여 request id 추가
      ctx := r.Context()
      ctx = context.WithValue(ctx, ReqIdKey, reqId)

      next.ServeHTTP(w, r.WithContext(ctx))
   }

   return fn
}
```

request id를 얻어내기 위해 두개의 간단한 함수들을 추가한다. 이 함수들은 필수는 아니지만 쉽게 현재의 current id를 가져올 수 있도록 한다.
```
func GetReqIdReq(r *http.Request) string {
   return GetReqIdCtx(r.Context())
}

func GetReqIdCtx(ctx context.Context) string {
   reqId := ctx.Value(ReqIdKey).(string)
   return reqId
}
```

파이프라인을 사용하기 위해 실제 핸들러 함수를 파이프라인 함수로 래핑한다.
```
http.HandleFunc("/users", UseRequestId(handler.GetUsers))
```

이제 request id를 얻어내어 서비스의 모든 Request 헤더에 추가할 수 있다.
```
func DoCall(ctx context.Context, url string) error {
    ...
    reqId := httpcontext.GetReqIdCtx(ctx)
    if reqId != "" {
       req.Header.Add(httpcontext.ReqIdHeaderName, reqId)
    }
}
```

다른 서비스들과의 모든 HTTP 통신은 request id를 포함하게된다. 다른 서비스에서 UseRequestId를 구현하면 자동으로 컨텍스트에서 request id를 가져와서 재사용할 수 있다.

## 로거

마지막으로 로거를 설정해야한다. 여기서는 로깅을 위해 logrus를 사용할 것이지만 다른 어떠한 로거를 사용해도 상관없다. Logrus는 로그를 위한 필드들을 추가하는 것을 허용한다. 필드들은 로그 내 더 많은 정보를 제공하기 위한 키-값의 쌍이다.

Request 컨텍스트로 로거를 포함하기 위해 다른 HTTP 파이프라인 핸들러를 생성한다. 핸들러는 현재 컨텍스트에 request id를 포함하고 있는지를 확인하고, 로거의 필드로 request id를 입력한다. 그리고 나서 Request 컨텍스트로 로거를 추가한다. 
```
func UseLogger(next http.HandlerFunc) http.HandlerFunc {
   fn := func(w http.ResponseWriter, r *http.Request) {
      ctx := r.Context()
      if reqId := GetReqIdCtx(ctx); reqId == nil {
         // panics
         logrus.Fatal("No request id associated with request")
      } else {
         log := logrus.WithField(httpcontext.ReqIdKey, reqId)
         ctx = context.WithValue(ctx, LoggerKey, log)
      }

      next.ServeHTTP(w, r.WithContext(ctx))
   }

   return fn
}
```

컨텍스트에서 빠르게 로거를 획득할 수 있는 두가지 함수를 추가로 생성한다.
```
func GetLogReq(r *http.Request) *logrus.Entry {
   return GetLogCtx(r.Context())
}

func GetLogCtx(ctx context.Context) *logrus.Entry {
   log := ctx.Value(LoggerKey)

   if log == nil {
      logrus.Fatal("Logger is missing in the context") // panics
   }

   return log.(*logrus.Entry)
}
```

UseLogger 핸들러는 request id를 획득하기 위한 UseRequestId 핸들러 다음으로 호출되어야한다. 새로운 파이프라인은 다음과 같다.
```
http.HandleFunc("/users", UseRequestId(UseLogger(handler.GetUsers))
```

![](https://miro.medium.com/max/1400/1*rgKf-_W7zQx2DkwcAiAkJw.png)

# 결론

이 문서에서는 마이크로서비스 환경에서 요청 트레이싱을 활성화하기 위한 분산 로깅 전략에 대해 설명했다. 각각의 유입되는 요청들은 개별 서비스의 경계에서 추적될 수 있다. 

우리는 오픈소스 온프레미스 도커 컨테이너 레지스트리인 harbour.rocks에서 이와 같은 로깅 전략을 사용하고, 중소기업을 위한 서버를 구축한다. harbour는 활발히 개발 중이지만 7월말까지 v1 준비를 계획하고 있다. 이 문서에서 설명한 마이크로서비스 아키텍처, 고언어, 컨테이너화에 관심이 있다면 Github <https://github.com/harbourrocks/harbour>에 star를 통해 우리를 지원할 수 있다. 또한 트위터를 팔로우하면 향후 업데이트 소식을 들을 수 있다.

request 트레이싱을 위해 당신이 사용 중인 전략/라이브러리/프레임워크는 무엇이 있는가?


## words

* ubiquitous  : 아주 흔한
* tend to : ~하는 경향이 있다.
* nevertheless : 그럼에도 불구하고
* numerous : 많은
* incoherent : 일관성이 없는
* systemwide : 전조직[계열, 체계]에 미치는
* coherency : 일관성
* further : 더, 더 멀리에, 더 나아가