# Kubernetes Installation Guide - Ubuntu 22.04


### Step by Step Guides

## Step 1: Install kubectl binary with curl on Linux

Download the latest release with the command:
``` 
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/arm64/kubectl"
```

Validate the binary, Download the kubectl checksum file:
```
   curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/arm64/kubectl.sha256"
```
Validate the kubectl binary against the checksum file:
```
echo "$(cat kubectl.sha256)  kubectl" | sha256sum --check
```

If valid, the output is:

``kubectl: OK``

Install kubectl

``sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl``

Test to ensure the version you installed is up-to-date:

``kubectl version --client``


## Step 2: Install Docker Engine on Ubuntu

Set up Docker's apt repository.

```
# Add Docker's official GPG key:
sudo apt-get update
sudo apt-get install ca-certificates curl gnupg
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg

# Add the repository to Apt sources:
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update

```

Install the Docker packages.

```
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

Verify that the Docker Engine installation is successful by running the hello-world image.

```
sudo docker run hello-world
```
This command downloads a test image and runs it in a container. When the container runs, it prints a confirmation message and exits.



## Step 3: minikube start

minikube is local Kubernetes, focusing on making it easy to learn and develop for Kubernetes.

All you need is Docker (or similarly compatible) container or a Virtual Machine environment, and Kubernetes is a single command away: minikube start


To install the latest minikube stable release on ARM64 Linux using binary download:

```
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-arm64

sudo install minikube-linux-arm64 /usr/local/bin/minikube
```


Start a cluster using the docker driver:

``minikube start --driver=docker``

To make docker the default driver:

``minikube config set driver docker``

If minikube fails to start, try to set permission to docker.sock 

``sudo chmod 666 /var/run/docker.sock``


netcat is needed to check network address, install it with ``sudo apt install netcat``


kubeadm need to be installed to create a new cluster, visit https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/#k8s-install-0


Interact with your cluster
``kubectl get po -A``

You can also make your life easier by adding the following to your shell config: (for more details see: kubectl)

``alias kubectl="minikube kubectl --"``


Open dashboard
``minikube dashboard``


Congrats!!! Kubernetes installed and running on http://127.0.0.1:34307/



### Step 4: Deploy Sample application
Now deploy an example application


Create a sample deployment and expose it on port 8080:

```
kubectl create deployment hello-minikube --image=kicbase/echo-server:1.0

kubectl expose deployment hello-minikube --type=NodePort --port=8080
```

It may take a moment, but your deployment will soon show up when you run:

``kubectl get services hello-minikube``

The easiest way to access this service is to let minikube launch a web browser for you:

``minikube service hello-minikube``

Alternatively, use kubectl to forward the port:

``kubectl port-forward service/hello-minikube 7080:8080``

Tada! Your application is now available at http://localhost:7080/.

You should be able to see the request metadata in the application output. Try changing the path of the request and observe the changes. Similarly, you can do a POST request and observe the body show up in the output.


### Step 5: Managing your cluster
Pause Kubernetes without impacting deployed applications:

``minikube pause``

Unpause a paused instance:

``minikube unpause``

Halt the cluster:

``minikube stop``

Change the default memory limit (requires a restart):

``minikube config set memory 9001``

Browse the catalog of easily installed Kubernetes services:

``minikube addons list``

Create a second cluster running an older Kubernetes release:

``minikube start -p aged --kubernetes-version=v1.16.1``

Delete all of the minikube clusters:

``minikube delete --all``


#
### Resources
https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/

https://docs.docker.com/engine/install/ubuntu/


