# my-kube

sudo gem install ultrahook

minikube ip
kubectl get svc -n prow (get the nodeport)
ultrahook.ruby2.7 github http://192.168.49.2:30849/hook

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

tide invalid repo PR loop 

https://github.com/kubernetes/test-infra/issues/16643