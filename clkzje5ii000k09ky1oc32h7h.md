---
title: "Implement DevOps in Google Cloud: Challenge Lab"
datePublished: Sun Aug 06 2023 14:26:21 GMT+0000 (Coordinated Universal Time)
cuid: clkzje5ii000k09ky1oc32h7h
slug: implement-devops-in-google-cloud-challenge-lab
canonical: https://www.cloudskillsboost.google/games/4301/labs/27860
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/fDsCIIGdw9g/upload/47d131a1fe46e0ca85319d2370f98c96.jpeg
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1691331963796/98174b32-7d60-4750-938c-0d17b0740411.png
tags: docker, kubernetes, google-cloud-platform, ci-cd

---

### Task 1. Create the Lab resources

Enable APIs untuk GKE (Google Kubernetes Engine), Cloud Build dan Cloud Source Repositories

```bash
$ gcloud services enable container.googleapis.com \
    cloudbuild.googleapis.com \
    sourcerepo.googleapis.com
```

Menambahkan role Kubernetes Developer ke service akun Cloud Build

```bash
$ export PROJECT_ID=$(gcloud config get-value project)
gcloud projects add-iam-policy-binding $PROJECT_ID \
--member=serviceAccount:$(gcloud projects describe $PROJECT_ID \
--format="value(projectNumber)")@cloudbuild.gserviceaccount.com --role="roles/container.developer"
```

Konfigurasi Git di Cloud shell, ganti user.email menjadi username, dan user.name set menjadi student

```bash
$git config --global user.email student-04-fca41af536e9@qwiklabs.net
$git config --global user.name student
```

sebelum membuat repo di google artifacts, enak nya kita set beberapa variable yang dibutuhkan biar mempermudah untuk kedepannya.

```bash
export PROJECT_ID=qwiklabs-gcp-03-3a6e4046cacb
export CLUSTER_NAME=hello-cluster
export ZONE=us-central1-a
export REGION=us-central1
export REPO=my-repository

$gcloud artifacts repositories create $REPO \
    --repository-format=docker \
    --location=$REGION \
    --description="repo my-repository"
```

jika sudah membuat repository di gcloud artifacts selanjutnya buat cluster GKE dengan nama hello-cluster

```bash
gcloud beta container --project "$PROJECT_ID" \
clusters create "$CLUSTER_NAME" --zone "$ZONE" \
--no-enable-basic-auth --cluster-version latest \
--release-channel "regular" --machine-type "e2-medium" \
--image-type "COS_CONTAINERD" --disk-type "pd-balanced" \
--disk-size "100" --metadata disable-legacy-endpoints=true  \
--logging=SYSTEM,WORKLOAD --monitoring=SYSTEM --enable-ip-alias \
--network "projects/$PROJECT_ID/global/networks/default" \
--subnetwork "projects/$PROJECT_ID/regions/$REGION/subnetworks/default" \
--no-enable-intra-node-visibility --default-max-pods-per-node "110" \
--enable-autoscaling --min-nodes "2" --max-nodes "6" \
--location-policy "BALANCED" --no-enable-master-authorized-networks \
--addons HorizontalPodAutoscaling,HttpLoadBalancing,GcePersistentDiskCsiDriver \
--enable-autoupgrade --enable-autorepair --max-surge-upgrade 1 \
--max-unavailable-upgrade 0 --enable-shielded-nodes --node-locations "$ZONE"
```

Pembuatan cluster GKE cukup lumayan lama sekitar 10-15 menit, jika sudah maka langkah selanjutnya membuat namespace dengan nama prod dan dev

```bash
$kubectl create namespace prod	

$kubectl create namespace dev
```

### Task 2. Create a repository in Cloud Source Repositories

buat repo dengan nama **sample-app**

```bash
$gcloud source repos create sample-app
```

clone **sample-app**

```bash
$git clone https://source.developers.google.com/p/$PROJECT_ID/r/sample-app
```

pada baris ini akan mengcopy sample code ke dalam repo **sample-app**

```bash
cd ~
gsutil cp -r gs://spls/gsp330/sample-app/* sample-app
```

jika sudah buat first commit dan push ke branch **master.**

```bash
cd sample-app/
git init
git add .
git commit -m "first commit"
git push -u origin master
```

buat branch baru lagi dengan nama **dev,** commit dan push ke branch **dev.**

```bash
git checkout -b dev
git add .
git commit -m "first commit to dev"
git push -u origin dev
```

### Task 3. Create the Cloud Build Triggers

pada bagian ini tugas ada dua yaitu membuat cloud Build Triggers untuk development dan production. Masuk ke **Cloud Build** -&gt; **create triggers.**

