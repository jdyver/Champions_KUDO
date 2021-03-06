# Champions KUDO Video Script

## Purpose 
This should be a short recording that can be used in external awareness efforts to explain the value of KUDO to a highly technical audience.  Demonstration should show the deployment and use of KUDO with one of our operators (such as the Kafka operator).   Key is to convince someone who is interested in leveraging stateful data services that KUDO is the way to go.

## Preso Opening

Slide 1 - Life without KUDO

Slide 2 - KUDO Architecture

## KUDO architecture

Review Hello World - Nginx Example in system

## Install KUDO

- Environment variables setup for paths
```
export FIRST=~/Documents/Scripts/KUDO-Operators/operators/repository/first-operator/operator
export ZK=~/Documents/Scripts/KUDO-Operators/operators/repository/zookeeper/operator
export KAFKA=~/Documents/Scripts/KUDO-Operators/operators/repository/kafka/operator
export STUDIO=~/Documents/Scripts/Kubernetes/konvoy-kudo-studio
export SECOND=~/Documents/Scripts/KUDO-Labs/second-operator
```

- Konvoy Kubernetes already installed and connected to kubectl
```
K8S KUDO $ kubectl get nodes
NAME                                         STATUS   ROLES    AGE     VERSION
ip-10-0-128-147.us-west-2.compute.internal   Ready    <none>   3h16m   v1.15.4
...
ip-10-0-131-124.us-west-2.compute.internal   Ready    <none>   3h16m   v1.15.4
ip-10-0-192-172.us-west-2.compute.internal   Ready    master   3h19m   v1.15.4
ip-10-0-192-28.us-west-2.compute.internal    Ready    master   3h17m   v1.15.4
ip-10-0-192-70.us-west-2.compute.internal    Ready    master   3h18m   v1.15.4
```

- Install KUDO cli into local machine (extension to kubectl)
Run:
```
brew install kudo-cli  # OR brew upgrade kudo-cli
kubectl kudo version
```
```
K8S KUDO $ kubectl kudo version
KUDO Version: version.Info{GitVersion:"0.7.4", GitCommit:"81b14cb3", BuildDate:"2019-10-11T16:44:30Z", GoVersion:"go1.13.1", Compiler:"gc", Platform:"darwin/amd64"}
```

- Install KUDO to Kubernetes
Run:
```
kubectl kudo init
kubectl get pods --namespace=kudo-system
```
```
K8S KUDO $ kubectl get pods --namespace=kudo-system
NAME                        READY   STATUS    RESTARTS   AGE
kudo-controller-manager-0   1/1     Running   0          174m
```

## Demo KUDO Structure, Customization, Installation, Scale and Uninstallation with Nginx (Second Operator) Example

```
cd $SECOND
```
**KUDO Operator Structure**
Discuss and show directory structure
```
KUDO second-operator $ ls -Rl
total 16
-rw-r--r--  1 jamesdyckowski  staff  384 Oct 31 15:11 operator.yaml
-rw-r--r--  1 jamesdyckowski  staff  100 Sep  4 16:03 params.yaml
drwxr-xr-x  3 jamesdyckowski  staff   96 Sep  4 16:07 templates

./templates:
total 8
-rw-r--r--  1 jamesdyckowski  staff  401 Sep  4 16:02 deployment.yaml
```

Discuss and show declarative operator and parameter files
```
cat operator.yaml

cat params.yaml

cat templates/deployment.yaml
```

**KUDO Install (local repo) with Custom Parameter**
```
kubectl kudo install ./ --instance secondop --parameter IMAGE_ID=1.8
```

```
kubectl get pods
```

```
kubectl describe pod nginx-secondop-deployment-86b8c94d66-2x9lf
...
  Normal  Pulled     4m54s  kubelet, ip-10-0-132-227.us-west-2.compute.internal  Successfully pulled image "nginx:1.8"
  Normal  Created    4m54s  kubelet, ip-10-0-132-227.us-west-2.compute.internal  Created container nginx
  Normal  Started    4m54s  kubelet, ip-10-0-132-227.us-west-2.compute.internal  Started container nginx
```

```
kubectl kudo plan status --instance secondop
```

**KUDO Scale / Update Parameter**
```
kubectl kudo update --instance secondop -p replicas=5

k get po -w
```

**KUDO Uninstall**
Remove First Operator
```
kubectl kudo uninstall --instance secondop
```

## Demo KUDO Plans and Plan Status with Zookeeper

```
cd $ZK
clear
```

- Install Zookeeper with KUDO and check plan
Run:
```
K8S KUDO $ kubectl kudo install zookeeper --instance=zk
K8S KUDO $ kubectl kudo plan status --instance=zk
```
```
K8S KUDO $ kubectl kudo install zookeeper --instance=zk
instance.kudo.dev/v1alpha1/zk created

K8S KUDO $ kubectl kudo plan status --instance=zk
Plan(s) for "zk" in namespace "default":
.
└── zk (Operator-Version: "zookeeper-0.1.0" Active-Plan: "deploy")
    ├── Plan deploy (serial strategy) [IN_PROGRESS]
    │   ├── Phase zookeeper [IN_PROGRESS]
    │   │   └── Step everything (IN_PROGRESS)
    │   └── Phase validation [PENDING]
    │       └── Step validation (PENDING)
    └── Plan validation (serial strategy) [NOT ACTIVE]
        └── Phase connection (parallel strategy) [NOT ACTIVE]
            └── Step connection (parallel strategy) [NOT ACTIVE]
                └── connection [NOT ACTIVE]
```

