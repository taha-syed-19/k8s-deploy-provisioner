Kubernetes Deploy Provisioner 🚀

Automated Kubernetes Cluster Setup from Scratch with SSH Key-Based Authentication

Overview

This repository provides a step-by-step, automated approach to setting up a Kubernetes cluster from scratch. It includes scripts to install Kubernetes components, set up SSH key-based authentication, initialize the master node, configure networking, and join worker nodes to the cluster.

Features

✅ Automated Deployment – Install Kubernetes with a single script
✅ Secure SSH Authentication – Passwordless access between nodes
✅ Supports Multiple Networking Plugins – Calico & Weave Net
✅ Scalable Setup – Works for small to large Kubernetes clusters
✅ Easy-to-Follow Documentation – Step-by-step setup guide

Table of Contents

Prerequisites
Setup SSH Key-Based Authentication
Install Kubernetes Components
Initialize Kubernetes Master
Install Network Plugin
Join Worker Nodes
Verify Cluster Status
Usage
Contributing
License
1. Prerequisites

Before starting, ensure you have:

3 Ubuntu 20.04/22.04 servers:
1 Master Node (172.31.38.167)
2 Worker Nodes (172.31.44.188, 172.31.43.100)
User with sudo privileges
Internet access for downloading dependencies
2. Setup SSH Key-Based Authentication

To enable passwordless SSH access between nodes:

Generate SSH Key on the Master Node

ssh-keygen -t rsa -b 4096 -f ~/.ssh/id_rsa -N ""

Copy the SSH Key to Worker Nodes

ssh-copy-id ubuntu@172.31.44.188
ssh-copy-id ubuntu@172.31.43.100
Verify SSH Access

ssh ubuntu@172.31.44.188 "echo SSH to worker1 is working!"
ssh ubuntu@172.31.43.100 "echo SSH to worker2 is working!"
3. Install Kubernetes Components

Execute the install-k8s.sh script on all nodes (Master & Workers):

chmod +x install-k8s.sh
./install-k8s.sh
This script:

Disables swap
Installs dependencies
Configures container runtime (containerd)
Installs Kubernetes components (kubeadm, kubelet, kubectl)
4. Initialize Kubernetes Master

Run this command only on the master node:

sudo kubeadm init --pod-network-cidr=192.168.0.0/16
Configure kubectl for the current user:

mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
Get the worker node join command:

kubeadm token create --print-join-command
5. Install Network Plugin

Choose one of the following:

Option 1: Install Calico (Recommended)
kubectl apply -f https://raw.githubusercontent.com/projectcalico/calico/v3.26.1/manifests/calico.yaml
Option 2: Install Weave Net
kubectl apply -f https://github.com/weaveworks/weave/releases/latest/download/weave-daemonset-k8s.yaml
6. Join Worker Nodes

On the master node, run:

for NODE in 172.31.44.188 172.31.43.100; do
  ssh ubuntu@$NODE "sudo kubeadm join 172.31.38.167:6443 --token <TOKEN> --discovery-token-ca-cert-hash sha256:<HASH>"
done
Replace <TOKEN> and <HASH> with values from:

kubeadm token create --print-join-command
7. Verify Cluster Status

Check if all nodes joined successfully:

kubectl get nodes
Expected output:

NAME       STATUS   ROLES           AGE     VERSION
master     Ready    control-plane   10m     v1.29.0
worker1    Ready    <none>          2m      v1.29.0
worker2    Ready    <none>          2m      v1.29.0
8. Usage

Clone & Deploy from GitHub
To deploy on a new machine:

git clone https://github.com/YOUR_GITHUB_USERNAME/k8s-deploy-provisioner.git
cd k8s-deploy-provisioner
chmod +x install-k8s.sh
./install-k8s.sh
9. Contributing

We welcome contributions! If you want to:

Report an issue
Improve scripts
Add new features
Create a Pull Request (PR) or open an Issue in the repository.

10. License

This project is licensed under the MIT License.

🚀 Now You Have a Fully Functional Kubernetes Cluster!

You can now deploy applications and manage workloads on your new Kubernetes cluster. 🎯
