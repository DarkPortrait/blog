# Persistent storage in GKE

### Task 1. Create PVs and PVCs

In this task, you create a PVC, which triggers Kubernetes to automatically create a PV.

### Connect to the lab GKE cluster

In Cloud Shell, type the following command to set the environment variable for the zone and cluster name.
```
export my_zone=us-central1-a
export my_cluster=standard-cluster-1
```

Configure tab completion for the kubectl command-line tool.
```
source <(kubectl completion bash)
```

Configure access to your cluster for kubectl:
```
gcloud container clusters get-credentials $my_cluster --zone $my_zone
```

### Create and apply a manifest with a PVC

Most of the time, you don't need to directly configure PV objects or create Compute Engine persistent disks. Instead, you can create a PVC, and Kubernetes automatically provisions a persistent disk for you.

You create the PVC in this task using the pvc-demo.yaml manifest file that has been provided for you. This creates a 30 gigabyte PVC called hello-web-disk that can be mounted as a read-write volume on a single node at a time.
```
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: hello-web-disk
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 30Gi
```

In Cloud Shell, enter the following command to clone the repository to the lab Cloud Shell.
```
git clone https://github.com/GoogleCloudPlatform/training-data-analyst
```

Create a soft link as a shortcut to the working directory.
```
ln -s ~/training-data-analyst/courses/ak8s/v1.1 ~/ak8s
```

Change to the directory that contains the sample files for this lab.
```
cd ~/ak8s/Storage/
```

To show that you currently have no PVCs, execute the following command:
```
kubectl get persistentvolumeclaim
```

Output (Do not copy):
```
No resources found in default namespace.
```

To create the PVC, execute the following command:
```
kubectl apply -f pvc-demo.yaml
```

To show your newly created PVC, execute the following command:
```
kubectl get persistentvolumeclaim
```

Partial output (Do not copy):
```
NAME           STATUS VOLUME        CAP   ACCESS MODES      CLASS   AGE
hello-web-disk Bound  pvc-8...34   30Gi            RWO   standard    5s
```

## Task 2. Mount and verify Google Cloud persistent disk PVCs in Pods

In this task, you attach your persistent disk PVC to a Pod. You mount the PVC as a volume as part of the manifest for the Pod.

### Mount the PVC to a Pod

The manifest file pod-volume-demo.yaml deploys an nginx container, attaches the pvc-demo-volume to the Pod and mounts that volume to the path /var/www/html inside the nginx container. Files saved to this directory inside the container will be saved to the persistent volume and persist even if the Pod and the container are shutdown and recreated.

```
kind: Pod
apiVersion: v1
metadata:
  name: pvc-demo-pod
spec:
  containers:
    - name: frontend
      image: nginx
      volumeMounts:
      - mountPath: "/var/www/html"
        name: pvc-demo-volume
  volumes:
    - name: pvc-demo-volume
      persistentVolumeClaim:
        claimName: hello-web-disk
```

To create the Pod with the volume, execute the following command:
```
kubectl apply -f pod-volume-demo.yaml
```

List the Pods in the cluster.
```
kubectl get pods
```

Output (Do not copy):
```
NAME          READY    STATUS              RESTARTS   AGE
pvc-demo-pod  0/1      ContainerCreating   0          18s
```

If you do this quickly after creating the Pod, you will see the status listed as "ContainerCreating" while the volume is mounted before the status changes to "Running".

To verify the PVC is accessible within the Pod, you must gain shell access to your Pod. To start the shell session, execute the following command:
```
kubectl exec -it pvc-demo-pod -- sh
```

To create a simple text message as a web page in the Pod enter the following commands:
```
echo Test webpage in a persistent volume!>/var/www/html/index.html
chmod +x /var/www/html/index.html
```

Verify the text file contains your message.
```
cat /var/www/html/index.html
```

Output (Do not copy):
```
Test webpage in a persistent volume!
```

Enter the following command to leave the interactive shell on the nginx container.
```
exit
```

### Test the persistence of the PV

You will now delete the Pod from the cluster, confirm that the PV still exists, then redeploy the Pod and verify the contents of the PV remain intact.

Delete the pvc-demo-pod.
```
kubectl delete pod pvc-demo-pod
```

List the Pods in the cluster.
```
kubectl get pods
```

Output (Do not copy):
```
No resources found in default namespace.
```

There should be no Pods on the cluster.

To show your PVC, execute the following command:
```
kubectl get persistentvolumeclaim
```

Partial output (Do not copy):
```
NAME           STATUS VOLUME        CAP   ACCESS MODES      CLASS   AGE
hello-web-disk Bound  pvc-8...34   30Gi            RWO   standard   22m
```

Your PVC still exists, and was not deleted when the Pod was deleted.

Redeploy the pvc-demo-pod.
```
kubectl apply -f pod-volume-demo.yaml
```

List the Pods in the cluster.
```
kubectl get pods
```

Output (Do not copy):
```
NAME           READY     STATUS    RESTARTS   AGE
pvc-demo-pod   1/1       Running   0          15s
```

The Pod will deploy and the status will change to "Running" faster this time because the PV already exists and does not need to be created.

To verify the PVC is still accessible within the Pod, you must gain shell access to your Pod. To start the shell session, execute the following command:
```
kubectl exec -it pvc-demo-pod -- sh
```

To verify that the text file still contains your message execute the following command:
```
cat /var/www/html/index.html
```

Output (Do not copy):
```
Test webpage in a persistent volume!
```

The contents of the persistent volume were not removed, even though the Pod was deleted from the cluster and recreated.

