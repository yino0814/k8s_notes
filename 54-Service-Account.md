## Service Account:
There are two types of accounts in Kubernetes, **Service Account** and **User Account**\
User Account | Service Account
-------------|---------------
Used by human | Used by machine for differnet services to use Kubernetes
Admin or Developer | Prometheus or Jenkins

### Create a service account
```
kubectl create serviceaccount dashboard-sa
kubectl describe serviceaccount dashboard-sa
```
It will also create a token `dashboard-sa-token-kddbm` for external applications to authenticate kubernetes api.\
When the service account created, it first create a service account object, then generate the token for the service account, and then crete a secret to store that token inside the secret object.\
The secret object is then linked to the service account.\
To view the secret, use `kubectl describe secret dashboard-sa-token-kddbm`\
\
If the application is deployed in the kubernetes cluster itself, we can mount the service token secret as a vloume inside the pod hosting the 3rd party application.\
For every namespaces in k8s, a `default` service account is created whenever a pod is created.\
```
kubectl exec -it my-k8s-dashboard ls /var/run/secrets/kubernetes.io/serviceaccount
> ca.crt   namespace   token
```
The file `token` stores the tijeb ysed fir accessing the Kubernetes API.\
The default service account is restricted, so we need to create and modify the pod definition file to create new sa. **REMEMBER TO DELETE AND RECREATE** the pod. For deployments, it'll automatically deploy it.

### 1.22/1.24 update
Since 1.22, when a new pod is created, it no longer relies on the service account secret token, instead, a token with a defined lifetime is generated through the token request API by the service account admission controller when the pod is created.\
This token is then mount as a projected volume into the pod.
#### decode the tocken
```
jq -R 'split(".") | select(length > 0) | .[0],.[1] | @base64d | fromjson' <<< eyJhbGciOiJS.....```

Since 1.24, reduction of secret-based service account tokens.\
So when you create a service account, it no longer creates a token, so must use `kubectl create token dashboard-sa` to manually create the token.
 
