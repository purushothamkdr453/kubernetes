# kubeadm high availabilty setup manually(in Ubuntu OS)


### Node Details:

2 master - 2GB RAM, 2 CPU
1 loadbalancer - 1GB RAM, 1 CPU
1 worker - 1GB RAM, 1 CPU

### Add the following Firewall rules to your VPC

Add the following rules to your VPC in which you will be creating k8s clusters.

Ingress - > tcp,udp,icmp,ipip -> from subnet range
Ingress -> tcp:22,tcp:6443,icmp -> 0.0.0.0/0

### Commands that should be executed on loadbalancer node

```
apt update
apt install -y haproxy
```

Append the below lines to /etc/haproxy/haproxy.cfg

```
frontend kubernetes-frontend
    bind <loadbalancernodeip>:6443
    mode tcp
    option tcplog
    default_backend kubernetes-backend

backend kubernetes-backend
    mode tcp
    option tcp-check
    balance roundrobin
    server <master1-hostname> <master1-ip>:6443 check fall 3 rise 2
    server <master2-hostname> <master2-ip>:6443 check fall 3 rise 2
```

systemctl restart haproxy

### Comands that should be executed on matser,workernodes:

**update all the packages:**

```
apt install net-tools

apt update
```

**Disable swap:**

```
swapoff -a 

    or

sed -i '/swap/d' /etc/fstab
```

**Enable "br_netfilter" module:**

```
lsmod | grep br_netfilter
sudo modprobe br_netfilter

cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
EOF
sudo sysctl --system
```

**Install Docker**

```
sudo apt-get update

sudo apt-get install -y \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg-agent \
    software-properties-common

curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -

sudo apt-key fingerprint 0EBFCD88

sudo add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) \
   stable"

sudo apt-get update

sudo apt-get install -y docker-ce docker-ce-cli containerd.io
```

**Installing kudeadm,kubelet,kubectl:**

```
sudo apt-get update && sudo apt-get install -y apt-transport-https curl
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
cat <<EOF | sudo tee /etc/apt/sources.list.d/kubernetes.list
deb https://apt.kubernetes.io/ kubernetes-xenial main
EOF
sudo apt-get update

sudo apt-get install -y kubelet kubeadm kubectl
            or 
# to install specific version of kubernetes then run below command
sudo apt-get install -y kubelet=<version> kubeadm=<version> kubectl=<version>

sudo apt-mark hold docker-ce kubelet kubeadm kubectl
systemctl daemon-reload
systemctl restart kubelet
```

**Note:** <version> can be found by running below command.

```
curl -s https://packages.cloud.google.com/apt/dists/kubernetes-xenial/main/binary-amd64/Packages | grep Version | awk '{print $2}' -> to find out different versions of k8s
```

### Only on Master1:

**Initalize:**

```
kubeadm init --control-plane-endpoint="<loadbalancernodeip>:6443" --upload-certs --apiserver-advertise-address=<master1-ip> --pod-network-cidr=192.168.0.0/16 
```

**Install Calico:**

```
curl https://docs.projectcalico.org/manifests/calico.yaml -O
kubectl apply -f calico.yaml
```

taint the master node, required to not to schedule any pods on master node.

```
kubectl taint master node-role.kubernetes.io/master=true:NoSchedule
```

**Note:** kubeadm generates two commands 1 for master and another 1 for node to join the cluster


### Only on Master2:


run the kubeadm join command generated by "kubeadm init". Makesure you pass an extra flag i.e --apiserver-advertise-address=<master2-ip> to the command.

Eg:

```
 kubeadm join 10.0.1.12:6443 --token xv58br.ldsm9t5arj6n0nd8 --discovery-token-ca-cert-hash sha256:5b03b6efd60ce553f9cc2c453c3edbfc36fd276556de6245bb5ea6d9fb2845b3 --control-plane --certificate-key 711f281dbf2697530b512a2d1109a9e622178d055cfa75259e4ba4de0cd11c57  --apiserver-advertise-address=10.0.1.15
```

### Only on worker nodes:

Run the kubeadm join command generated by "kubeadm init".

eg:

```
kubeadm join 10.0.1.12:6443 --token xv58br.ldsm9t5arj6n0nd8 --discovery-token-ca-cert-hash sha256:5b03b6efd60ce553f9cc2c453c3edbfc36fd276556de6245bb5ea6d9fb2845b3 
```

### Adding Addition master nodes using new tokens:


Switch to master-1 node run kubeadm token list. this command will display existing tokens, however these are valid only for certain duration. If you want to add new nodes after sometime then you need to create new token.

kubeadm init phase upload-certs --upload-certs -> this command generates certificate key for example. 595fdbd60f84d991174f3c0a2d8692456c1c43c836a5f5b8e8865dc29b8288af
kubectl create token --certificate-key <certificatekey-generated-from-previous-command> --print-join-command
for example:
```
kubeadm token create --certificate-key 595fdbd60f84d991174f3c0a2d8692456c1c43c836a5f5b8e8865dc29b8288af --print-join-command
```
The above command prints the join command for adding additional master node. for example.
```
kubeadm join 10.0.1.12:6443 --token paikt9.uqnobyo3ybdjerek --discovery-token-ca-cert-hash sha256:5b03b6efd60ce553f9cc2c453c3edbfc36fd276556de6245bb5ea6d9fb2845b3 --control-plane --certificate-key 595fdbd60f84d991174f3c0a2d8692456c1c43c836a5f5b8e8865dc29b8288af
```
**Note:** makesure your add extra option i.e --apiserver-advertise-address=<master3-ip> to above command to add additional master node.

The above join command can also be used on workernodes however you need to remove ( --control-plane --certificate-key 595fdbd60f84d991174f3c0a2d8692456c1c43c836a5f5b8e8865dc29b8288af ) from command.


