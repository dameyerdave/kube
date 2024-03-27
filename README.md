# Kebernetes

## Setup

The following section compresses all required steps to be prepared:

&rarr; found [here](https://hbayraktar.medium.com/how-to-install-kubernetes-cluster-on-ubuntu-22-04-step-by-step-guide-7dbf7e8f5f99).

```bash
sudo apt update
sudo apt upgrade
sudo swapoff -a
sudo sed -i '/swap/ s/^\(.*\)$/#\1/g' /etc/fstab
sudo tee /etc/modules-load.d/containerd.conf <<EOF
overlay
br_netfilter
EOF
sudo modprobe overlay
sudo modprobe br_netfilter
sudo tee /etc/sysctl.d/kubernetes.conf <<EOF
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
net.ipv4.ip_forward = 1
EOF
sudo sysctl --system
sudo apt install -y curl gnupg2 software-properties-common apt-transport-https ca-certificates
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmour -o /etc/apt/trusted.gpg.d/docker.gpg
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
sudo apt update
sudo apt install -y containerd.io
containerd config default | sudo tee /etc/containerd/config.toml >/dev/null 2>&1
sudo sed -i 's/SystemdCgroup \= false/SystemdCgroup \= true/g' /etc/containerd/config.toml
sudo systemctl restart containerd
sudo systemctl enable containerd
```

Disable IPv6.

```bash
sudo vi /etc/default/grub
...
GRUB_CMDLINE_LINUX_DEFAULT="ipv6.disable=1"
GRUB_CMDLINE_LINUX="ipv6.disable=1"
...
sudo update-grub
sudo reboot
```

Disable apparmor.

```bash
sudo systemctl stop apparmor
sudo systemctl disable apparmor
sudo systemctl restart containerd.service
```

Install the latest [kubernetes release](https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/).

```bash
KUBE_VERSION=$(curl -L -s https://dl.k8s.io/release/stable.txt | sed -e 's/\..$//')
sudo mkdir -p -m 755 /etc/apt/keyrings
curl -fsSL https://pkgs.k8s.io/core:/stable:/${KUBE_VERSION}/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
echo "deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/${KUBE_VERSION}/deb/ /" | sudo tee /etc/apt/sources.list.d/kubernetes.list
sudo apt-get update
sudo apt install -y kubelet kubeadm kubectl
sudo apt-mark hold kubelet kubeadm kubectl
```

Init the cluster (on the master node).

```bash
sudo kubeadm init
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
sudo chmod 600 $HOME/.kube/config
```

Get the join command (on the master node).

```bash
kubeadm token create --print-join-command
```

Join worker nodes (on the worker nodes).

```bash
sudo kubeadm join ... --token ... --discovery-token-ca-cert-hash sha256:...
```

Install network plugin (calico).

```bash
kubectl apply -f https://raw.githubusercontent.com/projectcalico/calico/v3.27.0/manifests/calico.yaml
```

Install helm.

&rarr; [https://helm.sh/docs/intro/install/](https://helm.sh/docs/intro/install/)

```bash
sudo snap install helm --classic
```

(optional) Use control plane node as worker.

```bash
kubectl taint nodes --all node-role.kubernetes.io/control-plane-
```