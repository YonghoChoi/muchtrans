title: Go 언어 사용 시 Provider 패턴을 사용해야하는 이유
author: Oren Rose
source: https://medium.com/swlh/provider-model-in-go-and-why-you-should-use-it-clean-architecture-1d84cfe1b097

![](https://miro.medium.com/max/1000/1*q79E45FbOpbdZq6mpzQvNA.png)

**Maria Letta**가 그린 [낚시하는 고퍼](https://github.com/MariaLetta/free-gophers-pack)와 **Vecteezy**가 그린 [부두 배경그림](https://www.vecteezy.com/free-vector/pier)

우리가 코드를 작성할 때, 같은 문제와 실험들을 반복하게 된다. 이 실험들 중 하나는 우리 모두가 가끔씩 직면하게되는 외부 서비스와의 상호작용이다. 별거 아닌 것 처럼 보이지만 단순한 요구에도 일관된 해결책이 필요하다. 프로그래머로써 우리는 코드를 쉽게 작성하고, 변경에 개방하고, 읽기 쉽게 만드는 익숙한 패턴을 좋아한다. 이 글에서 제안 할 외부 서비스와 상호작용을 하기 위한 일반적인 해결책은 공급자(Provider)라는 의존성 주입을 사용하는 것이다.

# 공급자 모델이란?

2005년경 마이크로소프트에서 써드파티 API를 호출하거나 데이터 저장소들에 접근할 때 .NET 2.0 어플리케이션이 하나 이상의 구현 중에서 선택할 수 있도록 하는 [공급자 모델](https://docs.microsoft.com/en-us/previous-versions/dotnet/articles/ms972319%28v=msdn.10%29)을 공식화했다. 개발자들은 실행시점에 기능의 구현을 연결하고 컴포넌트들을 교체할 수 있다.

MSFT 아이디어에 대해 많은 의견들이 있다. 어떤 사람들은 그것이 실제 패턴이 아니라고 말하고, 또 다른 사람들은 [이미 존재하는 전략 패턴](https://www.simplethread.com/the-provider-model-pattern-really/)에 그럴싸한 이름을 붙인 것이라고 말한다. 이 내용은 마이크로소프트에서 2005년에 작성한 130페이지의 백서에서 확인할 수 있기 때문에 더이상의 설명은 생략하도록 한다. 이 글의 내용과는 크게 연관이 없다. 앞서 언급했다시피 공급자 패턴이라는 것은 의존성 주입을 하는 하나의 방법 일 뿐이다. 

이제 Go의 예제를 살펴보자. 여기서의 아이디어는 외부 서비스와의 상호 작용을 추상화 하는 방법을 제시하는 것이다. API, 써드파티 서비스의 SDK, 데이터 스토어, 또는 어플리케이션 계층 자체에 직접적인 영향 없이 모든 컴포넌트들을 호출할 수 있다. 

# SimpleWeather 예제

예제에서 우리는 짧은 소매의 옷을 입을지 긴 소매의 옷을 입을지 알려주는 프로그램을 작성할 것이다. 특정 도시의 날씨를 알려줄 하나의 외부 서비스와 통신을 하게 될 것이고, 이는 매우 깔끔하고, 획기적이다.

우리가 만들 어플리케이션에서 사용할 컴포넌트들을 잠깐 살펴보자. 컴포넌트는 엔티티와 서비스 사용사례, 공급자 패키지이다.

## 엔티티

엔티티는 날씨를 대표하는 하나의 구조체이다. 간단한 어플리케이션이기 때문에 이런 종류의 DTO는 아무런 동작도 하지 않는다. 더 큰 어플리케이션에서는 엔티티 계층에 전체 어플리케이션에 대해 유효하게 유지되어야 하는 비즈니스 정책이 있을 수도 있다.  

이 간단한 예제에서 유즈케이스는 날씨에 따라 어떤 것을 입을지 결정하는 것 뿐이다. 우리는 이 "복잡한" BL을 가진 간단한 어플리케이션 서비스를 갖게 될 것이다. 온도가 20도보다 높은 경우 우리에게 짧은 소매를 입으라고 말해줄 것이다. 반대의 경우에 우리는 긴 소매가 필요하다. 이 서비스는 `WeatherProvider` 인터페이스로 날씨를 얻고, 우리가 어떤 옷을 입어야할지 계산한다. 

Go에서의 추상화는 (다른 인터페이스와 마찬가지로) 공급자 인터페이스로 적합하다. 인터페이스가 암시적으로 구현된다는 사실을 통해 구현이 아닌 사용에 인터페이스를 선언할 수 있다. 이는 실제로 Go에서 사용되는 관행 중 하나이다.

`WeatherProvider` 인터페이스 메소드는 엔티티의 존재에 대해서만 알고 있다. 우리는 이 인터페이스를 초기화 할 때 서비스에 의존성을 주입할 것이다. 이 서비스는 누가 데이터를 가져오든 데이터가 어떻게 생성이되든 신경쓰지 않는다.

`forcasting` 서비스는 WeatherProvider 인터페이스를 사용하여 날씨를 가져오고 무엇을 입을지 계산한다.

심지어 **우리**는 이 단계에서 데이터를 가져올 위치조차 알 필요가 없다. 예제를 통해 이 인터페이스를 위한 stub 구현체를 가질수 있고, 모두 정상 동작한다. 외부 서비스에 의존성이 없기 때문에 유즈케이스 자체를 테스트 할 수 있다.

## 공급자

공급자는 외부 서비스와 우리의 어플리케이션 사이에서 어댑터 역할을 한다. 우리는 외부 서비스에 대한 세부사항을 알아야 구현할 수 있다. 우리의 경우 세부사항은 실제 날씨를 가져오는 `[OpenWeather](https://openweathermap.org/current)` API 이다. 공급자는  API의 Response를 어플리케이션의 `Weather` 엔티티로 변환한다.

Response 구조체는 openweather 패키지에 정의되어있다. 외부 서비스의 언어를 우리의 어플리케이션에서 사용하는 언어로 변환하는데 사용된다. 

우리는 response 바디를 디코딩하기 위해 Response 객체인 `weatherResponse`를 사용할 것이다. 이 객체는 어떠한 태그도 가지고 있지 않은 어플리케이션 엔티티와는 다르게 OpenWeather API와 연결되고, 이에 따라 JSON 태그가 포함된다. 엔티티를 JSON 태그와 혼합하지 않기 위해 이렇게 구분하는 것이 중요하다. `OpenWeather` API가 변경되더라도 우리의 엔티티는 변경되지 않는다. 예를들어, "temp" 대신에 "temperature"를 반환한다고 해도 우리는 어플리케이션 코드를 수정할 필요가 없고, 공급자의 코드만 수정하면 된다. 

공급자 내에서 외부 API를 호출하고 엔티티에 response를 변환하여 전달한다. 

이 패키지의 이름은 실제 외부 서비스의 이름인 `openweather`와 동일하다. 이는 BL이 아닌 메커니즘과 연관이 있고, 가장 바깥쪽 레이어에 있기 때문이다. 그렇게함으로써 이 패키지가 실제 애플리케이션 BL 또는 정책과 관련이 없다는 단서를 설정하고 있다. 이는 구현 세부사항이다. 

## Main

Main은 우리가 모든 것을 구성하는 곳이다.

main은 도시를 대표하는 플래그를 가져오고, 공급자와 서비스를 초기화하며 예측 서비스 메소드를 트리거한다. 

이제 준비는 끝났다. 언뜻보기에 이런 작은 어플리케이션에 비해 오버엔지니어링처럼 보일 것이다. 하지만 이 후에 이 어플리케이션이 성장하고 변화하길 바란다. 21도에 대한 중요한 정책을 유지하는 엔티티를 갖길 원할 수 있다. 이 예제에서는 그 다지 중요하진 않다. 외부와 의존된 코드를 분리하는 것은 향후 이러한 변화를 가능하게 하는데 중요하다.

이러한 분리는 클린아키텍처 접근 방식의 따르기 위한 주요 핵심이며 다음 섹션에서 자세히 살펴볼 것이다. 

# Clean Architecture

[Clean Architecture](https://blog.cleancoder.com/uncle-bob/2012/08/13/the-clean-architecture.html) is a software architecture design that was created by Uncle Bob. Its objective is the *[separation of concerns](https://en.wikipedia.org/wiki/Separation_of_concerns)*, allowing developers to encapsulate the business logic and keep it independent from the delivery and framework mechanism. The same goal is also shared by former ideas such as Onion and Hexagon Architectures. They all achieve this separation by dividing the software into layers.

The most common image in this subject for visualizing the layers is probably this one:

![Provider%20Pattern%20in%20Go%20and%20Why%20You%20Should%20Use%20It%20b%20dfefdb2283dc48cf8b4f18910cf2d3e5/1B7LkQDyDqLN3rRSrNYkETA.jpeg](Provider%20Pattern%20in%20Go%20and%20Why%20You%20Should%20Use%20It%20b%20dfefdb2283dc48cf8b4f18910cf2d3e5/1B7LkQDyDqLN3rRSrNYkETA.jpeg)

credit and source: https://blog.cleancoder.com/uncle-bob/2012/08/13/the-clean-architecture.html

The arrows in the circles show the *dependency rule*. If something is declared in an outer circle, it must not be mentioned in the inner circle code. It goes both for actual code dependencies, but also for naming. The name `OpenWeather` in our example is only mentioned in the provider that implements the `WeatherProvider`. It is done to demonstrate that inner layers are not dependent on any outer layer.

The outer layer contains the low-level components such as UI, DB, or a 3rd party service. They all can be thought of as details or plugins to the application. The goal is that you could replace these details without a change in the inner layers, which are more related to the application.

## Crossing boundaries

OK, but how can we cross the boundaries outwards? In our example, the application-service still needs to call `OpenWeather` API somehow. Meaning the use-case layer must call the outer layer. It would violate the dependency rule. The obvious answer is *interfaces*.

![Provider%20Pattern%20in%20Go%20and%20Why%20You%20Should%20Use%20It%20b%20dfefdb2283dc48cf8b4f18910cf2d3e5/1Fk-W5bHzZvi-YKiYh2_iqA.jpeg](Provider%20Pattern%20in%20Go%20and%20Why%20You%20Should%20Use%20It%20b%20dfefdb2283dc48cf8b4f18910cf2d3e5/1Fk-W5bHzZvi-YKiYh2_iqA.jpeg)

Well, not really everywhere. But instead of calling the outer layer directly, the application-service calls an interface. This interface is implemented in the outer layer. Just like the `forecasting.service` calls its `WeatherProvider` interface.

A simplified diagram for our application could be something like this:

![Provider%20Pattern%20in%20Go%20and%20Why%20You%20Should%20Use%20It%20b%20dfefdb2283dc48cf8b4f18910cf2d3e5/1DJBSyyUPKufjNriUMN8oNw.png](Provider%20Pattern%20in%20Go%20and%20Why%20You%20Should%20Use%20It%20b%20dfefdb2283dc48cf8b4f18910cf2d3e5/1DJBSyyUPKufjNriUMN8oNw.png)

`simpleweather` app divided into layers.

That is where Go has an advantage. A type doesn’t need to specify which interface it implements. It enables the `WeatherProvider` interface to belong to the application-service layer. The `forecasting` package and the provider can be independent of one another. In case the `OpenWeather` API is changed, the only change will be in our `opwnweather` package.

# Test yourself, check for violation.

There are a couple of ways to check yourself to see if you violate the dependency rule. Of course, these are rules of thumb and may have exceptions.

## Wording

The word *openweather* should appear only in `main` and in the `openweather` package. Note that in the application layer, the name of the interface is `WeatherProvider` and not `OpenWeatherProvider`. It’s a vital restriction since it emphasizes that the application use-case doesn’t know *how* the `weather` is being brought into the application. Your application should say what it needs, not how it needs it to be done.

## Import

The only import to the openweather package is from `main`. It’s because this package belongs to the outermost layer. So this package can’t be a dependency of any other package except the `main`.

## External Awareness

The only component that is aware of the external `OpenWeather` API’s existence is the provider (and `main`). It doesn’t account only for HTTP calls. It was also the case if `OpenWeather` had SDK, which the provider would import.

## Package Naming

The provider package’s name is the same as the name of the external service it uses. It is to make it clear that it’s an implementation detail, a mechanism. Just like that in the package `HTTP`, you will find an `HTTP` implementation. Keeping it in a different package from the application packages is making these concerns truly separated. For connivance, you could put all of your providers in a provider’s folder. (But don’t use it as a package, though).

# Benefits

As been said, it may look like an overhead within such a small application, but in the long run, you will notice the benefits it provides. Let’s summarize some of them.

## Separation of Dependencies

First and foremost is making your dependencies separated. Your BL doesn’t know anything about the outside world. Your application itself doesn’t aware of `HTTP`. If you see an import to the `http` package in the service, you may raise an eyebrow and start thinking about refactoring.

The same goes for authentication into the external service. The auth and transport utilities aren’t scattered all over your code, which is a good practice in general.

It also helps during pull requests. When reviewing a changed code due to a change in the external service, you will know pretty much what to expect. You would expect only the provider package to be changed. If you see a change in the entity, for example, it will make you ask questions.

## Consistency and Standardization

In most cases, it won’t be such a small app, and it won’t be only you as its developer. Keeping a consistent style guide for dealing with familiar problems will make your team live easier. You will find something quicker in the code. It also provides a good starting point when writing a new feature or service. The template is always the same.

Note that this provider pattern is suitable for any external service, whether it’s a service within your company or AWS SDK. You could have a package `aws` just to adapting the languages of AWS and your entities.

For data access, you already familiar with the *repository* pattern. It is just a private case for the provider pattern. If your DB is SQL, you could have a package `sql`. If your DB is Postgres, you could name it `postgres`.

## Testable

As been said, you could write your service before having the provider code ready or without even knowing who the provider is. Imagine your company is waiting for some external service approval before they will provide you the API key. It would be best if you didn’t wait for them in order to start writing some BL.

It makes the use case testable, of course, since it relies on an interface rather than an actual implementation.

In addition, the provider itself can get the endpoint in the initializer. If the external service has a QA endpoint, or even if you have a different environment configuration, this becomes extremely helpful.

# Wrapping it up

We saw an example in Go for accessing an external service API. We wrote it with *[separation of concerns](https://en.wikipedia.org/wiki/Separation_of_concerns)* in mind by using the *[Clean Architecture](https://blog.cleancoder.com/uncle-bob/2012/08/13/the-clean-architecture.html)* ideas for achieving a code that is separated by dependencies, flexible, and testable.

Go is suitable for this, thanks to the implicit interface implementation and packages structure and naming, but the ideas are true in general. Even if it looks like a lot of code for something mundane, it has its benefits in the long run. If you are not convinced, I hope this post at least showed you another option for writing code.