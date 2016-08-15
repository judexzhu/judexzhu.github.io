---
layout: post
title: "Docker Engine"
description: "Docker Engine"
tags: [Linux, Docker]
---

# Docker Overview

Docker is container technique overall. 

Docker is an open platform for developing, shipping, and running applications. Docker is designed to deliver your applications faster.With Docker you can separate your applications from your infrastructure and treat your infrastructure like a managed application. Docker helps you ship code faster, test faster, deploy faster, and shorten the cycle between writing code and running code.

Docker does this by combining **kernel containerization** features with workflows and tooling that help you manage and deploy your applications.

## What is the Docker platform?
At its core, Docker provides a way to run almost any application securely isolated in a **container**. 

Surrounding the container is tooling and a platform which can help you in several ways:

* Get your applications (and supporting components) into Docker containers
* Distribute and ship those containers to your teams for further development and testing
* Deploy those applications to your production environment, whether it is in a local data center or the Cloud

## What is Docker Engine?

Docker Engine is a client-server application with these major components:
* A server which is a type of long-running program called a **daemon** process.
* A **REST API** which specifies interfaces that programs can use to talk to the daemon and instruct it what to do.
* A command line interface (**CLI**) client.

<figure>
	<img src="https://docs.docker.com/engine/article-img/engine-components-flow.png" alt="">
</figure>

The daemon creates and manages Docker objects. Docker objects include **images**, **containers**, **networks**, **data volumes**, and so forth.

## What Can I use Docker for?

* ***Faster delivery of your applications***
* ***Deploying and scaling more easily***
* ***Achieving higher density and running more workloads***
 
## What is Docker's architecture?

Docker uses a client-server architecture.

The **Docker client** talks to the **Docker daemon**, which does the heavy lifting of *building, running*, and *distributing* your **Docker containers**. 

Both the Docker client and the daemon can run on the same system, or you can connect a Docker client to a remote Docker daemon. 

The Docker client and daemon communicate via **sockets** or through a **RESTful API**.

<figure>
	<img src="https://docs.docker.com/engine/article-img/architecture.svg" alt="">
</figure>

### The Docker daemon

The Docker daemon runs on a host machine. The user does not directly interact with the daemon, but instead through the Docker client.

### The Docker Client

The Docker client, in the form of the docker binary, is the primary user interface to Docker.

### Inside Docker

To understand Docker’s internals, you need to know about three resources:
* Docker images
* Docker registries 
* Docker containers

#### Docker images

***A Docker image is a read-only template.*** Images are used to create Docker containers.

Docker provides a simple way to build new images or update existing images, or you can download Docker images that other people have already created. 

Docker images are the **build** component of Docker.

### Docker registries

***Docker registries hold images.***

The public Docker registry is provided with the [Docker Hub][1].
[1]:https://hub.docker.com/

Docker registries are the **distribution** component of Docker.

#### Docker containers

Each container is created from a Docker image. Docker containers can be run, started, stopped, moved, and deleted. Each container is an isolated and secure application platform. 

Docker containers are the **run** component of Docker.

### How does a Docker image work?

 Each image consists of a series of layers. Docker makes use of [union file systems][2] to combine these layers into a single image. Union file systems allow files and directories of separate file systems, known as branches, to be transparently overlaid, forming a single coherent file system.
 [2]:http://en.wikipedia.org/wiki/UnionFS
 
 One of the reasons Docker is so lightweight is because of these layers. When you change a Docker image—for example, update an application to a new version— a new layer gets built. Thus, rather than replacing the whole image or entirely rebuilding, as you may do with a virtual machine, only that layer is added or updated. Now you don’t need to distribute a whole new image, just the update, making distributing Docker images faster and simpler.
 
 Every image starts from a base image, for example ```ubuntu```, a base Ubuntu image, or ```fedora```, a base Fedora image. You can also use images of your own as the basis for a new image, for example if you have a base Apache image you could use this as the base of all your web application images.
 
 Docker images are then built from these base images using a simple, descriptive set of steps we call *instructions*. Each instruction creates a new layer in our image. Instructions include actions like:
 
 * Run a command
 * Add a file or directory
 * Creat an environment variable
 * What process to run when launching a container from this image
 
These instructions are stored in a file called a ```Dockerfile```. A ```Dockerfile``` is a text based script that contains instructions and commands for building the image from the base image. Docker reads this Dockerfile when you request a build of an image, executes the instructions, and returns a final image.

### How does a Docker registry work?
The Docker registry is the store for your Docker images. Once you build a Docker image you can push it to a public registry such as [Docker Hub][1] or to your own registry running behind your firewall.

Using the Docker client, you can search for already published images and then pull them down to your Docker host to build containers from them.

### How does a container work?
A container consists of an operating system, user-added files, and meta-data. As we’ve seen, each container is built from an image. That image tells Docker what the container holds, what process to run when the container is launched, and a variety of other configuration data. The Docker image is read-only. When Docker runs a container from an image, it adds a read-write layer on top of the image (using a **union file system** as we saw earlier) in which your application can then run.

### What happens when you run a container?
Using the docker binary or via the API, the Docker client tells the Docker daemon to run a container.

``` bash
$ docker run -i -t ubuntu /bin/bash
```

The Docker Engine client is launched using the ```docker``` binary with the ```run``` option running a new container. The bare minimum the Docker client needs to tell the Docker daemon to run the container is:
* What Docker image to build the container from, for example, ```ubuntu```
* The command you want to run inside the container when it is launched, for example,```/bin/bash```
 
In order, Docker Engine does the following:
* **Pulls the ```ubuntu``` image:** Docker Engine checks for the presence of the ubuntu image. If the image already exists, then Docker Engine uses it for the new container. If it doesn’t exist locally on the host, then Docker Engine pulls it from Docker Hub.
