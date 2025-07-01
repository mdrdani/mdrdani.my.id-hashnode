---
title: "Google Kubernetes Engine Pipeline using Cloud Build"
datePublished: Wed Aug 16 2023 00:57:23 GMT+0000 (Coordinated Universal Time)
cuid: clld0wblc00000aju87c08fin
slug: google-kubernetes-engine-pipeline-using-cloud-build
canonical: https://www.cloudskillsboost.google/games/4301/labs/27865
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/0gkw_9fy0eQ/upload/83c5e0df91e931687c6e180d594264b1.jpeg
tags: devops, google-cloud-platform, gke, ci-cd

---

![](https://cdn.qwiklabs.com/7swem2VpBgLbbDMGVgWrtmVQtBlYOADPBh89k%2Bbb1S4%3D align="left")

Pada Lab Cloud Skill Boost ini, kita membuat CI/CD pipeline yang mengotomasi build container image **Cloud Build** dari code commit, image di simpan di **Artifact Registry**, dan Deploy ke **Google Kubernetes Engines**.

Di lab ini kita membuat 2 Git Repo:

* app repo
    
* env repo
    

![](https://cdn.qwiklabs.com/qEN8Qxr82h1FkL8DhD5sqJmblM11i7JjhZv14OGahr0%3D align="left")

Gambar di atas gamabaran flow pipeline CI/CD.

### Task 1. Initialize Your Lab

1. set variable yang akan mendukung command Cloud Shell.
    
    ```bash
    export PROJECT_ID=$(gcloud config get-value project)
    export PROJECT_NUMBER=$(gcloud projects describe $PROJECT_ID --format='value(projectNumber)')
    export REGION=
    gcloud config set compute/region $REGION
    ```
    
2. Enable APIs GKE, Cloud Build, Cloud Source Repositories, dan Container Analysis
    
    ```bash
    gcloud services enable container.googleapis.com \
        cloudbuild.googleapis.com \
        sourcerepo.googleapis.com \
        containeranalysis.googleapis.com
    ```
    
3. Buat Artifact Registry Docker Repository dengan nama **my-repository**
    
    ```bash
    gcloud artifacts repositories create my-repository \
      --repository-format=docker \
      --location=$REGION
    ```
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1692069599854/41447019-84a8-45e7-9b0e-1e66f4c895d8.jpeg align="center")
    
4. Buat GKE Cluster untuk mendeploy sample aplikasi, kita buat 1 nodes.
    
    ```bash
      gcloud container clusters create hello-cloudbuild --num-nodes 1 --region $REGION
    ```
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1692069643360/260b456c-f7f7-4237-a209-f3efe14c2fcb.jpeg align="center")
    
5. set git config menggunakan email dan user generate lab.
    
    ```bash
    git config --global user.email "you@example.com"  
    git config --global user.name "Your Name"
    ```
    

### Task 2. **Create the Git repositories in Cloud Source Repositories**

Pada task ini kita membuat 2 git repo (**hello-cloudbuild-app** and **hello-cloudbuild-env**).

1. create git repo
    
    ```bash
    gcloud source repos create hello-cloudbuild-app
    gcloud source repos create hello-cloudbuild-env
    ```
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1692073008512/9b0c2227-4879-49f9-95b5-773cf74ae932.jpeg align="center")
    
2. Clone sample code dari Github
    
    ```bash
    cd ~
    git clone https://github.com/GoogleCloudPlatform/gke-gitops-tutorial-cloudbuild hello-cloudbuild-app
    ```
    
3. Config cloud source repo sebagai remote
    
    ```bash
    cd ~/hello-cloudbuild-app
    PROJECT_ID=$(gcloud config get-value project)
    git remote add google "https://source.developers.google.com/p/${PROJECT_ID}/r/hello-cloudbuild-app"
    ```
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1692072779822/ccc0a0b2-5a33-402e-be74-78032aaddc2a.jpeg align="center")
    

### Task 3. Create a Container image with Cloud Build

Dockerfile sudah terbuat di dalam app yang sudah di clone dari Github.

