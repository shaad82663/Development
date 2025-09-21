# Kubernetes Basics: A Complete Tutorial

Kubernetes (K8s) is often called the "operating system of the cloud," as it manages a cluster of machines that communicate to run applications efficiently. This tutorial provides a beginner-friendly introduction to Kubernetes, covering its architecture, components, and a step-by-step guide to setting up and deploying a simple application. It’s designed for backend developers familiar with Node.js, Docker, and Vagrant, with practical examples to help you understand and use Kubernetes effectively.

## What is Kubernetes?

Kubernetes is an open-source platform for automating the deployment, scaling, and management of containerized applications across multiple machines (virtual or physical). It solves the problem of orchestrating containers (e.g., Docker containers) across multiple virtual machines (VMs) or servers, ensuring they communicate, scale, and recover from failures seamlessly.

- **Why Kubernetes?**
  - Manages multiple containers across multiple VMs or servers.
  - Handles load balancing, scaling, and self-healing (e.g., restarting failed containers).
  - Simplifies deployment of complex applications, like a Node.js app with a MySQL database.

- **Key Idea**: Your notes mention Docker solving the OS problem by running multiple containers in a VM, and Kubernetes managing multiple VMs. Kubernetes acts as a layer above Docker, coordinating containers across a cluster of machines.

## Kubernetes Architecture

Kubernetes follows a **master-worker architecture**, where a control plane (master) manages worker nodes that run your applications. Below are the main components and their roles.

### 1. Cluster
A Kubernetes cluster is a set of machines (nodes) that work together to run containerized applications. It consists of:
- **Control Plane (Master)**: Manages the cluster, scheduling tasks, and maintaining state.
- **Worker Nodes**: Run the actual application containers.

### 2. Control Plane Components
The control plane manages the cluster and includes:

- **API Server (kube-apiserver)**:
  - The main interface for communication, accepting commands from users (via `kubectl`) or other components.
  - Example: You send a command to create a pod, and the API server processes it.

- **etcd**:
  - A distributed key-value store that holds the cluster’s state (e.g., pod configurations, node status).
  - Think of it as the database for Kubernetes.

- **Scheduler (kube-scheduler)**:
  - Assigns pods to worker nodes based on resource availability, constraints, and policies.
  - Example: Places a Node.js app pod on a node with enough CPU and memory.

- **Controller Manager (kube-controller-manager)**:
  - Runs controllers that maintain the desired state (e.g., ensuring the correct number of pod replicas).
  - Example: If a pod crashes, the controller creates a new one.

- **Cloud Controller Manager** (optional):
  - Integrates with cloud providers (e.g., AWS, GCP) for resources like load balancers.

### 3. Worker Node Components
Each worker node runs your application containers and includes:

- **Kubelet**:
  - An agent that communicates with the control plane and manages pods on the node.
  - Ensures containers in a pod are running and healthy.

- **Kube-Proxy**:
  - Manages networking, routing traffic to pods and services.
  - Example: Directs HTTP requests to a Node.js app pod.

- **Container Runtime**:
  - Software (e.g., Docker, containerd) that runs containers.
  - Your notes highlight Docker as the runtime for containers in a VM.

- **Pods**:
  - The smallest deployable unit in Kubernetes, containing one or more containers that share resources (e.g., network, storage).
  - Example: A pod might contain a Node.js app container and a sidecar container for logging.

### 4. Other Key Concepts
- **Service**:
  - Provides a stable endpoint (IP or DNS) to access a set of pods, enabling load balancing.
  - Example: A service routes traffic to multiple Node.js app pods.

- **Deployment**:
  - Manages pods and ensures the desired number are running, handling updates and rollbacks.
  - Example: Deploy three replicas of a MySQL pod.

- **ConfigMap and Secret**:
  - Store configuration data (e.g., environment variables) and sensitive data (e.g., database passwords), respectively.

- **Namespace**:
  - Divides a cluster into virtual sub-clusters for organization (e.g., separate dev and prod environments).

## Kubernetes Workflow

Here’s how Kubernetes works to deploy and manage an application:

