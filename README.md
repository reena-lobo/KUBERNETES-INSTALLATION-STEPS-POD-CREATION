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
 if we run taint command again then
 root@ip-172-31-58-195:~# kubectl taint node ip-172-31-58-195 node-role.kubernetes.io/control-plane:NoSchedule-
error: taint "node-role.kubernetes.io/control-plane:NoSchedule" not found
//beacuse already we untainted it 


//to get pods for specific namespcaes ex:kube-system namespace(name of name space)
root@ip-172-31-58-195:~# kubectl get pod -n kube-system
NAME                                       READY   STATUS    RESTARTS   AGE
coredns-5dd5756b68-6xjs7                   1/1     Running   0          20h
coredns-5dd5756b68-pkcrx                   1/1     Running   0          20h
etcd-ip-172-31-58-195                      1/1     Running   0          20h
kube-apiserver-ip-172-31-58-195            1/1     Running   0          20h
kube-controller-manager-ip-172-31-58-195   1/1     Running   0          20h
kube-proxy-shhdm                           1/1     Running   0          20h
kube-scheduler-ip-172-31-58-195            1/1     Running   0          20h


//another way to create a pod is by creating pod.yaml file
A pod.yaml file is used to define a Kubernetes Pod, which is the smallest deployable unit in a Kubernetes cluster. Below is an example of the basic structure of a pod.yaml file:
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
  labels:
    app: my-app
spec:
  containers:
  - name: my-container
    image: my-image:tag
    ports:
    - containerPort: 80
apiVersion: Specifies the version of the Kubernetes API being used. In this case, it's v1, which is the API version for core Kubernetes objects like Pods.

kind: Specifies the kind of Kubernetes object, which is Pod in this case.

metadata: Contains metadata about the Pod, such as its name and labels. Labels are key-value pairs that can be used to organize and select Pods.

spec: Describes the specification of the Pod.

containers: This is an array of container definitions within the Pod.
name: The name of the container.
image: The container image to be used, specified in the format image:tag.
ports: Optional. Specifies the ports to open on the container.
containerPort: The port number the container is listening on.
This is a minimal example of a pod.yaml file. You can further customize the Pod by adding more properties to the spec section, such as environment variables, volumes, resource requests and limits, security settings, and more, based on your specific requirements.

To create a Pod using this pod.yaml file, you can use the kubectl apply command:
kubectl apply -f pod.yaml


root@ip-172-31-58-195:~# kubectl run pod3 --image=nginx --dry-run=client -o yaml>podbasic.yaml
root@ip-172-31-58-195:~# kubectl apply -f podbasic.yaml
pod/pod3 created
root@ip-172-31-58-195:~# kubectl get pod
NAME   READY   STATUS    RESTARTS   AGE
pod1   1/1     Running   0          7m50s
pod2   1/1     Running   0          5m3s
pod3   1/1     Running   0          9s



//now we have 3 pods access al three pods 
first describe pod and get the ip address of that pod and then curl ipadress

//
root@ip-172-31-58-195:~# kubectl describe pod pod1
Name:             pod1
Namespace:        default
Priority:         0
Service Account:  default
Node:             ip-172-31-58-195/172.31.58.195
Start Time:       Thu, 02 Nov 2023 06:23:49 +0000
Labels:           run=pod1
Annotations:      <none>
Status:           Running
IP:               10.244.0.5
IPs:
  IP:  10.244.0.5
Containers:
  pod1:
    Container ID:   containerd://e36941e6dc10d18bb769b2bda2132693540bb1529f92aff607ab4f3694839031
    Image:          nginx
    Image ID:       docker.io/library/nginx@sha256:775b948ed18e931cfe48c4664387162764dcf286303bbe02bd72703f72f17f02
    Port:           <none>
    Host Port:      <none>
    State:          Running
      Started:      Thu, 02 Nov 2023 06:23:50 +0000
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-f84nl (ro)
Conditions:
  Type              Status
  Initialized       True
  Ready             True
  ContainersReady   True
  PodScheduled      True
Volumes:
  kube-api-access-f84nl:
    Type:                    Projected (a volume that contains injected data from multiple sources)
    TokenExpirationSeconds:  3607
    ConfigMapName:           kube-root-ca.crt
    ConfigMapOptional:       <nil>
    DownwardAPI:             true
QoS Class:                   BestEffort
Node-Selectors:              <none>
Tolerations:                 node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                             node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:
  Type    Reason     Age   From               Message
  ----    ------     ----  ----               -------
  Normal  Scheduled  27m   default-scheduler  Successfully assigned default/pod1 to ip-172-31-58-195
  Normal  Pulling    27m   kubelet            Pulling image "nginx"
  Normal  Pulled     27m   kubelet            Successfully pulled image "nginx" in 279ms (279ms including waiting)
  Normal  Created    27m   kubelet            Created container pod1
  Normal  Started    27m   kubelet            Started container pod1
root@ip-172-31-58-195:~# curl 10.244.0.5
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

