---
author: "Péter Huba"
title: "Building backends using Quarkus"
date: "2023-01-28"
summary: "How powerful this new framework proves to be in practice?"
description: "How powerful this new framework proves to be in practice?"
tags: ["Java", "Backend", "Quarkus"]
showToc: true
TocOpen: false
hidemeta: false
comments: true
disableHLJS: true
disableShare: false
disableHLJS: false
hideSummary: false
searchHidden: true
ShowReadingTime: true
ShowBreadCrumbs: true
ShowPostNavLinks: true
UseHugoToc: true
draft: false
cover:
    # image: "<image path/url>" # image path/url
    # alt: "<alt text>" # alt text
    # caption: "<text>" # display caption under cover
    relative: false # when using page bundles set this to true
    hidden: true # only hide on current single page
---

[Quarkus](https://quarkus.io) is a Kubernetes-native Java framework that is designed to make Java a leading platform in cloud-native environments. It aims to bring Java into the modern era by addressing the limitations of traditional Java frameworks, such as slow startup times, high memory usage and sometimes cumbersome usage.
But how good is it in practice?

![](/quarkus_logo.png)

### Introduction to Quarkus
Quarkus is a relatively new, open-source framework for building cloud-native, container-first Java applications. It is designed to be fast, efficient, and lightweight, making it an ideal choice for developing microservices and serverless applications while offering developers a unified reactive and imperative programming model. Key technology components surrounding it are OpenJDK HotSpot and GraalVM, for the support of optionally building native executables from the usual Java code.

In addition to its technical capabilities, Quarkus also offers a number of benefits in terms of developer productivity. Its development mode allows for live reloading, which means that changes to the code can be immediately reflected in the application without the need for a full rebuild. It also has a wide array of extensions and plugins that can be used to easily add functionality to your application, such as database connectivity, security, and more.

It is built on top of well known standards, libraries and APIs. For dependency injection it uses CDI, for REST endpoints JAX-RS and JBoss-based logging out of the box, has JPA support etc.

### Quarkus' key features
#### Container-first
Quarkus is designed around a container-first philosophy, optimizing for fast startup times, excellent performance and small memory footprint, making it a great option for resource-constrained environments or use cases. All included libraries and application code is optimized for these goals during compile-time. It doesn't rely on reflection during runtime, it uses static binding instead. Code that doesn't have an execution path isn't loaded at runtime into the JVM.

Besides these optimizations, it provides several handy features for the application to be used in a containerized environment like health checks with liveness and readiness support for dependencies like DBs and messaging brokers, tracing and metrics support etc.

#### Extensions
It has an extension framework, the goal of which is to offer extensibility via including custom or pre-built libraries while enforcing the guidelines which make the optimizations mentioned in the previous section possible. Creating an extension involves an augmentation part, which is responsible for all metadata processing, such as reading annotations, XML descriptors etc. The output of this augmentation phase is recorded bytecode which is responsible for directly instantiating the relevant runtime services.

Using these optimizations, Quarkus can provide support for existing libraries, in an optimized way. In the spirit of building on existing standards, extensions – in many cases with starter code too – are provided for many commonly used technologies: Kafka, Kafka Streams, Hibernate, Jackson, RESTEasy, gRPC, AWS S3, Secrets Manager, SQS, Lambda, Liquibase, Redis and the list goes on.

#### Developer productivity
It's clear that having the best developer productivity was an important design goal. For starters, Quarkus has a CLI that can be used to generate an application or an extension, and to run or build them.

The development mode is the bigger deal though. It offers many features that just can't be found in many other places:
* live reload: the application is restarted automatically upon changes, without any manual action, alternatively, once enabled, code can be replaced while still running, to have an instant feedback time while developing
* continuous testing: tests are re-ran automatically on code or test code changes, only those tests are re-ran that are affected by the code change
![](/terminal.png)

* can change log level while the application is running
* developer UI: web interface with built-in terminal to trigger test runs, modify configuration, observe beans and their dependencies
![](/dev_ui1.png)
![](/dev_ui2.png)
* terminal hotkeys are available for most of these features
* automatic provisioning and configuration of common application dependencies to be used when developing in form of Docker containers like DBs, Kafka brokers, schema registries etc.

All these in practice do make the development way faster and make Quarkus joy to use.

#### Native builds
In case performance is paramount for a use case, Quarkus can produce a native build of the application using GraalVM, out of the box. Besides better performance, especially fast first response time, memory usage also can be significantly reduced this way. This makes Quarkus a great choice in a landscape where serverless computing is used too.

Occasionally, one can hit road bumps with native builds, especially if external (non-Quarkus supplied) extensions or libraries are used, in most cases when the library relies on reflection. However, this most of the time can be solved by including manually classes into the native build's scope, by registering the class for reflection explicitly.

### How does it perform in the real world? Is it usable compared to Spring Boot?
Despite the fact that it's a newer framework, it's fast and it performs really well and our team has been happy with choosing it for our microservices. We didn't have much surprises using it because of the vast number extensions making possible to use libraries we're familiar with – even Spring Data JPA can be used as usual with it. The powerful live reload and continuous testing features really can make a difference during development.

There are some things to keep in mind though. Naturally, given the age of the framework, one can run into bugs from time to time, although rarely in our experience. At one time this meant for us abandoning our Quarkus-based lambda function and migrating it to a microservice (a Kafka library was causing the issue). This went quite smoothly however, because of the extensions for Lambda made possible having a codebase that needed basically no Java code change when migrating to a conventional setup. Regarding bugs, the Quarkus development team deserves a shout-out, as bug reports and feature requests are taken care of incredibly quickly.

Compared to Spring Boot, for most things it never feels like there are features missing, all the basics work as expected from configuration to monitoring. Nevertheless, when considering using Quarkus rather than Spring Boot for a new project, one has to weigh also the advantages of the Spring ecosystem's numerous projects that can bring capabilities that could make all the difference depending on the requirements. Spring Batch, Spring Kafka's built-in DLQs, Spring Security's Authentication Provider can save a lot of time and effort, although only if it's clear that no custom solution will be needed anyways.

When several functionalities are needed for an application that are present in Spring, and are not available in Quarkus, and can be foreseen that in case of picking Quarkus, developing a custom solution is not feasible, the best choice might be to stick to Spring.

### Conclusion
Given its performance, ease of use and development tooling, Quarkus definitely deserves a try for anyone working with the Java ecosystem. Given careful analysis on what libraries and framework features are must-haves for a project, it can be safely considered for production use. One just has to go ahead and generate a new project including starter code, using [code.quarkus.io](https://code.quarkus.io).