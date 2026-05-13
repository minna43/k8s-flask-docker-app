# Kubernetes Self-Hosted Deployment Assignment

## Project Overview
This project demonstrates how to containerize and deploy a simple Python Flask Application on a self-hosted Kubernetes cluster using Docker and Kubernetes.

## Technologies Used
Python Flask - 3.0.3

Docker - 29.1.3

Kubernetes (kubeadm) - 1.34.7

Ubuntu Server - 26.04 LTS

DockerHub 

kubectl - 1.34.7

## Project Structure

├── k8s-flask-docker-app

    ├── flask-docker-api
    
        ├── .dockerignore 
        
        ├── requirements.txt 
        
        ├── app.py
        
        ├── Dockerfile 
        
    ├── k8s
    
        ├──  k8s_app
        
             ├── app.yaml
             
             ├── app_service.yaml
             
        ├──  kubernetes_setup
        
             ├── containerd_install.yml
             
             ├── dist_update_upgrade.yml
             
             ├── docker_install.yml
             
             ├── inventory.ini
             
             ├── kube-playbook.yml
             
             ├── kube_install.yml
             
     ├── README.md
     
## 1. Application & Docker
### Run the below docker commands in Application folder where docker file exist
### Build Docker Image
docker build -t flask-api .
### Run Docker Container Locally
docker run -p 5000:5000 flask-api
### Login to DockerHub
docker login
### Tag image
docker tag flask-api:latest minna43/flask-api:v1
### Push Image to DockerHub
docker push minna43/flask-api:v1

## 2. Self-Hosted Kubernetes Cluster Setup
### Server Requirements
### Ubuntu 26.04 LTS
### Minimum 2 CPU & 4GB RAM
### Docker installed
### Internet access
### Install Docker
### Disable Swap
### Install Kubernetes Components
### Add Kubernetes repository:
### Install kubeadm, kubelet and kubectl:
### In this case you can use Ansible to install and setup k8s so first need to install Ansible and then run playbook command
sudo apt install ansible -y
### Playbook Command
ansible-playbook -i inventory.ini kube-playbook.yml

### Incase any issues related to ssh check ssh service is enabled or not if not installed then install and start ssh server 
sudo apt install -y openssh-server

sudo systemctl start ssh

ssh-keygen -t rsa

ssh-copy-id test@10.0.2.15

### Initialize Kubernetes Cluster
sudo kubeadm init --pod-network-cidr=10.244.0.0/16 
### Configure kubectl
mkdir -p $HOME/.kube

sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config

sudo chown $(id -u):$(id -g) $HOME/.kube/config

### Install Pod Network (Flannel)
kubectl apply -f https://raw.githubusercontent.com/flannel-io/flannel/master/Documentation/kube-flannel.yml
### Verify Cluster Setup
kubectl get nodes

## 3. Kubernetes Deployment
### Use below command to create a registry to connect k8s with your dockerhub account
kubectl create secret docker-registry dockerhub-secret \
  --docker-server=https://index.docker.io/v1/ \
  --docker-username=<username> \
  --docker-password=dckr_pat.... \
  --docker-email=<yourmailid>
### Apply deployment YAML:
kubectl apply -f app.yaml
### Create Service
### Apply service YAML:
kubectl apply -f app_service.yaml
### Verify Pods
kubectl get pods
### Verify Services
kubectl get services
### Access Application
### Get Node IP:
ip addr
### Access application:
http://<SERVER-IP>:<NODEPORT>
http://10.0.2.15:30080

## 4. Basic Security
### SSH Key-Based Authentication
### Generate SSH key on local machine:
ssh-keygen
### Copy public key to server:
ssh-copy-id ubuntu@<server-ip>
### Disable Password Authentication
### Edit SSH configuration:
sudo nano /etc/ssh/sshd_config
### Update the following:
PasswordAuthentication no

PermitRootLogin no
### Restart SSH service:
sudo systemctl restart ssh
<img width="1214" height="726" alt="Screenshot From 2026-05-12 02-14-46" src="https://github.com/user-attachments/assets/5f34d93d-3db3-4300-960d-281a6348bf4b" />
<img width="1213" height="553" alt="Screenshot From 2026-05-12 02-11-46" src="https://github.com/user-attachments/assets/6322de06-8bfb-4efa-8350-9b030fd7f0e2" />
