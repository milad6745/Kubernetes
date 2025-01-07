How To Setup Kubernetes Cluster Using Kubeadm
by
Bibin Wilson
March 17, 2024
Kubeadm cluster setup guide
In this blog post, I have covered the step-by-step guide to setting up a kubernetes cluster using Kubeadm with one master and two worker nodes.

Kubeadm is an excellent tool to set up a working kubernetes cluster in less time. It does all the heavy lifting in terms of setting up all kubernetes cluster components. Also, It follows all the configuration best practices for a kubernetes cluster.

Table of Contents  show 
What is Kubeadm?
Kubeadm is a tool to set up a minimum viable Kubernetes cluster without much complex configuration. Also, Kubeadm makes the whole process easy by running a series of prechecks to ensure that the server has all the essential components and configs to run Kubernetes.

It is developed and maintained by the official Kubernetes community. There are other options like minikube, kind, etc., that are pretty easy to set up. You can check out my minikube tutorial. Those are good options with minimum hardware requirements if you are deploying and testing applications on Kubernetes.

But if you want to play around with the cluster components or test utilities that are part of cluster administration, Kubeadm is the best option. Also, you can create a production-like cluster locally on a workstation for development and testing purposes.

📌 Looking for an organized way to learn Kubernetes and prepare for the CKA exam ? Check out our CKA course and practice test bundle (use code DCUBE25 to get 25% OFF). We explain concepts using illustrations, hands-on exercises, real-world examples, and provide dedicated discord based user support.

Kubeadm Setup Prerequisites
Following are the prerequisites for Kubeadm Kubernetes cluster setup.

Minimum two Ubuntu nodes [One master and one worker node]. You can have more worker nodes as per your requirement.
The master node should have a minimum of 2 vCPU and 2GB RAM.
For the worker nodes, a minimum of 1vCPU and 2 GB RAM is recommended.
10.X.X.X/X network range with static IPs for master and worker nodes. We will be using the 192.x.x.x series as the pod network range that will be used by the Calico network plugin. Make sure the Node IP range and pod IP range don’t overlap.
Note: If you are setting up the cluster in the corporate network behind a proxy, ensure set the proxy variables and have access to the container registry and docker hub. Or talk to your network administrator to whitelist registry.k8s.io to pull the required images.

Kubeadm Port Requirements
Please refer to the following image and make sure all the ports are allowed for the control plane (master) and the worker nodes. If you are setting up the kubeadm cluster cloud servers, ensure you allow the ports in the firewall configuration.

Kubeadm kubernetes cluster port requirements
If you are using vagrant-based Ubuntu VMs, the firewall will be disabled by default. So you don’t have to do any firewall configurations.

Kubeadm for Kubernetes Certification Exams
If you are preparing for Kubernetes certifications like CKA, CKAD, or CKS, you can use the local kubeadm clusters to practice for the certification exam. In fact, kubeadm itself is part of the CKA and CKS exam. For CKA you might be asked to bootstrap a cluster using Kubeadm. For CKS, you have to upgrade the cluster using kubeadm.

If you use Vagrant-based VMs on your workstation, you can start and stop the cluster whenever you need. By having the local Kubeadm clusters, you can play around with all the cluster configurations and learn to troubleshoot different components in the cluster.

Important Note: If you are planning for Kubernetes certification, make use of the CKA/CKAD/CKS coupon Codes before the price increases.

Vagrantfile, Kubeadm Scripts & Manifests
Also, all the commands used in this guide for master and worker nodes config are hosted in GitHub. You can clone the repository for reference.

git clone https://github.com/techiescamp/kubeadm-scripts
This guide intends to make you understand each config required for the Kubeadm setup. If you don’t want to run the commands one by one, you can run the script file directly.

If you are using Vagrant to set up the Kubernetes cluster, you can make use of my Vagrantfile. It launches 3 VMs. A self-explanatory basic Vagrantfile. If you are new to Vagrant, check the Vagrant tutorial.

If you are a Terraform and AWS user, you can make use of the Terraform script present under the Terraform folder to spin up ec2 instances.

Also, I have created a video demo of the whole kubeadm setup. You can refer to it during the setup.

Setup Kubernetes Cluster Using Kubeadm [Multi-node]
Kubernetes Cluster Setup Using Kubeadm
Following are the high-level steps involved in setting up a kubeadm-based Kubernetes cluster.

Install container runtime on all nodes- We will be using cri-o.
Install Kubeadm, Kubelet, and kubectl on all the nodes.
Initiate Kubeadm control plane configuration on the master node.
Save the node join command with the token.
Install the Calico network plugin (operator).
Join the worker node to the master node (control plane) using the join command.
Validate all cluster components and nodes.
Install Kubernetes Metrics Server
Deploy a sample app and validate the app
All the steps given in this guide are referred from the official Kubernetes documentation and related GitHub project pages.

