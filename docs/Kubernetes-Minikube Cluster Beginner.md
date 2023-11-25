#Creating a Kubernetes Cluster with Minikube
**Introduction**

Minikube is a tool that allows you to run Kubernetes clusters on your local machine. It's great for development, testing, and learning Kubernetes concepts. This guide will walk you through the process of setting up a Kubernetes cluster using Minikube.

**Prerequisites**

Hypervisor: Minikube requires a hypervisor to run the virtual machines. Common choices include VirtualBox, Hyper-V, KVM, or Docker. Ensure your preferred hypervisor is installed and properly configured.We use Docker for that 

Docker | installation guide [Click Here](https://docs.docker.com/engine/install/ubuntu/)


kubectl: The kubectl command-line tool is used to interact with your Kubernetes cluster. Make sure it's installed on your machine.

Kubectl | installation guide [Click Here](https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/)

###**Install Minikube**


Visit the Minikube Installation Guide and follow the instructions for your operating system to install Minikube.

Minikube | installation guide [Click Here](https://minikube.sigs.k8s.io/docs/start/)

###**Start Minikube**



Open a terminal and run the following command to start Minikube:

```minikube start```


This command will download the Minikube ISO, start a virtual machine, and set up the Kubernetes cluster.

###**Verify Cluster Status**



After Minikube has started, you can check the cluster status using:


```kubectl cluster-info```


This command will display information about the cluster, including the Kubernetes master and services.

###**Check Nodes**




Verify that Minikube has created a node for your cluster:


```kubectl get nodes```


This command should show the Minikube node with a status of Ready.

###**Kubernetes Dashboard (Optional)**




If you want to use the Kubernetes Dashboard, you can start it with:


```minikube dashboard```


This will open a web browser with the Kubernetes Dashboard, providing a visual interface to manage your cluster.

###**Interact with Kubernetes**



Now that your Minikube cluster is running, you can start deploying applications and managing your Kubernetes cluster using kubectl.

###Example Deployment

As a simple test, let's deploy a sample Nginx application. Save the following YAML as **nginx-deployment.yaml:**

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx
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
```
**Apply the configuration:**


```kubectl apply -f nginx-deployment.yaml```

This will deploy two replicas of the Nginx web server.

###Conclusion


Congratulations! You've successfully created a Kubernetes cluster using Minikube and deployed a sample application. This is just the beginning—explore more Kubernetes features and concepts as you continue your journey with container orchestration.

