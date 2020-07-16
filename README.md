# spring-buildpacks-poc
POC using Spring Boot and create image with buildpacks

This POC is being make based on instructions from [this site](https://medium.com/@TimvanBaarsen/first-look-at-cloud-native-buildpacks-support-in-spring-boot-2-3-milestone-1-ece8e72ed93f), but using Spring Gradle plugin instead Spring Maven plugin.

## What are Cloud Native Buildpacks?

From https://buildpacks.io

_"The Cloud Native Buildpacks project was initiated by Pivotal and Heroku in January 2018 and joined the Cloud Native Sandbox in October 2018. The project aims to unify the buildpack ecosystems with a platform-to-buildpack contract that is well-defined and that incorporates learnings from maintaining production-grade buildpacks for years at both Pivotal and Heroku."_ 

Buildpacks provide a higher-level abstraction for building applications compared to Dockerfiles. The job of a buildpack is to detect and gather everything your application needs to build and run.

Long story short, a buildpack will transform your source code into a runnable application image!

## Prerequisites
Make sure you have **Docker** installed. If not, install Docker (optionally install Docker Compose as well).

Verify Docker:
```bash
$ docker -v
Docker version 19.03.8, build afacb8b7f0
```
And optionally Docker Compose:
```bash
$ docker-compose -v
docker-compose version 1.25.0, build unknown
```

## Building docker image
Now package the application source code into an OCI image using the Spring Boot Gradle plugin and buildpacks:
```bash
$ gradle bootBuildImage
```

You can check that Gradle will start to create the image with name **docker.io/library/buildpack-demo:0.0.1-SNAPSHOT**.

To do that it will pull buildpack builder/runtime images from [paketo-buildpacks](https://paketo.io/) and, after that, start to create the image.

When the build succeeds, you should be able to see the image using Docker:
```bash
docker images |grep buildpack-demo                                                                                 ░▒▓ ✔ 
buildpack-demo    0.0.1-SNAPSHOT   fd0862cad8fb     40 years ago    266MB
```

##Run the application using Docker
Now it’s time to start a container via Docker.
```bash
$ docker run -d -p 8080:8080 --name springbootcontainer buildpack-demo:0.0.1-SNAPSHOT                                ░▒▓ ✔ 
```
Check whether or not the container is running:
```bash
$ docker ps
CONTAINER ID        IMAGE                           COMMAND                  CREATED             STATUS              PORTS                                        NAMES
9c86ed8828f7        buildpack-demo:0.0.1-SNAPSHOT   "/cnb/lifecycle/laun…"   12 seconds ago      Up 5 seconds        0.0.0.0:8080->8080/tcp                     
```

Now hit http://localhost:8080 in your favorite browser to see the greeting from the simple Spring MVC Rest controller. Excellent, the application is up and running in the container!

Also take a look at the Spring Boot info actuator endpoint: http://localhost:8080/actuator/info

You can verify the Spring Boot application is running on Bellsoft version 11.0.8. The show the Java and application details customize the Spring Boot info actuator in your application.yml configuration file. The version can change based on image updates by Paketo.

To stop the container run:
```
$ docker stop springbootcontainer
```

## Reference Documentation
For further reference, please consider the following sections:

* [Official Gradle documentation](https://docs.gradle.org)
* [Spring Boot Gradle Plugin Reference Guide](https://docs.spring.io/spring-boot/docs/2.3.1.RELEASE/gradle-plugin/reference/html/)
* [Create an OCI image](https://docs.spring.io/spring-boot/docs/2.3.1.RELEASE/gradle-plugin/reference/html/#build-image)
* [Spring Web](https://docs.spring.io/spring-boot/docs/2.3.1.RELEASE/reference/htmlsingle/#boot-features-developing-web-applications)
* [Spring Boot Actuator](https://docs.spring.io/spring-boot/docs/2.3.1.RELEASE/reference/htmlsingle/#production-ready)

### Guides
The following guides illustrate how to use some features concretely:

* [Building a RESTful Web Service](https://spring.io/guides/gs/rest-service/)
* [Serving Web Content with Spring MVC](https://spring.io/guides/gs/serving-web-content/)
* [Building REST services with Spring](https://spring.io/guides/tutorials/bookmarks/)
* [Building a RESTful Web Service with Spring Boot Actuator](https://spring.io/guides/gs/actuator-service/)

### Additional Links
These additional references should also help you:

* [Gradle Build Scans – insights for your project's build](https://scans.gradle.com#gradle)

