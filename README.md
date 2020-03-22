#Terraform script for EKS

# Terraform script for EKS #

Update the OS
apt update -y && apt install -y unzip 2>&1 >/dev/null
Install AWS CLI
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip 
cd aws/;ls
sudo ./install 
aws --version
cd
mkdir .aws
cat << 'EOF' > .aws/credentials
[default]
aws_access_key_id=AKIAXXXXXX
aws_secret_access_key=xHJELXXXXX
EOF

cat << 'EOF' > .aws/config
[default]
region=us-west-2
output=json
EOF

#Add AWS IAM Authenticator

jay@hp:$ curl -o aws-iam-authenticator https://amazon-eks.s3-us-west-2.amazonaws.com/1.15.10/2020-02-22/bin/linux/amd64/aws-iam-authenticator
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100 33.6M  100 33.6M    0     0  16.9M      0  0:00:01  0:00:01 --:--:-- 16.9M
jay@hp:$ chmod +x ./aws-iam-authenticator
jay@hp:$ mkdir -p $HOME/bin && cp ./aws-iam-authenticator $HOME/bin/aws-iam-authenticator && export PATH=$PATH:$HOME/bin
jay@hp:$ echo 'export PATH=$PATH:$HOME/bin' >> /.bashrc
rm -rf aws-iam-authenticator

#Install Kubectl
 sudo snap install kubectl --classic
 Or
curl -LO https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl

chmod +x ./kubectl
mv ./kubectl /usr/bin/kubectl

#Install Terraform :
curl https://releases.hashicorp.com/terraform/0.12.24/terraform_0.12.24_linux_amd64.zip    
unzip terraform_0.12.24_linux_amd64.zip 
rm -rf terraform_0.12.24_linux_amd64.zip
sudo cp terraform /usr/bin/
rm -rf terraform


jay@hp:$ terraform  --version

#Get the Terraform AWS provider:

jay@hp:$mkdir eks
jay@hp:$cd eks/;ls
jay@hp:/eks$git clone https://github.com/terraform-providers/terraform-provider-aws.git


jay@hp:$ cd terraform-provider-aws/examples/eks-getting-started/;ls
eks-cluster.tf       outputs.tf    README.md     vpc.tf
eks-worker-nodes.tf  providers.tf  variables.tf  workstation-external-ip.tf

jay@hp:/eks/terraform-provider-aws/examples/eks-getting-started$ ls
eks-cluster.tf  eks-worker-nodes.tf  outputs.tf  providers.tf  README.md  variables.tf  vpc.tf  workstation-external-ip.tf

#Terraform - init , plan , apply and destroy

jay@hp:/eks/terraform-provider-aws/examples/eks-getting-started$ terraform init 
Initializing the backend...
Initializing provider plugins...
- Checking for available provider plugins...
- Downloading plugin for provider "aws" (hashicorp/aws) 2.54.0...
- Downloading plugin for provider "http" (hashicorp/http) 1.2.0...

jay@hp:/eks/terraform-provider-aws/examples/eks-getting-started$ terraform plan
Refreshing Terraform state in-memory prior to plan...
The refreshed state will be used to calculate this plan, but will not be
persisted to local or remote state storage.

data.http.workstation-external-ip: Refreshing state...
data.aws_region.current: Refreshing state...
data.aws_availability_zones.available: Refreshing state...

An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
  + create
Terraform will perform the following actions:
  aws_eks_cluster.demo will be created

Plan: 18 to add, 0 to change, 0 to destroy.

Note: You didn't specify an "-out" parameter to save this plan, so Terraform
can't guarantee that exactly these actions will be performed if
"terraform apply" is subsequently run.



jay@hp:/eks/terraform-provider-aws/examples/eks-getting-started$ terraform apply
data.http.workstation-external-ip: Refreshing state...
data.aws_availability_zones.available: Refreshing state...
data.aws_region.current: Refreshing state...

An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
  + create


Plan: 18 to add, 0 to change, 0 to destroy.

Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value: yes

aws_eks_cluster.demo: Creating...
aws_eks_cluster.demo: Still creating... [11m20s elapsed]
aws_eks_cluster.demo: Creation complete after 11m30s [id=terraform-eks-demo]
aws_eks_node_group.demo: Creating...
aws_eks_node_group.demo: Still creating... [2m30s elapsed]
aws_eks_node_group.demo: Creation complete after 2m39s [id=terraform-eks-demo:demo]

Apply complete! Resources: 18 added, 0 changed, 0 destroyed.

Outputs:.............



jay@hp:$cd eks/terraform-provider-aws/examples/eks-getting-started
jay@hp:/eks/terraform-provider-aws/examples/eks-getting-started$ mkdir /.kube/
jay@hp:/eks/terraform-provider-aws/examples/eks-getting-started$ terraform output kubeconfig>/.kube/config
jay@hp:/eks/terraform-provider-aws/examples/eks-getting-started$ less /.kube/config 


jay@hp:/eks/terraform-provider-aws/examples/eks-getting-started$ kubectl version
Client Version: version.Info{Major:"1", Minor:"17", GitVersion:"v1.17.3", GitCommit:"06ad960bfd03b39c8310aaf92d1e7c12ce618213", GitTreeState:"clean", BuildDate:"2020-02-12T13:43:46Z", GoVersion:"go1.13.7", Compiler:"gc", Platform:"linux/amd64"}

jay@hp:/eks/terraform-provider-aws/examples/eks-getting-started$ kubectl apply -f configmap.yml
Warning: kubectl apply should be used on resource created by either kubectl create --save-config or kubectl apply
configmap/aws-auth configured
jay@hp:/eks/terraform-provider-aws/examples/eks-getting-started$ kubectl get nodes
NAME                                       STATUS   ROLES    AGE   VERSION
ip-10-0-0-138.us-west-2.compute.internal   Ready    <none>   10m   v1.14.9-eks-1f0ca9

jay@hp:/eks/terraform-provider-aws/examples/eks-getting-started$ terraform destroy
data.http.workstation-external-ip: Refreshing state...
data.aws_availability_zones.available: Refreshing state...
…..
Plan: 0 to add, 0 to change, 18 to destroy.
Do you really want to destroy all resources?
  Terraform will destroy all your managed infrastructure, as shown above.
  There is no undo. Only 'yes' will be accepted to confirm.
  Enter a value: yes

aws_eks_cluster.demo: Still destroying... [id=terraform-eks-demo, 9m30s elapsed]
aws_eks_cluster.demo: Destruction complete after 9m36s

Destroy complete! Resources: 18 destroyed.

jay@hp:/eks/terraform-provider-aws/examples/eks-getting-started$ aws resourcegroupstaggingapi get-resources
