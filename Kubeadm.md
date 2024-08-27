# On both
Sudo apt install docker.io -y
sudo chmod 777 /var/run/docker.sock
sudo apt-get update
sudo apt-get install -y apt-transport-https ca-certificates curl gpg
curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.28/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.28/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list
sudo apt-get update
sudo apt-get install -y kubelet kubeadm kubectl

# On Master
sudo kubeadm init --pod-network-cidr=192.168.0.0/16

# To start using your cluster, you need to run the following as a regular user:
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config

# For pod network
kubectl create -f https://raw.githubusercontent.com/projectcalico/calico/v3.28.1/manifests/tigera-operator.yaml

# NGINX ingress-Controller
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v0.49.0/deploy/static/provider/baremetal/deploy.yaml

# Then you can join any number of worker nodes by running the following on each as root:

kubeadm join 172.31.14.83:6443 --token vmnbvv.t1zr96p0y7u5m48i \
        --discovery-token-ca-cert-hash sha256:fde557f130730c4b9209da9117b4133436dc8c4aaa2a84e799b7569e03d63301

