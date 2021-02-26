# my-kubee

sudo gem install ultrahook

ultrahook.ruby2.7 github http://192.168.43.65:8888/hook

kubectl delete namespace prow test-pods

kubectl create clusterrolebinding cluster-admin-binding-"${USER}" \
  --clusterrole=cluster-admin --user="${USER}"

kubectl create namespace prow

kubectl create secret -n prow generic github-token --from-file=token=/home/nmam/my-kube/git.pat.token

kubectl create secret -n prow generic hmac-token --from-file=hmac=/home/nmam/my-kube/hmac.token

kubectl apply -f s3-secret.yaml

kubectl apply -f starter-s3.yaml --validate=false