| Name | sample-app-dev-deploy |
| --- | --- |
| Event | Push to branch |
| Source Repository | sample-app |
| Branch | ^dev$ |
| Cloud Build Configuration File | cloudbuild-dev.yaml |

| Name | sample-app-**prod**\-deploy |
| --- | --- |
| Event | Push to branch |
| Source Repository | sample-app |
| Branch | `^master$` |
| Cloud Build Configuration File | cloudbuild.yaml |

contoh jika sukses membuat job triggers.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1691317424604/ac28d850-be3a-4c34-9e3e-3a957c2f7b3d.png align="center")

### Task 4. Deploy the first versions of the application

pada task ini tugas nya deploy image container dari **Cloud Build** ke **GKE.**

```bash
COMMIT_ID="$(git rev-parse --short=7 HEAD)"
gcloud builds submit --tag="${REGION}-docker.pkg.dev/${PROJECT_ID}/$REPO/hello-cloudbuild:${COMMIT_ID}" .
```

**Development**

cloudbuild-dev.yaml

```bash
steps:
  # Step 1: Compile the Go Application
  - name: 'gcr.io/cloud-builders/go'
    env: ['GOPATH=/gopath']
    args: ['build', '-o', 'main', 'main.go']

  # Step 2: Build the Docker image for the Go application
  - name: 'gcr.io/cloud-builders/docker'
    args: ['build', '-t', 'us-central1-docker.pkg.dev/$PROJECT_ID/my-repository/hello-cloudbuild-dev:v1.0', '.']

  # Step 3: Push the Docker image to Artifact Registry
  - name: 'gcr.io/cloud-builders/docker'
    args: ['push', 'us-central1-docker.pkg.dev/$PROJECT_ID/my-repository/hello-cloudbuild-dev:v1.0']

  # Step 4: Apply the production deployment YAML file to the production namespace
  - name: 'gcr.io/cloud-builders/kubectl'
    id: 'Deploy'
    args: ['-n', 'dev', 'apply', '-f', 'dev/deployment.yaml']
    env:
    - 'CLOUDSDK_COMPUTE_REGION=us-central1-a'
    - 'CLOUDSDK_CONTAINER_CLUSTER=hello-cluster'
```

dev/deployment.yaml

```bash
apiVersion: apps/v1
kind: Deployment
metadata:
  name: development-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: dev-app
  template:
    metadata:
      labels:
        app: dev-app
    spec:
      containers:
      - name: dev-container
        image: us-central1-docker.pkg.dev/qwiklabs-gcp-03-3a6e4046cacb/my-repository/hello-cloudbuild:v1.0
        ports:
        - containerPort: 8080
```

push to branch **dev** untuk mentriggers perubahan.

```bash
git add .
git commit -m "change version image"
git push -u origin dev
```

**Production**

cloudbuild.yaml

```bash
steps:
  # Step 1: Compile the Go Application
  - name: 'gcr.io/cloud-builders/go'
    id: 'Compile application'
    env: ['GOPATH=/gopath']
    args: ['build', '-o', 'main', 'main.go']

  # Step 2: Build the Docker image for the Go application
  - name: 'gcr.io/cloud-builders/docker'
    id: 'Build Docker image'
    args: ['build', '-t', 'us-central1-docker.pkg.dev/$PROJECT_ID/my-repository/hello-cloudbuild:v1.0', '.']

  # Step 3: Push the Docker image to Artifact Registry
  - name: 'gcr.io/cloud-builders/docker'
    id: 'Push Docker image'
    args: ['push', 'us-central1-docker.pkg.dev/$PROJECT_ID/my-repository/hello-cloudbuild:v1.0']

  # Step 4: Apply the production deployment YAML file to the production namespace
  - name: 'gcr.io/cloud-builders/kubectl'
    id: 'Deploy'
    args: ['-n', 'prod', 'apply', '-f', 'prod/deployment.yaml']
    env:
    - 'CLOUDSDK_COMPUTE_REGION=us-central1-a'
    - 'CLOUDSDK_CONTAINER_CLUSTER=hello-cluster'
```

prod/deployment.yaml

```bash
apiVersion: apps/v1
kind: Deployment
metadata:
  name: production-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: production-app
  template:
    metadata:
      labels:
        app: production-app
    spec:
      containers:
      - name: production-container
        image: us-central1-docker.pkg.dev/qwiklabs-gcp-03-3a6e4046cacb/my-repository/hello-cloudbuild:v1.0
        ports:
        - containerPort: 8080
```

push to branch **master,** untuk mentriggers perubahan

