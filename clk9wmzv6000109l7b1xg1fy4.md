---
title: "Deploy, Rollback, Scale, and Autoscaling an Application with Helm (Kubernetes)"
datePublished: Wed Jul 19 2023 15:55:08 GMT+0000 (Coordinated Universal Time)
cuid: clk9wmzv6000109l7b1xg1fy4
slug: deploy-rollback-scale-and-autoscaling-an-application-with-helm-kubernetes
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/eBRTYyjwpRY/upload/05bd80085ce79a5245bb671175d6a1a0.jpeg
tags: kubernetes, helm

---

untuk memudahkan deploy ke **kubernetes** maka di sini memakai **Helm (package manager untuk kubernetes)** dan di sini juga memakai **Minikube** untuk menjalankan cluster.

How to Install **Helm**

```bash
$ wget https://get.helm.sh/helm-v3.12.0-linux-amd64.tar.gz
$ tar -zxvf helm-v3.12.0-linux-amd64.tar.gz
$ sudo mv linux-amd64/helm /usr/local/bin/helm
$ helm
The Kubernetes package manager

Common actions for Helm:

- helm search:    search for charts
- helm pull:      download a chart to your local directory to view
- helm install:   upload the chart to Kubernetes
- helm list:      list releases of charts

........
```

# Deploy

create folder h**elm,** next create file **values.yml & secrets.yml** di dalam folder **helm.**

**helm/values.yml**

```yaml
php:
  repository: "cehamot/rssejahterapp"
  tag: "8"
  fpmEnabled: false
  envVars:
    - name: APP_ENV
      value: production
    - name: APP_DEBUG
      value: false
    - name: DB_PORT
      value: 3306
    - name: DB_HOST
      value: localhost
```

**helm/secrets.yml**

```yaml
mysql:
  rootPassword: "secret123"
  user: dani
  password: "secret123"
  database: db_rssejahtera

php:
  envVars:
    - name: APP_KEY
      value: "base64:V0xAWsrcyAPMY1+kgysAWm2ptVSA11+UAu78o/MqDjI="
    - name: DB_DATABASE
      value: db_rssejahtera
    - name: DB_USERNAME
      value: dani
    - name: DB_PASSWORD
      value: "secret123"
```

selanjutnya secrets.yml di tambahkan ke .dockerignore & .gitignore

```bash
echo '/helm/secrets.yml' >> ./.dockerignore && echo '/helm/secrets.yml' >> ./.gitignore
```

tambahkan Repo LAMP (Linux, Apache, Mysql, PHP).

```bash
$ minikube start
$ helm repo add lamp https://lead4good.github.io/lamp-helm-repository
$ helm install rssejahteraapp -f helm/values.yml -f helm/secrets.yml lamp/lamp
$ minikube service rssejahteraapp-lamp --url
```