If you want to understand every cluster component in detail, refer to the comprehensive Kubernetes Architecture.

Now let’s get started with the setup.

Step 1: Enable iptables Bridged Traffic on all the Nodes
Execute the following commands on all the nodes for IPtables to see bridged traffic. Here we are tweaking some kernel parameters and setting them using sysctl.

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
Step 2: Disable swap on all the Nodes
For kubeadm to work properly, you need to disable swap on all the nodes using the following command.

sudo swapoff -a
(crontab -l 2>/dev/null; echo "@reboot /sbin/swapoff -a") | crontab - || true
The fstab entry will make sure the swap is off on system reboots.

You can also, control swap errors using the kubeadm parameter --ignore-preflight-errors Swap we will look at it in the latter part.

Note: From 1.28 kubeadm has beta support for using swap with kubeadm clusters. Read this to understand more.

Step 3: Install CRI-O Runtime On All The Nodes
Note: We are using cri-o instead if containerd because, in Kubernetes certification exams, cri-o is used as the container runtime in the exam clusters.

The basic requirement for a Kubernetes cluster is a container runtime. You can have any one of the following container runtimes.

CRI-O
containerd
Docker Engine (using cri-dockerd)
We will be using CRI-O instead of Docker for this setup as Kubernetes deprecated Docker engine

Execute the following commands on all the nodes to install required dependencies and the latest version of CRIO.


sudo apt-get update -y
sudo apt-get install -y software-properties-common gpg curl apt-transport-https ca-certificates

curl -fsSL https://pkgs.k8s.io/addons:/cri-o:/prerelease:/main/deb/Release.key |
    gpg --dearmor -o /etc/apt/keyrings/cri-o-apt-keyring.gpg
echo "deb [signed-by=/etc/apt/keyrings/cri-o-apt-keyring.gpg] https://pkgs.k8s.io/addons:/cri-o:/prerelease:/main/deb/ /" |
    tee /etc/apt/sources.list.d/cri-o.list

sudo apt-get update -y
sudo apt-get install -y cri-o

sudo systemctl daemon-reload
sudo systemctl enable crio --now
sudo systemctl start crio.service
Install crictl.

VERSION="v1.30.0"
wget https://github.com/kubernetes-sigs/cri-tools/releases/download/$VERSION/crictl-$VERSION-linux-amd64.tar.gz
sudo tar zxvf crictl-$VERSION-linux-amd64.tar.gz -C /usr/local/bin
rm -f crictl-$VERSION-linux-amd64.tar.gz
crictl, a CLI utility to interact with the containers created by the container runtime.

When you use container runtimes other than Docker, you can use the crictl utility to debug containers on the nodes. Also, it is useful in CKS certification where you need to debug containers.

Step 4: Install Kubeadm & Kubelet & Kubectl on all Nodes
Download the GPG key for the Kubernetes APT repository on all the nodes.

KUBERNETES_VERSION=1.30

sudo mkdir -p /etc/apt/keyrings
curl -fsSL https://pkgs.k8s.io/core:/stable:/v$KUBERNETES_VERSION/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
echo "deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v$KUBERNETES_VERSION/deb/ /" | sudo tee /etc/apt/sources.list.d/kubernetes.list
Update apt repo

sudo apt-get update -y
Note: If you are preparing for Kubernetes certification, install the specific version of kubernetes. For example, the current Kubernetes version for CKA, CKAD and CKS exams is Kubernetes version 1.30

You can use the following commands to find the latest versions. Install the first version in 1.30 so that you can practice cluster upgrade task.

apt-cache madison kubeadm | tac
Specify the version as shown below. Here I am using 1.30.0-1.1

sudo apt-get install -y kubelet=1.30.0-1.1 kubectl=1.30.0-1.1 kubeadm=1.30.0-1.1
Or, to install the latest version from the repo use the following command without specifying any version.

sudo apt-get install -y kubelet kubeadm kubectl
Add hold to the packages to prevent upgrades.

sudo apt-mark hold kubelet kubeadm kubectl
Now we have all the required utilities and tools for configuring Kubernetes components using kubeadm.

Add the node IP to KUBELET_EXTRA_ARGS.

sudo apt-get install -y jq
local_ip="$(ip --json addr show eth0 | jq -r '.[0].addr_info[] | select(.family == "inet") | .local')"
cat > /etc/default/kubelet << EOF
KUBELET_EXTRA_ARGS=--node-ip=$local_ip
EOF
Step 5: Initialize Kubeadm On Master Node To Setup Control Plane
Here you need to consider two options.

Master Node with Private IP: If you have nodes with only private IP addresses the API server would be accessed over the private IP of the master node.
Master Node With Public IP: If you are setting up a Kubeadm cluster on Cloud platforms and you need master Api server access over the Public IP of the master node server.
Only the Kubeadm initialization command differs for Public and Private IPs.

