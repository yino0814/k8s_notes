## Taints Tolerations vs Node Affinity
### Taints Tolerations
Apply taints (color) to the `node` and then set a toleration on the pods to tolerate the respective colors.\
However, it doesn't **guarantee** the pods will only prefer the tainted node, (e.g. it might end up goomg to node with no taint at all if it is scheduled)

### Node Affinity
Set on the node with labels (colors) then set the node selectors on the pods to tie the pods to the nodes.\
However, it doesn't guarantee one of the other pods doesn't end up in the nodes.

### Combination of the both.
Use the taint/tolerations to ensure **no pods without the label** can be placed on the node, then use Node Affinity to ensure **only certain pods with the mathcing label** will be placed on the label.