```bash
git add .
git commit -m "change version image production"
git push -u origin master
```

kurang lebih seperti ini history dari **Cloud Build** mentriggers perubahan dari git commit yang sudah di push.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1691319502101/6e9fa4fb-c086-4953-a2f8-c6488b1d4c14.png align="center")

dan check juga di GKE, ke bagian **Workloads** status sudah **OK** tandanya sudah terdeploy untuk **deployment dan production.**

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1691319512790/cfbbe092-7d52-4d00-81ff-9c4653434b04.png align="center")

### Task 5. Deploy the second versions of the application

**Development Revision**

main.go

```bash
func main() {
	http.HandleFunc("/blue", blueHandler)
	http.HandleFunc("/red", redHandler)
	http.ListenAndServe(":8080", nil)
}
...
func redHandler(w http.ResponseWriter, r *http.Request) {
	img := image.NewRGBA(image.Rect(0, 0, 100, 100))
	draw.Draw(img, img.Bounds(), &image.Uniform{color.RGBA{255, 0, 0, 255}}, image.ZP, draw.Src)
	w.Header().Set("Content-Type", "image/png")
	png.Encode(w, img)
}
```

cloudbuild-dev.yaml

```bash
...
# Step 2: Build the Docker image for the Go application
  - name: 'gcr.io/cloud-builders/docker'
    args: ['build', '-t', 'us-central1-docker.pkg.dev/$PROJECT_ID/my-repository/hello-cloudbuild-dev:v2.0', '.']

  # Step 3: Push the Docker image to Artifact Registry
  - name: 'gcr.io/cloud-builders/docker'
    args: ['push', 'us-central1-docker.pkg.dev/$PROJECT_ID/my-repository/hello-cloudbuild-dev:v2.0']
.....
```

dev/deployment.yaml

```bash
...
    image: us-central1-docker.pkg.dev/qwiklabs-gcp-03-3a6e4046cacb/my-repository/hello-cloudbuild:v2.0
...
```

commit dan push ke branch **dev.**

```bash
git add .
git commit -m "change version image revison 2.0"
git push -u origin dev
```

**Production Revision**

main.go

```bash
func main() {
	http.HandleFunc("/blue", blueHandler)
	http.HandleFunc("/red", redHandler)
	http.ListenAndServe(":8080", nil)
}
...
func redHandler(w http.ResponseWriter, r *http.Request) {
	img := image.NewRGBA(image.Rect(0, 0, 100, 100))
	draw.Draw(img, img.Bounds(), &image.Uniform{color.RGBA{255, 0, 0, 255}}, image.ZP, draw.Src)
	w.Header().Set("Content-Type", "image/png")
	png.Encode(w, img)
}
```

cloudbuild.yaml

```bash
...
# Step 2: Build the Docker image for the Go application
  - name: 'gcr.io/cloud-builders/docker'
    id: 'Build Docker image'
    args: ['build', '-t', 'us-central1-docker.pkg.dev/$PROJECT_ID/my-repository/hello-cloudbuild:<version>', '.']

  # Step 3: Push the Docker image to Artifact Registry
  - name: 'gcr.io/cloud-builders/docker'
    id: 'Push Docker image'
    args: ['push', 'us-central1-docker.pkg.dev/$PROJECT_ID/my-repository/hello-cloudbuild:<version>']
...
```

prod/deployment.yaml

```bash
...
    image: us-central1-docker.pkg.dev/qwiklabs-gcp-03-3a6e4046cacb/my-repository/hello-cloudbuild:v2.0
...
```

commit dan push ke branch **master.**

```bash
git add .
git commit -m "change version image revision 2.0 production"
git push -u origin master
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1691320175122/9a12b2aa-d16f-4e86-87d4-6288a23cafd5.png align="center")

terlihat revisions **deployment** sudah menggunakan image container dengan tag **v2.0**

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1691320202315/3a72a3a8-e03e-4ea4-87ff-4b903abb79ff.png align="center")

terlihat revisions **production** sudah menggunakan image container dengan tag **v2.0**

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1691320215708/2d61c6f3-7919-420c-a425-8c1144c61fe1.png align="center")

### Task 6. Roll back the production deployment

Pada bagian ini di **history cloud build,** klik name container sebelum build terakhir. klik di atas **rebuild.**

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1691320411421/d5bc6e29-cccb-426b-b3b0-4e3c4610f479.png align="center")

bagian ini juga di bagian **history cloud build,** klik name container sebelum build terakhir. klik di atas **rebuild.**

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1691320471533/ed7caafc-3523-4c2e-af62-57135a985358.png align="center")

sekian thank you.