Execute the commands in this section only on the master node.

If you are using a Private IP for the master Node,

Set the following environment variables. Replace 10.0.0.10 with the IP of your master node.

IPADDR="10.0.0.10"
NODENAME=$(hostname -s)
POD_CIDR="192.168.0.0/16"
If you want to use the Public IP of the master node,

Set the following environment variables. The IPADDR variable will be automatically set to the server’s public IP using ifconfig.me curl call. You can also replace it with a public IP address

IPADDR=$(curl ifconfig.me && echo "")
NODENAME=$(hostname -s)
POD_CIDR="192.168.0.0/16"
Now, initialize the master node control plane configurations using the kubeadm command.

For a Private IP address-based setup use the following init command.

sudo kubeadm init --apiserver-advertise-address=$IPADDR  --apiserver-cert-extra-sans=$IPADDR  --pod-network-cidr=$POD_CIDR --node-name $NODENAME --ignore-preflight-errors Swap
--ignore-preflight-errors Swap is actually not required as we disabled the swap initially.

For Public IP address-based setup use the following init command.

Here instead of --apiserver-advertise-address we use --control-plane-endpoint parameter for the API server endpoint.

sudo kubeadm init --control-plane-endpoint=$IPADDR  --apiserver-cert-extra-sans=$IPADDR  --pod-network-cidr=$POD_CIDR --node-name $NODENAME --ignore-preflight-errors Swap
All the other steps are the same as configuring the master node with private IP.

On a successful kubeadm initialization, you should get an output with kubeconfig file location and the join command with the token as shown below. Copy that and save it to the file. we will need it for joining the worker node to the master.

Kubeadm init command output
Use the following commands from the output to create the kubeconfig in master so that you can use kubectl to interact with cluster API.

mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
Now, verify the kubeconfig by executing the following kubectl command to list all the pods in the kube-system namespace.

kubectl get po -n kube-system
You should see the following output. You will see the two Coredns pods in a pending state. It is the expected behavior. Once we install the network plugin, it will be in a running state.


You verify all the cluster component health statuses using the following command.

kubectl get --raw='/readyz?verbose'
You can get the cluster info using the following command.

kubectl cluster-info 
By default, apps won’t get scheduled on the master node. If you want to use the master node for scheduling apps, taint the master node.

kubectl taint nodes --all node-role.kubernetes.io/control-plane-
Note: You can also pass the kubeadm configs as a file when initializing the cluster. See Kubeadm Init with config file

Step 6: Join Worker Nodes To Kubernetes Master Node
We have set up cri-o, kubelet, and kubeadm utilities on the worker nodes as well.

Now, let’s join the worker node to the master node using the Kubeadm join command you have got in the output while setting up the master node.

If you missed copying the join command, execute the following command in the master node to recreate the token with the join command.

kubeadm token create --print-join-command
Here is what the command looks like. Use sudo if you running as a normal user. This command performs the TLS bootstrapping for the nodes.

sudo kubeadm join 10.128.0.37:6443 --token j4eice.33vgvgyf5cxw4u8i \
    --discovery-token-ca-cert-hash sha256:37f94469b58bcc8f26a4aa44441fb17196a585b37288f85e22475b00c36f1c61
On successful execution, you will see the output saying, “This node has joined the cluster”.

kubeadm node join output.
Now execute the kubectl command from the master node to check if the node is added to the master.

kubectl get nodes
Example output,

root@controlplane:~# kubectl get nodes

NAME           STATUS   ROLES           AGE     VERSION
controlplane   Ready    control-plane   8m42s   v1.29.0
node01         Ready    worker          2m6s    v1.29.0
In the above command, the ROLE is <none> for the worker nodes. You can add a label to the worker node using the following command. Replace worker-node01 with the hostname of the worker node you want to label.

kubectl label node node01  node-role.kubernetes.io/worker=worker
You can further add more nodes with the same join command.

Step 7: Install Calico Network Plugin for Pod Networking
Kubeadm does not configure any network plugin. You need to install a network plugin of your choice for kubernetes pod networking and enable network policy.

I am using the Calico network plugin for this setup.

Note: Make sure you execute the kubectl command from where you have configured the kubeconfig file. Either from the master of your workstation with the connectivity to the kubernetes API.

Execute the following commands to install the Calico network plugin operator on the cluster.

kubectl apply -f https://docs.projectcalico.org/manifests/calico.yaml
After a couple of minutes, if you check the pods in kube-system namespace, you will see calico pods and running CoreDNS pods.

kubectl get po -n kube-system
Kubeadm calico and coreDNS pods in running state.
Step 8: Setup Kubernetes Metrics Server
Kubeadm doesn’t install metrics server component during its initialization. We have to install it separately.

