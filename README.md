# my-kubee

kubectl delete namespace prow

kubectl create clusterrolebinding cluster-admin-binding-"${USER}" \
  --clusterrole=cluster-admin --user="${USER}"

kubectl create namespace prow

kubectl create secret -n prow generic github-token --from-file=token=/home/nmam/my-kube/git.pat.token

kubectl create secret -n prow generic hmac-token --from-file=hmac=/home/nmam/my-kube/hmac.token

kubectl apply -f s3-secret.yaml

kubectl create configmap -n prow config --from-file config.yaml

kubectl create configmap -n prow plugins --from-file plugins.yaml

kubectl apply -f starter-s3.yaml
