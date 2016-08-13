---
layout: post
title: "Docker Engine"
description: "Docker Engine"
tags: [Linux, Docker]
---

## Docker Overview

Docker is container technique overall. 

Docker is an open platform for developing, shipping, and running applications. Docker is designed to deliver your applications faster.With Docker you can separate your applications from your infrastructure and treat your infrastructure like a managed application. Docker helps you ship code faster, test faster, deploy faster, and shorten the cycle between writing code and running code.

Docker does this by combining **kernel containerization** features with workflows and tooling that help you manage and deploy your applications.

### What is the Docker platform?
At its core, Docker provides a way to run almost any application securely isolated in a **container**. 

Surrounding the container is tooling and a platform which can help you in several ways:

* Get your applications (and supporting components) into Docker containers
* Distribute and ship those containers to your teams for further development and testing
* Deploy those applications to your production environment, whether it is in a local data center or the Cloud

### What is Docker Engine?

Docker Engine is a client-server application with these major components:
* A server which is a type of long-running program called a **daemon** process.
* A **REST API** which specifies interfaces that programs can use to talk to the daemon and instruct it what to do.
* A command line interface (**CLI**) client.

<figure>
	<img src="https://docs.docker.com/engine/article-img/engine-components-flow.png" alt="">
</figure>

The daemon creates and manages Docker objects. Docker objects include **images**, **containers**, **networks**, **data volumes**, and so forth.

### What Can I use Docker for?

* ***Faster delivery of your applications***
* ***Deploying and scaling more easily***
* ***Achieving higher density and running more workloads***
 
### What is Docker's architecture?

Docker uses a client-server architecture.

The **Docker client** talks to the **Docker daemon**, which does the heavy lifting of *building, running, and distributing* your **Docker containers**. 
