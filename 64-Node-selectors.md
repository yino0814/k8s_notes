## Node Selectors
### Label nodes first
```
kubectl label nodes <node-name> <label-key>=<label-value>
kubectl label nodes node-1 size=Large
```
### Go back to the pod creation and add nodeSelector to the pod
```
apiVersion: v1
kind: Pod
spec:
  containers:
  - name: data-processor
    image: data-processor
  nodeSelector:
    size: Large
```

Limitation: We used a single label and selector, but when the requirement becomes more complex, like we have **large or medium** for a pod, or **NOT SMALL**.\
--> **Node affinity** is the solution
