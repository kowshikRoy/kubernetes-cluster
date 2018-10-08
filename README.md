# Local Kubernetes Cluster
A multi node cluster setup for kubernetes using vagrant and virtualbox

## Getting Started

These instructions will get you a copy of the project up and running on your local machine for development and testing purposes. 
I am using MAC, but simillar procedure should work on other platforms as well.

### Prerequisites
We need virtualbox and vagrant to run the local cluster. 
- Download and install vagrant from [here](https://www.vagrantup.com/downloads.html)
- Download and install virtualbox from [here](https://www.virtualbox.org/wiki/Downloads)
- Install vagrant plugin for faster build
```
vagrant plugin install vagrant-cachier
```

### Installing
Clone the repo. Then run the following commands
```
cd local-kubernetes-cluster
vagrant up
```
This will start 3 vms. 
- master
- worker1
- worker2

In the way of installation, you can see some things like this
```
[init] Using Kubernetes version: vX.Y.Z
[preflight] Running pre-flight checks
[kubeadm] WARNING: starting in 1.8, tokens expire after 24 hours by default (if you require a non-expiring token use --token-ttl 0)
[certificates] Generated ca certificate and key.
[certificates] Generated apiserver certificate and key.
[certificates] apiserver serving cert is signed for DNS names [kubeadm-master kubernetes kubernetes.default kubernetes.default.svc kubernetes.default.svc.cluster.local] and IPs [10.96.0.1 10.138.0.4]
[certificates] Generated apiserver-kubelet-client certificate and key.
[certificates] Generated sa key and public key.
[certificates] Generated front-proxy-ca certificate and key.
[certificates] Generated front-proxy-client certificate and key.
[certificates] Valid certificates and keys now exist in "/etc/kubernetes/pki"
[kubeconfig] Wrote KubeConfig file to disk: "admin.conf"
[kubeconfig] Wrote KubeConfig file to disk: "kubelet.conf"
[kubeconfig] Wrote KubeConfig file to disk: "controller-manager.conf"
[kubeconfig] Wrote KubeConfig file to disk: "scheduler.conf"
[controlplane] Wrote Static Pod manifest for component kube-apiserver to "/etc/kubernetes/manifests/kube-apiserver.yaml"
[controlplane] Wrote Static Pod manifest for component kube-controller-manager to "/etc/kubernetes/manifests/kube-controller-manager.yaml"
[controlplane] Wrote Static Pod manifest for component kube-scheduler to "/etc/kubernetes/manifests/kube-scheduler.yaml"
[etcd] Wrote Static Pod manifest for a local etcd instance to "/etc/kubernetes/manifests/etcd.yaml"
[init] Waiting for the kubelet to boot up the control plane as Static Pods from directory "/etc/kubernetes/manifests"
[init] This often takes around a minute; or longer if the control plane images have to be pulled.
[apiclient] All control plane components are healthy after 39.511972 seconds
[uploadconfig] Storing the configuration used in ConfigMap "kubeadm-config" in the "kube-system" Namespace
[markmaster] Will mark node master as master by adding a label and a taint
[markmaster] Master master tainted and labelled with key/value: node-role.kubernetes.io/master=""
[bootstraptoken] Using token: <token>
[bootstraptoken] Configured RBAC rules to allow Node Bootstrap tokens to post CSRs in order for nodes to get long term certificate credentials
[bootstraptoken] Configured RBAC rules to allow the csrapprover controller automatically approve CSRs from a Node Bootstrap Token
[bootstraptoken] Creating the "cluster-info" ConfigMap in the "kube-public" namespace
[addons] Applied essential addon: CoreDNS
[addons] Applied essential addon: kube-proxy

Your Kubernetes master has initialized successfully!

To start using your cluster, you need to run (as a regular user):

  mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config

You should now deploy a pod network to the cluster.
Run "kubectl apply -f [podnetwork].yaml" with one of the addon options listed at:
  http://kubernetes.io/docs/admin/addons/

You can now join any number of machines by running the following on each node
as root:

  kubeadm join --token <token> <master-ip>:<master-port> --discovery-token-ca-cert-hash sha256:<hash>
 ```
 Take a note of ```kubeadm join --token <token> <master-ip>:<master-port> --discovery-token-ca-cert-hash sha256:<hash>``` this line. we will need this to join node in the cluster
 
#### Commands in Master Node
After this ssh into master vm by ``` vagrant ssh master ```
```
kubectl apply -f https://docs.projectcalico.org/v3.1/getting-started/kubernetes/installation/hosted/rbac-kdd.yaml
kubectl apply -f https://docs.projectcalico.org/v3.1/getting-started/kubernetes/installation/hosted/kubernetes-datastore/calico-networking/1.7/calico.yaml
kubectl get pods --all-namespaces 
```
Wait for sometime, make sure that all the pods are running.

#### Commands in each Worker Node
SSH into worker node. I will show example for only the worker1, you need to do the steps for worker2. In this step we need the kubeadm join line we previously noted. In this example, I am using the one in my local cluster setup
```
vagrant ssh worker1
sudo su
kubeadm join 192.168.3.3:6443 --token 1jaez3.5qxaaehjd5ivbj2v --discovery-token-ca-cert-hash sha256:2241d36a6d292a61b320e7779e618bef2ce961cbed26bc3c5647c12bd87dd937
```
After that run ```kubectl get nodes -w```  on the master see the status. It may take some time.
Same instruction is applicable in worker2 as well.

### Running Kubernetes
On the master node, use this commands to check the kubernetes cluster is working properly
```
kubectl run nginx --image=nginx --replicas=4
kubectl get pods -o wide
```
Check that the pods the being created.

## License

This project is licensed under the MIT License - see the [LICENSE.md](LICENSE.md) file for details