```bash
FROM python:3.7-slim
RUN pip install flask
WORKDIR /app
COPY app.py /app/app.py
ENTRYPOINT ["python"]
CMD ["/app/app.py"]
```

1. buat Cloud Build base commit terakhir
    
    ```bash
    cd ~/hello-cloudbuild-app
    COMMIT_ID="$(git rev-parse --short=7 HEAD)"
    gcloud builds submit --tag="${REGION}-docker.pkg.dev/${PROJECT_ID}/my-repository/hello-cloudbuild:${COMMIT_ID}" .
    ```
    
2. Jika sudah selesai build, di **Artifact Registry &gt; Repository** akan terbentuk container image, klik **my-repository**
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1692073228581/4ecbe18d-edb1-4f0d-888b-5c7cf1f6b697.jpeg align="center")
    

### Task 4. Create the Continuous Integration (CI) pipeline

Pada Tahap ini akan mengkonfigurasi small unit test, yaitu otomasi build container image yang di trigger (Cloud Build) dari commit last dari branch yang di bentuk dan di push ke Artifact Registry.

![](https://cdn.qwiklabs.com/z3p4mZq3O2H5X9gBCmSJLRV9KcC9hL9yhYcO3k3Oq7Q%3D align="left")

Step:

* Ke **Cloud Build** &gt; **Triggers**
    
* **Create Trigger**
    
* Buat nama **field**
    
* Bagian **Event**, select **Push to a branch**
    
* Bagian **Source,** pilih Repo di sini kita tadi buat **hello-cloudbuild-app,** dan set **Branch** ke **.\* (any branch)**
    
* Bagian **Build Configuration,** type **cloudbuild.yaml.**
    
* **Create**
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1692073586340/321ae676-535f-437c-b6db-228a706fb7ed.jpeg align="center")

untuk start trigger, run command

```bash
cd ~/hello-cloudbuild-app
git push google master
```

watch prosess build image container

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1692074503418/1191b417-a0c6-440c-b8d5-9f5e81d10fbb.jpeg align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1692074515263/582eb1f5-9675-4344-82e9-5d24a4d0b31a.jpeg align="center")

success build container image

### **Task 5. Create the Test Environment and CD pipeline**

Cloud Build juga dapat di pakai untuk **Continuous Delivery (CD)** pipeline.

Grant Cloud Build access Ke GKE

```bash
PROJECT_NUMBER="$(gcloud projects describe ${PROJECT_ID} --format='get(projectNumber)')"

gcloud projects add-iam-policy-binding ${PROJECT_NUMBER} \
--member=serviceAccount:${PROJECT_NUMBER}@cloudbuild.gserviceaccount.com \
--role=roles/container.developer
```

selanjutnya kita initialize clone repo **hello-cloudbuild-env** dengan 2 branche (production dan candidate).

```bash
cd ~
gcloud source repos clone hello-cloudbuild-env
cd ~/hello-cloudbuild-env
git checkout -b production
```

next copy file **cloudbuild-delivery.yaml** ke repo **hello-cloudbuild-app**

```bash
cd ~/hello-cloudbuild-env
cp ~/hello-cloudbuild-app/cloudbuild-delivery.yaml ~/hello-cloudbuild-env/cloudbuild.yaml
git add .
git commit -m "Create cloudbuild.yaml for deployment"
```

file **cloudbuild-delivery.yaml**

