---
layout: post
title: "Docker Swarm Study Notes (1)"
description: "Docker Swarm Study Notes (1)"
tags: [Linux, Docker, Swarm]
---
<figure>
	<a href="https://blog.docker.com/media/2015/11/image00.png"><img src="https://blog.docker.com/media/2015/11/image00.png" alt=""></a>
  <figcaption><a href="https://blog.docker.com/2015/11/deploy-manage-cluster-docker-swarm//" title="From Official Website Blog: Deploy And Manage Any Cluster Manager With Docker Swarm">From Official Website Blog: Deploy And Manage Any Cluster Manager With Docker Swarm</a>.</figcaption>
</figure>

## Docker Swarm Overview
Docker Swarm is native clustering for Docker. It turns a pool of Docker hosts into a single, virtual Docker host. Beacause Docker.Swarm serves the standard Docker API, any tool that already communicates with a Docker daemon can use Swarm to transparently scale to multiple hosts.

Support tools include, but are not limited to, the following:

* Dokku
* Docker Compose
* Docker Machine
* Jenkins

And of course, the Docker clients itself is also supported.

Like other Docker projects, Docker Swarm follows the "***Swap, Olug, and Play***" principle.

### Understand Swarm cluster creation
The first step to creating a Swarm cluster on your network is to pull the Docker Swarm image. Then, using Docker, you configure the Swarm manager and all the nodes to run Docker Swarm. 

Method:

	1. open a TCP port on each node for communication with the Swarm manager
	2. install Docker on each node
	3. create and manage TLS certificates to secure your cluster

As a starting point, the manual method is best suited for experienced administrators or programmers contributing to Docker Swarm. The alternative is to use docker-machine to install a cluster.

Using ***Docker Machine***, you can quickly install a Docker Swarm on cloud providers or inside your own data center.

If you have ***VirtualBox*** installed on your local machine, you can quickly build and explore Docker Swarm in your local environment. 
This method automatically generates a certificate to secure your cluster.

## How to get Docker Swarm

You can create a Docker Swarm cluster using the ***swarm executable image*** from a container or using an ***executable swarm binary*** you install on your system.

> For I have a MacOX system, I'm gonna install docker on my Mac, Details about ["How to install Docker on Mac "][1]

[1]:https://docs.docker.com/engine/installation/mac/

### 
