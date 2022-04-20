Creating a cluster with kubeadm - 
https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/

1. create two ec2 instances. (1 for master node (t2.medium)and 1 for worker node(t2.micro))
1.1. Ports need to be opened:
  https://kubernetes.io/docs/reference/ports-and-protocols/ 
  <img width="686" alt="image" src="https://user-images.githubusercontent.com/25228357/164200818-632665df-26ce-44ea-b671-ce6c3ba30c3a.png">

3. Swap disabled. You MUST disable swap in order for the kubelet to work properly. - 
4. Install kubectl - kubectl inetracts with our cluster to manage.
5. install docker on bothe machines:
   https://docs.aws.amazon.com/AmazonECS/latest/developerguide/create-container-image.html
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

    cat >> /etc/sysctl.d/kubernetes.conf <<EOF
    net.bridge.bridge-nf-call-ip6tables = 1
    net.bridge.bridge-nf-call-iptables = 1
    EOF
    sysctl --system
                                               
 10. Add yum repository for kubernetes packages
    ```diff                                     
    cat >>/etc/yum.repos.d/kubernetes.repo<<EOF
    [kubernetes]
    name=Kubernetes
    baseurl=https://packages.cloud.google.com/yum/repos/kubernetes-el7-x86_64
    enabled=1
    gpgcheck=1
    repo_gpgcheck=1
    gpgkey=https://packages.cloud.google.com/yum/doc/yum-key.gpg
            https://packages.cloud.google.com/yum/doc/rpm-package-key.gpg
    EOF
  11. Install Kubernetes
  ```diff 
    yum install -y kubeadm-1.15.6-0.x86_64 kubelet-1.15.6-0.x86_64 kubectl-1.15.6-0.x86_64
    Enable and Start kubelet service
    systemctl enable kubelet
    systemctl start kubelet
                                               
