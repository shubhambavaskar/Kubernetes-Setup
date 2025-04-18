 Kubernetes-Setup
 Here's the **Kubernetes Setup Guide on AWS EC2 (in English)** — explained step by step in a **simple way for beginners**:

---

## ✅ How to Set Up Kubernetes on EC2?

# 1️⃣ **Launch EC2 Instances**
- Create **2 EC2 instances** on AWS using Amazon Linux 2 or CentOS.
- One will be your **Master Node**, and the other will be the **Worker Node**.
- Make sure both are in the **same VPC and security group** with these ports open:  
  `22`, `6443`, `10250`, `10255`, `30000-32767`, `2379-2380`, `179`, `443`, `80`

---

### 2️⃣ **Add Kubernetes Repository**

On **both EC2 instances** (master and worker), run:

```bash
cat <<EOF | sudo tee /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=https://packages.cloud.google.com/yum/repos/kubernetes-el7-\$basearch
enabled=1
gpgcheck=0
EOF
```

---

### 3️⃣ **Install Kubernetes Tools**

Install `kubeadm`, `kubelet`, and `kubectl` on both instances:

```bash
sudo yum install -y kubelet kubeadm kubectl
sudo systemctl enable kubelet && sudo systemctl start kubelet
```

---

### 4️⃣ **Initialize Kubernetes Cluster (on Master Node only)**

Run this command on the **Master Node**:

```bash
sudo kubeadm init
```

After successful initialization, you'll get a `kubeadm join` command — **save this**, you’ll need it to connect the Worker Node.

---

### 5️⃣ **Configure kubectl (on Master Node only)**

To allow running `kubectl` as a regular user:

```bash
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

---

### 6️⃣ **Start kubelet service**

Usually, it's already running after installation, but to be sure:

```bash
sudo systemctl enable kubelet && sudo systemctl start kubelet
```

---

### 7️⃣ **Join Worker Node to the Cluster**

Go to the **Worker Node** and paste the `kubeadm join` command from the Master Node output. Example:

```bash
sudo kubeadm join <master-ip>:6443 --token <token> --discovery-token-ca-cert-hash sha256:<hash>
```

After this step, your Kubernetes cluster is ready with **1 Master + 1 Worker** node.

---

