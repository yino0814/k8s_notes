## Taints andTolerations

The taint sparied on the person will throw the bugs off. However, it is possible that somg bugs can tollerant the spray and land on the person.\
So there are **two** factors that decide whether the bug can land on a person: 1. the taint on the person 2. the bug's toleration level to that particular taint.\

In Kubernetes, the person is the **node** and the bugs are **pods**. Taints and Tolerations are used to set restrictions on what pods can be scheduled on a node.\
By default, the pods will be placed into nodes by the scheduler evenly. 
### 1. Taint 
- However, we can use the taint like `Taint=blue` to `node-1` to only allow pods with the specified to be placed in the node.\
```
kubectl taint nodes <node-name> key=value:<taint-effect>
kubectl taint nodes node-1 app=blue:noSchedule
# Remove the taint
kubectl taint nodes node-1 app=blue:noSchedule-
```
- 3 types of taint-effect: \
`NoSchedule`: the pod will not be scheduled on the node\
`PreferNoSchedule`: will try to avoid placing a pod on the node, but no guarantee\
`NoExecute`: new pods will not be scheduled on the node and existing pods on the node if any will be evicted if they do not tolerate the taint.
***Note:***\
Adding the taints & toleration only restricts certain pods from being placed to the node, however it **doesn't guarantee** the pod with the toleration to be placed into the specific pod with the taint if the other pods doesn;t have taint and stuff (eg. `pod-D` could be placed in `node-2`or `node-3` by the scheduler.\
To achieve this, we have the `node affinity`.

### 2. Toleration
- Then, we add the toleration to `pod-D`, so it can be placed in `node-1`.
```
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  containers:
  - name: nginx
    image: nginx
  tolerations:
  - key: "app"           # must be in double quotes
    operator: "Equal"
    value: "blue"
    effect: "NoSchedule"
```
- The scheduler **doesnot** schedule any pods on the `master` node:\
Because when a k8s cluster is first setup, a taint is automatically setup that prevents any pods from being scheculed on the `master` node.
```
kubectl describe node kubemaster | grep Taint
> Taints:                node-role.kubernetes.io/master:NoSchedule
```
