# KUBERNETES-INSTALLATION-STEPS-POD-CREATION
steps to install k8s in ec2 and create cluster and pods 
KUBERNETES-INSTALLATION,CLUSTER CREATION,POD CREATION AND DELETION’

1.ssh -i "cluster2.pem" ubuntu@ec2-34-239-253-17.compute-1.amazonaws.com

1.	sudo apt update -y
2.	sudo apt install -y ca-certificates curl gnupg lsb-release
3.	sudo mkdir -p /etc/apt/keyrings
4.	curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
5.	echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
6.	sudo apt update
7.	sudo apt install -y containerd.io
8.	sudo service containerd status
9.	sudo -i
10.	containerd config default > /etc/containerd/config.toml

11.	vim /etc/containerd/config.toml
12.	sudo service containerd restart
13.	sudo curl -fsSLo /usr/share/keyrings/kubernetes-archive-keyring.gpg https://dl.k8s.io/apt/doc/apt-key.gpg
14.	echo "deb [signed-by=/usr/share/keyrings/kubernetes-archive-keyring.gpg] https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list
15.	sudo apt update
16.	sudo apt install -y kubeadm kubectl kubelet
17.	sudo apt-mark hold kubeadm kubectl kubelet
18.	sudo swapoff -a
19.	cat /etc/fstab
20.	sudo modprobe br_netfilter
21.	echo br_netfilter | sudo tee /etc/modules-load.d/kubernetes.conf
22.	sudo kubeadm init --pod-network-cidr=10.244.0.0/16
23.	//if error comes then 
24.	cat /proc/sys/net/ipv4/ip_forward
25.	kubeadm reset
26.	echo 1 > /proc/sys/net/ipv4/ip_forward
27.	kubeadm init --pod-network-cidr=10.244.0.0/16
28.	mkdir -p $HOME/.kube
29.	sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
30.	sudo chown $(id -u):$(id -g) $HOME/.kube/config
31.	kubectl apply -f https://raw.githubusercontent.com/flannel-io/flannel/master/Documentation/kube-flannel.yml
32.	kubectl get nodes
33.	kubectl get pods --all-namespaces
34.	kubectl version
35.	kubectl get nodes
36.	kubectl get pods --all-namespaces
37.	kubectl get pod
38.	kubectl get pod -A
39.	kubectl run my-pod --image=nginx
40.	kubectl get pod  //pending state because by default master node is tainted so we shoud untaint it
41.	kubectl taint node node-name(ip add) node-role.kubernetes.io/control-plane:NoSchedule-
//below exam[le’
42.	kubectl taint node ip-172-31-58-195 node-role.kubernetes.io/control-plane:NoSchedule-
43.	kubectl get pod //running
44.	kubectl describe pod 
//gives ip address where it is running in thedescription

root@ip-172-31-58-195:~# curl 10.244.0.4
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
html { color-scheme: light dark; }
body { width: 35em; margin: 0 auto;
font-family: Tahoma, Verdana, Arial, sans-serif; }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
45. root@ip-172-31-58-195:~# kubectl delete pod my-pod
pod "my-pod" deleted
root@ip-172-31-58-195:~# kubectl get pod
No resources found in default namespace.





