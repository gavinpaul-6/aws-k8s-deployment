<p align="center">
  <img src="images\cka.png" width="300" height="auto">
</p>

# Kubernetes Cluster Setup and CKA Exam Documentation  

Welcome to the documentation for setting up and administering a Kubernetes cluster on AWS! This guide provides a hands-on approach to deploying a Kubernetes cluster from scratch using **three EC2 instances**. The setup avoids managed services like Amazon EKS, giving you a deeper understanding of Kubernetes internals. Additionally, this documentation includes tasks relevant to the **Certified Kubernetes Administrator (CKA)** exam.

---

## Features  

### Kubernetes Cluster Deployment  
- Launch and configure **three EC2 instances** to serve as the master and worker nodes.  
- Install and configure **Kubernetes components** (`kubeadm`, `kubectl`, `kubelet`).  
- Set up the **Kubernetes control plane** and join worker nodes to the cluster.  
- Configure **Container Networking Interface (CNI)** for inter-node communication.  
- Deploy an **Ingress Controller** to manage external access to services.  

### Cluster Administration and Management  
- Implement **Role-Based Access Control (RBAC)** and TLS encryption for security.  
- Configure **etcd** backups to ensure cluster state preservation.  
- Set up **monitoring and logging** using **Prometheus, Grafana, and Fluentd**.  
- Enable **autoscaling** for workloads with the Horizontal Pod Autoscaler (HPA).  

### CKA Exam Preparation  
- Hands-on exercises covering **cluster architecture, workloads, networking, and security**.  
- Real-world scenarios for **troubleshooting Kubernetes clusters**.  
- Essential `kubectl` commands and automation strategies.  

---

## Prerequisites  

Before proceeding, ensure you have the following:  

- **AWS Account** with EC2 permissions  
- **Three EC2 instances** (Ubuntu 22.04 recommended)  
- **AWS CLI and kubectl installed** on your local machine  
- **Basic understanding of Kubernetes concepts**  

IAM Role/Permissions required for cluster setup:  

- **EC2:** `ec2:DescribeInstances`, `ec2:CreateSecurityGroup`  
- **Networking:** `vpc:CreateVpc`, `ec2:CreateSubnet`, `ec2:AuthorizeSecurityGroupIngress`  
- **IAM:** `iam:CreateRole`, `iam:AttachRolePolicy`  

---

## How to Set Up the Kubernetes Cluster  

### 1. Launch EC2 Instances  
Create three EC2 instances and configure security groups to allow SSH, API Server (`6443`), and inter-node communication.  

The following TCP ports need to be open for the Control Plane node:

   - **6443:**  Kubernetes API Server
   - **2379-2380:** etcd server client API
   - **10250:** Kubelet API
   - **10259:** kube-scheduler
   - **10257:** kube-controller-manager


<img src="images\configure_inbound_rules-sg.png" width="600" height="auto">


The following TCP ports need to be open for the Worker nodes:

- **10250:** Kubelet API
- **30000-23767:** NodePort Services


<img src="images\configure_inbound_rules-sg-1.png" width="600" height="auto">


### 2. Configure the Hostnames

Configure the hostnames for the Control and Worker nodes.

⚠️ Note: If you stop the EC2 instances, you will have to repeat this step and reconfigure them again.

<img src="images\configure_hostnames.png
" width="600" height="auto">


### 3. Install Container Runtime

After following the steps in the official K8s documentation, I decided to automate this for the worker nodes after initial setup on the Control plane node.

```bash
sudo systemctl stop apparmor.service
sudo systemctl disable apparmor.service

# Control Plane

cat <<EOF | sudo tee /etc/modules-load.d/k8s.conf
overlay
br_netfilter
EOF

sudo modprobe overlay
sudo modprobe br_netfilter

# sysctl params required by setup, params persist across reboots
cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-iptables  = 1
net.bridge.bridge-nf-call-ip6tables = 1
net.ipv4.ip_forward                 = 1
EOF

# Apply sysctl params without reboot
sudo sysctl --system

sudo sysctl -w net.ipv6.conf.all.disable_ipv6=1 && 
sudo sysctl -w net.ipv6.conf.default.disable_ipv6=1 && 
sudo sysctl -w net.ipv6.conf.lo.disable_ipv6=1

# Make the changes persistent across reboots:

echo "net.ipv6.conf.all.disable_ipv6=1" | sudo tee -a /etc/sysctl.conf && 
echo "net.ipv6.conf.default.disable_ipv6=1" | sudo tee -a /etc/sysctl.conf && 
echo "net.ipv6.conf.lo.disable_ipv6=1" | sudo tee -a /etc/sysctl.conf

# Then, apply the changes:

sudo sysctl -p

# Installing Containerd

sudo apt update &&
sudo apt-get install -y containerd
sudo mkdir /etc/containerd
containerd config default | sudo tee /etc/containerd/config.toml
sudo sed -i 's/SystemdCgroup \= false/SystemdCgroup \= true/g' /etc/containerd/config.toml
sudo systemctl restart containerd

```