![](https://file.notion.so/f/s/b342e0e6-9b4b-43a5-84e0-84431bfdcb8f/Untitled.png?id=f67bd32b-7f25-4eaf-820e-9e55189e3200&table=block&spaceId=355c59a1-9514-4ca4-8a2f-60fc7da6dd8b&expirationTimestamp=1689868800000&signature=sXsZP5G_ODfom6yW_DxuvEwZqgIQxVX1wbGj2WvB2Dc&downloadName=Untitled.png align="left")

migrate database

```bash
$ kubectl exec **nama pods** -- php artisan migrate:fresh --seed --force
```

![](https://file.notion.so/f/s/83b38877-83f1-48a8-8af9-3f91a2b27eef/Untitled.png?id=d75ec595-b5de-4b16-a787-cdde0738e175&table=block&spaceId=355c59a1-9514-4ca4-8a2f-60fc7da6dd8b&expirationTimestamp=1689868800000&signature=mtcfdEi2FBaKbdPzOna4TwzG73lBM4JGvs3Jxj429rQ&downloadName=Untitled.png align="left")

![](https://file.notion.so/f/s/349b1be3-b8a6-4321-911c-6c35a5251dfe/Untitled.png?id=80b4895c-7660-481c-82b6-b7d431d08a2b&table=block&spaceId=355c59a1-9514-4ca4-8a2f-60fc7da6dd8b&expirationTimestamp=1689868800000&signature=ziua5FAodLFVCaWLmawUwKbjlYMI4nwwDDOEWCIpBhs&downloadName=Untitled.png align="left")

untuk upgrade dapat menggunakan command, namun di file helm/values.yml dirubah dahulu tag nya.

```bash
$ nano helm/values.yaml
$ helm upgrade rssejahteraapp lamp/lamp -f helm/values.yml -f helm/secrets.yml 
```

![](https://file.notion.so/f/s/721b99eb-eda0-4d97-858b-dbd95386335f/Untitled.png?id=37fcb8b5-4fce-426f-82a2-52c665a770ae&table=block&spaceId=355c59a1-9514-4ca4-8a2f-60fc7da6dd8b&expirationTimestamp=1689868800000&signature=f6OarPytkxFzFspW-3dpXPaoozJ8SzPYFmGv7VXOAwI&downloadName=Untitled.png align="left")

![](https://file.notion.so/f/s/532710ea-aed9-4a3e-93ff-efa33fdd1e90/Untitled.png?id=5a34e671-e142-4923-b6bc-9378c2140259&table=block&spaceId=355c59a1-9514-4ca4-8a2f-60fc7da6dd8b&expirationTimestamp=1689868800000&signature=ok9o0OYJaU_zZIkxawH3_XKzGngB2wvSXQOf-9uSwV8&downloadName=Untitled.png align="left")

**Rollback**

```bash
$ helm history rssejahteraapp
```

![](https://file.notion.so/f/s/9be581fc-b516-482b-864a-2cccfce7e418/Untitled.png?id=15d04796-9ac6-4de3-a6f5-3759bf6213a9&table=block&spaceId=355c59a1-9514-4ca4-8a2f-60fc7da6dd8b&expirationTimestamp=1689868800000&signature=srlwADiskhtxUOIcqqmBn_DztOh8Kd2EguQ0hMz5otA&downloadName=Untitled.png align="left")

and then

```bash
$ helm rollback rssejahteraapp 1
```

![](https://file.notion.so/f/s/f9ca33f6-db26-4417-bef5-6f50c9a5766b/Untitled.png?id=680e61db-66a2-422e-aaa0-6e8b3ce16960&table=block&spaceId=355c59a1-9514-4ca4-8a2f-60fc7da6dd8b&expirationTimestamp=1689868800000&signature=mAbKYJbL1xN_PB3ZcsFFGntLEaMqM5RYPzcJ2FQktsg&downloadName=Untitled.png align="left")

# Scale

**Scale Up**

```bash
$ kubectl get deployments
$ kubectl scale --replicas 2 deployments/rssejahteraapp-lamp
```

![](https://file.notion.so/f/s/7c4aca23-5b73-4901-9445-c907b9d704f4/Untitled.png?id=1166d9f2-1eae-4497-91d5-1f75fdcbed9e&table=block&spaceId=355c59a1-9514-4ca4-8a2f-60fc7da6dd8b&expirationTimestamp=1689868800000&signature=rvWmI3Qr4PCU69IzVMSqHKpGGsALADLB7c-3vY9JNSo&downloadName=Untitled.png align="left")

delete salah satu pod, agar terlihat akan mereplica kembali pod yang sudah di delete.

![](https://file.notion.so/f/s/e1adf3ef-2bb9-4ffa-821f-cf983dbfe0a5/Untitled.png?id=c309b616-1be5-43a7-8a97-281c1718b8eb&table=block&spaceId=355c59a1-9514-4ca4-8a2f-60fc7da6dd8b&expirationTimestamp=1689868800000&signature=XPYjDDvM-0xMj0xmAl-xpeaFyzKkjGPcuhbhfwX2mMI&downloadName=Untitled.png align="left")

**Scale down**

```bash
$ kubectl scale --replicas 1 deployments/rssejahteraapp-lamp
```

![](https://file.notion.so/f/s/4be66858-c9be-4af0-8e18-173548346383/Untitled.png?id=9c3431b6-3235-4a64-9295-72175e9d3e63&table=block&spaceId=355c59a1-9514-4ca4-8a2f-60fc7da6dd8b&expirationTimestamp=1689868800000&signature=S5HbAmUTHnok073FHMf7oRkRvlRSgZ5-zZ5ha_36Mnw&downloadName=Untitled.png align="left")

# Autoscaling

untuk autoscaling create **hpa.yml**

```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: rssejahteraapp-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: rssejahteraapp-lamp
  minReplicas: 1
  maxReplicas: 4
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 60
```

```bash
$ kubectl apply -f helm/hpa.yml
```

![](https://file.notion.so/f/s/08f29baf-d357-4217-9185-a2d6c2ebd87c/Untitled.png?id=71587ad1-27be-4378-b756-55877cbb5684&table=block&spaceId=355c59a1-9514-4ca4-8a2f-60fc7da6dd8b&expirationTimestamp=1689868800000&signature=isFyPvj26i1ToeKf7h-i2DJkibHlpNB63gMV0rCKr6A&downloadName=Untitled.png align="left")

terlihat pods autoscalling.

```bash
$ helm delete rssejahteraapp
```

![](https://file.notion.so/f/s/aac6285e-8eba-4981-973d-edd45d3494dc/Untitled.png?id=b2362e5a-cd8a-4e86-a22c-8f8c195f1085&table=block&spaceId=355c59a1-9514-4ca4-8a2f-60fc7da6dd8b&expirationTimestamp=1689868800000&signature=WzgQ-_0Pj8k6K1D9p8YeL7Z2cxEK8h3wG5WGow7kyO8&downloadName=Untitled.png align="left")

```bash
$ minikube stop
```