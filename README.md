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

### enable the following addons

minikube addons enable metrics-server

minikube addons enable dashboard

minikube addons enable volumesnapshots

minikube addons enable csi-hostpath-driver

minikube addons enable ingress

### configure github repo webhook

generate github webhook secret:

openssl rand -hex 20

Create a github bot user account and add a PAT with full access. Note down the generated PAT

### deploy prow yaml

kubectl create clusterrolebinding cluster-admin-binding-"${USER}" --clusterrole=cluster-admin --user="${USER}"

kubectl apply -f starter-s3.yaml

### strategic merge patch

F:\my-kube>type hmac-token.yaml
stringData:
  hmac: abcd

F:\my-kube>type c:\github-token.yaml
stringData:
  token: abcd

minikube kubectl -- patch secret hmac-token -n prow --patch $(Get-Content C:\hmac-token.yaml -Raw)

minikube kubectl -- patch secret github-token -n prow --patch $(Get-Content C:\github-token.yaml -Raw)

at this point verify that all prow pods are up and running

### ultrahook to forward github events to local minikube instance

sign up for ultrakook. You'll recieve a api key in your email
Place the contents of the api key in ".ultrahook" file in the current user's home dir
install ruby
sudo gem install ultrahook

minikube ip

add minikube ip to etc hosts like :

"172.17.185.40 prow.nmam.com"

kubectl get svc -n prow (get the nodeport)

ultrahook github http://192.168.21.101:31931/hook

ultrahook github http://prow.nmam.com/hook

### To access the dashboard
 
kubectl proxy

http://127.0.0.1:8001/api/v1/namespaces/kubernetes-dashboard/services/http:kubernetes-dashboard:/proxy/#/overview?namespace=default

### To access prow deck (and hook)

http://prow.nmam.com/  -- should show the prow PR dashboard

http://prow.nmam.com/hook   -- should give method not found

### online yaml linter
https://jsonformatter.org/yaml-validator


## setup 2 node kubernetes cluster on Hyper-V

create a debian VM with 2 GB RAM.
initially assign the network adapter of the VM to the default/external network so that we can download all binaries and containers locally. boot up the VM.

disable swap: 
  sudo swapoff -a 
  comment out all swap entries in the /etc/fstab file. 

open ports on your Master Node:

sudo ufw allow 6443/tcp
sudo ufw allow 2379/tcp
sudo ufw allow 2380/tcp
sudo ufw allow 10250/tcp
sudo ufw allow 10251/tcp
sudo ufw allow 10252/tcp
sudo ufw allow 10255/tcp
sudo ufw reload

Additionally, these ports need to be open on each Worker Node:

sudo ufw allow 10251/tcp
sudo ufw allow 10255/tcp
sudo ufw reload

Install DockerCE

Download/cache all kubernetes container images required for an offline install
  kubeadm config images pull
Download/cache the flannel csi container image:
  docker pull quay.io/coreos/flannel:v0.14.0-rc1

List all downloaded images:
  docker images

Download Kubernetes binaries:

Add a signing key to ensure that the software is authentic:
  curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add –
Add kubernetes apt repository
  sudo apt-add-repository "deb http://apt.kubernetes.io/ kubernetes-xenial main"

sudo apt-get update
sudo apt-get install -y kubelet kubeadm kubectl
sudo apt-mark hold kubelet kubeadm kubectl

shutdown the VM
create a hyper-v internal switch
configure the internal network's switch with 10.0.0.1/24 ip range
Start the vm and configure it with a static IP in the above 10.0.0.1/24 range
------
#iface eth0 inet dhcp
iface eth0 inet static
  address 10.0.0.2
  netmask 255.255.255.0
  gateway 10.0.0.1
------
Make sure the pind works both ways i.e. host-tp-vm and vm-to-host

Reboot

Initialize the master node uing the below POD CIDR. 
"The --pod-network-cidr=10.244.0.0/16 option is a requirement for Flannel - don't change that network address!"
  
  sudo kubeadm init --pod-network-cidr=10.244.0.0/16

To start using your cluster, you need to run the following as a regular user:

  mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config

apply flannel CSI networking
  
  kubectl apply -f flannel.yaml

All the pods should be up and running in around a minute or so:

nmam@debian:~$ k get pods --all-namespaces

NAMESPACE     NAME                             READY   STATUS    RESTARTS   AGE
kube-system   coredns-558bd4d5db-9kxtb         1/1     Running   0          122m
kube-system   coredns-558bd4d5db-wcbsv         1/1     Running   0          122m
kube-system   etcd-debian                      1/1     Running   7          122m
kube-system   kube-apiserver-debian            1/1     Running   6          122m
kube-system   kube-controller-manager-debian   1/1     Running   3          122m
kube-system   kube-flannel-ds-54v2j            1/1     Running   0          21m
kube-system   kube-proxy-6fvvv                 1/1     Running   2          122m
kube-system   kube-scheduler-debian            1/1     Running   3          122m

Control plane node isolation 
By default, your cluster will not schedule Pods on the control-plane node for security reasons. If you want to be able to schedule Pods on the control-plane node, for example for a single-machine Kubernetes cluster for development, run:

  kubectl taint nodes --all node-role.kubernetes.io/master-

Add Worker Nodes to the Cluster

  sudo kubeadm join 10.0.0.2:6443 --token hfnk4b.2yppq7qdtrmj1ah2 \
        --discovery-token-ca-cert-hash sha256:c6bb2c3b78edcca6130df69fc4a62eace83ecbc3f6071ed033405b593ef2d2af

If you do not have the token, you can get it by running the following command on the control-plane node:

  kubeadm token list

By default, tokens expire after 24 hours. If you are joining a node to the cluster after the current token has expired, you can create a new token by running the following command on the control-plane node:

  kubeadm token create

If you don't have the value of --discovery-token-ca-cert-hash, you can get it by running the following command chain on the control-plane node:

openssl x509 -pubkey -in /etc/kubernetes/pki/ca.crt | openssl rsa -pubin -outform der 2>/dev/null | \
   openssl dgst -sha256 -hex | sed 's/^.* //'

(Optional) Controlling your cluster from machines other than the control-plane node
In order to get a kubectl on some other computer (e.g. laptop) to talk to your cluster, you need to copy the administrator kubeconfig file from your control-plane node to your workstation like this:

  scp root@<control-plane-host>:/etc/kubernetes/admin.conf .
  kubectl --kubeconfig ./admin.conf get nodes

for cleanup refer:

https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/create-cluster-kubeadm/
