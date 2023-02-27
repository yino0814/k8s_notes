## Resource Requirements
Scheduler schedule how the the Pod is placed in the nodes. If there's no sufficient resources avaliable on any of the nodes, k8s holds back scheduling the pod and set it at `appending` state.\
By default, min requirement for the pod:
| CPU | MEMORY | Disk |
|-|-|-|
|0.5|256 mb||

And you can add to the definition under spec:containers:resources: **requests** and **limits**\
1 count of cpu = 1 aws vCPU = 1 GCP Core = 1 Azure Core = 1 Hyperthread\
0.1 cpu = 100m cpu, and min is 1m\

In a docker container has no limits on the resource it can consume, but can add limits to the config file.\
A pod **cannot** use more CPU than the limits, but a container **can** consume more memory than the limit, and when the pod tries to consume more memory than it's limit constantly, the pod will be terminated.

```
apiVersion: v1
kind: Pod
metadata:
  name: frontend
spec:
  containers:
  - name: app
    image: images.my-company.example/app:v4
    resources:
      requests:
        memory: "64Mi"
        cpu: "250m"
      limits:
        memory: "128Mi"
        cpu: "500m"
```
