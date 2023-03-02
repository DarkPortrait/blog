# Basic gcloud commands

gcloud: for working with Compute Engine, Google Kubernetes Engine (GKE) and many Google Cloud services
gsutil: for working with Cloud Storage
kubectl: for working with GKE and Kubernetes
bq: for working with BigQuery

## gcloud

List zones in a region
```
gcloud compute zones list | grep $REGION
```

Set default zone
```
gcloud config set compute/zone $ZONE
```

Create a VM
```
gcloud compute instances create $MY_VMNAME \
--machine-type "e2-standard-2" \
--image-project "debian-cloud" \
--image-family "debian-9" \
--subnet "default"
```

List of virtual machine instances
```
gcloud compute instances list
```

Create a service account
```
gcloud iam service-accounts create service-account-name --display-name "service-account-name"
```

Provide editor access to the service account 
```
gcloud projects add-iam-policy-binding $GOOGLE_CLOUD_PROJECT --member serviceAccount:service-account-name@${GOOGLE_CLOUD_PROJECT}.iam.gserviceaccount.com --role roles/viewer
```

View configuration
```
gcloud config list
```

Authenticate as a service account in the cloud shell
```
gcloud auth activate-service-account --key-file credentials.json
```

List of active accounts
```
gcloud auth list
```

Copy file to a virtual machine
```
gcloud compute scp index.html first-vm:index.nginx-debian.html --zone=us-central1-c
```

## Cloud storage

Create bucket
```
gsutil mb gs://$BUCKET
```

Copy files
```
gsutil cp gs://$SOURCE DESTINATION
```

Get access list of a file
```
gsutil acl get gs://$BUCKET/$FILE
```

Set private access

```
gsutil acl set private gs://$BUCKET/$FILE
```

Make bucket public
```
gsutil iam ch allUsers:objectViewer gs://$BUCKET
```
