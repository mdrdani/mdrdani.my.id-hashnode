---
title: "Build & Push Docker Image with Jenkins"
datePublished: Wed Jul 19 2023 15:04:09 GMT+0000 (Coordinated Universal Time)
cuid: clk9uteu1000e08l22mo70lcr
slug: build-push-docker-image-with-jenkins
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/zNRITe8NPqY/upload/dff08ac305a928e90a496819e9d23f56.jpeg
tags: docker, jenkins

---

Setting credentials Manage Jenkins → Security (Credentials). Masukan Username dan Password Docker Hub

![](https://file.notion.so/f/s/35fce9db-c3cb-4160-9c99-9a04acff58dc/Untitled.png?id=f5ed5e99-7a6f-47fe-abd8-3bfc242d6a5c&table=block&spaceId=355c59a1-9514-4ca4-8a2f-60fc7da6dd8b&expirationTimestamp=1689868800000&signature=n43eDXGNdt5XcJFdO3PIT4oXDrBfMTHvI33aqYMrYz4&downloadName=Untitled.png align="left")

Create New Job Pipeline

```bash
pipeline {
    agent any

    environment {
        REGISTRY_CREDENTIALS = credentials('dockerhub-cehamot')
        DOCKER_IMAGE_NAME = "cehamot/rssejahterapp"
        DOCKER_IMAGE_TAG = "${env.BUILD_NUMBER}"
        REGISTRY_URL = "https://index.docker.io/v1/"
        REPO_URL = "https://github.com/mdrdani/appointmentApp.git"
        REPO_BRANCH = "master"
    }

    stages {
        stage('Clone Git Repository') {
            steps {
                git branch: REPO_BRANCH, url: REPO_URL
            }
        }

        stage('Build Docker Image') {
            steps {
                sh "docker build -t ${DOCKER_IMAGE_NAME}:${DOCKER_IMAGE_TAG} ."
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    withDockerRegistry([credentialsId: 'dockerhub-cehamot', url: REGISTRY_URL]) {
                        sh "docker push ${DOCKER_IMAGE_NAME}:${DOCKER_IMAGE_TAG}"
                    }
                }
            }
        }
    }
}
```

Install Juga plugin **Jenkins** &gt; **Docker Pipeline.**

**Build Now.**

![](https://file.notion.so/f/s/3551809a-ee18-45cf-bc88-6f475413ed93/Untitled.png?id=ecb759d7-2386-432a-8dcd-95869fda46a5&table=block&spaceId=355c59a1-9514-4ca4-8a2f-60fc7da6dd8b&expirationTimestamp=1689868800000&signature=PVJJtUKpX7UPAPQr1ZOOshDrSmHXvWs_SNhDlhg2NM4&downloadName=Untitled.png align="left")

![](https://file.notion.so/f/s/44fe5d07-27a1-4921-84cb-48a93143e2b8/Untitled.png?id=6d3f5e1c-0310-475a-9262-d155a346cdb0&table=block&spaceId=355c59a1-9514-4ca4-8a2f-60fc7da6dd8b&expirationTimestamp=1689868800000&signature=8BI-iUeQtOa3a-TwPWyt4wxOU-nJt8fKHIjrUwxTGug&downloadName=Untitled.png align="left")