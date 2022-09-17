Creating a cluster with kubeadm - 
[r2h]: https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/

1. create two ec2 instances. (1 for master node (t2.medium)and 1 for worker node(t2.micro))
1.1. Ports need to be opened:
  https://kubernetes.io/docs/reference/ports-and-protocols/ 
  <img width="686" alt="image" src="https://user-images.githubusercontent.com/25228357/164200818-632665df-26ce-44ea-b671-ce6c3ba30c3a.png">

3. Swap disabled. You MUST disable swap in order for the kubelet to work properly. - 
4. install docker on both the machines:
   https://docs.aws.amazon.com/AmazonECS/latest/developerguide/create-container-image.html
   sudo yum update -y <br />
   amazon-linux-extras install docker -y <br />
   service docker start <br/>
   systemctl enable docker <br />
5. Install kubectl - kubectl inetracts with our cluster to manage.

6. Disable SELinux

    setenforce 0 <br />
    sed -i --follow-symlinks 's/^SELINUX=enforcing/SELINUX=disabled/' /etc/sysconfig/selinux
7. Disable Firewall

    systemctl disable firewalld <br />
    systemctl stop firewalld <br />
    sometimes below error may occur: <br />
    ```diff 
    - Unit firewalld.service could not be found.
      It means firewall-cmd is not installed
        **yum install firewalld**
8. Disable swap

    sed -i '/swap/d' /etc/fstab <br />
    swapoff -a
9. Update sysctl settings for Kubernetes networking
```diff 
cat >> /etc/sysctl.d/kubernetes.conf <<EOF
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
EOF
sysctl --system
```                                               
 10. Add yum repository for kubernetes packages
 ```diff                                     
cat >>/etc/yum.repos.d/kubernetes.repo<<EOF
[kubernetes]
name=Kubernetes
baseurl=https://packages.cloud.google.com/yum/repos/kubernetes-el7-x86_64
enabled=1
sslverify = 0
gpgkey=https://packages.cloud.google.com/yum/doc/yum-key.gpg
https://packages.cloud.google.com/yum/doc/rpm-package-key.gpg
EOF

  ```
  11. Install Kubernetes
  ```diff 
yum install -y kubeadm-1.15.6-0.x86_64 kubelet-1.15.6-0.x86_64 kubectl-1.15.6-0.x86_64
```

```diff
- failure: repodata/repomd.xml from kubernetes: [Errno 256] No more mirrors to try.
https://packages.cloud.google.com/yum/repos/kubernetes-el7-x86_64/repodata/repomd.xml: [Errno -1] repomd.xml signature could not be verified for kubernetes
```
```diff
+ to resolve above error use
mv /etc/yum.repos.d/kubernetes.repo /etc/yum.repos.d/kubernetes.repo.bak
cat >> /etc/yum.repos.d/kubernetes.repo <<EOF
[kubernetes]
name=Kubernetes
baseurl=https://mirrors.aliyun.com/kubernetes/yum/repos/kubernetes-el7-x86_64/
enabled=1
gpgcheck=0
EOF
```

12. Enable and Start kubelet service
```diff
systemctl enable kubelet
systemctl start kubelet
```
**On Master Node:**
1. Initialize Kubernetes Cluster <br />
https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/create-cluster-kubeadm/

check the Ips of your nodes if range of nodes are 192.168.0.0, dont use below  --pod-network-cidr=192.168.0.0/16
otherwise you can use <br /
```diff 
kubeadm init --apiserver-advertise-address=<MasterServerIP(give privateIP address here)> --pod-network-cidr=192.168.0.0/16
kubeadm init --apiserver-advertise-address=172.31.24.148 --pod-network-cidr=192.168.0.0/16
```
O/P: 
```diff
+ Your Kubernetes control-plane has initialized successfully!

