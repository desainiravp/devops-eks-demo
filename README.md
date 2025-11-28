# Jump host EC2 Creation
curl -o kubectl https://amazon-eks.s3.us-west-2.amazonaws.com/1.19.6/2021-01-05/bin/linux/amd64/kubectl
chmod +x ./kubectl
sudo mv ./kubectl /usr/local/bin
kubectl version --short --client

sudo apt install unzip
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install
aws --version

curl --silent --location "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp
sudo mv /tmp/eksctl /usr/local/bin
eksctl version
Add role to EC2
Following command for cluster creation:eksctl create cluster \
  --name devops-eks-demo \
  --region ap-south-1 \
  --zones ap-south-1a,ap-south-1b \
  --nodegroup-name devops-nodegroup \
  --node-type t3.medium \
  --nodes 2 \
  --nodes-min 1 \
  --nodes-max 3 \
  --managed
Add Required Secrets in github repo
Create deployment and service.yaml files

# Add Monitoring tools:**
curl https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 | bash
helm version
kubectl create namespace monitoring
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
helm install prometheus-stack prometheus-community/kube-prometheus-stack \
  --namespace monitoring
# Open port 9090 in security group

# verify Dashboard using grafana 
# Expose Grafana
kubectl patch svc prometheus-stack-grafana -n monitoring -p '{"spec": {"type": "LoadBalancer"}}'

# Expose Prometheus
kubectl patch svc prometheus-stack-kube-prometheus-sta-prometheus -n monitoring -p '{"spec": {"type": "LoadBalancer"}}'

🚀 Jump Host EC2 Setup & EKS Cluster Deployment

This guide provides step-by-step instructions to set up a jump host EC2 instance, install required tools, create an Amazon EKS cluster using eksctl, and install monitoring tools (Prometheus + Grafana).

📌 Prerequisites

Ubuntu-based EC2 instance (Jump Host)

IAM role attached to the EC2 with permissions for EKS, EC2, VPC, CloudFormation, and IAM

AWS CLI configured (aws configure)

🛠️ Step 1: Install kubectl
curl -o kubectl https://amazon-eks.s3.us-west-2.amazonaws.com/1.19.6/2021-01-05/bin/linux/amd64/kubectl
chmod +x ./kubectl
sudo mv ./kubectl /usr/local/bin
kubectl version --short --client

🛠️ Step 2: Install AWS CLI (v2)
sudo apt install unzip -y
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install
aws --version

🛠️ Step 3: Install eksctl
curl --silent --location "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" \
| tar xz -C /tmp

sudo mv /tmp/eksctl /usr/local/bin
eksctl version

👤 Step 4: Add IAM Role to EC2

Ensure the EC2 instance has an IAM role attached with:

AmazonEKSClusterPolicy

AmazonEKSWorkerNodePolicy

AmazonEC2FullAccess

IAMReadOnlyAccess

CloudFormationFullAccess

☸️ Step 5: Create EKS Cluster Using eksctl
eksctl create cluster \
  --name devops-eks-demo \
  --region ap-south-1 \
  --zones ap-south-1a,ap-south-1b \
  --nodegroup-name devops-nodegroup \
  --node-type t3.medium \
  --nodes 2 \
  --nodes-min 1 \
  --nodes-max 3 \
  --managed

🔐 Step 6: Add Required Secrets in GitHub

Add secrets in your repository:

AWS_ACCESS_KEY_ID

AWS_SECRET_ACCESS_KEY

AWS_REGION

Any application-specific secrets

📦 Step 7: Create Deployment & Service Manifests

Create your deployment.yaml and service.yaml as required for your application.

📊 Monitoring Setup (Prometheus + Grafana)
🛠️ Install Helm
curl https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 | bash
helm version

📈 Deploy Prometheus & Grafana
kubectl create namespace monitoring

helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update

helm install prometheus-stack prometheus-community/kube-prometheus-stack \
  --namespace monitoring

➤ Make sure port 9090 is open in the Security Group if needed.
🌐 Expose Grafana & Prometheus (LoadBalancer)
Expose Grafana
kubectl patch svc prometheus-stack-grafana -n monitoring \
  -p '{"spec": {"type": "LoadBalancer"}}'

Expose Prometheus
kubectl patch svc prometheus-stack-kube-prometheus-sta-prometheus -n monitoring \
  -p '{"spec": {"type": "LoadBalancer"}}'



