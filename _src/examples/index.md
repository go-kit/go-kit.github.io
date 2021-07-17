{
    "template": "../inc/page.template.source",
    "subtitle": "Examples"
}
---
Users have consistently found that the most effective way to learn Go kit
 is to study and learn from example services.
Here you can find some examples that will orient you to Go kit idioms,
 patterns, and best practices.

- **[stringsvc](stringsvc.html)** is a tutorial that takes you through
  writing a service from first principles. It can help you understand
  the decisions that went into Go kit's design.

- **[addsvc](https://github.com/go-kit/examples/blob/master/addsvc)**
  is the original example service.
  It exposes a set of operations over all supported transports.
  It's fully logged, instrumented, and uses distributed tracing.
  It also demonstrates how to create and use client packages.
  It demonstrates almost all of Go kit's features.

- **[profilesvc](https://github.com/go-kit/examples/blob/master/profilesvc)**
  demonstrates how to use Go kit
  to write a microservice with a REST-ish API.
  It uses net/http and the excellent Gorilla web toolkit.

- **[shipping](https://github.com/go-kit/examples/blob/master/shipping)**
  is a complete, "real-world" application composed of multiple microservices,
  based on Domain Driven Design principles.

- **[apigateway](https://github.com/go-kit/examples/blob/master/apigateway)**
  demonstrates how to implement the [API gateway pattern](http://microservices.io/patterns/apigateway.html)
  backed by a Consul service discovery system.