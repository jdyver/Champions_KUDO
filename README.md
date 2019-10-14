# Champions KUDO Video

## Purpose: This should be a short recording that can be used in external awareness efforts to explain the value of KUDO to a highly technical audience.  Demonstration should show the deployment and use of KUDO with one of our operators (such as the Kafka operator).   Key is to convince someone who is interested in leveraging stateful data services that KUDO is the way to go.

## Opening:

Slide 1 - Life without KUDO

Slide 2 - KUDO Architecture

## Demo Overview:

Installation
- Konvoy Kubernetes already installed and connected to kubectl
```
brew install kudo-cli  # OR brew upgrade kudo-cli
kubectl get nodes
kubectl kudo version
```
```
K8S KUDO $ kubectl kudo version
KUDO Version: version.Info{GitVersion:"0.7.4", GitCommit:"81b14cb3", BuildDate:"2019-10-11T16:44:30Z", GoVersion:"go1.13.1", Compiler:"gc", Platform:"darwin/amd64"}
```

```
kubectl kudo init
kubectl get namespaces --namespace=kudo-system
```
```
K8S KUDO $ kubectl get namespaces --namespace=kudo-system
NAME                    STATUS   AGE
cert-manager            Active   57m
default                 Active   58m
kommander               Active   51m
kube-node-lease         Active   58m
kube-public             Active   58m
kube-system             Active   58m
kubeaddons              Active   57m
kudo-system             Active   31m
velero                  Active   47m
velero-minio-operator   Active   47m
```