Enter the following command to leave the interactive shell on the nginx container.
```
exit
```

## Task 3. Create StatefulSets with PVCs

In this task, you use your PVC in a StatefulSet. A StatefulSet is like a Deployment, except that the Pods are given unique identifiers.

### Release the PVC

Before you can use the PVC with the statefulset, you must delete the Pod that is currently using it. Execute the following command to delete the Pod:
```
kubectl delete pod pvc-demo-pod
```

Confirm the Pod has been removed.
```
kubectl get pods
```

Output (Do not copy):
```
No resources found in default namespace.
```

### Create a StatefulSet

The manifest file statefulset-demo.yaml creates a StatefulSet that includes a LoadBalancer service and three replicas of a Pod containing an nginx container and a volumeClaimTemplate for 30 gigabyte PVCs with the name hello-web-disk. The nginx containers mount the PVC called hello-web-disk at /var/www/html as in the previous task.

```
kind: Service
apiVersion: v1
metadata:
  name: statefulset-demo-service
spec:
  ports:
  - protocol: TCP
    port: 80
    targetPort: 9376
  type: LoadBalancer
---
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: statefulset-demo
spec:
  selector:
    matchLabels:
      app: MyApp
  serviceName: statefulset-demo-service
  replicas: 3
  updateStrategy:
    type: RollingUpdate
  template:
    metadata:
      labels:
        app: MyApp
    spec:
      containers:
      - name: stateful-set-container
        image: nginx
        ports:
        - containerPort: 80
          name: http
        volumeMounts:
        - name: hello-web-disk
          mountPath: "/var/www/html"
  volumeClaimTemplates:
  - metadata:
      name: hello-web-disk
    spec:
      accessModes: [ "ReadWriteOnce" ]
      resources:
        requests:
          storage: 30Gi
```

To create the StatefulSet with the volume, execute the following command:
```
kubectl apply -f statefulset-demo.yaml
```

Output (Do not copy):
```
service "statefulset-demo-service" created
statefulset.apps "statefulset-demo" created
```

You now have a statefulset running behind a service named statefulset-demo-service.

### Verify the connection of Pods in StatefulSets

Use "kubectl describe" to view the details of the StatefulSet:
```
kubectl describe statefulset statefulset-demo
```

Note the event status at the end of the output. The service and statefulset created successfully.
```
Normal  SuccessfulCreate  10s   statefulset-controller
Message: create Claim hello-web-disk-statefulset-demo-0 Pod statefulset-demo-0 in StatefulSet statefulset-demo success
Normal  SuccessfulCreate  10s   statefulset-controller
Message: create Pod statefulset-demo-0 in StatefulSet statefulset-demo successful
```

List the Pods in the cluster.
```
kubectl get pods
```

Output (Do not copy):
```
NAME                 READY     STATUS    RESTARTS   AGE
statefulset-demo-0   1/1       Running   0          6m
statefulset-demo-1   1/1       Running   0          3m
statefulset-demo-2   1/1       Running   0          2m
```

To list the PVCs, execute the following command:
```
kubectl get pvc
```

Output (Do not copy):
```
NAME                          STATUS    VOLUME           CAPACITY ACCESS
hello-web-disk                Bound     pvc-86117ea6-...34   30Gi    RWO
hello-web-disk-st...-demo-0   Bound     pvc-92d21d0f-...34   30Gi    RWO
hello-web-disk-st...-demo-1   Bound     pvc-9bc84d92-...34   30Gi    RWO
hello-web-disk-st...-demo-2   Bound     pvc-a526ecdf-...34   30Gi    RWO
```

The original hello-web-disk is still there and you can now see the individual PVCs that were created for each Pod in the new statefulset Pod.

Use "kubectl describe" to view the details of the first PVC in the StatefulSet:
```
kubectl describe pvc hello-web-disk-statefulset-demo-0
```

## Task 4. Verify the persistence of Persistent Volume connections to Pods managed by StatefulSets

In this task, you verify the connection of Pods in StatefulSets to particular PVs as the Pods are stopped and restarted.

To verify that the PVC is accessible within the Pod, you must gain shell access to your Pod. To start the shell session, execute the following command:
```
kubectl exec -it statefulset-demo-0 -- sh
```

Verify that there is no index.html text file in the /var/www/html directory.
```
cat /var/www/html/index.html
```

To create a simple text message as a web page in the Pod enter the following commands:
```
echo Test webpage in a persistent volume!>/var/www/html/index.html
chmod +x /var/www/html/index.html
```

Verify the text file contains your message.
```
cat /var/www/html/index.html
```

Output (Do not copy):
```
Test webpage in a persistent volume!
```

Enter the following command to leave the interactive shell on the nginx container.
```
exit
```

Delete the Pod where you updated the file on the PVC.
```
kubectl delete pod statefulset-demo-0
```

List the Pods in the cluster.
```
kubectl get pods
```

You will see that the StatefulSet is automatically restarting the statefulset-demo-0 Pod.

Note: You need to wait until the Pod status shows that it is running again.

Connect to the shell on the new statefulset-demo-0 Pod.
```
kubectl exec -it statefulset-demo-0 -- sh
```

Verify that the text file still contains your message.
```
cat /var/www/html/index.html
```

Output (Do not copy):
```
Test webpage in a persistent volume!
```

The StatefulSet restarts the Pod and reconnects the existing dedicated PVC to the new Pod, ensuring that the data for that Pod is preserved.

Enter the following command to leave the interactive shell on the nginx container.
```
exit
```
