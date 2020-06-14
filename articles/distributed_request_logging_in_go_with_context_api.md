title: Distributed Request Logging in Go with Context API
author: Oliver Seitz
source: https://medium.com/swlh/distributed-request-logging-in-go-with-context-api-16040688ed36

# Distributed Request Logging in Go with Context API - The Startup - Medium

Microservice architectures have become ubiquitous nowadays and tend to be the preferred way of building server applications. Back when logging was as easy as injecting a logger class into your domain classes, tracing the request was achieved by a unique id generated somewhere in the request pipeline and attached to the logger. You can still follow this method in microservice architectures. Nevertheless, you will end with numerous incoherent log messages distributed over your several services. This article presents a simple HTTP pipeline in Golang, paired with a request-id shared between all services.

To achieve systemwide log coherency we have to share a common identifier between our services. In the case of HTTP APIs, we can transmit this identifier in a header. This way, we don’t have to change the request bodies or query parameters that contain the business-related data — it stays hidden from the domain logic. To put the identifier in the headers, you need to have it accessible in any place of your program that communicates with other services.

## Context

In Golang, the context API is the recommended way of transferring context-related data between different methods. Context-related data refers to any data that is not included in the domain logic. For example, an article number is domain-related data, while the caller’s IP address might not (depending on the business case). The context is an immutable struct that can be extended with key-value pairs by cloning the current context and creating a new one. Every http.Request comes with a context attached to it that can be extended with additional data.

The context has to be passed into any method that wants to make API calls. By convention, the context has to be the first parameter named ctx.

```
func DoCall(ctx context.Context, url string) error {
  ...
}
```

## HTTP pipeline

To put the request-id into the context, we build an HTTP handler to intercept every incoming request. This is straight forward in Golang, as shown below. The **UseRequestId** handler captures the request-id from a specific HTTP header ***ReqIdHeaderName*** and puts it into the request context. If there is no request-id, we generate a new request-id. This happens for every incoming request.
```
func UseRequestId(next http.HandlerFunc) http.HandlerFunc {
   fn := func(w http.ResponseWriter, r *http.Request) {
      var reqId string
      if reqId = r.Header.Get(ReqIdHeaderName); reqId == "" {
         // first occurrence
         reqId = uuid.New().String()
      }      // clone current context and append request-id
      ctx := r.Context()
      ctx = context.WithValue(ctx, ReqIdKey, reqId)

      next.ServeHTTP(w, r.WithContext(ctx))
   }

   return fn
}
```

To get the request-id, we create two simple functions. Those functions are not required but make it a lot easier to access the current request-id.
```
func GetReqIdReq(r *http.Request) string {
   return GetReqIdCtx(r.Context())
}

func GetReqIdCtx(ctx context.Context) string {
   reqId := ctx.Value(ReqIdKey).(string)
   return reqId
}
```

To use the pipeline, we wrap our actual handler function with the pipeline.
```
http.HandleFunc("/users", UseRequestId(handler.GetUsers))
```

Now we can obtain the request-id and plug it into the header of any request our service makes.
```
func DoCall(ctx context.Context, url string) error {
    ...
    reqId := httpcontext.GetReqIdCtx(ctx)
    if reqId != "" {
       req.Header.Add(httpcontext.ReqIdHeaderName, reqId)
    }
}
```

Every HTTP communication with other services will now include the request-id. In case the other service does implement the UseRequestId pipeline as well, it will automatically capture the request-id from the context and reuse it.

## Logger

The last missing piece is the logger. For logging, we use logrus, but you can use any other logger. Logrus allows appending fields to your logs. Fields are key-value pairs that provide further information in your logs.

We create another HTTP pipeline handler to plug a logger into our request context. The handler checks if there is a request-id inside the current context and plugs the request-id into a field of the logger. It then appends the logger to the request context.
```
func UseLogger(next http.HandlerFunc) http.HandlerFunc {
   fn := func(w http.ResponseWriter, r *http.Request) {
      ctx := r.Context()      if reqId := GetReqIdCtx(ctx); reqId == nil {
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

We again create two functions to obtain the logger from the context quickly.
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

The UseLogger handler has to come after the UseRequestId handler to capture the request-id. Out new pipeline now looks like this.
```
http.HandleFunc("/users", UseRequestId(UseLogger(handler.GetUsers))
```

![](https://miro.medium.com/max/1400/1*rgKf-_W7zQx2DkwcAiAkJw.png)


# Conclusion

This article presented a distributed logging strategy that enables request tracing between different microservices. Each incoming request can be traced over the borders of individual services.

We use the exact same logging strategy in harbour.rocks, which is an open-source on-premise docker container registry and build server for small to medium scale companies. harbour is actively under development, but we plan to have a v1 ready by the end of July. If this article got you interested in microservice architectures, Golang, containerization feel free to support us with a star on GitHub [https://github.com/harbourrocks/harbour](https://github.com/harbourrocks/harbour). And also, make sure to follow me on twitter for upcoming updates!

What strategies/libraries/frameworks do you use for request tracing?