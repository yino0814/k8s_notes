## Taints Tolerations vs Node Affinity
### TT
Apply taints (color) to the `node` and then set a toleration on the pods to tolerate the respective colors.\
However, it doesn't **guarantee** the pods will only prefer the tainted node, (e.g. it might end up goomg to node with no taint at all if it is scheduled)

### Node Affinity
Set on the node with labels (colors) then set the node selectors on the pods to tie the pods to the nodes.
