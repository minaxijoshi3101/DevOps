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
    systemctl stop firewalld
8. Disable swap

    sed -i '/swap/d' /etc/fstab <br />
    swapoff -a