+ To start using your cluster, you need to run the following as a regular user:(so create the user first following step 2

  mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config
```
<img width="831" alt="image" src="https://user-images.githubusercontent.com/25228357/190843472-f15ce60a-be80-45f2-9f1b-532060925616.png">

2. Create a user for kubernetes administration and copy kube config file.
To be able to use kubectl command to connect and interact with the cluster, the user needs kube config file.
In this case, we are creating a user called kubeadmin
```diff
useradd kubeadmin 
mkdir /home/kubeadmin/.kube
cp /etc/kubernetes/admin.conf /home/kubeadmin/.kube/config
chown -R kubeadmin:kubeadmin /home/kubeadmin/.kube
```
3. Deploy Calico network as a kubeadmin user.

This should be executed as a user (heare as a kubeadmin )
```diff
sudo su - kubeadmin 
kubectl create -f https://docs.projectcalico.org/v3.9/manifests/calico.yaml
```
4. Cluster join command

kubeadm token create --print-join-command


**On Worker Nodes:**
```diff
kubeadm join 172.31.31.26:6443 --token 5vml77.1g1rh6b4lhbeg80v     --discovery-token-ca-cert-hash sha256:c2eee6235ea3c1a45a7d141b8abf5fe5890eb8c6e76f233dd7952ac897f02c23
```
```diff
- **issues**
- Joining cluster takes forever
+ check the firewall, make sure all the required ports are open in master machine.
```
```diff
1  hostnamectl set-hostname master
    2  sudo yum check-update -y
    3  curl -sSL https://get.docker.com/ | sh
    4  curl -fsSL https://get.docker.com/ | sh
    5  curl -sSL https://get.docker.com/
    6  curl -sSL https://get.docker.com/ | sh
    7  yum update -y
    8  amazon-linux-extras install docker
    9  service docker start
   10  systemctl enable docker
   11  systemctl status docker
   12  cat <<EOF | sudo tee /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=https://packages.cloud.google.com/yum/repos/kubernetes-el7-\$basearch
enabled=1
gpgcheck=1
gpgkey=https://packages.cloud.google.com/yum/doc/yum-key.gpg https://packages.cloud.google.com/yum/doc/rpm-package-key.gpg
exclude=kubelet kubeadm kubectl
EOF

   13  sudo setenforce 0
   14  sudo sed -i 's/^SELINUX=enforcing$/SELINUX=permissive/' /etc/selinux/config
   15  sudo yum install -y kubelet kubeadm kubectl --disableexcludes=kubernetes
   16  sudo systemctl enable --now kubelet
   17  kubeadm init --apiserver-advertise-address=172.31.25.192 --pod-network-cidr=192.168.0.0/16
   18  mkdir -p $HOME/.kube
   19  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
   20  sudo chown $(id -u):$(id -g) $HOME/.kube/config
   21  kubectl get pods -A
   22  kubectl apply -f "https://cloud.weave.works/k8s/net?k8s-version=$(kubectl version | base64 | tr -d '\n')"
   23  kubectl get pods -A
   24  kubectl get pods --all-namespaces
   25  kubectl get pods -A
   26  history
```
<img width="1047" alt="image" src="https://user-images.githubusercontent.com/25228357/190846026-9c45daab-99a8-4247-a013-0a7b808a1092.png">
```diff
on worker node:
1  hostnamectl set-host-name worker
    2  hostnamectl set-hostname worker
    3  sudo yum check-update
    4  yum update -y
    5  amazon-linux-extras install docker -y
    6  service docker start
    7  systemctl enable docker
    8  docker --version
    9  cat <<EOF | sudo tee /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=https://packages.cloud.google.com/yum/repos/kubernetes-el7-\$basearch
enabled=1
gpgcheck=1
gpgkey=https://packages.cloud.google.com/yum/doc/yum-key.gpg https://packages.cloud.google.com/yum/doc/rpm-package-key.gpg
exclude=kubelet kubeadm kubectl
EOF

   10  cat <<EOF | sudo tee /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=https://packages.cloud.google.com/yum/repos/kubernetes-el7-\$basearch
enabled=1
gpgcheck=1
gpgkey=https://packages.cloud.google.com/yum/doc/yum-key.gpg https://packages.cloud.google.com/yum/doc/rpm-package-key.gpg
exclude=kubelet kubeadm kubectl
EOF

   11  clear
   12  sudo setenforce 0
   13  sudo sed -i 's/^SELINUX=enforcing$/SELINUX=permissive/' /etc/selinux/config
   14  sudo yum install -y kubelet kubeadm kubectl --disableexcludes=kubernetes
   15  sudo systemctl enable --now kubelet
   16  history
   17  watch uptime
   18  ubeadm join 172.31.25.192:6443 --token hq6c9b.24ovrf5olnb9c4fk --discovery-token-ca-cert-hash sha256:ea764abc350cfebecda0d155043e11193cbbcdb043d01d3ea98163d54753d208
   19  kubeadm join 172.31.25.192:6443 --token hq6c9b.24ovrf5olnb9c4fk --discovery-token-ca-cert-hash sha256:ea764abc350cfebecda0d155043e11193cbbcdb043d01d3ea98163d54753d208
```
<img width="930" alt="image" src="https://user-images.githubusercontent.com/25228357/190846139-1320b53a-3c60-4b41-8f97-936a2f7aa75f.png">

<img width="1037" alt="image" src="https://user-images.githubusercontent.com/25228357/190846124-d0f3aac6-1742-4ab8-9462-2886ba03159c.png">
