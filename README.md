# Deployement of Flask-App on AMAZON ELASTIC KUBERNETES SERVICE (EKS)

This guide provides step-by-step instructions for deploying a Flask-App on AWS EKS The deployment process involves setting up Docker and Docker Compose, building Docker images, and setting up kubernetes manifests files and creating a EKS cluster.

## Setting Up AWS EKS
1) Launch an AWS EC2 server and connect

2) Install AWS CLI
Install and configure the AWS Command Line Interface (CLI) on your local.

    curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
    sudo apt install unzip
    unzip awscliv2.zip
    sudo ./aws/install -i /usr/local/aws-cli -b /usr/local/bin --update
    
3) Create an IAM Role for EKS management
Create an IAM role with the necessary permissions for EKS.

   STEPS:
   1. Create an IAM User:

      - Go to the AWS IAM console.
      - Create a new IAM user named "eks-admin."
      - Attach the "AdministratorAccess" policy to this user.
    
    2. Create Security Credentials:
       - After creating the user, generate an Access Key and Secret Access Key for this user.
    
4) Launch AWS instance and get access to the instance

5) Configure AWS CLI:

6) Configure the AWS CLI with the Access Key and Secret Access Key from step 3
    
   Commands:
   
       aws configure
   
7) Kubernetes tools setup:
    Install kubectl:
        
        curl -o kubectl https://amazon-eks.s3.us-west-2.amazonaws.com/1.19.6/2021-01-05/bin/linux/amd64/kubectl
        chmod +x ./kubectl
        sudo mv ./kubectl /usr/local/bin
        kubectl version --short –client
       
    Install eksctl:

        curl --silent --location "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp
        sudo mv /tmp/eksctl /usr/local/bin
        eksctl version
        
8) EKS Cluster Setup:
    
   Use eksctl to create the EKS cluster:
      
   Syntax:

   eksctl create cluster --name <cluster-name> --region <region> --node-type <node-type> --nodes-min <size> --nodes-max <size>

       eksctl create cluster --name two-tier --region us-east-1 --node-type t2.micro --nodes-min 2 --nodes-max 2

    
    to delete: syntax:
   
    eksctl delete cluster --name <cluster-name> --region <region>
    
       eksctl delete cluster --name two-tier --region us-east-1
    
    Update your kubeconfig to connect to the newly created EKS cluster:

       aws eks update-kubeconfig --region <region> --name <cluster-name>
    
10) Verify Nodes:

        kubectl get nodes
    
12) Clone the code

13) Apply Manifests:

        cd Flask-App-on-EKS/eks-manifests
        kubectl apply -f mysql-secrets.yml -f mysql-configmap.yml -f mysql-deployment.yml -f mysql-svc.yml
        kubectl apply -f two-tier-app-deployment.yml -f  two-tier-app-svc.yml

14) Get LoadBalancer IP:

        kubectl get svc two-tier-app-service

### Done
Go to the LoadBalancer IP, your app is Running.
<!--
### For CI/CD Pipeline for this app:
Follow django-notes-app repo: https://github.com/acodenil/django-notes-app.git
-->
