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

1. Run ```sh common/initscript.sh``` to reserve static IPs that will be used by the deployment
2. Run ```kubectl apply -f common/namespaces.yml``` to create the namespaces in the cluster

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