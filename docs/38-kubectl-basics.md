## Kubectl 

kubectl is used to communicate with the kube-APIserver on the control plane. 

It must be configured with the location and credentials of the kubernetes cluster.

kubectl configuration is in a config file: $HOME/.kube/config

Configuration contains 
* Target cluster name
* Credentials for the cluster

View the configuration
```
kubectl config view
```

Retrieve the credentials 
```
gcloud container clusters \
get-credentuals $CLUSTER_NAME \
--zone $ZONE
```

This will update the configuration file with appropriate credentials. 


List the pods
```
kubectl get pods
```

Example of deployment object in YAML format 
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app
spec:
  replicas: 3
  template:
    metadata:
      labels:
	app: my-app
    spec:
      containers:
      - name: my-app
        image: gcr.io/demo/my-app:1.0
        ports:
        - containerPort: 8080
```

Deploy using this command
```
kubectl apply -f $DEPLOYMENT_FILE
```

It is also possible to deploy imperatively using the `run` command, that specifies all parameters inline.
```
kubectl run $DEPLOYMENT_NAME \
--image gcr.io/demo/my-app:1.0 \
--replicas 3 \
--labels app:my-app \
--port 8080 \
--generator deployment/apps.v1 \
--save-config
```

Inspect the Deployment 
```
kubectl get deployment $DEPLOYMENT_NAME
```

This can be saved as a YAML for future reference
```
kubectl get deployment $DEPLOYMENT_NAME -o yaml > this.yaml
```

Scaling the deployment
```
kubectl scale deployment $DEPLOYMENT_NAME -replicas=5
```

Autoscaling the deployment by specifying a minimum and maximum number of replicas
```
kubectl autoscale deployment $DEPLOYMENT_NAME --min=5 --max=15 --cpu-percentage=75
```

This will create a Kubernetes object called Horizontal Pod Autoscaler. This object will scale the number of pods to match the cpu utilization. This will scale a particular deployment within a cluster, and not a cluster as a whole. 
Update a deployment using these commands 
```
kubectl apply -f $DEPLOYMENT_FILE
```

This will update the deployment according to the specifications in the YAML.

Secondly, deployment can be updated imperatively.
```
kubectl set image deployment $DEPLOYMENT_NAME $IMAGE $IMAGE:$TAG
```

Thirdly, this command will open the configuration file in vim editor. If changed and save, the update will be deployed.
```
kubectl edit deployment/$DEPLOYMENT_NAME
```

Rollout undo command
```
kubectl rollout undo deployment $DEPLOYMENT_NAME
```

Rolling out to a specific version
```
kubectl rollout undo deployment $DEPLOYMENT_NAME --to-revision=2
```

View version history
```
kubectl rollout history deployment $DEPLOYMENT_NAME --revision=2
```

When a deployment is edited, a rollout is triggered automatically. To change this behavior:
```
kubectl rollout pause deployment $DEPLOYMENT_NAME
```

The changes will be deployed in one rollout after its resumed:
```
kubectl rollout resume deployment $DEPLOYMENT_NAME
```

We can monitor the rollout status:
```
kubectl rollout status deployment $DEPLOYMENT_NAME
```

Delete a deployment
```
kubectl delete deployment $DEPLOYMENT_NAME
```

This will lead to deletion of all resources run by the deployment, including running pods.
