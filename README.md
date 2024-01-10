# Deploy React Applications on AWS EKS using GitHub Actions and Terraform.
We plan to utilize GitHub Actions and Terraform to deploy our React project on AWS EKS.
![readme (10)](https://github.com/codewithmuh/react-aws-eks-github-actions/assets/51082957/a45ea7e5-7c6b-4209-b06f-ac957aa4f5b9)

## Overview:
We will deploy React Application on aws Elastic Kubernetes(EKS). We will use Github actions for the ci/cd pipeline. We will use EC2 as the self-hosted runner for our GitHub Actions. We will integrate Sonarcube for code analysis and Trivt Image scan to scan our docker images. Also, we will integrate Slack to get Build/deployment notifications.

### Prerequisite
You should have basic Knowledge of AWS services, Docker, Kubernetes, and GitHub Actions.

### Table of Content/Steps:
**1.** Create an AWS EC2 Instance and an IAM Role 

**2.** Add a Self Hosted Runner To AWS EC2
   
**3.** Docker Installation and Running SonarQube Container

**4.** Integrate SonarQube with GitHub Actions

**5.** Installation of tools (Java JDK, Terraform, Trivy, Kubectl, Node.js, NPM, AWS CLI)

**6.** Provision AWS EKS With Terraform

**7.** Deploy Application(image) to AWS EKS

**8.** Integrate Slack Notifications

**9.** Running Final/Complete Github actions Workflow

**10.** Delete the infrastructure (To Avoid Extra Billing, if you are just using it for learning Purposes)

### Step 01:  Create an AWS EC2 Instance and an IAM Role 

#### Create IAM Role

You have to Navigate to **AWS Console**.


<img width="1557" alt="Screenshot 2024-01-10 at 1 46 32 PM" src="https://github.com/codewithmuh/react-aws-eks-github-actions/assets/51082957/bec44efe-1992-47b4-b90b-72b5c5e42626">

<br/>

Then Search/Enter **IAM**

<br/>

<img width="1612" alt="Screenshot 2024-01-10 at 1 48 17 PM" src="https://github.com/codewithmuh/react-aws-eks-github-actions/assets/51082957/37bb4ab4-6723-4b38-a599-06ced5d0d1c6">

<br/>

Click **Roles**

<br/>

<img width="1612" alt="Screenshot 2024-01-10 at 1 50 14 PM" src="https://github.com/codewithmuh/react-aws-eks-github-actions/assets/51082957/504e30ba-fe1e-47b9-a586-07e6e04a3dbd">

<br/>
<br/> 

Then Click **Create role**

<br/>

<img width="1728" alt="Screenshot 2024-01-10 at 1 56 03 PM" src="https://github.com/codewithmuh/react-aws-eks-github-actions/assets/51082957/a1b46904-9783-4b7a-84b2-72d9d7c34548">

<br/> 
<br/>

Now Click **AWS Service**, And Then Click **Choose a service or use case**

<br/>
<br/> 

<img width="1670" alt="Screenshot 2024-01-10 at 1 57 20 PM" src="https://github.com/codewithmuh/react-aws-eks-github-actions/assets/51082957/b5cb0173-9cf4-406e-8abd-915dba1680d2">

<br/>

Now Click **EC2** and Click **NEXT**

<br/>

<img width="1624" alt="Screenshot 2024-01-10 at 1 58 03 PM" src="https://github.com/codewithmuh/react-aws-eks-github-actions/assets/51082957/da3fe367-302b-48f7-a9b0-97fe3abf948a">

<br/> 
<br/>

Click the **Search** Fileds, Then Add permissions Policies

<br/>

<img width="1652" alt="Screenshot 2024-01-10 at 2 16 39 PM" src="https://github.com/codewithmuh/react-aws-eks-github-actions/assets/51082957/0a30570f-cb17-4317-a34b-8fb72ccfa141">

<br/> 
<br/>

Add These **Three Policies**:

1.  EC2 full access
2.  AmazonS3FullAccess
3.  AmazonEKSClusterPolicy
   
<br/> 
<br/> 

<img width="1449" alt="Screenshot 2024-01-10 at 2 07 27 PM" src="https://github.com/codewithmuh/react-aws-eks-github-actions/assets/51082957/4db00b93-e786-42f4-82f1-5a316a883ae1">

<br/> 

Click NEXT and Then click the **Role Name** Field.

Type **cicd-jenkins**

Click **Create role**

<br/> 

<img width="1671" alt="Screenshot 2024-01-10 at 2 07 55 PM" src="https://github.com/codewithmuh/react-aws-eks-github-actions/assets/51082957/7358906e-c95c-466b-8646-75b994e09efa">

<br/> 
<br/> 


**Note** We will use/attach this AIM Role during AWS EC2 instance Creation.

#### Create AWS EC2 Instance
To launch an AWS EC2 instance running Ubuntu 22.04 via the AWS Management Console, start by signing in to your AWS account and accessing the EC2 dashboard. Click on "Launch Instances" and proceed through the steps. In "Step 1," select "Ubuntu 22.04" as the AMI, and in "Step 2," opt for "t2.medium" as the instance type. Configure instance details, storage, tags, and security group settings according to your requirements. Additionally, attach the previously created IAM role in the advanced details. Review the settings, create or select a key pair for secure access, and then launch the instance. Once launched, utilize the associated key pair to connect via SSH for access, ensuring the security of your connection. (Look at image Below)

<br/> 
<br/> 

<img width="1496" alt="Screenshot 2024-01-10 at 5 40 06 PM" src="https://github.com/codewithmuh/react-aws-eks-github-actions/assets/51082957/00381dad-5aaf-4aa9-a36b-ac7c933c8db2">
<img width="1496" alt="Screenshot 2024-01-10 at 5 40 41 PM" src="https://github.com/codewithmuh/react-aws-eks-github-actions/assets/51082957/19822539-ca4f-4082-8a91-b4b6a3083cb7">



### Step 02: Add a Self Hosted Runner To AWS EC2
Now Go to GitHub Repository and  click on **Settings -> Actions -> Runners**

Click on New self-hosted runner

<img width="1134" alt="Screenshot 2024-01-10 at 1 35 16 PM" src="https://github.com/codewithmuh/react-aws-eks-github-actions/assets/51082957/d850fa22-fbf6-436d-931a-3d117cf9209f">


<br/>

<img width="1496" alt="Screenshot 2024-01-10 at 5 50 48 PM" src="https://github.com/codewithmuh/react-aws-eks-github-actions/assets/51082957/e2a9b727-10f3-4216-8bf5-e9c06b1d69dc">

<br/> 

Now select Linux and Architecture X64

<img width="1496" alt="Screenshot 2024-01-10 at 5 54 09 PM" src="https://github.com/codewithmuh/react-aws-eks-github-actions/assets/51082957/95aa24b2-5090-43f2-92c6-d743ae588ebd">

Use the below commands to add a self-hosted runner

**Note:** In pic Commads are related to my account, Use your commands, it appears on your GitHub  self-hosted runner Page. 

<img width="1496" alt="Screenshot 2024-01-10 at 5 55 47 PM" src="https://github.com/codewithmuh/react-aws-eks-github-actions/assets/51082957/09c52dc0-b2b0-45ba-9375-9758bcce48aa">


Now SSH to your AWS Instac3e to connect with your Instance.

And Past/Run these commands.

  ```bash
  mkdir actions-runner && cd actions-runner
  ```
<img width="765" alt="Screenshot 2024-01-10 at 6 02 16 PM" src="https://github.com/codewithmuh/react-aws-eks-github-actions/assets/51082957/d1701fd1-fe05-446d-8759-927d64c81d76">

Command "mkdir actions-runner && cd actions-runner" serves to generate a fresh directory named "actions-runner" within the present working directory. Subsequently, it swiftly switches the current working directory to this newly created "actions-runner" directory. This approach streamlines file organization and facilitates executing successive actions within the newly formed directory without the need for separate navigation.

Download the latest runner package

```bash
curl -o actions-runner-linux-x64-2.311.0.tar.gz -L https://github.com/actions/runner/releases/download/v2.311.0/actions-runner-linux-x64-2.311.0.tar.gz
```

<img width="942" alt="Screenshot 2024-01-10 at 6 07 25 PM" src="https://github.com/codewithmuh/react-aws-eks-github-actions/assets/51082957/af67a190-54d8-4fb6-8f58-28a04253a452">

Validate the hash.

```bash
echo "29fc8cf2dab4c195bb147384e7e2c94cfd4d4022c793b346a6175435265aa278  actions-runner-linux-x64-2.311.0.tar.gz" | shasum -a 256 -c
```

<img width="937" alt="Screenshot 2024-01-10 at 6 10 55 PM" src="https://github.com/codewithmuh/react-aws-eks-github-actions/assets/51082957/963edd64-b96b-49f0-b7c7-2de0a37b370b">

Now Extract the installer

```bash
tar xzf ./actions-runner-linux-x64-2.311.0.tar.gz
```

Create the runner and start the configuration experience

```bash
./config.sh --url https://github.com/codewithmuh/react-aws-eks-github-actions --token AMFXNTP3IVE6IAZSWO3ZEGDFT2QV6
```
<img width="926" alt="Screenshot 2024-01-10 at 6 16 09 PM" src="https://github.com/codewithmuh/react-aws-eks-github-actions/assets/51082957/6710c4bc-5086-47ca-8a88-29d641b79100">


If you have provide multiple labels use commas for each label.

The last step, run it!

```bash
./run.sh
```
<img width="613" alt="Screenshot 2024-01-10 at 6 17 33 PM" src="https://github.com/codewithmuh/react-aws-eks-github-actions/assets/51082957/769cd052-4900-4f27-b8f5-9babe389e252">

Let's close Runner for now.

```bash
ctrl + c  # To close Run this Command.
```




### Step 03:  Docker Installation and Running SonarQube Container
Connect with your Instance using SSH or Putty.(The Method you are using). If already connected, ignore.

Run these commands

```bash
sudo apt-get update
sudo apt install docker.io -y
sudo usermod -aG docker ubuntu
newgrp docker
sudo chmod 777 /var/run/docker.sock
```

Now We will Pull SonarQube Docker Image and run the SonarQube Container.

**Note** Make sure to add a port in the security group of your instance.

```bash
docker run -d --name sonar -p 9000:9000 sonarqube:lts-community
```

<img width="947" alt="Screenshot 2024-01-10 at 6 31 19 PM" src="https://github.com/codewithmuh/react-aws-eks-github-actions/assets/51082957/06cdb226-6312-416e-9489-c6ddb7bf373d">

<img width="953" alt="Screenshot 2024-01-10 at 6 32 50 PM" src="https://github.com/codewithmuh/react-aws-eks-github-actions/assets/51082957/eb8e39f1-3550-4dcf-8426-ab22fcad59bf">

Now copy the IP address of Your EC2 instance

```bash
<EC2-PUBLIC-IP:9000>
```
<img width="625" alt="Screenshot 2024-01-10 at 6 36 04 PM" src="https://github.com/codewithmuh/react-aws-eks-github-actions/assets/51082957/836984b6-f96e-43c9-92ef-9a15f8e5693b">

Now Login with these creds.

```bash
login admin
password admin
```

Now Update your Sonarqube password.

<img width="639" alt="Screenshot 2024-01-10 at 6 38 58 PM" src="https://github.com/codewithmuh/react-aws-eks-github-actions/assets/51082957/80679eb3-fc4d-4620-93d7-fce3674e8a10">


This is the Sonarqube dashboard.

<img width="1269" alt="Screenshot 2024-01-10 at 6 40 16 PM" src="https://github.com/codewithmuh/react-aws-eks-github-actions/assets/51082957/2154577b-c0d0-4ece-a95d-6950979c00e4">

### Step 04: Integrate SonarQube with GitHub Actions
Integrating SonarQube with GitHub Actions allows you to automatically analyze your code for quality and security as part of your continuous integration pipeline.We already have Sonarqube up and running

Now On Sonarqube Dashboard click on Manually

<img width="1279" alt="Screenshot 2024-01-10 at 6 42 53 PM" src="https://github.com/codewithmuh/react-aws-eks-github-actions/assets/51082957/d8b4ef49-1a27-46a3-a98b-864e14ac9077">

On the Next Page, You have to provide the name of your project and provide a branch name. The Click on SetUp Button.

<img width="1269" alt="Screenshot 2024-01-10 at 6 44 58 PM" src="https://github.com/codewithmuh/react-aws-eks-github-actions/assets/51082957/865c5671-23e9-4144-8fa3-49c936cbcba7">

On the Next Page, You have to click on "With GitHub Actions"

<img width="1277" alt="Screenshot 2024-01-10 at 6 47 12 PM" src="https://github.com/codewithmuh/react-aws-eks-github-actions/assets/51082957/cd0c30ee-bf80-4f46-acce-6d66ce8beff6">

This will provide an overview of the project and provide some instructions to integrate.

<img width="1277" alt="Screenshot 2024-01-10 at 6 49 02 PM" src="https://github.com/codewithmuh/react-aws-eks-github-actions/assets/51082957/ace9100e-f2c0-417a-bf14-c24b845b721b">

Now Let's open your Giuthub Repository. 

Now Click on Settings. (if you are using my repo, make sure you have forked it)

<img width="1280" alt="Screenshot 2024-01-10 at 7 34 09 PM" src="https://github.com/codewithmuh/react-aws-eks-github-actions/assets/51082957/3cbf4def-3fd6-4a8c-8cef-b74b0c1ec747">


Click on Secrets and variables and then click on actions.

<img width="1277" alt="Screenshot 2024-01-10 at 7 40 03 PM" src="https://github.com/codewithmuh/react-aws-eks-github-actions/assets/51082957/118bc70b-8155-4ed4-b730-7d5932381f90">

It will open a page, Clock on **New Repository secret**.

<img width="1278" alt="Screenshot 2024-01-10 at 7 41 05 PM" src="https://github.com/codewithmuh/react-aws-eks-github-actions/assets/51082957/9e48bf2c-62e4-4bbb-bcc3-668e5c2e2584">

Now Go to your Sonarqube dashboard

Copy SONAR_TOKEN and click on Generate Token

<img width="1270" alt="Screenshot 2024-01-10 at 8 00 42 PM" src="https://github.com/codewithmuh/react-aws-eks-github-actions/assets/51082957/dbfbd234-d6aa-4803-afe9-f61421f87c38">

Click on Generate

<img width="705" alt="Screenshot 2024-01-10 at 8 02 41 PM" src="https://github.com/codewithmuh/react-aws-eks-github-actions/assets/51082957/58cc56e6-caeb-4690-ab3d-fca596082372">


Let's copy the Token and add it to GitHub secrets

<img width="835" alt="Screenshot 2024-01-10 at 8 03 34 PM" src="https://github.com/codewithmuh/react-aws-eks-github-actions/assets/51082957/a793ca5d-381a-40b2-ae78-cb473aa0ee37">


Now Go back to GitHub and Paste the copied name for the secret and token

Name: SONAR_TOKEN

Secret: Paste Your Token and click on Add secret

<img width="1250" alt="Screenshot 2024-01-10 at 8 05 26 PM" src="https://github.com/codewithmuh/react-aws-eks-github-actions/assets/51082957/72e9053a-0fd4-46fd-9476-cee3f642c211">

Now go back to the Sonarqube Dashboard

Copy the Name and Value
<img width="1276" alt="Screenshot 2024-01-10 at 8 06 09 PM" src="https://github.com/codewithmuh/react-aws-eks-github-actions/assets/51082957/00d0a056-e33a-413c-bd69-47249e26fe1c">

Go to GitHub now and paste-like this and click on add secret

<img width="1229" alt="Screenshot 2024-01-10 at 8 07 25 PM" src="https://github.com/codewithmuh/react-aws-eks-github-actions/assets/51082957/4d519e35-bb51-45b9-84da-899830a47658">

Our Sonarqube secrets are added and you can see it.

<img width="1220" alt="Screenshot 2024-01-10 at 8 07 57 PM" src="https://github.com/codewithmuh/react-aws-eks-github-actions/assets/51082957/7db62bfd-519b-4106-9d5e-df9ef1dc1d60">


Go to Sonarqube Dashboard and click on continue

<img width="1266" alt="Screenshot 2024-01-10 at 8 08 32 PM" src="https://github.com/codewithmuh/react-aws-eks-github-actions/assets/51082957/65f44332-fd89-45fd-bcee-d6bcf54092fb">

Now create your Workflow for your Project. In my case, I am using React Js. That's why I am selecting Other.

<img width="1272" alt="Screenshot 2024-01-10 at 8 09 43 PM" src="https://github.com/codewithmuh/react-aws-eks-github-actions/assets/51082957/c0c1a1ba-2873-4797-b891-a22385030b9f">

Now it Generates and workflow for my Project

**Note:** Make sure to use your files for this Section.

Go back to GitHub. click on Add file and then create a new file

<img width="1263" alt="Screenshot 2024-01-10 at 8 10 25 PM" src="https://github.com/codewithmuh/react-aws-eks-github-actions/assets/51082957/766d6d86-3ab0-4fff-9021-53450b07b29b">

Go back to the Sonarqube dashboard and copy the file name and content

<img width="916" alt="Screenshot 2024-01-10 at 8 12 07 PM" src="https://github.com/codewithmuh/react-aws-eks-github-actions/assets/51082957/3e7509fa-6af7-4880-b9b4-1299f9011531">

Add in GitHub like this (Look at image)

<img width="1267" alt="Screenshot 2024-01-10 at 8 13 31 PM" src="https://github.com/codewithmuh/react-aws-eks-github-actions/assets/51082957/0f36dac1-5d79-4ca8-93db-da463e6c701f">

Let's add our workflow

To do that click on Add file and then click on Create a new file

<img width="1263" alt="Screenshot 2024-01-10 at 8 10 25 PM" src="https://github.com/codewithmuh/react-aws-eks-github-actions/assets/51082957/d13fccbf-e580-4fab-b1bc-35eabfa01991">

Here is the file name

```bash
.github/workflows/sonar.yml  #you can use any name I am using sonar.yml
```

<img width="992" alt="Screenshot 2024-01-10 at 8 16 17 PM" src="https://github.com/codewithmuh/react-aws-eks-github-actions/assets/51082957/21c18913-6d2c-406c-9b47-a3fe61f5d5cd">

Copy content and add it to the file

```bash
name: Build,Analyze,scan

on:
  push:
    branches:
      - main


jobs:
  build-analyze-scan:
    name: Build
    runs-on: [self-hosted]
    steps:
      - name: Checkout code
        uses: actions/checkout@v2
        with:
          fetch-depth: 0  # Shallow clones should be disabled for a better relevancy of analysis

      - name: Build and analyze with SonarQube
        uses: sonarsource/sonarqube-scan-action@master
        env:
          SONAR_TOKEN: ${{ secrets.SONAR_TOKEN }}
          SONAR_HOST_URL: ${{ secrets.SONAR_HOST_URL }}

```

Click on commit changes

<img width="1270" alt="Screenshot 2024-01-10 at 8 16 59 PM" src="https://github.com/codewithmuh/react-aws-eks-github-actions/assets/51082957/6ea933ba-fdff-4a98-bcbb-d18c9250dd96">


Now workflow is created.

Start again GitHub actions runner from the Ec2 instance
Run These Commands

```bash
cd actions-runner
./run.sh
```

Click on Actions now


Now it's automatically started the workflow

<img width="972" alt="Screenshot 2024-01-10 at 8 19 23 PM" src="https://github.com/codewithmuh/react-aws-eks-github-actions/assets/51082957/bc534840-518f-4db6-bc9c-e39f38afe358">


Let's click on Build and see what are the steps involved

<img width="1121" alt="Screenshot 2024-01-10 at 8 20 55 PM" src="https://github.com/codewithmuh/react-aws-eks-github-actions/assets/51082957/ab4cd711-8882-4dfe-ab75-95038bb44033">


Click on Run Sonarsource and you can do this after the build completion

<img width="775" alt="Screenshot 2024-01-10 at 8 22 19 PM" src="https://github.com/codewithmuh/react-aws-eks-github-actions/assets/51082957/4ff72b8a-ff74-4012-afcb-8236364a2480">

Build complete.

Go to the Sonarqube dashboard and click on projects and you can see the analysis

<img width="1278" alt="Screenshot 2024-01-10 at 8 23 26 PM" src="https://github.com/codewithmuh/react-aws-eks-github-actions/assets/51082957/89b3fa85-a3f5-479b-89fe-9c8ad38bcb1b">

If you want to see the full report, click on issues.

### Step 05: Installation of tools (Java JDK, Terraform, Trivy, Kubectl, Node.js, NPM, AWS CLI)

Use this script to automate the installation of these tools.

Create script on Your aws ec2.

```bash
vim  run.sh
```

Copy the Below given content

```bash
#!/bin/bash
sudo apt update -y
sudo touch /etc/apt/keyrings/adoptium.asc
sudo wget -O /etc/apt/keyrings/adoptium.asc https://packages.adoptium.net/artifactory/api/gpg/key/public
echo "deb [signed-by=/etc/apt/keyrings/adoptium.asc] https://packages.adoptium.net/artifactory/deb $(awk -F= '/^VERSION_CODENAME/{print$2}' /etc/os-release) main" | sudo tee /etc/apt/sources.list.d/adoptium.list
sudo apt update -y
sudo apt install temurin-17-jdk -y
/usr/bin/java --version

# Install Trivy
sudo apt-get install wget apt-transport-https gnupg lsb-release -y
wget -qO - https://aquasecurity.github.io/trivy-repo/deb/public.key | gpg --dearmor | sudo tee /usr/share/keyrings/trivy.gpg > /dev/null
echo "deb [signed-by=/usr/share/keyrings/trivy.gpg] https://aquasecurity.github.io/trivy-repo/deb $(lsb_release -sc) main" | sudo tee -a /etc/apt/sources.list.d/trivy.list
sudo apt-get update
sudo apt-get install trivy -y

# Install Terraform
sudo apt install wget -y
wget -O- https://apt.releases.hashicorp.com/gpg | sudo gpg --dearmor -o /usr/share/keyrings/hashicorp-archive-keyring.gpg
echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/hashicorp.list
sudo apt update && sudo apt install terraform

# Install AWS CLI 
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
sudo apt-get install unzip -y
unzip awscliv2.zip
sudo ./aws/install

# Install kubectl
sudo apt update
sudo apt install curl -y
curl -LO https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
kubectl version --client


# Install Node.js 16 and npm
curl -fsSL https://deb.nodesource.com/gpgkey/nodesource.gpg.key | sudo gpg --dearmor -o /usr/share/keyrings/nodesource-archive-keyring.gpg
echo "deb [signed-by=/usr/share/keyrings/nodesource-archive-keyring.gpg] https://deb.nodesource.com/node_16.x focal main" | sudo tee /etc/apt/sources.list.d/nodesource.list
sudo apt update
sudo apt install -y nodejs

```

Now Run this script:

```bash
chmod +x run.sh

./run.sh
```


Now Check if these tools are installed or not. By checking Their versions.

```bash
kubectl version
aws --version
java --version
trivy --version
terraform --version
node -v
```

### Part 06: Provision AWS EKS With Terraform

Note: Before starting this part 06, Make sure Terraform and AWS CLI are installed and an aws account is configured on your system. You can see my [article](https://medium.com/@codewithmuh/installing-and-configuring-terraform-and-aws-cli-preparing-your-environment-for-infrastructure-as-1be1d3d0e92) to get aws and terraform installation and configuration done.


Now let's clone repo:

```bash
https://github.com/codewithmuh/react-aws-eks-github-actions.git
cd react-aws-eks-github-actions
cd terraform-eks
```

This will change your directory to terraform-eks files.

Now Change your s3 bucket in the backend file. (You can create S3 Bucket on AWS S3)

Now initialize the terraform.

```bash
terraform init
```

<img width="1095" alt="Screenshot 2024-01-10 at 9 51 07 PM" src="https://github.com/codewithmuh/react-aws-eks-github-actions/assets/51082957/b6a64078-8a87-4ba6-b846-815f0fab072c">


Now validate the configurations and syntax of all files.

```bash
terraform validate
```

<img width="605" alt="Screenshot 2024-01-10 at 9 54 29 PM" src="https://github.com/codewithmuh/react-aws-eks-github-actions/assets/51082957/cb1fe8e9-3b9e-499e-9010-6fa16a9b8c70">

Now Plan and apply your infrastructure.

```bash
terraform plan
terraform apply
```

<img width="1204" alt="Screenshot 2024-01-10 at 9 57 37 PM" src="https://github.com/codewithmuh/react-aws-eks-github-actions/assets/51082957/1bebcfd9-d57a-453b-8983-e19685ae6661">

It can take up to 10 Minutes to create your AWS EKS cluster.

You can check by going to aws EKS service.

Also, check your Node Grpup EC2 Instance, by going to EC2 Dashboard.

# Coming Soon



