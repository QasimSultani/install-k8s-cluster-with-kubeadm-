<!DOCTYPE html>
<html>
<head>
</head>
<body>
<h1>How to set up a Kubernetes cluster with kubeadm</h1>
<p>Kubernetes is a popular container orchestration platform that automates the deployment, scaling, and management of containerized applications. In this tutorial, we will go through the steps to set up a Kubernetes cluster on Ubuntu using kubeadm.</p>
<h2>Step 1: Install Kubernetes</h2>
<p>The first step is to install Kubernetes on each server. Here are the commands to do that:</p>
<pre>
- apt-get update
- sudo apt-get install -y apt-transport-https ca-certificates curl
- mkdir /etc/apt/keyrings
- swapoff -a
- nano /etc/fstab  (comment the swap line)
- nano /etc/hosts  (put ip and hostname)
- sudo curl -fsSLo /etc/apt/keyrings/kubernetes-archive-keyring.gpg https://packages.cloud.google.com/apt/doc/apt-key.gpg
- echo "deb [signed-by=/etc/apt/keyrings/kubernetes-archive-keyring.gpg] https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list
- sudo apt-get update
- sudo apt-get install -y kubelet kubeadm kubectl docker.io
</pre>

<h2>Step 2: Set up the container runtime</h2>
<p>Next, we need to set up the container runtime on each server. Here are the commands to do that:</p>
<pre>
- cat <<EOF | sudo tee /etc/modules-load.d/k8s.conf
overlay
br_netfilter
EOF 
- sudo modprobe overlay
- sudo modprobe br_netfilter
# sysctl params required by setup, params persist across reboots

- cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-iptables  = 1
net.bridge.bridge-nf-call-ip6tables = 1
net.ipv4.ip_forward                 = 1
EOF

# Apply sysctl params without reboot
- sysctl supports a --system
- sudo mkdir -p /etc/containerd/containerd config default > /etc/containerd/config.toml
- nano +125 /etc/containerd/config.toml              // do this true
- SystemdCgroup = true
- sudo systemctl restart containerd.service
- sudo systemctl restart kubelet.service

//Till now run these above command in both machine worker and master
</pre>

<h2>Step 3: Bootstrapping the master node</h2>
<p>The next step is to bootstrap the master node. Here are the commands to do that:</p>
<pre>
- sudo kubeadm config images pull
- sudo kubeadm init --pod-network-cidr=192.168.0.0/16 --ignore-preflight-errors=all

Copy the command to run on the worker nodes and save it in a notepad file. Then, run the following commands on the master node:

- sudo mkdir -p $HOME/.kube
- sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
- sudo chown $(id -u):$(id -g) $HOME/.kube/config
- kubeadm join 182.118.0.80:6443 --token 550ejc.yb4ny30rwnuki \
        --discovery-token-ca-cert-hash sha256:b3ce747d3274c76911caf9c3e3d60873d9e02199ef72eb7dc50ac0ac      //this is the sample token to join worker with master 
</pre>

<h2>Step 4: Install network pods with Calico</h2>
<p>The final step is to install network pods with Calico
  <pre>
- kubectl create -f https://raw.githubusercontent.com/projectcalico/calico/v3.25.1/manifests/tigera-operator.yaml
- curl https://raw.githubusercontent.com/projectcalico/calico/v3.25.1/manifests/custom-resources.yaml -O
- kubectl apply -f custom-resources.yaml
  </pre>
  
<h3>  //CONFIGURE WORKER NODES (IN MASTER) </h3>
  <pre>   
  #Paste this command in the worker 
- kubeadm join 182.118.0.80:6443 --token 550ejc.yb4ny30rwnuki \
        --discovery-token-ca-cert-hash sha256:b3ce747d3274c76911caf9c3e3d60873d9e02199ef72eb7dc50ac0ac      
  #GO TO MASTER AND RUN THIS COMMAND
- kubectl get nodes

<h4>#All nodes come in ready status once all the pods are running sucessfully so wait till all the pods are came in running status </h4>

![image](https://github.com/QasimSultani/install-k8s-cluster-with-kubeadm-/assets/64696469/33e2244b-f4dc-46ce-b3a6-a22a59b1d8ce)

  </pre>

<h4>Here is link of my profile if you face any problem DM here!!</h4>
https://www.linkedin.com/in/muhammad-qasim-sultani/
