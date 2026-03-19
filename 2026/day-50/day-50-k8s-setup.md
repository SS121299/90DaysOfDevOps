Q:- Why was Kubernetes created? What problem does it solve that Docker alone cannot? 
Docker gave us containers… but only on a single machine.
Now imagine this situation:
·	You have 200 containers
·	Running on 10 servers
·	Some crash 
·	Traffic spikes 
·	You need updates without downtime
Docker alone says:
 “I can run containers.”
But it doesn’t answer:
·	Where should containers run?
·	What if a container dies?
·	How to scale automatically?
·	How to expose services to users?

Problem Kubernetes solves:
Kubernetes was created to manage:
·	Container orchestration
·	Auto-healing (restart failed containers)
·	Auto-scaling
·	Load balancing
·	Rolling updates (zero downtime)
👉 In short:
 Docker = engine 🚗
 Kubernetes = traffic system + autopilot + city planner 

Q:-Who created Kubernetes and what was it inspired by? 
Kubernetes was created by eng at Google 
Inspired by internal google system Borg and this is open source for everyone Later, it was donated to: CNCF(cloud native computing foundation)

Q:-What does the name "Kubernetes" mean?

“Kubernetes” = “Helmsman” or “Ship Pilot” 
Think:
·	Containers = ships
·	Cluster = ocean 
·	Kubernetes = the one steering everything
K8s


Draw Kubernetes Archetecture:

API Server: Every command and intruction goes through it
Scheduler: decide which node a new pods should run on
Etcd: the database which store all cluster state
Controller Manager: maintain desired state ex: you say i want 3 pods , it ensure 3 are always running 

Worker Node-
Kubelet: Talk to control panel , Ensure container are running properly
Kube-proxy: Handles networking , Routes traffic to correct pods
Pods: Smallest unit, Contains 1 or more containers where you app is run.


What happens when you run kubectl apply -f pod.yaml? Trace the request through each component.
kubectl apply -f pod.yaml
kubectl → API Server
kubectl sends an HTTP request
API Server is the front door 
“Here’s my desired state: create this Pod”
2. API Server → etcd
Validates YAML (syntax + auth)
Stores it in etcd
 Now Kubernetes remembers your request 
3. Scheduler wakes up
Sees: “New Pod without a node 
Chooses best worker node based on:
CPU
Memory
Availability
Assigns Pod → Node
4. API Server updates state
Pod is now “scheduled”
5. Kubelet (on that node)
Watches API Server
Sees: “Pod assigned to me”
“Got it, I’ll run it” 
6. Container Runtime
Pulls image (nginx, node, etc.)
Starts container inside a Pod
7. Kube-proxy
Sets up networking
Makes pod reachable (internally)

What happens if the API server goes down?
This is like the control tower going silent 
Ø	What stops:
No kubectl commands
No new Pods
No scaling / updates
Ø	 What keeps working:
Already running containers keep running
Kubelet continues managing existing pods
Ø	 Insight:
Kubernetes becomes frozen but not dead
Ø	 That’s why in production:
Multiple API servers (HA setup) are used

What happens if a Worker Node goes down?

Step 1: Control Plane detects issue
Kubelet stops sending heartbeats 
Node marked: NotReady
Step 2: Pods on that node die
All containers on that node are gone
Step 3: Controller reacts
“I need 3 replicas… now only 2 exist!”
 It creates new Pods on other healthy nodes
 Final Result:
Pods are recreated automatically 
System heals itself


Install Kubectl 
# Linux (amd64)
curl -LO "https://dl.k8s.io/release/$(curl -L -s  https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
chmod +x kubectl
sudo mv kubectl /usr/local/bin/

Verify:
kubectl version --client

Set up your local Cluster
curl -Lo ./kind https://kind.sigs.k8s.io/dl/latest/kind-linux-amd64
chmod +x ./kind
sudo mv ./kind /usr/local/bin/kind

# Create a cluster
kind create cluster --name devops-cluster

# Verify
kubectl cluster-info
kubectl get nodes




# Get detailed info about your node
kubectl describe node <node-name>

# List all namespaces
kubectl get namespaces

# See ALL pods running in the cluster (across all namespaces)
kubectl get pods -A

# Delete your cluster
kind delete cluster --name devops-cluster
