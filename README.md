# Kubernetes Installation Using KOPS on EC2

Creating this repo with the intent to make Kubernetes easy for beginners. This is a work-in-progress repo.

## Introduction

This guide will walk you through the process of setting up a Kubernetes cluster on AWS EC2 instances using KOPS (Kubernetes Operations). KOPS is a Kubernetes provisioning tool that helps you create, destroy, upgrade, and maintain Kubernetes clusters on AWS.

## Prerequisites

Before you begin, ensure you have the following:

- An AWS account
- Python 3 installed
- AWS CLI installed and configured
- kubectl installed
- kops installed
- A registered domain name (for the cluster DNS)
- S3 bucket for kops state storage

## Step 1: Create an EC2 Instance or Use Your Personal Laptop

You can either create an EC2 instance on AWS or use your personal laptop to follow these steps.

## Step 2: Install Dependencies

### Python3

Ensure Python 3 is installed. Most systems come with Python 3 pre-installed.

### AWS CLI

Install the AWS CLI:

```sh
pip3 install awscli --upgrade
export PATH="$PATH:/home/ubuntu/.local/bin/"
```

### kubectl

Install kubectl:

```sh
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
echo "deb https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee -a /etc/apt/sources.list.d/kubernetes.list
sudo apt-get update
sudo apt-get install -y apt-transport-https kubectl
```

## Step 3: Install KOPS (Our Hero for Today)

Install kops:

```sh
curl -LO https://github.com/kubernetes/kops/releases/download/$(curl -s https://api.github.com/repos/kubernetes/kops/releases/latest | grep tag_name | cut -d '"' -f 4)/kops-linux-amd64

chmod +x kops-linux-amd64

sudo mv kops-linux-amd64 /usr/local/bin/kops
```

## Step 4: Set Up AWS CLI Configuration

Configure AWS CLI on your EC2 instance or laptop:

```sh
aws configure
```

## Step 5: Provide Necessary Permissions to Your IAM User

Ensure your IAM user has the following permissions:

- AmazonEC2FullAccess
- AmazonS3FullAccess
- IAMFullAccess
- AmazonVPCFullAccess

If you are using the admin user, these permissions are available by default.

## Step 6: Create an S3 Bucket for Storing the KOPS Objects

Create an S3 bucket to store the state of your Kubernetes cluster. Replace `<BUCKET_NAME>` with a unique name for your bucket:

```sh
aws s3api create-bucket --bucket kops-raja-storage --region us-east-1
```

## Step 7: Create the Cluster

Create the cluster with the following command. Replace `<CLUSTER_NAME>`, `<BUCKET_NAME>`, and `<ZONES>` with your desired cluster name, S3 bucket name, and AWS availability zones respectively:

```sh
kops create cluster --name=demok8scluster.k8s.local --state=s3://kops-raja-storage --zones=us-east-1a --node-count=1 --node-size=t2.micro --master-size=t2.micro --master-volume-size=8 --node-volume-size=8
```

## Step 8: Edit the Cluster Configuration

Edit the configuration to ensure it falls within the free tier limits. For example, you can reduce the number of nodes or the size of instances:

```sh
kops edit cluster demok8scluster.k8s.local
```

## Step 9: Build the Cluster

Apply the configuration and create the cluster:

```sh
kops update cluster demok8scluster.k8s.local --yes --state=s3://kops-raja-storage
```

This will take a few minutes to complete.

## Step 10: Validate the Cluster

After a few minutes, validate your cluster to ensure it is up and running:

```sh
kops validate cluster --name demok8scluster.k8s.local --state s3://kops-raja-storage
```

## Conclusion

You have successfully set up a Kubernetes cluster on AWS EC2 using KOPS. You can now deploy and manage your containerized applications on this cluster.

For more details and advanced configuration options, refer to the [KOPS documentation](https://github.com/kubernetes/kops/blob/master/docs/README.md).

---

Feel free to contribute to this repository by submitting issues and pull requests.

---

## License

This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for details.
