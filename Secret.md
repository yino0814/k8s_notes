## Secret
### create secret
`kubectl create secret generic my-secret --from-literal=key1=supersecret`

### to view the secrets and decode the secrets
```
k get secret my-secret -o yaml

echo "c3VwZXJzZWNyZXQ=" | base64 --decode
> supersecret 
```
### Focus on how the data is stored in the ectd server\
[verifying the data is encrypted](https://kubernetes.io/docs/tasks/administer-cluster/encrypt-data/#verifying-that-data-is-encrypted:~:text=Verifying%20that%20data%20is%20encrypted)
```
apt-get install etcd-clinet

ETCDCTL_API=3 etcdctl \
   --cacert=/etc/kubernetes/pki/etcd/ca.crt   \
   --cert=/etc/kubernetes/pki/etcd/server.crt \
   --key=/etc/kubernetes/pki/etcd/server.key  \
   get /registry/secrets/default/my-secret | hexdump -C

# to check whether it has the encryption option (two ways) nothing yet
ps -aux | grep kube-api | grep "encryption-provider-config"
vi /etc/kubernetes/manifests/kube-apiserver.yaml
```
Secret config file
```
apiVersion: apiserver.config.k8s.io/v1
kind: EncryptionConfiguration
resources:
  - resources:
      - secrets
      - configmaps
      - pandas.awesome.bears.example
    providers:
      - identity: {}                             # the order of the list matter, identity first means what will be used for encrypted.
      - aesgcm:                                  # different encryption algorithm
          keys:
            - name: key1
              secret: c2VjcmV0IGlzIHNlY3VyZQ==
            - name: key2
              secret: dGhpcyBpcyBwYXNzd29yZA==
      - aescbc:                                            
          keys:
            - name: key1
              secret: c2VjcmV0IGlzIHNlY3VyZQ==
            - name: key2
              secret: dGhpcyBpcyBwYXNzd29yZA==
      - secretbox:
          keys:
            - name: key1
              secret: YWJjZGVmZ2hpamtsbW5vcHFyc3R1dnd4eXoxMjM0NTY=
```
## create a new encryption config file
### get the base 64 encoded secret
```
controlplane $ head -c 32 /dev/urandom | base64
> 41D8gZ+8TBRJRVfx9j3gccAT3s3dyF9sEKDWuUJ0GTk=
controlplane $ vi enc.yaml
```
### add the secret to the config file
```apiVersion: apiserver.config.k8s.io/v1
kind: EncryptionConfiguration
resources:
  - resources:
      - secrets
      - configmaps
      - pandas.awesome.bears.example
    providers:
      - aescbc:
          keys:
            - name: key1
              secret: <41D8gZ+8TBRJRVfx9j3gccAT3s3dyF9sEKDWuUJ0GTk=>
      - identity: {}
```
### move the created config file to the enc folder
```
mkdir /etc/kubernetes/enc
mv enc.yaml /etc/kubernetes/enc
```
### Edit the kube-api manifest file\
add `--encryption-provider-config=/etc/kubernetes/enc/enc.yaml` to the `/etc/kubernetes/manifests/kube-apiserver.yaml` `spec:containers:-command`\
In the local host, the ETCD kubernetes ENC directory is going to be mapped to the `/etc/kubernetes/enc` dir inside the pod\
since the file is stored locally, the encryption-provider-config command should work.\
And then **wait** until it is applied
```
spec:
  containers:
  - command:
    - kube-apiserver
    ...
    - --encryption-provider-config=/etc/kubernetes/enc/enc.yaml  # <-- add this line
    volumeMounts:
    ...
    - name: enc                                                  # <-- add this line
      mountPath: /etc/kubernetes/enc                             # <-- add this line
      readonly: true                                             # <-- add this line
    ...
  volumes:
  ...
  - name: enc                                                    # <-- add this line
    hostPath:                                                    # <-- add this line
      path: /etc/kubernetes/enc                                  # <-- add this line
      type: DirectoryOrCreate                                    # <-- add this line
  ...
  ```  
See the status of the api server, you can use\ 
`crictl pods`\
After the encryption is enabled, only the **new** secrets created after will be encrypted, not the old ones.\
But if you update the config, all secrets will be encrypted
`kubectl get secrets --all-namespaces -o json | kubectl replace -f -`
