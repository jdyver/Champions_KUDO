# Champions KUDO Video Script

## Purpose 
This should be a short recording that can be used in external awareness efforts to explain the value of KUDO to a highly technical audience.  Demonstration should show the deployment and use of KUDO with one of our operators (such as the Kafka operator).   Key is to convince someone who is interested in leveraging stateful data services that KUDO is the way to go.

## Preso Opening

Slide 1 - Life without KUDO

Slide 2 - KUDO Architecture

## KUDO architecture

Review Hello World - Nginx Example in system

## Demo KUDO Installation with Simple Example

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
**Install Nginx Example**

Discuss and show directory structure
```
KUDO first-operator $ ls -Rl
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
KUDO first-operator $ cat operator.yaml && cat params.yaml
name: "first-operator"
version: "0.1.0"
kudoVersion: ">= 0.2.0"
kubernetesVersion: ">= 1.14"
maintainers:
- jdyckowski@d2iq.com
url: https://kudo.dev
tasks:
  nginx:
    resources:
      - deployment.yaml
plans:
  deploy:
    strategy: serial
    phases:
      - name: main
        strategy: serial
        steps:
          - name: everything
            tasks:
              - nginx
replicas:
 description: Number of replicas that should be run as port of the deployment
 default: 2
 ```

 Discuss and show template/deployment with KUDO parameter
 ```
 KUDO first-operator $ cat templates/deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  selector:
    matchLabels:
      app: nginx
  replicas: {{ .Params.Replicas }} # tells deployment to run X pods matching the template
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx-container
        image: nginx:1.7.9
        ports:
        - containerPort: 80
 ```

Install KUDO - Nginx (first-operator) with parameters

```
kubectl kudo install ./ --instance=nginx1 --parameter replicas=5
```

## Demo KUDO Install and Plan
- Install Zookeeper with KUDO and check plan
Run:
```
kubectl kudo install zookeeper --instance=zk
kubectl kudo plan status --instance=zk
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

- Install Kafka with KUDO and check plan
Run:
```
kubectl kudo install kafka --instance=kafka
kubectl kudo plan status --instance=kafka
```
```
K8S KUDO $ kubectl kudo install kafka --instance=kafka
instance.kudo.dev/v1alpha1/kafka created

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

### Setup Kafka Studio Demo

### 