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




