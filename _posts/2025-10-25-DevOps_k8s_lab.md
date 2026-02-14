---
layout: post
title: K8s - HomeLab -  Home-Lap-Setup
date: 25-10-2025
categories: [DevOps, Kubernetes]
tags: [docker, devops]
published: false
---


## Lab summary

This extended lab we're exploring the following: 

1- shows the details of my home lab setup interacting with production-grade workflow lab.
2- hands on practice core concenpts of containerization. 
3- Hands-on CI/CD concepts of React lab. 


The ```workflow``` is basically the deveopment - testing - deveopment cycle.

> #### Lab objective
> building production-grade workflow using docker concepts [docker volumes, docker devfile, volumes bookmark,] 

## Lab summary setup

# 🧩 Kubernetes Cluster Setup Using Kubeadm (Ubuntu 22.04 on VMware)

This guide documents a full **multi-node Kubernetes setup** using **kubeadm**.  
The cluster consists of **1 Master Node** and **2 Worker Nodes**, all running **Ubuntu 22.04 LTS**.

---

## ⚙️ Cluster Topology

| Role     | Hostname     | IP Address      | OS           | Notes                                                           |
| -------- | ------------ | --------------- | ------------ | --------------------------------------------------------------- |
| Master   | `k8s-master` | `192.168.1.170` | Ubuntu 22.04 | Control Plane (API Server, Scheduler, Controller Manager, etcd) |
| Worker A | `k8s-node-1` | `192.168.1.171` | Ubuntu 22.04 | Worker Node                                                     |
| Worker B | `k8s-node-2` | `192.168.1.172` | Ubuntu 22.04 | Worker Node                                                     |

---

## 🧱 Step 1: Basic System Preparation (Run on **All Nodes**)

```bash
sudo apt update -y
sudo apt install -y apt-transport-https ca-certificates curl gnupg lsb-release

# Disable swap (Kubernetes requires it)
sudo swapoff -a
sudo sed -i '/ swap / s/^/#/' /etc/fstab

# Load required kernel modules
cat <<EOF | sudo tee /etc/modules-load.d/k8s.conf
overlay
br_netfilter
EOF

sudo modprobe overlay
sudo modprobe br_netfilter

# Set required sysctl params
cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-iptables  = 1
net.bridge.bridge-nf-call-ip6tables = 1
net.ipv4.ip_forward                 = 1
EOF

sudo sysctl --system
```

---

## 🧩 Step 2: Install Container Runtime (Run on **All Nodes**)

Here we use **containerd** as the runtime.

```bash
sudo apt install -y containerd

# Generate default config
sudo mkdir -p /etc/containerd
containerd config default | sudo tee /etc/containerd/config.toml

# Set SystemdCgroup to true
sudo sed -i 's/SystemdCgroup = false/SystemdCgroup = true/' /etc/containerd/config.toml

# Restart containerd
sudo systemctl restart containerd
sudo systemctl enable containerd
```

---

## 🔑 Step 3: Install Kubernetes Components (Run on **All Nodes**)

```bash
# Add Kubernetes apt repo
sudo mkdir -p /etc/apt/keyrings
curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.31/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.31/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list

sudo apt update -y
sudo apt install -y kubelet kubeadm kubectl
sudo apt-mark hold kubelet kubeadm kubectl

sudo systemctl enable kubelet
```

---

## 🏗️ Step 4: Initialize the Control Plane (Run **Only on Master Node**)

```bash
sudo kubeadm init   --apiserver-advertise-address=192.168.1.170   --pod-network-cidr=10.244.0.0/16
```

Once completed, you’ll see an output that includes a **join command** like:
```bash
kubeadm join 192.168.1.170:6443 --token <TOKEN>     --discovery-token-ca-cert-hash sha256:<HASH>
```
> 💡 **Save this join command** — it will be used on worker nodes.

---

## ⚙️ Step 5: Configure kubectl Access (Run **Only on Master Node**)

```bash
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

Test:
```bash
kubectl get nodes
```
Expected output: only the master node (`NotReady` until network plugin is applied).

---

## 🌐 Step 6: Install Pod Network Add-on (Run **Only on Master Node**)

Use **Flannel** as the CNI plugin:

```bash
kubectl apply -f https://github.com/flannel-io/flannel/releases/latest/download/kube-flannel.yml
```

Wait a few moments:
```bash
kubectl get pods -n kube-system
```
All should reach `Running` state.

---

## 🧩 Step 7: Join Worker Nodes (Run on **Each Worker Node**)

Use the join command you saved earlier:

```bash
sudo kubeadm join 192.168.1.170:6443 --token <TOKEN>     --discovery-token-ca-cert-hash sha256:<HASH>
```

After success, check on master:
```bash
kubectl get nodes
```
You should see all 3 nodes in `Ready` state.

---

## ✅ Step 8: Verify Cluster (Run on **Master Node**)

```bash
kubectl get nodes -o wide
kubectl get pods -A
```

---

## 🧭 Step 9: (Optional) Deploy Test Pod (Run on **Master Node**)

```bash
kubectl run nginx --image=nginx --port=80
kubectl expose pod nginx --type=NodePort --port=80
kubectl get svc
```

Access via:
```
http://<Worker-Node-IP>:<NodePort>
```

---

## 📦 Summary of Command Groups

| Node Type | Purpose                                                         | Steps     |
| --------- | --------------------------------------------------------------- | --------- |
| All Nodes | Basic setup, container runtime, kubeadm/kubelet/kubectl install | Steps 1–3 |
| Master    | Cluster init, network plugin, config setup                      | Steps 4–6 |
| Workers   | Join cluster                                                    | Step 7    |
| Master    | Validation and testing                                          | Steps 8–9 |

---

## 🧠 Tips

- Use **thin-provisioned** disks on VMware to save space.  
- Take **snapshots** before initializing kubeadm — helps rollback easily.  
- Keep `kubeadm reset` in mind to reset a failed init:  
  ```bash
  sudo kubeadm reset -f
  sudo rm -rf ~/.kube
  sudo systemctl restart containerd
  ```

---

## ✍️ Author Notes
Created by **Noor S.**, as part of my **CKA in-house Kubernetes Lab project** using **VMware** and **Ubuntu 22.04**.





## References
