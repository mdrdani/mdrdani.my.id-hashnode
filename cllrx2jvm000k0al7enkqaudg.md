---
title: "Containerize your #nodejs application"
datePublished: Sat Aug 26 2023 11:06:48 GMT+0000 (Coordinated Universal Time)
cuid: cllrx2jvm000k0al7enkqaudg
slug: containerize-your-nodejs-application
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/xkBaqlcqeb4/upload/c48f811101481055ac178e7bc2761345.jpeg
tags: docker, nodejs

---

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1693037937586/de027d38-0524-468a-9272-35efdbed39f6.png align="center")

### Set the base image

> FROM node:20-alpine

Use the official Node.js container image in 𝘢𝘭𝘱𝘪𝘯𝘦 variant  
  
Advantages of using minimal base images:  
  
➡️ reduced attack surface thanks to fewer unnecessary packages and security vulnerabilities,  
➡️ improved performance thanks to efficient disk space, memory and network utilization.

### Set the working directory

> WORKDIR /app

Set the working directory which defines the target location inside the container for all the subsequent instructions.

### Copy and install depedencies

> COPY package\*.json /app
> 
> RUN npm install

Use 𝘯𝘱𝘮 to install dependencies. By separating the dependencies from application source code you can take advantage of container layer caching. As long as you do not change the versions of libraries used by your application, the cached version of previously built layers will be reused, which will significantly shorten the build time.

### Copy the application code

> COPY --chown=node:node app.js /app

Copy the source code of your application to the working directory configured in the second step.

### Run as non-privileged user

> USER node

Ensure that processes running in your container will be executed in non-privileged mode. Since a non-privileged user called 𝘯𝘰𝘥𝘦 is already registered in the base Node.js image, just use it.

### Set the entry command

> CMD \["node", "app.js"\]

Finally, specify which application should be executed when the container starts.

complete Dockerfile

```bash
FROM node:20-alpine

WORKDIR /app

COPY package*.json /app
RUN npm install

COPY --chown=node:node app.js /app

USER node

CMD ["node", "app.js"]
```