```bash
# [START cloudbuild-delivery]
steps:
# This step deploys the new version of our container image
# in the hello-cloudbuild Kubernetes Engine cluster.
- name: 'gcr.io/cloud-builders/kubectl'
  id: Deploy
  args:
  - 'apply'
  - '-f'
  - 'kubernetes.yaml'
  env:
  - 'CLOUDSDK_COMPUTE_REGION=us-central1'
  - 'CLOUDSDK_CONTAINER_CLUSTER=hello-cloudbuild'

# This step copies the applied manifest to the production branch
# The COMMIT_SHA variable is automatically
# replaced by Cloud Build.
- name: 'gcr.io/cloud-builders/git'
  id: Copy to production branch
  entrypoint: /bin/sh
  args:
  - '-c'
  - |
    set -x && \
    # Configure Git to create commits with Cloud Build's service account
    git config user.email $(gcloud auth list --filter=status:ACTIVE --format='value(account)') && \
    # Switch to the production branch and copy the kubernetes.yaml file from the candidate branch
    git fetch origin production && git checkout production && \
    git checkout $COMMIT_SHA kubernetes.yaml && \
    # Commit the kubernetes.yaml file with a descriptive commit message
    git commit -m "Manifest from commit $COMMIT_SHA
    $(git log --format=%B -n 1 $COMMIT_SHA)" && \
    # Push the changes back to Cloud Source Repository
    git push origin production
# [END cloudbuild-delivery]
```

jadi file di atas mendeskripsikan process deployment yang di jalankan di Cloud Build. Ada 2 steps :

* cloud build apply manifest di cluster GKE
    
* jika sukses, cloud build copy manifest di branch production
    

Buat branch candidate dan push branch keduanya agar tersedia di Cloud Source Repositories

```bash
git checkout -b candidate
git push origin production
git push origin candidate
```

berikan role IAM Source Repository Writer ke Cloud Build service account

```bash
PROJECT_NUMBER="$(gcloud projects describe ${PROJECT_ID} \
--format='get(projectNumber)')"
cat >/tmp/hello-cloudbuild-env-policy.yaml <<EOF
bindings:
- members:
  - serviceAccount:${PROJECT_NUMBER}@cloudbuild.gserviceaccount.com
  role: roles/source.writer
EOF
```

```bash
gcloud source repos set-iam-policy \
hello-cloudbuild-env /tmp/hello-cloudbuild-env-policy.yaml
```

**Create trigger di Cloud build untuk Continuous Delivery pipeline**

1. **Cloud Build &gt; Triggers**
    
2. **Create Trigger**
    
3. Masukan **Field Nama**
    
4. Bagian **Event,** pilih **Push to a branch.**
    
5. Bagian **Source** pilih **hello-cloudbuild-env** sebagai **Repository** dan ^candidate$ sebagai **Branch**
    
6. Bagian **Cloud Build configuration file location,** masukan **cloudbuild.yaml**
    
7. **Create**
    

Modifikasi CI Pipeline untuk trigger ke Pipeline CD

```bash
cd ~/hello-cloudbuild-app
cp cloudbuild-trigger-cd.yaml cloudbuild.yaml
```

file **cloudbuild-trigger-cd.yaml**

```bash
steps:
# This step runs the unit tests on the app
- name: 'python:3.7-slim'
  id: Test
  entrypoint: /bin/sh
  args:
  - -c
  - 'pip install flask && python test_app.py -v'

# This step builds the container image.
- name: 'gcr.io/cloud-builders/docker'
  id: Build
  args:
  - 'build'
  - '-t'
  - 'us-central1-docker.pkg.dev/$PROJECT_ID/my-repository/hello-cloudbuild:$SHORT_SHA'
  - '.'

# This step pushes the image to Artifact Registry
# The PROJECT_ID and SHORT_SHA variables are automatically
# replaced by Cloud Build.
- name: 'gcr.io/cloud-builders/docker'
  id: Push
  args:
  - 'push'
  - 'us-central1-docker.pkg.dev/$PROJECT_ID/my-repository/hello-cloudbuild:$SHORT_SHA'
# [END cloudbuild]

# [START cloudbuild-trigger-cd]
# This step clones the hello-cloudbuild-env repository
- name: 'gcr.io/cloud-builders/gcloud'
  id: Clone env repository
  entrypoint: /bin/sh
  args:
  - '-c'
  - |
    gcloud source repos clone hello-cloudbuild-env && \
    cd hello-cloudbuild-env && \
    git checkout candidate && \
    git config user.email $(gcloud auth list --filter=status:ACTIVE --format='value(account)')

# This step generates the new manifest
- name: 'gcr.io/cloud-builders/gcloud'
  id: Generate manifest
  entrypoint: /bin/sh
  args:
  - '-c'
  - |
     sed "s/GOOGLE_CLOUD_PROJECT/${PROJECT_ID}/g" kubernetes.yaml.tpl | \
     sed "s/COMMIT_SHA/${SHORT_SHA}/g" > hello-cloudbuild-env/kubernetes.yaml

# This step pushes the manifest back to hello-cloudbuild-env
- name: 'gcr.io/cloud-builders/gcloud'
  id: Push manifest
  entrypoint: /bin/sh
  args:
  - '-c'
  - |
    set -x && \
    cd hello-cloudbuild-env && \
    git add kubernetes.yaml && \
    git commit -m "Deploying image us-central1-docker.pkg.dev/$PROJECT_ID/my-repository/hello-cloudbuild:${SHORT_SHA}
    Built from commit ${COMMIT_SHA} of repository hello-cloudbuild-app
    Author: $(git log --format='%an <%ae>' -n 1 HEAD)" && \
    git push origin candidate
```

