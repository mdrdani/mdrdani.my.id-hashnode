---
title: "Frontend is for Everyone (AWS Lambda & AWS Amplify)"
datePublished: Wed Aug 09 2023 09:05:31 GMT+0000 (Coordinated Universal Time)
cuid: cll3i93it00120amh0jjv5ito
slug: frontend-is-for-everyone-aws-lambda-aws-amplify
canonical: https://learn.labs.awsevents.com/
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/GnvurwJsKaY/upload/7aadfb50820dfba79530357db56d1d78.jpeg
tags: aws, aws-lambda, awsamplify

---

### Lab Overview

Sebagai seorang profesional teknologi dengan pengalaman dalam scripting dan ide baru, membangun antarmuka depan untuk konsep bukti atau produk minimal yang bisa dicintai mungkin terasa sulit dicapai. Dalam skenario ini, tim Anda bergantung pada Anda untuk menyediakan instans Linux untuk penggunaan mereka. Anda sudah meningkatkan proses ini dengan menulis fungsi AWS Lambda yang meluncurkan instans Elastic Compute Cloud (EC2) dengan penandaan yang sesuai. Bagaimana jika ada alat self-service berbasis web yang tidak memerlukan masuk ke konsol AWS? Dalam lab ini, Anda membangun aplikasi satu halaman full-stack yang serverless dengan otentikasi yang memungkinkan pengguna meluncurkan dan menghentikan instans EC2.

Anda akan mempelajari fitur-fitur pustaka React untuk membangun antarmuka pengguna. Kemudian, Anda menggunakan AWS Amplify untuk menambahkan layanan-layanan AWS dan mengintegrasikannya dengan antarmuka depan Anda. Ini termasuk menambahkan otentikasi melalui Amazon Cognito dan membuat REST API yang aman yang didukung oleh Amazon API Gateway. Anda juga memperluas backend dengan fungsi serverless melalui AWS Lambda yang berinteraksi dengan layanan Amazon EC2.

Akhirnya, Anda menggunakan AWS Amplify untuk menambahkan hosting aplikasi Anda, sehingga memungkinkan aplikasi disampaikan kepada pengguna akhir melalui jaringan pengiriman konten AWS menggunakan Amazon CloudFront. Semua pengembangan aplikasi dilakukan dalam lingkungan pengembangan AWS Cloud9 IDE.

---

Kurang lebih Lab Overview dari **3 FREE Builder Labs for Beginners and Pros alike** yang akan kita pelajari sekarang.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1691629387311/5f70cbb0-cd39-4ed9-8f9e-eb68461e78fe.png align="center")

### **Task 1: Create and run a blank React app in AWS Cloud9**

Pada task 1 ini kita membuat fresh project React dan menggunakan IDE Code Editor dari AWS yaitu **Cloud9.**

Buat Fresh app React dengan command.