//running second pod
root@ip-172-31-58-195:~# kubectl describe pod pod2
Name:             pod2
Namespace:        default
Priority:         0
Service Account:  default
Node:             ip-172-31-58-195/172.31.58.195
Start Time:       Thu, 02 Nov 2023 06:26:36 +0000
Labels:           run=pod2
Annotations:      <none>
Status:           Running
IP:               10.244.0.6
IPs:
  IP:  10.244.0.6
Containers:
  pod2:
    Container ID:   containerd://63b7f37839b4f3863e84a88cbd2627d7b316b218699157c9f7d84c70a54fcbe4
    Image:          nginx
    Image ID:       docker.io/library/nginx@sha256:775b948ed18e931cfe48c4664387162764dcf286303bbe02bd72703f72f17f02
    Port:           <none>
    Host Port:      <none>
    State:          Running
      Started:      Thu, 02 Nov 2023 06:26:37 +0000
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-49nh7 (ro)
Conditions:
  Type              Status
  Initialized       True
  Ready             True
  ContainersReady   True
  PodScheduled      True
Volumes:
  kube-api-access-49nh7:
    Type:                    Projected (a volume that contains injected data from multiple sources)
    TokenExpirationSeconds:  3607
    ConfigMapName:           kube-root-ca.crt
    ConfigMapOptional:       <nil>
    DownwardAPI:             true
QoS Class:                   BestEffort
Node-Selectors:              <none>
Tolerations:                 node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                             node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:
  Type    Reason     Age   From               Message
  ----    ------     ----  ----               -------
  Normal  Scheduled  26m   default-scheduler  Successfully assigned default/pod2 to ip-172-31-58-195
  Normal  Pulling    25m   kubelet            Pulling image "nginx"
  Normal  Pulled     25m   kubelet            Successfully pulled image "nginx" in 187ms (187ms including waiting)
  Normal  Created    25m   kubelet            Created container pod2
  Normal  Started    25m   kubelet            Started container pod2

root@ip-172-31-58-195:~# curl 10.244.0.6
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

//running 3rd pod
root@ip-172-31-58-195:~# kubectl describe pod pod3
Name:             pod3
Namespace:        default
Priority:         0
Service Account:  default
Node:             ip-172-31-58-195/172.31.58.195
Start Time:       Thu, 02 Nov 2023 06:31:31 +0000
Labels:           run=pod3
Annotations:      <none>
Status:           Running
IP:               10.244.0.7
IPs:
  IP:  10.244.0.7
Containers:
  pod3:
    Container ID:   containerd://4f81cc5f097a7a42626c6b4ca5571a01fa5619711a594a94457b356dcfa8a891
    Image:          nginx
    Image ID:       docker.io/library/nginx@sha256:775b948ed18e931cfe48c4664387162764dcf286303bbe02bd72703f72f17f02
    Port:           <none>
    Host Port:      <none>
    State:          Running
      Started:      Thu, 02 Nov 2023 06:31:31 +0000
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-fql9d (ro)
Conditions:
  Type              Status
  Initialized       True
  Ready             True
  ContainersReady   True
  PodScheduled      True
Volumes:
  kube-api-access-fql9d:
    Type:                    Projected (a volume that contains injected data from multiple sources)
    TokenExpirationSeconds:  3607
    ConfigMapName:           kube-root-ca.crt
    ConfigMapOptional:       <nil>
    DownwardAPI:             true
QoS Class:                   BestEffort
Node-Selectors:              <none>
Tolerations:                 node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                             node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:
  Type    Reason     Age   From               Message
  ----    ------     ----  ----               -------
  Normal  Scheduled  21m   default-scheduler  Successfully assigned default/pod3 to ip-172-31-58-195
  Normal  Pulling    21m   kubelet            Pulling image "nginx"
  Normal  Pulled     21m   kubelet            Successfully pulled image "nginx" in 145ms (145ms including waiting)
  Normal  Created    21m   kubelet            Created container pod3
  Normal  Started    21m   kubelet            Started container pod3
root@ip-172-31-58-195:~# curl 10.244.0.7
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



//to access a pod outside of a Kubernetes cluster, you typically need to set up a Service, Ingress, or use a NodePort to expose the pod to external traffic. 
Here's an example of the YAML code for creating a simple deployment, a NodePort Service, and a pod running an Nginx web server to expose it outside the cluster. Please note that this is a simplified example, and you should adapt it to your specific application and cluster configuration.

Create a Deployment:
vim deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
      - name: my-app-container
        image: nginx:latest
        ports:
        - containerPort: 80
        
Apply this Deployment to your cluster:
kubectl apply -f deployment.yaml

Create a NodePort Service to expose the pod:
vim service.yaml

apiVersion: v1
kind: Service
metadata:
  name: my-app-service
spec:
  type: NodePort
  selector:
    app: my-app
  ports:
  - protocol: TCP
    port: 80
    targetPort: 80


Apply this NodePort Service:
kubectl apply -f service.yaml

 
Get the NodePort assigned to the Service:
You can either check the assigned NodePort using the following command:
kubectl get svc

NAME             TYPE       CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
my-app-service   NodePort   10.96.77.154   <none>        80:32722/TCP   44



goto browser
type ip add of instance:port
//if failed go to security group and add inbound rule 
custom tcp 32722(port that you got ) anywhere ipv4
save
and rerun
u will logged in to nginx





