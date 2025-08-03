<p align="center">
    <img src="https://skillicons.dev/icons?i=ansible,kubernetes" />
</p>

# Kubernetes Ansible Deployment

This Ansible project automates the deployment of a Kubernetes cluster using kubeadm with CRI-O as the container runtime.

## 📖 Blog Post
**[[Kubernetes] 01. Creating Kubernetes Cluster
](https://mon-roman.tistory.com/entry/Kubernetes-01-Creating-Kubernetes-Cluster)** - My Blog Post



- **Controller Node**: Single master node running the Kubernetes control plane
- **Compute Nodes**: Multiple worker nodes for running workloads
- **Management Node**: Separate node for cluster management and Helm operations

### Infrastructure as Code
**[Terraform Repository](https://github.com/yoonhyunchan/aws-terraform-project)** - My Repo

 Infrastructure setup for AWS EC2 instances, networking, and security groups required for the Kubernetes cluster deployment.

## 📋 Prerequisites

### System Requirements
- **OS**: Amazon Linux 2023 or RHEL/CentOS/Rocky Linux
- **Memory**: Minimum 2GB RAM per node
- **CPU**: Minimum 2 vCPUs per node

### Network Requirements
- All nodes must be able to communicate with each other
   ### Control Plane

   | Protocol | Direction | Port Range | Purpose                 | Used By              |
   | -------- | --------- | ---------- | ----------------------- | -------------------- |
   | TCP      | Inbound   | 6443       | Kubernetes API server   | All                  |
   | TCP      | Inbound   | 2379-2380  | etcd server client API  | kube-apiserver, etcd |
   | TCP      | Inbound   | 10250      | Kubelet API             | Self, Control plane  |
   | TCP      | Inbound   | 10259      | kube-scheduler          | Self                 |
   | TCP      | Inbound   | 10257      | kube-controller-manager | Self                 |

   ---

   ### Worker Node(s)

   | Protocol | Direction | Port Range  | Purpose           | Used By              |
   | -------- | --------- | ----------- | ----------------- | -------------------- |
   | TCP      | Inbound   | 10250       | Kubelet API       | Self, Control plane  |
   | TCP      | Inbound   | 10256       | kube-proxy        | Self, Load balancers |
   | TCP      | Inbound   | 30000-32767 | NodePort Services | All                  |
   | UDP      | Inbound   | 30000-32767 | NodePort Services | All                  |

   ---

   ### All Nodes (Calico Networking)

   | Configuration Host(s)                | Connection   | Type          | Port/Protocol    |
   | ------------------------------------ | ------------ | ------------- | ---------------- |
   | Calico networking with VXLAN enabled | All          | Bidirectional | UDP 4789         |
   | Calico networking with Typha enabled | Typha agents | Incoming      | TCP 5473         |
   | All → kube-apiserver host            | Incoming     | -             | TCP 443 or 6443* |


### Ansible Requirements
- Ansible 2.9+
- SSH access to all target nodes
- Python 3.6+ on target nodes

## 🚀 Quick Start

### 1. Clone the Repository
```bash
git clone https://github.com/yoonhyunchan/kubernetes-ansible.git
cd kubernetes-ansible
```

### 2. Configure Inventory
Edit `inventory.ini` to match your infrastructure:

```ini
[k8s_controller]
your-controller-node.example.com

[k8s_compute]
your-compute-node1.example.com
your-compute-node2.example.com

[mgmt]
your-management-node.example.com

[kubernetes:children]
k8s_controller
k8s_compute

[all:vars]
ansible_user=ec2-user
ansible_ssh_private_key_file=/path/to/your/private-key.pem
```

### 3. Deploy the Cluster
```bash
# Deploy the complete Kubernetes cluster
ansible-playbook -i inventory.ini site.yml

# Deploy Helm add-ons (optional, Not Yet)
ansible-playbook -i inventory.ini site_helm.yml
```

## 📁 Project Structure

```
.
├── README.md                
├── ansible.cfg              # Ansible configuration
├── inventory.ini            # Host inventory
├── site.yml                 # Main deployment playbook
├── site_helm.yml           # Helm add-ons playbook
└── roles/
    ├── setup_k8s/          # Kubernetes prerequisites setup
    │   ├── tasks/main.yml
    │   ├── handlers/main.yml
    │   └── vars/main.yml
    ├── kubeadm_init/       # Initialize Kubernetes master
    │   ├── tasks/main.yml
    │   └── templates/init-config.yaml.j2
    ├── kubeadm_join/       # Join worker nodes
    │   ├── tasks/main.yml
    │   └── templates/join-config.yaml.j2
    ├── privision_admin/    # Configure admin access
    │   ├── tasks/main.yml
    │   └── vars/main.yml
    ├── helm_install/       # Install Helm
    │   └── tasks/main.yml
    └── helm_addons/        # Install Helm charts (Not Yet)
        └── tasks/main.yml
```

## 🔧 Configuration

### Kubernetes Version
Edit `roles/setup_k8s/vars/main.yml` to change Kubernetes version:
```yaml
kubernetes_version: v1.32
crio_version: v1.32
```

## 🔍 Verification

After deployment, verify your cluster:

```bash
# From the management node
kubectl get nodes
kubectl get pods --all-namespaces
kubectl cluster-info
```