```bash
cd ~/environment
npx create-react-app ec2-vendor
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1691566823091/ab8ceac4-7b77-4286-9997-47eef51069f2.png align="center")

pada bagian **package.json** tambahkan dependencies ini

```bash
"@babel/plugin-proposal-private-property-in-object": "^7.14.5"
```

```bash
{
  "name": "ec2-vendor",
  "version": "0.1.0",
  "private": true,
  "dependencies": {
    "@testing-library/jest-dom": "^5.17.0",
    "@testing-library/react": "^13.4.0",
    "@testing-library/user-event": "^13.5.0",
    "react": "^18.2.0",
    "react-dom": "^18.2.0",
    "react-scripts": "5.0.1",
    "web-vitals": "^2.1.4",
    "@babel/plugin-proposal-private-property-in-object": "^7.14.5"
  },
......
```

jika sudah running aplikasi secara lokal di AWS CLOUD9 instance

```bash
cd ~/environment/ec2-vendor
npm run start
```

untuk melihat **GUI** aplikasi yang sudah di buat bisa klik di atas **Preview &gt; Preview Running Application.**

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1691567005266/22d4c8f7-1065-46a6-bd09-9f66eda549a1.png align="center")

### **Task 2: Update the React app to use traditional HTML elements for the EC2 Vendor app**

pada task 2 kita menghapus template bawaan react dan mengganti dengan template aplikasi yang sudah di sediakan AWS.

```bash
cd ~/environment/ec2-vendor/src
rm -f *
cp ~/environment/lab-resources/frontend/example1/* ~/environment/ec2-vendor/src
```

seperti ini tampilan nya. Kurang lebih aplikasi ini untuk memanage **AWS EC2 Instance**.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1691567188301/1415621e-3ae5-44ab-a631-d66d7239b38e.png align="center")

### **Task 3: Update the app to use Amplify UI components**

Sampai pada task 3 kita menggunakan service dari **AWS** yaitu **Amplify UI.** untuk mempermudah kita untuk membuat reusable components dan customizable. Install **Amplify UI**

```bash
cd ~/environment/ec2-vendor
npm install @aws-amplify/ui-react
```

copy component library, dan lihat tampilan lebih fresh.

```bash
cp ~/environment/lab-resources/frontend/example2/* ~/environment/ec2-vendor/src
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1691567671944/f53251ef-7115-4c9a-92eb-75ecbd6f024a.png align="center")

### **Task 4: Update the app to include states**

Pada task 4 ini mengupdate tampilan dari aplikasi nya theme dark or light, modif component nya. ketika component state berubah maka child component akan merender.

```bash
cp ~/environment/lab-resources/frontend/example3/* ~/environment/ec2-vendor/src
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1691567813659/eb36d670-6d12-4ce9-be1a-13ca2e9d6986.png align="center")

### **Task 5: Update the app to include effects, props and a reusable component**

Pada Task 5 update aplikasi dengan mengimplementasikan useEffect, React Props ke React Components dan reusable components.

```bash
cp ~/environment/lab-resources/frontend/example4/* ~/environment/ec2-vendor/src
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1691567967268/b5b249f5-8711-49be-bf94-26ea5f18f01a.png align="center")

### **Task 6: Create an Amplify app using the CLI and add authentication services**

nah jika task sebelumnya tadi kita jalankan dengan data statis maka sekarang kita tambahkan AWS service.

Pertama buat authentication service menggunakan **Amplify CLI** dengan **AWS IAM** permissions boundary, dan authentication service Sign-up, Sign-in dan access control di [**Amazon Cognito**](https://docs.aws.amazon.com/id_id/cognito/latest/developerguide/what-is-amazon-cognito.html#:~:text=Amazon%20Cognito%20adalah%20platform%20identitas%20untuk%20aplikasi%20web%20dan%20seluler.)**.**

```bash
npm install -g @aws-amplify/cli
```

next jika sudah install maka selanjutnya menambahkan permission dengan **AmplifyPermissionBoundary.**

```bash
policy_arn=$(aws iam list-policies --scope Local --query 'Policies[?contains(PolicyName, `AmplifyPermissionBoundary`)].Arn | [0]' --output text) && echo -e "\n Policy ARN: $policy_arn\n"

cd ~/environment/ec2-vendor
amplify init --permissions-boundary $policy_arn
```

ada beberapa pertanyaan yang di ajukan, bisa di lihat gambar di bawah ini.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1691568600215/51b2d439-6cbf-4874-8093-197dafbb3c97.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1691568778003/70aaddf1-2f17-4b9e-81fb-e24465de30fc.png align="center")

untuk menambahkan authentication ke Amplify App jalankan command

```bash
amplify add auth
```

ada pertanyaan kembali lihat gambar di bawah ini

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1691568824808/6bf03da7-10a5-4f5a-a55d-5e3b11a2fd69.png align="center")

deploy authentication service ke **AWS.**

```bash
amplify push --yes
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1691568906851/66341965-704e-41e6-a123-60a18462090d.png align="center")

### **Task 7: Update the app to use authentication**

sekarang service AWS Authentication sudah terkonfigurasi di backend, kita pakai library *aws-amplify* untuk link ke frontend.

```bash
cd ~/environment/ec2-vendor
npm install aws-amplify
```

```bash
cp ~/environment/lab-resources/frontend/example5/* ~/environment/ec2-vendor/src
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1691569255734/0651eefd-0f17-4beb-af53-8b83363a54d5.png align="center")

untuk buat akun baru bisa ke bagian **Create Account.** Pastikan **email** yang di masukan benar karena akan ada **Verification Code** ke email.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1691569341318/ad71033a-5953-43ef-b877-a1a56e31ed76.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1691569390677/3ecfa0fd-f7f9-406e-b613-5316eb240422.png align="center")

jika sudah **Sign Out.**

### **Task 8: Create an AWS Lambda function**

Pada Task 8 kita menggunakan **Amplify** untuk membuat **Node.js Lambda Function** untuk menerima masukan dari **API** dan berinteraksi dengan **AWS EC2** menggunakan **AWS SDK**.

```bash
cd ~/environment/ec2-vendor
amplify add function
```

ada beberapa pertanyaan perhatikan gambar di bawah ini.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1691569597062/5b297ec6-bae6-40d8-8f3e-de73ee0f64c9.png align="center")

update local lambda code.

```bash
cp -rp ~/environment/lab-resources/backend/ec2VendorApi/* ~/environment/ec2-vendor/amplify/backend/function/ec2VendorApi
```

Test function local. Install AWS SDK.

```bash
cd ~/environment/ec2-vendor/amplify/backend/function/ec2VendorApi/src/
npm install aws-sdk
```

Test AMI QUERY logic.

```bash
amplify mock function ec2VendorApi --event "src/event.json"
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1691569804306/3705ea75-ce4f-4e1d-b8d4-787ea7851382.png align="center")

success.

### **Task 9: Create a secure REST API backed by an AWS Lambda function**

Sampai di Task 9 kita menggunakan Amplify untuk membuat secure REST API powered by [Amazon API Gateway](https://docs.aws.amazon.com/apigateway/latest/developerguide/welcome.html).

```bash
cd ~/environment/ec2-vendor
amplify add api
```

jika ada seperti ini lakukan **CTRL + C** dahulu untuk mengakhiri

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1691570028836/a7b48dd5-2fa4-4dbe-b36c-4aa5d51dff3c.png align="center")

dan jalankan kembali **amplify add api.** ada beberapa pertanyaan perhatikan gambar di bawah ini.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1691570112948/a9418133-3cbf-4a77-97db-061957165f42.png align="center")

deploy secure **REST API** dan **Lambda Function** to AWS.

```bash
amplify push --yes
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1691570198704/51e79495-c862-4747-8294-f4e6eff12484.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1691570219724/e33bf510-f97c-430b-a115-614016558e98.png align="center")

jika sukses deploy akan ada url REST API yang terbentuk dan klik.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1691570352546/d58f0e21-3bf8-4fb0-8287-0237cf4a5e44.png align="center")

dan **missing authentication,** ya karena di perlukan **IAM Authentication**.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1691570377376/5573c5ac-71e8-440a-b24b-67af517cbce2.png align="center")

### **Task 10: Update the app to use the API to query Amazon Linux 2 AMI data**

Pada task 10 kita update aplikasi untuk berinteraksi dengan secure REST API dan query untuk Amazon Linux 2 AMI data base on arsitektur x86\_64 atau arm64 .

```bash
cp ~/environment/lab-resources/frontend/example6/* ~/environment/ec2-vendor/src
```

AMI Data berbentuk **JSON**. dari API ini kita bisa manage EC2 Instance.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1691570636548/cd5d9a04-be63-4705-9392-0ca469cf73ec.png align="center")

### **Task 11: Update the app to launch and terminate Amazon EC2 instances**

Pada task 11 update app untuk mengoperasikan launch dan terminate EC2 instances lewat aplikasi yang sudah di buat.

```bash
cp ~/environment/lab-resources/frontend/example7/* ~/environment/ec2-vendor/src
```

Klik **Launch** akan terbentuk 1 **EC2 Instance**.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1691570824490/c18ff2c8-5f96-45e7-a002-f5231a758e72.png align="center")

Tunggu sampai status Instance **Running** jika blum berubah bisa klik **Refresh.**

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1691571001225/88f1a7d5-b494-4bba-b093-67807d32f598.png align="center")

kita coba **Terminate** ya instance yang sudah running.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1691571051820/fdd6ea39-9825-4e9f-a774-5b88c2295c26.png align="center")

Oke success.

### **Task 12: Host the app on Amazon CloudFront**

finally task yang terakhir kita hosting dan deploy app ke Amazon Cloud.

```bash
cd ~/environment/ec2-vendor
amplify add hosting
```

ada pertanyaan perhatikan gambar di bawah ini

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1691571522240/7b954f3b-8288-49f1-a684-33919c87552f.png align="center")

publish app dengan command.

```bash
amplify publish --yes
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1691571567456/595cfd94-5755-4b5b-9ac6-7bbc0ccae589.png align="center")

untuk access public copy dan paste url yang sudah terbuat.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1691571719571/f4a1c607-93d6-4840-8b1e-8eb35a1ecc88.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1691571736187/660df34c-8003-44b6-b8b1-549193c4740b.png align="center")

success.

### **Task 13: Cleanup**

jika tidak di gunakan dalam waktu lama kita bisa menghapus keseluruhan resource dengan command.

```bash
cd ~/environment/ec2-vendor
amplify delete
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1691571857394/972ed4d1-5467-4abf-a885-266c0c41452a.png align="center")

AWS training and certification. ([https://learn.labs.awsevents.com/](https://learn.labs.awsevents.com/))