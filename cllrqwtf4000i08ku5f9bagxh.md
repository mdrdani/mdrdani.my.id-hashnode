---
title: "Containerize your #go application"
datePublished: Sat Aug 26 2023 08:14:22 GMT+0000 (Coordinated Universal Time)
cuid: cllrqwtf4000i08ku5f9bagxh
slug: containerize-your-go-application
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/fd9mIBluHkA/upload/30d0ed5155240d70acc56dec109dab02.jpeg
tags: docker, golang

---

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1693036899789/50672595-82e3-4cc5-a3a1-bb31fadd1873.png align="center")

### Set the base image

> FROM golang:1.21

use the official Go Docker image.

### Set the working directory

> *WORKDIR /app*

set the working directory which defines the target location inside the container for all the subsequent instructions.

### Copy and install dependencies

> *COPY go.mod go.sum ./*
> 
> RUN go mod download

Use 𝘨𝘰.𝘮𝘰𝘥 to manage dependencies. By separating the dependencies from application source code you can take advantage of container layer caching. As long as you do not change the versions of libraries used by your application, the cached version of previously built layers will be reused, which will significantly shorten the build time. Additionally, including 𝘨𝘰.𝘴𝘶𝘮 file with checksums of all direct and indirect dependencies ensures that their content has not been modified.

### Copy the application code

> COPY \*.go ./

Copy the source code of your application to the working directory created in the second step.

### Compile the application

> RUN go build -o /myapp

Compile your application from the source code and

### Run as non-privileged user

> USER 1000

Ensure that processes running in your container will be executed in non-privileged mode. Since a non-privileged user with id 1000 is already registered in the base Go image, just use it.

### Set the entry command

> CMD \["/myapp"\]

this complete Dockerfile

```bash
FROM golang:1.21

WORKDIR /app

COPY go.mod go.sum ./
RUN go mod download

COPY *.go ./

RUN go build -o /myapp

USER 1000

CMD ["/myapp"]
```