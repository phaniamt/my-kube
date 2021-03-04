# my-kube

minikube cache add gcr.io/k8s-prow/hook:latest

minikube cache add gcr.io/k8s-prow/sinker:latest

minikube cache add gcr.io/k8s-prow/deck:latest

minikube cache add gcr.io/k8s-prow/horologium:latest

minikube cache add gcr.io/k8s-prow/tide:latest

minikube cache add gcr.io/k8s-prow/status-reconciler:latest

minikube cache add gcr.io/k8s-prow/ghproxy:latest

minikube cache add gcr.io/k8s-prow/prow-controller-manager:latest

minikube cache add gcr.io/k8s-prow/crier:latest

sudo gem install ultrahook

minikube ip
kubectl get svc -n prow (get the nodeport)
ultrahook github http://192.168.21.101:31931/hook

kubectl delete namespace prow test-pods


minikube addons enable ingress

kubectl create clusterrolebinding cluster-admin-binding-"${USER}" --clusterrole=cluster-admin --user="${USER}"

kubectl apply -f starter-s3.yaml --validate=false

kubectl create configmap -n prow config \
--from-file=config.yaml=/home/nmam/my-kube/config.yaml --dry-run=server -o yaml | kubectl replace configmap -n prow config -f -


kubectl proxy

http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/http:kubernetes-dashboard:/proxy/#/overview?namespace=default

-----
minikube addons enable dashboard

------
strategic merge patch

F:\my-kube>type patch.yaml
stringData:
  token: 4606953fcabfa2024fa33b8a29ce1386a4057d60

PS C:\Windows\system32> minikube kubectl -- patch secret github-token -n prow --patch $(Get-Content F:\my-kube\patch.yaml -Raw)
secret/github-token patched