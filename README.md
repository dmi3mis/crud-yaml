# yaml manifests to deploy simple Crud-PHP-MySQL Application with Tekton and ArgoCD


Application source codes are [Here](https://github.com/dmi3mis/crud-php-mysql)

Here is quick and very dirty instruction

```console
git clone https://giuthub.com/dmi3mis/crud-php-mysql
cd crud-php-mysql

docker login -u <username>
<password>

cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Secret
metadata:
  name: docker-credentials
data:
  config.json: $(cat ~/.docker/config.json | base64 -w0)
EOF
```

Create ssh key and upload it to github

```console
ssh-keygen 
cat ~/.ssh/id_rsa

firefox https://github.com/<accountname> -> Settings -> SSH and GPG keys -> New SSH Key

cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Secret
metadata:
  name: git-ssh-key
  annotations:
    tekton.dev/git-0: github.com
type: kubernetes.io/ssh-auth
data:
  ssh-privatekey: $(cat ~/.ssh/id_rsa | base64 -w0)
EOF
```

Install Tekton 
Install ArgoCD

```
tkn hub install task git-clone
tkn hub install task kaniko
kubectl apply -f crud-php-mysql/Kubernetes/tekton/tasks/list-source.yaml 
kubectl apply -f crud-php-mysql/Kubernetes/tekton/tasks/update-manifests.yaml 
kubectl apply -f crud-php-mysql/Kubernetes/tekton/serviceaccount/service-account.yaml
kubectl apply -f crud-php-mysql/Kubernetes/tekton/pipeline/pipeline.yaml
```



## to test locally

```console
kubectl create -f ~/crud-php-mysql/Kubernetes/tekton/pipelinerun/pipelinerun.yaml
tkn pipelinerun ls
NAME                         STARTED          DURATION   STATUS
clone-build-push-run-tff6b   2 seconds ago    ---        Running

tkn pipelinerun logs -f clone-build-push-run-tff6b
```

kubectl apply -f crud-php-mysql/Kubernetes/tekton/tasks/
