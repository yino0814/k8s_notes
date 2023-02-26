## Secret
```
# to view the secrets
k get secret my-secret -o yaml
# decode the secret
echo "c3VwZXJzZWNyZXQ=" | base64 --decode
> supersecret 
```
Focus on how the data is stored in the ectd server\
[verifying the data is encrypted](https://kubernetes.io/docs/tasks/administer-cluster/encrypt-data/#verifying-that-data-is-encrypted:~:text=Verifying%20that%20data%20is%20encrypted)
```
apt-get install etcd-clinet
ETCDCTL_API=3 etcdctl \
   --cacert=/etc/kubernetes/pki/etcd/ca.crt   \
   --cert=/etc/kubernetes/pki/etcd/server.crt \
   --key=/etc/kubernetes/pki/etcd/server.key  \
   get /registry/secrets/default/my-secret | hexdump -C
```
