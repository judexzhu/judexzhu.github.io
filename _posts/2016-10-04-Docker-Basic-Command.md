---
layout: post
title: "Docker Basic Command"
description: "Docker Basic Command"
tags: [Docker]
---

## Check Docker Info

### Check Docker Version 

```
docker version
```

### check docker system info

```
docker info
```

## Image

### search images

```
docker search image_name
```

### download images

```
docker puch image_name
```

### list images

```
docker images
```

### delete one or more images

```
docker rmi image_name
```

### show docker image history

```
docker history image_name
```

## start container 

### run cmd "echo" in container and output "hello world"

```
docker run image_name echo "hello world"
```

### login container tty

```
docker run -i -t image_name /bin/bash
```

### install package in the container 

```
docker run image_name apt-get install -y package-name
```

## Check container 

### list all the running containers

```
docker ps
```

### list all the containers include offline 

```
docker ps -a 
```

### list last time running container

```
docker ps -l
```

## save modified container to a new image 

```
docker commit ID new_image_name
```

## container operations

### delete all the containers

```
docker rm 'docker ps -a -q'
```

### delete specific container 

```
docker rm NAME/ID
```

### start, stop, kill specific container  