<img src="images\containerd_running.png" width="600" height="auto">

### 4. Install Kubernetes and Dependencies  
On each instance, install `kubeadm`, `kubectl`, and `kubelet`:  
```bash
# Instructions for installing kubeadm

# https://v3-28.docs.kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/


sudo apt-get update
# apt-transport-https may be a dummy package; if so, you can skip that package
sudo apt-get install -y apt-transport-https ca-certificates curl gpg

curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.28/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg

# This overwrites any existing configuration in /etc/apt/sources.list.d/kubernetes.list
echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.28/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list

sudo apt-get update
sudo apt-get install -y kubelet=1.28.0-1.1 kubeadm=1.28.0-1.1 kubectl=1.28.0-1.1

sudo apt-mark hold kubelet kubeadm kubectl

kubeadm version
kubectl version
kubelet --version
```

<img src="images\install_kubeadm,_kubelet_and_kubectl.png" width="600" height="auto">


### 5. Initialize the Cluster  
On the **Control node**, run:  
```bash
sudo kubeadm init 
```

Set up `kubectl` for the current user:  
```bash
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

<img src="images\initialize_cluster_with_kubeadm.png" width="600" height="auto">


### 6. Deploy Networking  
Install a network plugin (Cilium):  
```bash
CILIUM_CLI_VERSION=$(curl -s https://raw.githubusercontent.com/cilium/cilium-cli/main/stable.txt)
CLI_ARCH=amd64
if [ "$(uname -m)" = "aarch64" ]; then CLI_ARCH=arm64; fi
curl -L --fail --remote-name-all https://github.com/cilium/cilium-cli/releases/download/${CILIUM_CLI_VERSION}/cilium-linux-${CLI_ARCH}.tar.gz{,.sha256sum}
sha256sum --check cilium-linux-${CLI_ARCH}.tar.gz.sha256sum
sudo tar xzvfC cilium-linux-${CLI_ARCH}.tar.gz /usr/local/bin
rm cilium-linux-${CLI_ARCH}.tar.gz{,.sha256sum}
```

<img src="images\configure_network_plugin-cilium-pt1.png" width="600" height="auto">

<img src="images\configure_network_plugin-cilium-pt2.png" width="600" height="auto">

<img src="images\configure_network_plugin-cilium-pt3.png" width="600" height="auto">

Cilium requires certain ports to be open to enable connectivity which can be configured on the Security Groups for the Control and Worker nodes.

<img src="images\cilium_configure_connectivity_open_ports.png" width="600" height="auto">

<img src="images\cilium_configure_connectivity_open_ports-pt1.png" width="600" height="auto">


<img src="images\cilium_configure_connectivity_open_port-pt2.png" width="600" height="auto">



### 7. Join Worker Nodes  
Copy the `kubeadm join` command from the master node and run it on worker nodes:  
```bash
sudo kubeadm join <master-node-ip>:6443 --token <token> --discovery-token-ca-cert-hash sha256:<hash>
```

<img src="images\join_worker_nodes_to_cluster-pt1.png" width="600" height="auto">

<img src="images\join_worker_nodes_to_cluster-pt2.png" width="600" height="auto">




---

## Verifying the Cluster Setup  

If all nodes are `Ready`, the cluster is successfully deployed.  



---

## Additional Features  


### Security Enhancements  
- Implement **RBAC policies** to restrict access.  
- Enable **etcd encryption** for securing cluster secrets.  
 
---

## Further Enhancements  

- Automate cluster setup with **Terraform and Ansible**.  
- Implement **GitOps workflows** with **ArgoCD**.  
- Explore **service mesh solutions** like Istio for traffic management.  

---

## Resources  

- [Kubernetes Official Documentation](https://kubernetes.io/docs/)  
- [Certified Kubernetes Administrator (CKA) Exam Guide](https://www.cncf.io/certification/cka/)  


This guide serves as a reference for deploying and managing Kubernetes clusters while preparing for the **CKA exam**. 🚀
