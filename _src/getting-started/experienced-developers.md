{
    "template": "../inc/page.template.source",
    "subtitle": "Getting Started Guide for Experienced Developers",
    "toc": true
}
---

# Introduction

## Understanding Go kit key concepts

### Go kit is not an MVC framework

If you're coming from Symfony (PHP), Rails (Ruby), Django (Python),
or one of the many popular MVC-style frameworks out there, the first
thing you should know is that Go kit is not an MVC framework.

Three primary concepts you'll learn about are:
1. Transports
2. Endpoints
3. Services

With a HTTP microservice, requests enter at no. 1 and flow down to no. 3.

This is a bit of an adjustment, but once you grok the concepts,
you'll hopefully see that the Go kit design is well suited for modern,
enterprise-grade microservices.

### Transports

The Transport domain is bound to concrete transports like HTTP or gRPC.

In a world where microservices may support one or more transports,
this is very powerful; you can support a legacy HTTP API and a newer RPC service,
all in a single microservice.

When implementing a HTTP API, your routing is defined within a HTTP Transport.
It is most common to see routes defined in a HTTP Router function like this:

```
r.Methods("POST").Path("/profiles/").Handler(httptransport.NewServer(
		e.PostProfileEndpoint,
		decodePostProfileRequest,
		encodeResponse,
		options...,
))
```

### Endpoints

An Endpoint is like an action/handler on a Controller; it's where safety and antifragile logic lives.

If you implement two Transports (HTTP and RPC), you might have two methods of sending requests to the same Endpoint.

### Services

Services are where all of the business logic is implemented.

An Endpoint might glue multiple Services together.

### Middlewares

Go kit tries to enforce a strict separation of concerns through use of the middleware (or decorator) pattern.

Middlewares can wrap Endpoints or Services to add functionality such as:

  * Logging
  * Rate limiting
  * Load balancing
  * Distributed tracing

You can even chain multiple Middlewares to an Endpoint or Service.

# Dependency Injection

## How do I wire up my Component Graph? 

Inversion of control is a common feature of frameworks, implemented via Dependency Injection or Service Locator patterns.

When using Go kit, you should wire up your entire component graph in `func main() {}`.

This avoids using global state as a shortcut, and requires you to pass dependencies to components explicitly as parameters to constructors.

### Example Component Graph

Let's say we have the following components:

  1. `Logger`
  2. `ToDoService` (implements `ToDoServiceInterface`)
  3. `ToDoServiceLoggingMiddleware` (implements `ToDoServiceInterface`)
  4. `ToDoEndpoints`
  5. `ToDoHTTP`

With the following dependencies:

  * `ToDoServiceLoggingMiddleware` requires `Logger` and `ToDoServiceInterface`
  * `ToDoEndpoints` requires a `ToDoServiceInterface`
  * `ToDoHTTP` requires a `ToDoEndpoints`

In your `func main() {}` you would wire this up as:

```
l = log.NewLogger()
s = todo.NewService()`
s = todo.NewServiceLoggingMiddleware(l)(s)
e = todo.NewEndpoints(s)
h = todo.NewHttpHandler(e)
```

At the cost of having a potentially large `func main() {}`, composition is explicit and declarative.

# Persistence

## How should Go kit services manage persistence?

Go kit encourages you to implement a persistence layer using the Repository pattern.

This isolates domain structs from the details of database access code.

When you define a Repository interface as a Service dependency, it also enables you to have multiple implementations.

For example, it is common to write a PostgreSQL implementation for production and an In-Memory implementation for running tests.

