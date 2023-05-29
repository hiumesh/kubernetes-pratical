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

In our case we will be using "simple-pod.yaml" file
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: demo
  labels:
    app: demo
    type: web
spec:
  containers:
    name: nginx
    image: nginx
    ports:
    - containerPort: 80
```

## 3.Create the pod

```bash
kubectl apply -f pod.yaml
```
or if you have the file somewhere on the internet
```bash
kubectl apply -f https://k8s.io/examples/pods/simple-pod.yaml
```

## 4.Connect to Pod
The following command will provide prompt inside pod
```bash
kubectl exec -it demo -- bash
```

## 5.Check the Pod logs
```bash
kubectl logs demo
```

## 6.Stream the Pod logs
```bash
kubectl logs -f demo
```
stream from the latest log or current log
```bash
kubectl logs -f demo --tail=1
```

## 7.Pod's Node
```bash
kubectl get pods -o wide
```

## 8.Pod's Detail
```bash
kubectl describe pod demo
```

## 9.Delete Pod
```bash
kubectl delete pod demo
```

## 10.Get Yaml for creating pod
```bash
kubectl run demo --image=nginx  --dry-run=client -o yaml
```

## 11.Get Pod Detail in JSON
```bash
kubectl run demo --image=nginx  --dry-run=client -o json
```