commit modifikasi dan push ke **Cloud Source Repositories.**

```bash
cd ~/hello-cloudbuild-app
git add cloudbuild.yaml
git commit -m "Trigger CD pipeline"
git push google master
```

### **Task 6. Review Cloud Build Pipeline**

1. ke **Cloud Build &gt; Dashboard**
    
2. Click Trigger dengan nama **hello-cloudbuild-app,** step terakhir pipeline ini akan push manifest baru ke repo **hello-cloudbuild-env** yang mana repo tersebut untuk mentrigger **Continuous Delivery Pipeline.**
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1692145616524/e2d8ed22-5009-4f06-b011-608f0d2fdef1.jpeg align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1692145621853/c5d58625-de12-4c5c-9674-2f75a7e317b8.jpeg align="center")

### Task 7. Test the complete pipeline

CI/CD pipeline sudah sempurna kita konfigurasi.

1. Pergi ke **Kubernetes Engine &gt; Service & Ingress**
    
2. Pada bagian endpoint akan terlihat service **hello-cloudbuild,** biasa nya akan menunggu beberapa menit baru akan muncul IP Public sambil kita refresh. Jika sudah terlihat tinggal di klik akan muncul tampilan sederhana HTML.
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1692146338819/657646b4-687c-4d17-beba-1c9da18d7162.jpeg align="center")
    
3. kita coba update HTML nya untuk mentrigger perubahan nya apakah work pipeline yang sudah kita buat tadi.
    
    ```bash
    cd ~/hello-cloudbuild-app
    sed -i 's/Hello World/Hello Cloud Build/g' app.py
    sed -i 's/Hello World/Hello Cloud Build/g' test_app.py
    ```
    
4. Commit and push to Cloud Source Repositories
    
    ```bash
    git add app.py test_app.py
    git commit -m "Hello Cloud Build"
    git push google master
    ```
    
5. Sesudah beberapa menit reload browser, and kita bisa melihat perubahan nya.
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1692146346939/1f032fa5-9447-4fe9-9088-8aab921145f0.jpeg align="center")
    
6. kita juga bisa melihat revision history, perubahan menjadi ke bagian 2
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1692146429076/362d8d87-a30f-4ac3-b0c8-230d80fbe92e.jpeg align="center")
    

### Task 8. Test Rollback

Pada task ini kita akan coba rollback versi aplikasi, ini di butuhkan jika suatu saat ada bug dalam aplikasi yang sudah di deploy bisa dengan cepat kita rubah kembali ke version sebelum nya.

1. Ke **Cloud Build &gt; Dashboard**
    
2. Ke Bagian **History,** dan pilih image sebelumnya.
    
3. Click **Rebuild**
    

Jika sudah di bagian **Kubernetes Engine &gt; Workloads &gt; revision history** akan terlihat.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1692147277900/1ce49717-4efa-44fc-aadd-0de613a8d509.jpeg align="center")

check browser reload.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1692147352638/44805ccb-a6c1-4f77-b349-7b5be6b7e2fb.jpeg align="center")

Success !!