```
cd $ZK
cat operator.yaml
cat params.yaml
```

## Demo KUDO Operator / Application Upgrades with Kafka
Run:
```
kubectl kudo install kafka --instance=kafka -p ZOOKEEPER_URI=zk-zookeeper-0.zk-hs:2181,zk-zookeeper-1.zk-hs:2181,zk-zookeeper-2.zk-hs:2181 --version=0.1.3

kubectl kudo plan status --instance=kafka
```
```
K8S KUDO $ kubectl kudo install ./ --instance=kafka -p ZOOKEEPER_URI=zk-zookeeper-0.zk-hs:2181,zk-zookeeper-1.zk-hs:2181,zk-zookeeper-2.zk-hs:2181 --version=0.1.3
instance.kudo.dev/v1beta1/kafka created

K8S KUDO $ kubectl kudo plan status --instance=kafka
Plan(s) for "kafka" in namespace "default":
.
└── kafka (Operator-Version: "kafka-0.2.0" Active-Plan: "deploy")
    ├── Plan deploy (serial strategy) [IN_PROGRESS]
    │   └── Phase deploy-kafka [IN_PROGRESS]
    │       └── Step deploy (IN_PROGRESS)
    └── Plan not-allowed (serial strategy) [NOT ACTIVE]
        └── Phase not-allowed (serial strategy) [NOT ACTIVE]
            └── Step not-allowed (serial strategy) [NOT ACTIVE]
                └── not-allowed [NOT ACTIVE]
```

(While installing)
Move to Kafka operator directory

- Look at BROKER options and defaults
- Look at ZOOKEEPER options and defaults

```
cd $KAFKA
clear
vi params.yaml
cat params.yaml | grep ZOO
```

**Setup Kafka Quick Demo**

Build Kafka Example

```
cd $SECOND
cd ../kafka-eg/
bash run-timedate-log.sh
```

```
kubectl get pods | grep cons

kubectl logs kudo-kafka-consumer-6b4dd5cd59-k8bpc -f
```

**Upgrade Kafka**

Baseline:

```
JD kafka-eg $ kubectl get instances.kudo.dev kafka -o yaml | grep "name: kafka"
  name: kafka
    name: kafka-0.1.3

JD kafka-eg $ k describe po kafka-kafka-0 | grep Image
    Image:         mesosphere/kafka:0.1.3-2.2.1
    Image ID:      docker.io/mesosphere/kafka@sha256:909f231266fbba1b8f4a85fdfc32d55d44d704257db5cf592b3950aeccc8a8b4
```

Run Upgrade:

```
k kudo upgrade kafka --version=1.1.0 --instance kafka

kubectl kudo plan status --instance=kafka

JD KUDO $ kubectl get instances.kudo.dev kafka -o yaml | grep "name: kafka"
  name: kafka
    name: kafka-1.1.0

JD KUDO $ kubectl describe po kafka-kafka-0 | grep Image
    Image:         quay.io/prometheus/node-exporter
    Image ID:      quay.io/prometheus/node-exporter@sha256:a2f29256e53cc3e0b64d7a472512600b2e9410347d53cdc85b49f659c17e02ee
    Image:         mesosphere/kafka:1.1.0-2.4.0
    Image ID:      docker.io/mesosphere/kafka@sha256:a8720ff3b1bd02b1317b135dd977da52a6b312641bcf5e98cd06b82b59aa4092
```

Check to make sure we are still running messages:

```
kubectl logs kudo-kafka-consumer-6b4dd5cd59-k8bpc -f
```




### BACKUP: Scale Out Kafka

```
K8S KUDO $ kubectl kudo update --instance=kafka -p BROKER_COUNT=4
Instance kafka was updated.jamess-mbp-3:operator jamesdyckowski$ kubectl kudo plan status --instance=kafka
Plan(s) for "kafka" in namespace "default":
.
└── kafka (Operator-Version: "kafka-1.0.2" Active-Plan: "update")
    ├── Plan deploy (serial strategy) [NOT ACTIVE]
    │   └── Phase deploy-kafka (serial strategy) [NOT ACTIVE]
    │       └── Step deploy-kafka (serial strategy) [NOT ACTIVE]
    │           └── deploy [NOT ACTIVE]
    ├── Plan not-allowed (serial strategy) [NOT ACTIVE]
    │   └── Phase not-allowed (serial strategy) [NOT ACTIVE]
    │       └── Step not-allowed (serial strategy) [NOT ACTIVE]
    │           └── not-allowed [NOT ACTIVE]
    └── Plan update (serial strategy) [IN_PROGRESS]
        └── Phase update-kafka [IN_PROGRESS]
            └── Step update (IN_PROGRESS)

```