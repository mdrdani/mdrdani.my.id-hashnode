---
title: "Add Notification Jenkins to Discord"
datePublished: Wed Jul 19 2023 15:10:54 GMT+0000 (Coordinated Universal Time)
cuid: clk9v23gg000209mfhosbfpdu
slug: add-notification-jenkins-to-discord
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/FlPc9_VocJ4/upload/c51951421696f49b9e2e4c92ae7098c0.jpeg
tags: jenkins, discord

---

untuk memudahkan agar tidak menunggu saat build dan push docker image, maka di sini perlu menambahkan notifikasi melalui Discord saja.

**create server on Discord**

![](https://file.notion.so/f/s/85477cc1-2d5b-4051-a6e9-84944e8cea77/Untitled.png?id=94c82345-29ac-47ef-a5ca-d9a46180ca41&table=block&spaceId=355c59a1-9514-4ca4-8a2f-60fc7da6dd8b&expirationTimestamp=1689868800000&signature=x731Y9y_zmMCdjfYn1lKaPQxo9kPOsJX9ClCD6qHnI0&downloadName=Untitled.png align="left")

**Server Settings → Integrations → Create Webhook, Copy Webhook URL Pipeline Jenkins.**

![](https://file.notion.so/f/s/6f4391a7-15b8-4521-8ecd-91a4ae6e1f95/Untitled.png?id=3181c700-42c2-4241-be7a-14391efa77b6&table=block&spaceId=355c59a1-9514-4ca4-8a2f-60fc7da6dd8b&expirationTimestamp=1689868800000&signature=3DpWRmTcfSwOzrG7OZ8iaJ0KuK6J5Xk5tx5FQkiqGLk&downloadName=Untitled.png align="left")

balik ke server Jenkins, install plugin Discord Notification.

![](https://file.notion.so/f/s/743fd42f-cad5-4260-b66a-43d8a4a29c9f/Untitled.png?id=1172966c-7e7d-4013-a755-346076abd9b3&table=block&spaceId=355c59a1-9514-4ca4-8a2f-60fc7da6dd8b&expirationTimestamp=1689868800000&signature=yxDj3KudcRLf5VaoirX0ghU2BcXTJtqQxvtioUSZCkc&downloadName=Untitled.png align="left")

next **configure ulang jobs jenkins.** ke bagian paling bawah **Pipeline Script.** tambahkan paling bawah baris sesudah push to docker hub

```bash
stage('Send Discord Notification') {
            steps {
                discordSend description: "Docker image build and push succeeded!", 
                footer: "isikan footer", 
                link: env.BUILD_URL, 
                result: currentBuild.currentResult, 
                title: JOB_NAME,
                webhookURL: "your-webhook-url"
            }
        }
```

![](https://file.notion.so/f/s/c1c915d2-886e-4c75-b044-ef065b23ef99/Untitled.png?id=7f522dc6-3a74-423e-8699-314547314134&table=block&spaceId=355c59a1-9514-4ca4-8a2f-60fc7da6dd8b&expirationTimestamp=1689868800000&signature=274tB2ODwVU0xLDXj4QccZiFnAVMOUNoesB4CxM0dj8&downloadName=Untitled.png align="left")

Save dan coba Build Now lagi.

![](https://file.notion.so/f/s/7ee4bcde-efab-468b-b553-c18d04c9385c/Untitled.png?id=7c298c4a-84e4-4e48-b359-48444b1c79f2&table=block&spaceId=355c59a1-9514-4ca4-8a2f-60fc7da6dd8b&expirationTimestamp=1689868800000&signature=VFLkHQ9csZRR1vDyhZxLh-GqmPEE-YB6_8gCk9nrHDg&downloadName=Untitled.png align="left")

![](https://file.notion.so/f/s/320d9612-e2cb-4f56-8933-6e4960326366/Untitled.png?id=8bf5f1e2-ad99-4322-bd8c-1db7eaad896f&table=block&spaceId=355c59a1-9514-4ca4-8a2f-60fc7da6dd8b&expirationTimestamp=1689868800000&signature=AHZbxue9zkNuBzqhK4gVDlZ9CI9EK_wckzorD4BisSw&downloadName=Untitled.png align="left")