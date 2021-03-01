# my-kube

sudo gem install ultrahook

ultrahook.ruby2.7 github http://192.168.49.1:8888/hook

kubectl delete namespace prow test-pods

kubectl create clusterrolebinding cluster-admin-binding-"${USER}" \
  --clusterrole=cluster-admin --user="${USER}"

kubectl apply -f starter-s3.yaml --validate=false

kubectl create configmap -n prow config \
--from-file=config.yaml=/home/nmam/my-kube/config.yaml --dry-run=server -o yaml | kubectl replace configmap -n prow config -f -





-----
minikube addons enable ingress