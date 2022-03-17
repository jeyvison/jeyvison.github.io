---
title: "Setting Up Your Microservice With Micronaut"
date: 2018-07-01T19:34:39Z
draft: false
---

Micronaut is a JVM-based framework designed to help developers build building modular, easily testable microservice applications.

It has several tools that are needed to build a microservice application like:

- Dependency Injection and Inversion of Control (IoC)
- Sensible Defaults and Auto-Configuration
- Configuration and Configuration Sharing
- Service Discovery
- HTTP Routing
- HTTP Client with client-side load-balancing

At the same time, it provides:

- Fast startup time
- Reduced memory footprint
- Minimal use of reflection
- Minimal use of proxies
- Easy unit testing

It was designed to avoid some downsides of other frameworks used to build microservices. In this article, we’re gonna build a hello world app with it.
Installing the CLI tool

The framework provides us a CLI tool to help bootstraping applications, profiles and other things.We can do a couple of things to get it installed and running:
Using SDKMAN:

As easy as :

```
 sdk install micronaut
```

If the package manager of you OS doesn't have it yet, you can install it manually.

## Installing Manually

clone this repo:

```
git clone git@github.com:micronaut-projects/micronaut-core.git
cd micronaut-core/
./gradlew publishToMavenLocal
```

It will publish the CLI to your local cache. And then you will have to update your bashrc file(or zshrc, or *rc…) to include microunaut to the path, like this:

```
export MICRONAUT_HOME=~/path/to/micronaut-core
export PATH="$PATH:$MICRONAUT_HOME/cli/build/bin"
````

Don't forget to

```
source ~/.bash_profile
````

your file so it will be reloaded in your terminal ;)

Great! Now that we have Micronaut installed let's run it:

```
>$ mnUsage (optionals marked with *):’
mn [target] [arguments]*’| Examples:
$ mn create-app my-app| Language support for Groovy and/or Kotlin can be enabled with the corresponding feature::
$ mn create-app my-groovy-app -features=groovy
$ mn create-app my-kotlin-app -features=kotlin| Available Commands (type mn help ‘command-name’ for more info):
| Command Name                         Command Description
 — — — — — — — — — — — — — — — — — — — - - - - - - - - - - - - - - -
create-app                 Creates an application
create-federation          Creates a federation of services
create-function            Creates a serverless function application
create-profile             Creates a profile
help                       Prints help information for a command
list-profiles              Lists the available profiles
profile-info               Display information about a given profile| Detailed usage with help [command]
```

Wow! The first thing to notice is that Micronaut supports creating projects for Groovy and Kotlin.
But for this post we will stick with Java.

After installing the CLI tool and verify that everything is working fine, we are going to create our app.

## Creating the Application

It’s time to create our app!

let’s run :

```
mn create-app hello-micronaut.
```

This command creates a gradle-based java project with microunaut dependencies and an Application class with a main method.
This main method will be responsible to start our application, something like spring-boot does.

The generated project looks like this:

```
hello-micronaut
├── build.gradle
├── gradle
│   └── wrapper
│       ├── gradle-wrapper.jar
│       └── gradle-wrapper.properties
├── gradlew
├── gradlew.bat
├── micronaut-cli.yml
└── src
    ├── main
    │   ├── java
    │   │   └── hello
    │   │       └── micronaut
    │   │           └── Application.java
    │   └── resources
    │       ├── application.yml
    │       └── logback.xml
    └── test
        └── java
```

We have:

- A build.gradle file with some plugins and the dependencies we will need.
- An Application class that will start our app
- An application.yml file where we can configure the port where our service will be running, for example
- A logback.xml file with default log configuration.
- A microunaut-cli.yml file for code-generation purposes.

Well, why should we wait more? Let’s run our app and see what happens!
Running and configuring

When we run

```
./gradlew run
```

the service is started at a random port in localhost. If you access it via browser you will see something like this:

```
{"_links":{"self":{"href":"/","templated":false}},"message":"Page Not Found"}
```

It means our service is up but there is no index page.

What if we wanted to set port 8080 to our app? We can configure it using application.yml. This file was already created and looks like this:

```
micronaut:
    application:
        name: hello-micronaut
```

We can set

```
micronaut:
    application:
        name: hello-micronaut
    server:
        port: 8080
```

and restart it. Now you should be able to access our application at localhost:8080.

Next, we will create a controller to handle the requests and show our message :)


## Creating a controller

Now that we learned how to run and configure the port of our service , let’s create a controller that will respond to GET requests in “/hello”.
Let’s create the following class:

```
import io.micronaut.http.annotation.*;@Controller(“/hello”) 
public class HelloController {   @Get(“/”) 
   public String index() {
       return “Hello Micronaut”;   }
}
```

If you ever worked with Spring Framework, these annotations are not that strange to you but let’s check what they do:

- With @Controller, we are saying that this class will handle all the requests addressed to “/hello” .
- @Get annotation gives to the method index() the responsability to handle GET requests that comes to “/hello”.

Restart the server and navigate to localhost:8080/hello in your browser. You should see the string “Hello Micronaut” on it.

To conclude, we saw in this post how easy it is to create a service with Micronaut. It has AutoConfiguration, Client native features(Service discovery, load-balancing, etc) and much more!

Take a look at [their docs](https://docs.micronaut.io/latest/guide/index.html), and if you wanna see the code that was created here, you can clone [this repo](https://github.com/jeyvison/hello-micronaut).