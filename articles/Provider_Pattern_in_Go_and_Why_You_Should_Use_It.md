title: Provider Pattern in Go and Why You Should Use It
author: Oren Rose
source: https://medium.com/swlh/provider-model-in-go-and-why-you-should-use-it-clean-architecture-1d84cfe1b097

![](https://miro.medium.com/max/1000/1*q79E45FbOpbdZq6mpzQvNA.png)

fishing gopher by **Maria Letta**: [https://github.com/MariaLetta/free-gophers-pack](https://github.com/MariaLetta/free-gophers-pack), and pier background by **[Pier Vectors by Vecteezy](https://www.vecteezy.com/free-vector/pier)**

When we are writing code, we’re likely to deal with the same problems and challenges repeatedly. One of these challenges we are all facing from time to time is interacting with an external service. It may seem like a trivial issue, but even simple needs deserve a consistent solution. As programmers, we like familiar patterns that make it easy for us to write code, and more significantly, make our code open to changes and easier to read. The standard solution for interacting with an external service I will suggest in this post is to use a dependency injection named a provider.

# What is a provider model?

Around 2005, Microsoft formulated the [provider model](https://docs.microsoft.com/en-us/previous-versions/dotnet/articles/ms972319%28v=msdn.10%29) for allowing .NET 2.0 applications to choose from one or more implementations when calling a 3rd party API or accessing data stores. Developers can plug their implementation of a feature and swap components in runtime.

There are many opinions about the MSFT idea. Some say it’s not even a real pattern and others suggest that it’s just a fancy name for the [already existing strategy pattern](https://www.simplethread.com/the-provider-model-pattern-really/). I won’t go any further about what it is exactly. You can read all about it in the 130 pages white paper Microsoft wrote about it in 2005. For this post, it’s not very relevant. As I stated, what I will refer to as a provider pattern is just one use of dependency injection.

So let’s start with an example in Go. The idea is to present a way to abstract the interaction with an external service. It may be calling an API, 3rd party service SDK, data store, or any component which isn’t directly a concern of the application layer itself.

# SimpleWeather Example

For our example, we’ll write a program which will tell us if we need to wear short or long sleeves. It will do it by talking with an external service for getting the weather in a specific city. Very neat and groundbreaking, I know.

Let’s shortly go over the components that will compose this application. An entity, a use case service, and a provider package.

## Entity

Our entity is a struct representing the weather. Since it’s a simple application, this kind of DTO doesn’t hold any behavior. In bigger applications, the entity layer may have a business policy that should be kept valid for the whole application.

In this simple example, our only use case is deciding what to wear according to the weather. We’ll have a simple application-service that holds this “complex” BL. If the temperature is above 20°C, it will tell us we can have short sleeves. Otherwise, we need long sleeves. This service will get the weather from a `WeatherProvider` interface and then will calculate what we should wear.

The abstraction in Go comes in handy with the provider interface (like any other interface). The fact that interfaces are implemented implicitly allows you to declare the interface next to the use, not the implementation. It’s actually one of the practices to write idiomatic Go.

Note that the `WeatherProvider` interface method only knows about the existence of our entity. We will inject this dependency into the service when we initialize it. The service won’t care who brings the data nor how it is being generated.

`forcasting` service fetches the weather using the WeatherProvider interface and calculates what to wear.

In fact, ***we*** don’t even need to know at this stage from where we will bring the data. We can have a stub implementation for this interface, and everything will work. It makes the Use Case itself testable, as it doesn’t depend on any external service.

## Provider

The provider is acting as an *adapter* between the external service and our application. We can implement it only after we know the details of the external service. In our case, the details are `[OpenWeather](https://openweathermap.org/current)` API to fetch the actual weather. The provider will convert its response into the application `Weather` entity.

The response struct is defined in our openweather package. Used to convert the language of the external service to our own application language.

We will use the response struct `weatherResponse` for decoding the response body. It is coupled to the OpenWeather API and contains JSON tags according to it. Unlike the application entity, which doesn’t hold any tags. It is essential to make this distinction for not mixing our entity with JSON tags. If the `OpenWeather` API changes, our entity won’t be. For example, if it will return “temperature” instead of “temp”, we won’t need to change our application’s code, only the provider’s code.

The provider itself calls the external API and converts the response into the entity:

Note that this package’s name is the same as the actual external service’s name -`openweather`. This is since it resides in the most outer layer as it is related to the *mechanism* and not the BL. By doing so, we are setting a clue for the reader that this package has nothing to do with the actual application BL or policy. It’s the implementation detail.

## Main

Main is where we compose everything:

main gets a flag representing a city, initializes the provider and the service, and triggers our forecasting service method.

That is all there is. At first glance, it may look like it’s over-engineering for such a small application. But hopefully, after some time, the application will grow and change. You might want to have an entity outfit that holds the vital policy about 21°C. It doesn’t matter much for this example. Separating the code that knows about the outside world is crucial for enabling such changes in the future.

This separation is the main core of the clean architecture approach, which we’ll take a closer look at in the next section.

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