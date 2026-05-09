# EKS-cluster-with-terraform
Visit the site to install according to your vm or os:
[Terraform official site ](https://developer.hashicorp.com/terraform/install#linux)
Install Terraform (ubuntu/Debian) : 
```bash
wget -O - https://apt.releases.hashicorp.com/gpg | sudo gpg --dearmor -o /usr/share/keyrings/hashicorp-archive-keyring.gpg
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(grep -oP '(?<=UBUNTU_CODENAME=).*' /etc/os-release || lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/hashicorp.list
sudo apt update && sudo apt install terraform
```
----
To start terraform run
```bash
terraform init
```

To valiadte terraform  file run
```bash
terraform validate
```

To see all resources that gonna be create run
```bash
terraform plan
```

To apply and create resources run
```bash
terraform apply -auto-approve
```

To destroy created resources run
```bash
terraform destroy -auto-approve
```
---
# Create EKS Cluster 
Run
```bash
terraform apply -auto-approve
```
it will create the eks cluster. Before that set up set up AWS CLI and connect with your cloud.

# AWS CLI v2 Installation Guide (Ubuntu/Linux x86_64)

## Download AWS CLI v2

```bash
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"

#Install unzip Package
sudo apt update
sudo apt install unzip -y

#Extract the ZIP File

unzip awscliv2.zip

#Install AWS CLI

sudo ./aws/install
```

---

## Verify Installation

```bash
aws --version
```

Example output:

```bash
aws-cli/2.x.x Python/3.x Linux/x86_64
```

---

## Configure AWS CLI

```bash
aws configure
```

You will be asked for:

```text
AWS Access Key ID
AWS Secret Access Key
Default region name
Default output format
```
Create IAM rules and then copy AWS Access key ID , AWS Secret Access Key
In Deafault region write Region name which will use this IAM rule

Example:

```text
AWS Access Key ID [None]: AKIAxxxxxxxx
AWS Secret Access Key [None]: xxxxxxxxxxxxx
Default region name [None]: [ Give the rgeion name ]
Default output format [None]: [ just enter ]
```

---
Now Run
```bash
terraform apply -auto-approve
```

# 🔐 Configure IAM OIDC Provider

The IAM OIDC provider is required for:

- Connecting Amazon EKS with AWS IAM
- Enabling IAM Roles for Service Accounts (IRSA)

---

# 📌 Relationship

```text
OIDC Provider  ---> REQUIRED FIRST

IAM Service Account ---> Uses OIDC
```
Create IAM Service Account for EBS CSI Driver 
```bash
eksctl create iamserviceaccount \
  --region ap-south-1 \
  --name ebs-csi-controller-sa \
  --namespace kube-system \
  --cluster test-cluster \
  --attach-policy-arn arn:aws:iam::aws:policy/service-role/AmazonEBSCSIDriverPolicy \
  --approve \
  --override-existing-serviceaccounts
```
![image alt](https://github.com/Shuayeb-X/Mega-Capstone-Devops-CI-CD-Project/blob/22a037234fafe9fcafc0977fcaa98eedf44f14f9/OIDCAND%20SERVICE%20ACCOUNT.png)

# 🚀 Apply Terraform Again

After creating the IAM service account:

```bash
terraform apply -auto-approve
```
Creating Cluster

![image alt ](https://github.com/Shuayeb-X/Mega-Capstone-Devops-CI-CD-Project/blob/22a037234fafe9fcafc0977fcaa98eedf44f14f9/creating%20cluster.png)


Cluster created successfully

![image alt ](https://github.com/Shuayeb-X/Mega-Capstone-Devops-CI-CD-Project/blob/22a037234fafe9fcafc0977fcaa98eedf44f14f9/cluster%20created.png)

Verify EKS Cluster

```bash
aws eks list-clusters
```
![image alt](https://github.com/Shuayeb-X/Mega-Capstone-Devops-CI-CD-Project/blob/22a037234fafe9fcafc0977fcaa98eedf44f14f9/eks%20cluster.png)
# 🔗 Configure kubectl

install Kubectl
'''bash
 curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
 #Download the kubectl checksum file
 curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl.sha256"
 #Validate the kubectl binary against the checksum file:

echo "$(cat kubectl.sha256)  kubectl" | sha256sum --check

#Install kubectl

sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl

chmod +x kubectl
mkdir -p ~/.local/bin
mv ./kubectl ~/.local/bin/kubectl
# and then append (or prepend) ~/.local/bin to $PATH
```
Test to ensure the version you installed is up-to-date
```bash
kubectl version --client
```

Connect kubectl with AWS EKS cluster:

```bash
aws eks --region ap-south-1 update-kubeconfig --name test-cluster
```

This command allows:

- kubectl
- Amazon EKS

to communicate with each other.

---

# 💾 Install AWS EBS CSI Driver

Run:

```bash
kubectl apply -k \
"github.com/kubernetes-sigs/aws-ebs-csi-driver/deploy/kubernetes/overlays/stable/ecr/?ref=release-1.11"
```

The EBS CSI Driver enables:

- Dynamic Persistent Volumes
- EBS Storage Integration
- PVC Support in Kubernetes

---
## Useful Commands

### Check Current Configuration

```bash
aws configure list
```

### List S3 Buckets

```bash
aws s3 ls
```

### Update AWS CLI

```bash
sudo ./aws/install --update
```

### Uninstall AWS CLI

```bash
sudo rm -rf /usr/local/aws-cli
sudo rm /usr/local/bin/aws
```

---

## Official Documentation

- AWS CLI Documentation: https://docs.aws.amazon.com/cli/


