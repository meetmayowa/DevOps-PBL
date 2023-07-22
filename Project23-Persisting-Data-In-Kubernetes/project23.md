## PERSISTING DATA IN KUBERNETES
### INTRODUCTION
The pods created in Kubernetes are ephemeral, they don't run for long. When a pod dies, any data that is not part of the container image will be lost when the container is restarted because Kubernetes is best at managing stateless applications which means it does not manage data persistence. To ensure data persistent, the PersistentVolume resource is implemented to acheive this.

The following outlines the steps:

### STEP 1: Setting Up AWS Elastic Kubernetes Service With EKSCTL

* Downloading and extracting the latest release of eksctl with the following command:`$ curl --silent --location "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp`
* Moving the extracted binary to `/usr/local/bin:$ sudo mv /tmp/eksctl /usr/local/bin`
* Testing the installation was successful with the following command:$ eksctl version

![load-balancer](./img/1-eksctl.PNG)

* Setting up EKS cluster with a single commandline:

```
$ eksctl create cluster \
  --name my-eks-clusters \
  --version 1.27 \
  --region us-east-1 \
  --nodegroup-name worker-nodes \
  --node-type t2.medium \
  --nodes 2

```

![load-balancer](./img/2-cluster.PNG)
![load-balancer](./img/2-cluster-2.PNG)

### STEP 2: Creating Persistent Volume Manually For The Nginx Application
* Creating a deployment manifest file for the Nginx application and applying it:

```
sudo cat <<EOF | sudo tee ./nginx-pod.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    tier: frontend
spec:
  replicas: 3
  selector:
    matchLabels:
      tier: frontend
  template:
    metadata:
      labels:
        tier: frontend
    spec:
      containers:
      - name: nginx
        image: nginx:latest
        ports:
        - containerPort: 80
EOF


```

![load-balancer](./img/1-eksctl.PNG)

![load-balancer](./img/1-eksctl.PNG)


* Verifying that the pod is running: $ kubectl get pod
* Exec into the pod and navigating to the nginx configuration file:

![load-balancer](./img/1-eksctl.PNG)

* When creating a volume it must exists in the same region and availability zone as the EC2 instance running the pod. To confirm which node is running the pod: `$ kubectl get po nginx-deployment-6fdcffd8fc-tbvfk -o wide`