To verify this, if you run the top command, you will see the Metrics API not available error.

root@controlplane:~# kubectl top nodes

error: Metrics API not available
To install the metrics server, execute the following metric server manifest file. It deploys metrics server version v0.7.1

kubectl apply -f https://raw.githubusercontent.com/techiescamp/kubeadm-scripts/main/manifests/metrics-server.yaml
This manifest is taken from the official metrics server repo. I have added the --kubelet-insecure-tls flag to the container to make it work in the local setup and hosted it separately. Or else, you will get the following error.

 because it doesn't contain any IP SANs" node=""
Once the metrics server objects are deployed, it takes a minute for you to see the node and pod metrics using the top command.

kubectl top nodes
You should be able to view the node metrics as shown below.

root@controlplane:~# kubectl top nodes

NAME           CPU(cores)   CPU%   MEMORY(bytes)   MEMORY%
controlplane   142m         7%     1317Mi          34%
node01         36m          1%     915Mi           23%
You can also view the pod CPU and memory metrics using the following command.

kubectl top pod -n kube-system
Step 9: Deploy A Sample Nginx Application
Now that we have all the components to make the cluster and applications work, let’s deploy a sample Nginx application and see if we can access it over a NodePort

Create an Nginx deployment. Execute the following directly on the command line. It deploys the pod in the default namespace.

cat <<EOF | kubectl apply -f -
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  selector:
    matchLabels:
      app: nginx
  replicas: 2 
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:latest
        ports:
        - containerPort: 80      
EOF
Expose the Nginx deployment on a NodePort 32000

cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  selector: 
    app: nginx
  type: NodePort  
  ports:
    - port: 80
      targetPort: 80
      nodePort: 32000
EOF
Check the pod status using the following command.

kubectl get pods
Once the deployment is up, you should be able to access the Nginx home page on the allocated NodePort.

For example,

kubeadm Nnginx test deployment
Step 10: Add Kubeadm Config to Workstation
If you prefer to connect the Kubeadm cluster using kubectl from your workstation, you can merge the kubeadm admin.conf with your existing kubeconfig file.

Follow the steps given below for the configuration.

Step 1: Copy the contents of admin.conf from the control plane node and save it in a file named kubeadm-config.yaml in your workstation.

Step 2: Take a backup of the existing kubeconfig.

cp ~/.kube/config ~/.kube/config.bak
Step 3: Merge the default config with kubeadm-config.yaml and export it to KUBECONFIG variable

export KUBECONFIG=~/.kube/config:/path/to/kubeadm-config.yaml
Step 4: Merger the configs to a file

kubectl config view --flatten > ~/.kube/merged_config.yaml
Step 5: Replace the old config with the new config

mv ~/.kube/merged_config.yaml ~/.kube/config
Step 6: List all the contexts

kubectl config get-contexts -o name
Step 7: Set the current context to the kubeadm cluster.

kubectl config use-context <cluster-name-here>
Now, you should be able to connect to the Kubeadm cluster from your local workstation kubectl utility.

Possible Kubeadm Issues
Following are the possible issues you might encounter in the kubeadm setup.

Pod Out of memory and CPU: The master node should have a minimum of 2vCPU and 2 GB memory.
Nodes cannot connect to Master: Check the firewall between nodes and make sure all the nodes can talk to each other on the required kubernetes ports.
Calico Pod Restarts: Sometimes, if you use the same IP range for the node and pod network, Calico pods may not work as expected. So make sure the node and pod IP ranges don’t overlap. Overlapping IP addresses could result in issues for other applications running on the cluster as well.
For other pod errors, check out the kubernetes pod troubleshooting guide.

If your server doesn’t have a minimum of 2 vCPU, you will get the following error.

[ERROR NumCPU]: the number of available CPUs 1 is less than the required 2
If you use a public IP with --apiserver-advertise-address parameter, you will have failed master node components with the following error. To rectify this error, use --control-plane-endpoint parameter with the public IP address.

kubelet-check] Initial timeout of 40s passed.


Unfortunately, an error has occurred:
        timed out waiting for the condition

This error is likely caused by:
        - The kubelet is not running
        - The kubelet is unhealthy due to a misconfiguration of the node in some way (required cgroups disabled)

If you are on a systemd-powered system, you can try to troubleshoot the error with the following commands:
        - 'systemctl status kubelet'
        - 'journalctl -xeu kubelet'
You will get the following error in worker nodes when you try to join a worker node with a new token after the master node reset. To rectify this error, reset the worker node using the command kubeadm reset.

[ERROR FileAvailable--etc-kubernetes-kubelet.conf]: /etc/kubernetes/kubelet.conf already exists
        [ERROR Port-10250]: Port 10250 is in use
        [ERROR FileAvailable--etc-kubernetes-pki-ca.crt]: /etc/kubernetes/pki/ca.crt already exists
