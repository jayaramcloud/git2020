Terraform script for EKS

Update the OS
apt update -y && apt install -y unzip 2>&1 >/dev/null
Install AWS CLI
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o “awscliv2.zip”
unzip awscliv2.zip
cd aws/;ls
sudo ./install
aws --version
cd
mkdir .aws
cat << ‘EOF’ > .aws/credentials
[default]
aws_access_key_id=AKIAXXXXXX
aws_secret_access_key=xHJELXXXXX
EOF

cat << ‘EOF’ > .aws/config
[default]
region=us-west-2
output=json
EOF

Add AWS IAM Authenticator
