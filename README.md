# my-kube

sudo gem install ultrahook

kubectl get ingress -n prow
ultrahook.ruby2.7 github http://<ip addr from above>/hook

kubectl delete namespace prow test-pods


minikube addons enable ingress

kubectl create clusterrolebinding cluster-admin-binding-"${USER}" \
  --clusterrole=cluster-admin --user="${USER}"

kubectl apply -f starter-s3.yaml --validate=false

kubectl create configmap -n prow config \
--from-file=config.yaml=/home/nmam/my-kube/config.yaml --dry-run=server -o yaml | kubectl replace configmap -n prow config -f -



-----
minikube addons enable ingress

tide invalid repo PR loop 

https://github.com/kubernetes/test-infra/issues/16643