1. **You Define the Desired State**:
   - Write YAML files specifying pods, deployments, or services (e.g., a Node.js app with three replicas).
   - Example YAML for a Node.js app:
   ```yaml
   apiVersion: apps/v1
   kind: Deployment
   metadata:
     name: nodejs-app
   spec:
     replicas: 3
     selector:
       matchLabels:
         app: nodejs
     template:
       metadata:
         labels:
           app: nodejs
       spec:
         containers:
         - name: nodejs
           image: node:18
           ports:
           - containerPort: 3000
   ```

2. **Submit to API Server**:
   - Use `kubectl apply -f file.yaml` to send the YAML to the API server.
   - The API server stores the configuration in `etcd`.

3. **Scheduler Assigns Pods**:
   - The scheduler places pods on worker nodes based on resource needs and constraints.

4. **Kubelet Runs Containers**:
   - The kubelet on each node pulls container images (e.g., from Docker Hub) and starts them.

5. **Kube-Proxy Manages Networking**:
   - Routes traffic to pods, ensuring clients can access your app.

6. **Controllers Maintain State**:
   - If a pod crashes, the controller manager creates a new one to match the desired state.

7. **Access the App**:
   - Use a service to expose the app (e.g., via a load balancer or port forwarding).

## Step-by-Step Tutorial: Setting Up a Kubernetes Cluster

This section guides you through setting up a local Kubernetes cluster using Minikube, deploying a Node.js app, and exposing it via a service. Minikube is ideal for learning, as it runs a single-node cluster on your machine.

### Prerequisites
- **Docker**: Installed to run containers (your notes mention Docker as the container runtime).
- **Minikube**: For running a local Kubernetes cluster.
- **kubectl**: The Kubernetes command-line tool.
- **Vagrant** (optional): For managing VMs, as you’re familiar with Vagrant (from September 21, 2025).

### Step 1: Install Tools
1. **Install Docker**:
   - On macOS/Linux:
     ```bash
     brew install docker
     ```
   - On CentOS (from your Vagrant notes):
     ```bash
     sudo yum install docker
     sudo systemctl enable docker
     sudo systemctl start docker
     ```

2. **Install Minikube**:
   ```bash
   brew install minikube
   ```

3. **Install kubectl**:
   ```bash
   brew install kubectl
   ```

### Step 2: Start Minikube
- Start a local Kubernetes cluster:
  ```bash
  minikube start --driver=docker
  ```
- Verify the cluster:
  ```bash
  kubectl get nodes
  ```
  - Output: Shows one node (Minikube).

### Step 3: Create a Node.js App
- Create a simple Node.js app (`server.js`):
  ```javascript
  const http = require('http');
  const server = http.createServer((req, res) => {
    res.writeHead(200, { 'Content-Type': 'text/plain' });
    res.end('Hello from Kubernetes!');
  });
  server.listen(3000, () => console.log('Server running on port 3000'));
  ```

- Create a `Dockerfile`:
  ```dockerfile
  FROM node:18
  WORKDIR /app
  COPY server.js .
  CMD ["node", "server.js"]
  ```

- Build and push the Docker image:
  ```bash
  docker build -t my-nodejs-app:latest .
  docker tag my-nodejs-app:latest myusername/my-nodejs-app:latest
  docker push myusername/my-nodejs-app:latest
  ```
  - Replace `myusername` with your Docker Hub username.

### Step 4: Deploy the App to Kubernetes
- Create a deployment YAML (`nodejs-deployment.yaml`):
  ```yaml
  apiVersion: apps/v1
  kind: Deployment
  metadata:
    name: nodejs-app
  spec:
    replicas: 3
    selector:
      matchLabels:
        app: nodejs
    template:
      metadata:
        labels:
          app: nodejs
      spec:
        containers:
        - name: nodejs
          image: myusername/my-nodejs-app:latest
          ports:
          - containerPort: 3000
  ```

- Apply the deployment:
  ```bash
  kubectl apply -f nodejs-deployment.yaml
  ```

- Verify pods:
  ```bash
  kubectl get pods
  ```

