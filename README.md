# K8s Crud application deployment

This repository will contain the files that will describe the deployment of the crud application

The namespaces at the cluster will be

- staging
- production

The services will be

- Backend application (an RESTful API written in node)
- Frontend application (a web ui)

## How to recreate the cluster this repositorie uses

To create the cluster, the project ```crud-challenge``` must exist

``` bash 
gcloud beta container --project "crud-challenge" clusters create "crud-challenge-cluster" --zone "us-central1-a" --username "admin" --cluster-version "1.10.9-gke.5" --machine-type "n1-standard-1" --image-type "COS" --disk-type "pd-standard" --disk-size "100" --scopes "https://www.googleapis.com/auth/devstorage.read_only","https://www.googleapis.com/auth/logging.write","https://www.googleapis.com/auth/monitoring","https://www.googleapis.com/auth/servicecontrol","https://www.googleapis.com/auth/service.management.readonly","https://www.googleapis.com/auth/trace.append" --preemptible --num-nodes "3" --enable-cloud-logging --enable-cloud-monitoring --no-enable-ip-alias --network "projects/crud-challenge/global/networks/default" --subnetwork "projects/crud-challenge/regions/us-central1/subnetworks/default" --addons HorizontalPodAutoscaling,HttpLoadBalancing --enable-autoupgrade --enable-autorepair
```

To configure kubectl to use that cluster run

``` bash 
gcloud container clusters get-credentials crud-challenge-cluster --zone us-central1-a --project crud-challenge
```


## How to use this repository

### Requirements

1. You will need a gcloud account configured and linked to a valid and existing Google Cloud Platform account
2. You will need to create a projecdt called crud-challenge
3. At this poit you should already ran the steps described at "**How to recreate the cluster this repositorie uses**" in this document

### Steps

1. Run ```sh common/initscript.sh``` to reserve two static IPv4 that will be used by the deployment (Please notice that this requires a gcloud cli and an existing google cloud account)
- **WARNING**
**Please notice that the initscript.sh reserves two static IPv4 on google cloud, if reserve an static IPv4 and don't assign it to a resource Google cloud will charge you per hour it spends not assigned, as shown [here](https://cloud.google.com/compute/pricing), under the Unused IPv4 address pricing section, at the moment of writing of this document the price for unused Static IPv4 is 0.010 US$ per hour, that would mean 14.6 US$ a month if both those IPs are not used**

2. Run ```kubectl apply -f common/namespaces.yml``` to create the namespaces in the cluster

3. Create a secrets.yml file under a directory called secrets on this repository or in some place secure on your computer, with the secrets for both your staging and production database and ssl certificates, you can use the template below

**NOTE:** To encode your secrets use ```echo -n "yoursecrethere" | base64``` in a unix system

**WARNING: THIS REPOSITORY WILL IGNORE ANYTHING YOU PUT UNDER secrets FOLDER, DO NOT COMMIT ANY SECRETS, DOING SO WOULD COMPROMISE YOUR CREDENTIALS EVEN WHEN THEY ARE ENCODED WITH BASE64, THAT IS NO ENCRYPTION METHOD**

```
apiVersion: v1
kind: Secret
metadata:
  name: staging-db
  namespace: staging
type: Opaque
data:
  dbhost: YOUR_ENCODED_BASE64_SECRET_HERE
  dbuser: YOUR_ENCODED_BASE64_SECRET_HERE
  dbpassword: YOUR_ENCODED_BASE64_SECRET_HERE
  dbport: YOUR_ENCODED_BASE64_SECRET_HERE
  database: YOUR_ENCODED_BASE64_SECRET_HERE
---
apiVersion: v1
kind: Secret
metadata:
  name: prod-db
  namespace: prod
type: Opaque
data:
  dbhost: YOUR_ENCODED_BASE64_SECRET_HERE
  dbuser: YOUR_ENCODED_BASE64_SECRET_HERE
  dbpassword: YOUR_ENCODED_BASE64_SECRET_HERE
  dbport: YOUR_ENCODED_BASE64_SECRET_HERE
  database: YOUR_ENCODED_BASE64_SECRET_HERE
---
apiVersion: v1
data:
  tls.crt: YOUR_ENCODED_BASE64_SECRET_HERE
  tls.key: YOUR_ENCODED_BASE64_SECRET_HERE
kind: Secret
metadata:
  name: tls-secret
  namespace: prod
type: Opaque
---
apiVersion: v1
data:
  tls.crt: YOUR_ENCODED_BASE64_SECRET_HERE
  tls.key: YOUR_ENCODED_BASE64_SECRET_HERE
kind: Secret
metadata:
  name: tls-secret
  namespace: staging
type: Opaque
```

**To deploy to staging**

- Run 

```bash 
$ cd /staging
$ kubectl apply -f .
```

**To deploy to production**

- Run 

```bash 
$ cd /production
$ kubectl apply -f .
```