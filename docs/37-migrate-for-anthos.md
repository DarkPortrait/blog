## Steps for migration

1. Configure processing cluster
2. Add migration source
3. Generate the migration object, create a plan in yaml
4. Generate artifacts [container images, yaml files] for the migrate
5. Test the artifacts
6. Deploy to production cluster

## Installing the necessary tools 

Create the processing cluster using this command
```
gcloud container --project $PROJECT_ID \
clusters create $CLUSTER_NAME \
--zone $CLUSTER_ZONE \
--cluster-version 1.14 \
--machine-type "n1-standard-4" \
--image-type "UBUNTU" \
--num-nodes 1 \
--enable-stackdriver-kubernetes \
--scopes "cloud-platform" \
--enable-ip-alias \
--tags="http-server"
```

install migrate for anthos
```
migctl setup install
```

Specify location of the migration
```
migctl source create ce my-ce-src --project my-project --zone zone
```

This is for migration from GCP. Migrating from other cloud providers might require additional libraries.

Create a migration plan
```
migctl migration create test-migration --source my-ce-src --vm-id my-id --intent Image
```

This creates the yaml that can be customized based on migration need.

We can specify following intents:
* Image
* Data
* Image and Data
* PV Based Container

Generate the artifacts for the migration
```
migctl migration generate-artifacts my-migration
```

This will create two types of images. 
* A runnable image
* A non-runnable image layer that can be used to update the container in the future

Migration yaml are created and stored in a storage bucket as an intermediate step.

Get the YAML using this command
```
migctl migration get-artifacts test-migration
```

Deploy after editing the YAML
```
kubectl apply -f deployment spec.yaml
```
