# Deploy Pod using YAML file and Perform Various Operations

## 1.Create the Kubernetes Cluster

You can use any cloud provider to deploy kubernetes cluster such as Google cloud, AWS etc or you can use free kubernetes [playground](https://labs.play-with-k8s.com/) by docker, it is very simple and easy to setup.

Kubernetes Playground Setup:

* Create a node, it will be our master node and run following commands:
```bash
kubeadm init --apiserver-advertise-address $(hostname -i) --pod-network-cidr 10.5.0.0/16
```
The command above will produce the long output and somewhere in the output you will find out a command whcih we are going to use for connecting worker nodes to this master node.

```bash
kubectl apply -f https://raw.githubusercontent.com/cloudnativelabs/kube-router/master/daemonset/kubeadm-kuberouter.yaml
```

* Now create more nodes which will be our worker nodes based on your reqirements and register these new nodes to master node by running the command produced in the output of the commnd above, it will look like: 

```bash
kubeadm join XXX.XXX.X.XXX:6443 --token XXXXXXXXXXXXXXXXXXXXXX --discovery-token-ca-cert-hash XXXXXXXXXXXXXXXXXXXXXXXXXXXX
```

## 2.Write the yaml file for the pod

If you don't know how to write kubernetes's yaml file, you can check out the [official documentation](https://kubernetes.io/docs/concepts/workloads/pods/).

In our case we will be using "replicaset.yaml" file
```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: demo
  labels:
    app: demo
    type: web
spec:
  replicas: 3
  selector:
    matchLabels:
      type: web
  template:
    metadata:
      labels:
        type: web
    spec:
      containers:
      - name: demo-nginx
        image: nginx
        ports:
        - containerPort: 80
```

## 3.Create Replica Set

```bash
kubectl apply -f replicaset.yaml
```

## 4.Get All Replica Sets

```bash
kubectl get rs
```

## 5.Find All Podes created by a replica set

```bash
kubectl get pods
```
This will output all pods, by observing the name of the pods you can easily findout which pod belongs to which replica set as the name of the pods will have the replica set name's prefix in there name. In our case it will be "demo-XXXX".

## 6.Detail of Replica Set

```bash
kubectl describe rs demo
```

## 7.Replica Set Detail in Yaml/Json

YAML
```bash
kubectl get rs demo -o yaml
```
JSON
```bash
kubectl get rs demo -o json
```

## 8.Scale Replica Set

```bash
kubectl scale rs demo --replicas=5
```

## 9.Describe Pods managed by Replica Set

```bash
kubectl describe pods demo
```
In this command **demo** is the name of replica set


