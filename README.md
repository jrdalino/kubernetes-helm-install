# myproject-kubernetes-helm-install
## Install Helm CLI
### Install Prerequisites
- Install command line tools required
```
$ cd ~/environment
$ curl https://raw.githubusercontent.com/kubernetes/helm/master/scripts/get > get_helm.sh
$ chmod +x get_helm.sh
$ ./get_helm.sh
```

### Configure Helm accss with RBAC
- Create a new service account
```
$ vi ~/environment/rbac.yaml
```
```
---
apiVersion: v1
kind: ServiceAccount
metadata:
  name: tiller
  namespace: kube-system
---
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: ClusterRoleBinding
metadata:
  name: tiller
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
  - kind: ServiceAccount
    name: tiller
    namespace: kube-system
```

- Apply the config
```
$ kubectl apply -f ~/environment/rbac.yaml
```

- Install tiller using helm tooling to give to acess to manage resources in your cluster
```
$ helm init --service-account tiller
```

- Activate helm bash-completion
```
$ helm completion bash >> ~/.bash_completion
$ . /etc/profile.d/bash_completion.sh
$ . ~/.bash_completion
```

## Using Helm
- Add Bitnami Repository to local list of searchable charts
```
$ helm repo add bitnami https://charts.bitnami.com/bitnami
```

- Update the Chart Repository
```
$ helm repo update
```

- Search the chart repository
```
$ helm search bitnami/nginx
NAME                                    CHART VERSION   APP VERSION     DESCRIPTION                           
bitnami/nginx                           1.1.2           1.14.1          Chart for the nginx server            
bitnami/nginx-ingress-controller        2.1.4           0.20.0          Chart for the nginx Ingress...
```

- Install Helm Chart
```
$ helm install --name mywebserver bitnami/nginx
```

- Validate
```
$ kubectl describe deployment mywebserver
$ kubectl get pods -l app.kubernetes.io/name=nginx
$ kubectl get service mywebserver-nginx -o wide
```

- Cleanup and Check
```
$ helm list
$ helm delete --purge mywebserver
$ kubectl get pods -l app.kubernetes.io/name=nginx
$ kubectl get service mywebserver-nginx -o wide
```
