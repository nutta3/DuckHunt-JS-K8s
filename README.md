# DuckHunt-JS-K8s
![2022-11-21 12_58_39-DuckHuntJS and 2 more pages - Work - Microsoft​ Edge](https://user-images.githubusercontent.com/16913822/202973426-a3952552-b23c-4c34-aa90-daa50048b9cd.png)

Containerize DuckHunt which is a NodeJS app and deploy into a kubernetes cluster.

## Credit
I'd like to spend sometime to shootout to Matt who published this nostagic, fun game and makes it publicyly avaialble for us. Please feel free to grab his complete source code by hitting the following orinigal link.

https://github.com/MattSurabian/DuckHunt-JS

## How to use this repo

### 1. Clone th repo ###
you will have to clone this entire repo

```
git clone https://github.com/nutta3/DuckHunt-JS-K8s.git
```

### 2. build an image by either using Docker or Podman ###
you should have a copy of this repo on your local. You just have to change your directly to your local dir (where the Dockerfile is) and execute the following command by either using Docker / Podman in order to containerize the app.

```
docker build -t duckhunt-js:v1 .
```

if you use Podman, use the following command
```
podman build -t duckhunt-js:v1 .
```

it may take a little while to download all dependencies required by this nodeJS app. 

![2022-11-21 13_53_43-Editing DuckHunt-JS-K8s_README md at main · nutta3_DuckHunt-JS-K8s](https://user-images.githubusercontent.com/16913822/202975876-9e248b09-c9e1-4a46-8fea-aaa7afc7340e.png)

### 3. Check your docker/podman image. ###

at this point, you should have your own image created by now, simple execute the following command to see if you have the image created successfully.

#### Docker #### 
```
docker image list
```

#### Podman ####
```
podman image list
```
the dunkhunt-js image should listed there.
![docker image list](https://user-images.githubusercontent.com/16913822/202976227-ee6fb09e-c892-4cfa-83eb-531b78fd14da.png)


### 4. Run it locally. ###
now, we need to test it locally first before we deploy it to a kubernetes clusters.

#### Docker ####
```
docker run -d -p 80:80 duckhunt-js:v1
```

#### Podman ####
```
podman run -d -p 80:80 duckhunt-js:v1
```
It should take only a few seconds to start this containerized app (depends on your computer as well). So you can give a try by open a browser and try http://localhost

![local-duckhunt](https://user-images.githubusercontent.com/16913822/202976845-c18d25bb-0e11-4b2e-a038-0cadbec330f5.png)

### 5. let's move on to the real kubernetes cluster in the cloud. ###
In this case, we will be using AKS - Azure Kubernetes Service which. Prior to the real deployment to the real cluster, we need to have this image that just created stored in the right place. We can use either Docker Hub or Azure Container Registry, in this tutorial we will be using ACR.

#### Docker ####
```
docker tag duckhunt-js:v1 <your Azure Container Registry's name>.azurecr.io/duckhunt:v1
```

#### Podman ####
```
podman tag duckhunt-js:v1 <your Azure Container Registry's name>.azurecr.io/duckhunt:v1
```

and upload it into Azure Container Registry by login first.

#### Docker ####
```
docker login <your ACR's name>.azurecr.io
```

#### Podman ####
```
podman login <your ACR's name>.azurecr.io
```

By using credential that you can retrive from Azure Portal, Look for access key blade in your Azure Container Registry.

![2022-11-21 14_11_31-acr9001 - Microsoft Azure and 1 more page - Work - Microsoft​ Edge](https://user-images.githubusercontent.com/16913822/202978150-02beaec2-e462-4154-91d1-6c7668d4b55a.png)

after the login succeeded, let's make a push to the ACR

#### Docker ####
```
docker push <your ACR's name>.azurecr.io/duckhunt:v1
```

#### Podman ####
```
podman push <your ACR's name>.azurecr.io/duckhunt:v1
```

The image should be successfully stored in ACR by now, Check your ACR.

![pushed](https://user-images.githubusercontent.com/16913822/202980690-5d12ffbc-02f9-43de-b7e4-9ab560028df9.png)

### 6. Deploy the DuckHunt-JS to AKS - Azure Kubernetes Service ###
Now, let's deploy the image into AKS by using the pushed image and expose the deployment by using Load Balancer which we can later retrieve the public IP address and access to the DuckHunt deployed in AKS.

#### kubectl ####
```
# create a deployment in kubernetes cluster by using the image pushed to ACR
kubectl create deployment duckhunt-js --image=<your ACR's name>.azurecr.io/duckhunt:v1

# expose the deployment by using LoadBalancer
kubectl expose deployment duckhunt-js --port=80 --target-port=80 --name=duckhunt-js-svc --type=LoadBalancer
```

### 7. Check the running app in AKS ###

![service](https://user-images.githubusercontent.com/16913822/203000140-a61f8f94-392b-4af6-9aaa-a4c6d4f3120a.png)

AKS cluster should have the public IP address allocated just for this DuckHunt App, we can retrieve the public UP address from 'Services and ingresses'. Copy this and enter it to the browser, Bingo !! your DuckHunt JS has been successfully deployed on AKS.

![done](https://user-images.githubusercontent.com/16913822/203001917-a9a994a7-c4eb-4320-b091-aebca3da89e2.png)