### Step 5: Expose the App
- Create a service YAML (`nodejs-service.yaml`):
  ```yaml
  apiVersion: v1
  kind: Service
  metadata:
    name: nodejs-service
  spec:
    selector:
      app: nodejs
    ports:
    - protocol: TCP
      port: 80
      targetPort: 3000
    type: LoadBalancer
  ```

- Apply the service:
  ```bash
  kubectl apply -f nodejs-service.yaml
  ```

- Get the service URL:
  ```bash
  minikube service nodejs-service --url
  ```
  - Output: A URL like `http://192.168.49.2:xxxxx`. Open it in a browser to see “Hello from Kubernetes!”.

### Step 6: Scale and Update
- Scale the deployment to 5 replicas:
  ```bash
  kubectl scale deployment nodejs-app --replicas=5
  ```

- Update the app (e.g., change the Docker image version):
  ```bash
  kubectl set image deployment/nodejs-app nodejs=myusername/my-nodejs-app:new-version
  ```

### Step 7: Clean Up
- Delete the deployment and service:
  ```bash
  kubectl delete -f nodejs-deployment.yaml
  kubectl delete -f nodejs-service.yaml
  ```

- Stop Minikube:
  ```bash
  minikube stop
  ```

## Using Vagrant with Kubernetes (Optional)
Since you’re familiar with Vagrant (from September 21, 2025), you can set up a multi-node Kubernetes cluster using Vagrant for testing.

- **Vagrantfile Example**:
  ```ruby
  Vagrant.configure("2") do |config|
    config.vm.box = "spox/ubuntu-arm"
    config.vm.provider "vmware_fusion" do |v|
      v.memory = 2048
      v.cpus = 2
    end

    # Master Node
    config.vm.define "master" do |master|
      master.vm.hostname = "k8s-master"
      master.vm.network "private_network", ip: "192.168.56.10"
      master.vm.provision "shell", inline: <<-SHELL
        curl -sfL https://get.k3s.io | sh -  # Install lightweight K3s (Kubernetes)
        kubectl get nodes
      SHELL
    end

    # Worker Node
    config.vm.define "worker" do |worker|
      worker.vm.hostname = "k8s-worker"
      worker.vm.network "private_network", ip: "192.168.56.11"
      worker.vm.provision "shell", inline: <<-SHELL
        curl -sfL https://get.k3s.io | K3S_URL=https://192.168.56.10:6443 K3S_TOKEN=$(cat /vagrant/node-token) sh -
      SHELL
    end

    config.vm.synced_folder ".", "/vagrant"
  ```

- Start the cluster:
  ```bash
  vagrant up
  ```

- SSH into the master and deploy your app:
  ```bash
  vagrant ssh master
  kubectl apply -f /vagrant/nodejs-deployment.yaml
  ```

## Key Points
- **Pod**: Smallest unit, containing one or more containers (e.g., a Node.js app and a logging container).
- **Master-Based Architecture**: Control plane manages worker nodes via the API server, scheduler, and controllers.
- **Workflow**: Define YAML, apply with `kubectl`, scheduler assigns pods, kubelet runs containers, and services route traffic.
- **Scalability**: Kubernetes automatically scales pods and balances loads.
- **Self-Healing**: Restarts failed pods and reschedules them on healthy nodes.

## Troubleshooting Tips
- **Pod Not Running**: Check logs with `kubectl logs <pod-name>`.
- **Service Not Accessible**: Verify the service type and port with `kubectl describe service nodejs-service`.
- **Minikube Issues**: Ensure Docker is running and Minikube has enough resources (`minikube config set memory 4096`).
- **Vagrant Errors**: Confirm the box exists (`vagrant box list`) and synced folders are accessible.

## Further Reading
- [Kubernetes Documentation](https://kubernetes.io/docs/home/)
- [Minikube Tutorial](https://minikube.sigs.k8s.io/docs/start/)
- [K3s Lightweight Kubernetes](https://k3s.io/)
- [kubectl Cheat Sheet](https://kubernetes.io/docs/reference/kubectl/cheatsheet/)