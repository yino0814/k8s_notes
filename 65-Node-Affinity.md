## Node Affinity
The primary purpose is to ensure the pods are hosted on particular nodes, like ensure `Large processing pod` into a particular `node-1`\
```
apiVersion: v1
kind: Pod
spec:
  containers:
  - name: data-processor
    image: data-processor
  affinity:
    nodeAffinity:                                                      # This works the exact same as the nodeSelector 
      requiredDuringSchedulingIgnoredDuringExecution:                  # Just more complex
        nodeSelectorTerms:
        - matchExpressions:
          - key: size                                                  # Key-value pair
            operator: In                                               # operator can be NotIn, or Exists (which only checks if the label exists with no value set)
            values:
            - Large
            - Medium
```
### Node affinity types
If the node affinity doesn't match anything? -> The node affinity types will tell the story: \
Avaliable:\
  requiredDuringSchedulingIgnoredDuringExecution **(Type 1)** \
  preferredDuringSchedulingIgnoredDuringExecution **(Type 2)**\
Planned:\
  requiredDuringSchedulingRequiredDuringExecution **(Type 3)**\
#### Avaliable
| | DuringScheduling | DuringExecution|
|-|-|-|
|Type 1|Required|Ignored|
|Type 2|Prefered|Ignored|
|Type3|Required|Required|
Type 1:\
If required and cannot find the affinity, it will not be scheduled. Will be used where the placement of the pod is crucial.\
Type 2:\
If cannot find the matching node, the scheduler will simply ignore node affinity rules and place the pod to any avaliable node.\
DuringExecuting s the state where a pod has been running and a change is made in the environment that affects node affinity\
e.g. A change in the label of a node. Like the admin removes a label `size=Large` from the node, the pods will continue to run on the nodes and any changes to the node affinity will not impact them once they're scheduled.\
Type 3:\

