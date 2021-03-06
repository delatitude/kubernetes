# Cookbook-Install-Kubernetes-on-Ubuntu-18.04-kubeadm
Installation of Kubernetes and adding new cluster

#### Install on All Nodes ####

#### Docker ####
```sudo apt update
sudo apt install apt-transport-https ca-certificates curl software-properties-common
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo add-apt-repository “deb [arch=amd64] https://download.docker.com/linux/ubuntu bionic stable”
sudo apt update
apt-cache policy docker-ce
sudo apt install docker-ce
sudo systemctl status docker
sudo usermod -aG docker ${USER}
su — ${USER}
id -nG
```
#### Kubeadm ####
```curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add  
sudo apt-add-repository “deb http://apt.kubernetes.io/ kubernetes-xenial main”  
sudo apt-get update  
sudo apt-get install -y kubelet kubeadm kubectl  
sudo apt-mark hold kubelet kubeadm kubectl  
sudo swapoff -a  
```
#### Only on Master Node ####

#Activate Cidr  
`sudo kubeadm init — pod-network-cidr=10.168.0.0/16 — apiserver-advertise-address=(master node IP ADDRESS)`
  
#Install Flannel  
```sudo kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml

mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```
#Waiting till running all  
`kubectl get pods — all-namespaces`  

#Master Isolation  
`kubectl taint nodes — all node-role.kubernetes.io/master-`  

#Install Dashborad  
```kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/master/src/deploy/recommended/kubernetes-dashboard.yaml  
kubectl get pods — all-namespaces  
kubectl create clusterrolebinding kubernetes-dashboard — clusterrole=cluster-admin — serviceaccount=kube-system:kubernetes-dashboard  

kubectl create serviceaccount dashboard -n default  
kubectl create clusterrolebinding dashboard-admin -n default — clusterrole=cluster-admin — serviceaccount=default:dashboard  
kubectl get secret $(kubectl get serviceaccount dashboard -o jsonpath=”{.secrets[0].name}”) -o jsonpath=”{.data.token}” | base64 — decode  
```

#### Join worker node (Only on Worker Nodes) ####
`kubeadm join (Master node IP):6443 — token ua7joh.huzyhi8m0si1la7v — discovery-token-ca-cert-hash sha256:xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx  
`  

#### Tear down ####
```kubectl drain (Node Name) — delete-local-data — forcee — ignore-daemonsets  
kubectl delete node (Node Name)  

sudo kubeadm reset  
```  

[Original Source on Medium Nattawin](https://medium.com/@iambeboy/install-kubernetes-on-ubuntu-18-04-with-kubeadm-bf0f6b7ff3d4)
