---
title: "Set up develop branch build and test locally"
date: 2017-12-30T21:13:22-05:00
description: ""
categories: []
keywords: []
slug: ""
aliases: []
toc: false
draft: false
---

In this tutorial, we are going to setup the develop branch build and test for networknt
organization on github.com on my MacBookPro laptop. The configuration is designed for
networknt projects and light-examples-4j demo applications; however it is very easy to
customized the develop.yml to make it for your Java modules and microservices. If you
want to learn how to use it with your microservices, please refer to [microservices develop][]


### Preparation

To build all projects we need to have the following installed on your local.

* Java JDK 1.8 - JDK to compile Java source code
* Maven 3.3.X - light-4j frameworks are built with Maven
* Git - Clone source code from github.com
* Docker - Running some of the dependencies for end-to-end testing

To perform integration test and end-to-end test, we need to have the following Docker container
up and running. 

* Kafka 1.0.0 - used by light-eventuate-4j, light-tram-4j and light-saga-4j
* Zookeeper - used by Kafka
* Mysql - used by light-eventuate-4j, light-tram-4j, light-saga-4j and some examples
* Redis - used by light-session-4j for distributed session
* cdcserver - for light-tram-4j and examples
* cdcserver - for light-eventuate-4j and examples



It is very easy to start all services with a docker-compose in light-docker repo. However, we
need to setup DOCKER_HOST_IP environment variable on MacBookPro first by following [this tutorial][]

Let's assume we have a workspace called networknt under your user home directory. And we are in
the same terminal that have set up the DOCKER_HOST_IP export.

```
cd ~/networknt
git clone https://github.com/networknt/light-docker.git
cd light-docker
docker-compose -f docker-compose-integration-test.yml up -d
```

Above will start all dependencies for develop branch build and test; however, for the time being,
we need to start cdcserver for tram separately as there is a bug to prevent it to start with Kafka
at the same time. 

So after the above you have to run the following docker-compose for now. Wait for a minute or so
as we are running docker-compose as -d detached mode. If you want to see the output on the console
then you can use a command line with -d at the end. 

```
docker-compose -f docker-compose-cdcserver-for-tram.yml up -d
```

### Clone and build light-bot

Now we can clone and build light-bot repository. We are going to use the same networknt workspace. 


```
cd ~/networknt
git clone https://github.com/networknt/light-bot.git
cd light-bot
./gradlew build
```

As you can see this project is built with Gradle as we are trying to support both Maven and Gradle
in the light-codegen for API projects. 

The first time build will take a while as Gradle 4.4.1 and Kotlin DSL need to be downloaded. 

### Run the develop branch build with default config

```
cd ~/networknt/light-bot/bot-cli/build/libs
java -jar bot-cli-fat-1.0.jar -t develop

```

if the build is failed, you can check bot.log in the same directory as default email server is not
set up with a correct password, there would be no email will be sent out if build or test is failed.



### Run the develop branch build with customized config





[microservices develop]: /tutorial/bot/microservices-develop/
[this tutorial]: /tutorial/eventuate/getting-started/