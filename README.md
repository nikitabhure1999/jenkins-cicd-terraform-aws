
# **Implementation of React App Deployment on AWS Infrastructure via Jenkins CI/CD Pipeline**Jenkins CI/CD Pipeline

## **Table of Contents**

- [Objective](#objective)
- [Implementation Highlights](#implementation-highlights)
- [Troubleshooting and errors which I faced during this project implementation](#troubleshooting-and-errors-which-i-faced-during-this-project-implementation)
- [Step 1: Transfer Files files from Windows to Linux Machine](#step-1-transfer-files-files-from-windows-to-linux-machine)
- [Step 2: Install Terraform and AWS Command Line Interface (CLI)](#step-2-install-terraform-and-aws-command-line-interface-cli)
- [Step 3: Create AWS account](#step-3-create-aws-account)
- [Step 4: Ansible Playbook](#step-4-ansible-playbook)
- [Step 5: Provisioning AWS Infrastructure using Terraform and Ansible Playbook](#step-5-provisioning-aws-infrastructure-using-terraform-and-ansible-playbook)
- [Step 6: Access Jenkins Dashboard](#step-6-access-jenkins-dashboard)
- [Step 7: Configure GitHub Access Key in Jenkins](#step-7-configure-github-access-key-in-jenkins)
- [Step 8: Configure GitHub and DockerHub Credentials in Jenkins](#step-8-configure-github-and-dockerhub-credentials-in-jenkins)
- [Step 9: Create a Multibranch Pipeline project in Jenkins](#step-9-create-a-multibranch-pipeline-project-in-jenkins)
- [Step 10: Terraform Destroy](#step-10-terraform-destroy)

## **Objective:**

The objective of this project is to design and implement a robust CI/CD
pipeline for deploying a React application on AWS. This involves:

- Automating infrastructure provisioning using Terraform

- Managing server configuration and setup with Ansible

- Containerizing the application using Docker

- Setting up a Jenkins pipeline for continuous integration and deployment

- Integrating GitHub for source control and DockerHub for image storage

- Deploying the application to AWS EC2 instances

- Ensuring the entire process is scalable, repeatable, and automated

## **Implementation Highlights:**

- Files are transferred from a local Windows machine to a Linux server using scp.

- Terraform is used to provision EC2 instances.

- Ansible automates installation of Jenkins, Docker, and Maven on EC2.

- **Jenkins is configured to:**
<!-- -->
Use GitHub access tokens for repo access.

Use DockerHub credentials for image push/pull.

Automatically build branches using a multibranch pipeline.

<!-- -->

- A custom Jenkinsfile manages the build, Docker image creation, and deployment to EC2.

- The React app becomes accessible via the EC2 public IP and specified port.

- terraform destroy is used to deprovision all resources post-deployment.

## **Troubleshooting and errors which I faced during this project implementation**

### **Error 1: Handling Public IP Changes on EC2 Instance for Jenkins**

#### **Issue Summary:**

When an EC2 instance is stopped and restarted, AWS automatically assigns a new public IP address. As a result:

- The Jenkins server, which relies on the public IP, becomes inaccessible using the old IP.

- Jenkins does not automatically detect or update the new IP, leading to degraded performance.

- Builds may fail or not run at all due to this misconfiguration.

#### **Resolution Steps**

To update the Jenkins server with the new public IP and restore full functionality, follow these steps:

1. **Re-Provision Jenkins Server IP Using Terraform**

Run the following commands from your infrastructure directory to re-provision the new IP address:

```bash
terraform init
```

```bash
terraform validate
```

```bash
terraform plan
```

```bash
terraform apply
```

These commands will reinitialize the configuration, validate the setup, and apply the necessary changes, including the updated public IP.

2. **Manually Update Jenkins Configuration**

After Terraform provisions the new IP, you must manually update the Jenkins configuration to reflect this change.

Update the following Jenkins locations (as applicable):

- **Jenkins URL under:** Manage Jenkins → Under System Configuration go to System → Jenkins URL

Then enter the new public IP address in the Jenkins URL field.

![Image53](https://github.com/gurpreet2828/Jenkins-CICD/blob/8be79d034d204cf39413c2c1cc02b8673a5f6b72/Images/Image53.png)

***Note:** To avoid this issue in the future, consider assigning an Elastic IP to your EC2 instance to retain a static public IP.*

### **Error 2: Updating Docker Image Reference in Jenkinsfile**

**Issue:**

After forking the react-app repository from another GitHub account to my own, I encountered an issue in the Jenkinsfile where the Docker image was still referencing wessamabdelwahab/react-app.

While working on a Jenkins pipeline for deploying a React app, I am encountering the following error:

`curl: (7) Failed to connect to localhost port 1233`

To resolve this, the image reference needs to be updated to reflect my GitHub username, for example, gurpreet2828/react-app.

**Solution:** The Jenkinsfile uses a hardcoded Docker image reference (wessamabdelwahab/react-app) that needs to be updated to reflect your GitHub username (e.g., gurpreet2828/react-app).

1. **Locate the Jenkinsfile:** Find it in the root directory or in .ci/ or .jenkins/ folders.

    **I placed the Jenkinsfile at the following location:**

```shell
 /home/administrator/react-app/Jenkinsfile
```

**2. Search for the Reference:** Use the command ` grep -r  \"wessamabdelwahab\" . ` to find all instances of wessamabdelwahab/react-app.

**3. Update the Reference:** Replace wessamabdelwahab/react-app with your-username/react-app (Ex: gurpreet2828/react-app) in the Jenkinsfile by running the following command

```shell
sed -i \'s/wessamabdelwahab\\react-app/gurpreet2828\\react-app/g\' Jenkinsfile
```

 **4. Commit and Push Changes:** Run the following Git commands:

```shell
git add Jenkinsfile
```

```shell
git commit -m "Updated image reference to my GitHub username"
```

```shell
git push origin main
```

**5. Re-run the Jenkins Pipeline:** Trigger a manual build or wait for automatic execution.

**6. Verify the Fix:** Ensure the pipeline runs successfully and test the application (e.g., via curl localhost:1233).

**Outcome:**
By updating the Docker image reference in the Jenkinsfile, the Jenkins pipeline should run without issues, using the correct GitHub  repository image.

**Verify the Fix:**

Check if the pipeline completes without errors.

Test the connection by running `curl localhost:1233` to confirm the app is running on the correct port.

**Summary of Key Points:**

**Problem:** The Jenkinsfile was using wessamabdelwahab/react-app instead of your GitHub username.

**Solution:** Update the Docker image reference in the Jenkinsfile to your-username/react-app.

## **Step 1: Transfer Files files from Windows to Linux Machine**

Use `scp` to transfer your Terraform and Docker files from your local machine to your Ubuntu instance.
**Note:** Run the following command in Command Prompt (CMD) to copy the Terraform and Docker code to your Linux machine:

```shell
scp -r -v \"C:\Users\Gurpreet\OneDrive\Desktop\York Univ\Assignments\Assignment 6\Jenkins-CICD\" administrator@10.0.0.83:/home/administrator
```

![Image1](https://github.com/gurpreet2828/Jenkins-CICD/blob/47b28cca86aff817a0d18ae3a7d99cb69b7591f3/Images/Image1.png)

Next, it will prompt you for the password of your Linux machine, as shown in the image below.

![Image2](https://github.com/gurpreet2828/Jenkins-CICD/blob/47b28cca86aff817a0d18ae3a7d99cb69b7591f3/Images/Image2.png)

After entering the password, you will be logged into your Ubuntu Linux machine and will see the files in your home directory as shown below

![Image3](https://github.com/gurpreet2828/Jenkins-CICD/blob/47b28cca86aff817a0d18ae3a7d99cb69b7591f3/Images/Image3.png)

**Note:** To avoid permission issues, please run the following commands to ensure the appropriate permissions are set:

```shell
sudo chown -R administrator:administrator/home/administrator/Jenkins-CICD
```

```shell
sudo chmod -R u+rwx /home/administrator/Jenkins-CICD
```

These commands will assign ownership to the administrator user and grant the necessary read, write, and execute permissions for the Jenkins-CICD directory.

## **Step 2: Install Terraform and AWS Command Line Interface (CLI)**

### **1. Update and install dependencies**

```shell
sudo apt update && sudo apt install -y gnupg software-properties-common curl
```

### **2. Add the HashiCorp GPG key**

```shell
curl -fsSL https://apt.releases.hashicorp.com/gpg | sudo gpg --dearmor -o /usr/share/keyrings/hashicorp-archive-keyring.gpg
```

### **3. Add the HashiCorp repo**

```shell
echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(lsb_release -cs) main" | \
```

```shell
sudo tee /etc/apt/sources.list.d/hashicorp.list
```

### **4. Update and install Terraform**

```shell
sudo apt update
```

```shell
sudo apt install terraform -y
```

### **5. Verify installation**

```shell
terraform -v
```

![Image48](https://github.com/gurpreet2828/jenkins-cicd-terraform-aws/blob/60928b5141cfe4d27af56b5751f7a3ff40030e03/Images/Image48.png)

### **AWSCLI Install**

**To install** the AWS CLI, run the following command

```shell
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
```

```shell
unzip awscliv2.zip
```

```shell
sudo ./aws/install
```

**Run the following command to check if AWS CLI is installed correctly:**

```shell
aws --version
```

You see the following output

![Image49](https://github.com/gurpreet2828/Jenkins-CICD/blob/47b28cca86aff817a0d18ae3a7d99cb69b7591f3/Images/Image49.png)

## **Step 3: Create AWS account**

After Creating

Click on account name - Select Security Credentials

![Image50](https://github.com/gurpreet2828/jenkins-cicd-terraform-aws/blob/51d4f14a4001b6679c4c4977191cf7a04ea76768/Images/Image50.png)

Click **Create access key**.

![Image51](https://github.com/gurpreet2828/Jenkins-CICD/blob/47b28cca86aff817a0d18ae3a7d99cb69b7591f3/Images/Image51.png)

**Note:** Download the key file or copy the Access Key ID & Secret Access Key (Secret Key is shown only once!).

After install and creating AWS account configure the AWS

Configure AWS CLI with the New Access Key

```shell
aws configure
```

It will prompt you for:

**1. AWS Access Key ID**: Your access key from AWS IAM.

**2. AWS Secret Access Key**: Your secret key from AWS IAM.

**3. Default region name**: (e.g., us-east-1, us-west-2).

**4. Default output format**: (json, table, text --- default is json).

***Enter access key and secret key which you will get from aws account***

**Check credentials added to aws configure correctly**

```shell
aws sts get-caller-identity
```

If your AWS CLI is properly configured, you\'ll see a response like this:

![Image52](https://github.com/gurpreet2828/jenkins-cicd-terraform-aws/blob/2ffcf931d3af7705aa0c0f50a1d4282ab078d392/Images/Image52.png)

## **Step 4: Ansible Playbook**

Ansible playbook automates the installation of several tools on an AWS EC2 instance (running Amazon Linux 2) and configures them. Here's a summary of what the playbook will install and configure:

 **1. Dependencies**:

- Installs required packages like wget, git, yum-utils, and java-17-amazon-corretto-devel.

**2. Jenkins**:

- Downloads and installs Jenkins by adding its repository to the system.

- Imports the GPG key for Jenkins\' repository.

- Installs Jenkins and ensures that it is started and enabled to run on boot.

- Retrieves the Jenkins initial admin password for setup.

**3. Docker**:

- Installs Docker (specific version docker-20.10.25).

- Creates a Docker group if it doesn't already exist.

- Adds the Jenkins user to the Docker group to allow Jenkins to run Docker commands.

- Starts and enables the Docker service.

- Changes permissions for the Docker socket (docker.sock) to make it accessible.

**4. Maven**:

- Downloads and unarchives Apache Maven version 3.9.5 to /opt.

- Creates a symbolic link to Maven's mvn command in /usr/bin for easy
  access.

- Configures Maven environment variables and makes them globally available by updating /etc/profile.d/maven.sh.

The overall goal of this playbook is to set up Jenkins and Docker on the EC2 instance, configure Maven for build automation, and ensure these tools are ready for use in a development or CI/CD environment.

## **Step 5: Provisioning AWS Infrastructure using Terraform and Ansible Playbook**

1. `Terraform init`

- prepares your environment and configures everything Terraform needs to interact with your infrastructure.

![Image4](https://github.com/gurpreet2828/jenkins-cicd-terraform-aws/blob/b44dfff4b7ef4d3ac177f4c860b8586c9a3cb5ee/Images/Image4.png)

2. `terraform fmt`

- used to **automatically format** your Terraform configuration files to a standard style. It ensures that your code is consistently formatted, making it easier to read and maintain.

3. `Terraform validate`:

- used to **check the syntax and validity** of your Terraform configuration files. It helps you catch errors in the configuration before you attempt to run other Terraform commands, like terraform plan or terraform apply.

![Image5](https://github.com/gurpreet2828/Jenkins-CICD/blob/47b28cca86aff817a0d18ae3a7d99cb69b7591f3/Images/Image5.png)

4. `terraform plan`

- used to **preview the changes** Terraform will make to your infrastructure based on the current configuration and the existing state. It shows what actions will be taken (such as creating, modifying, or deleting resources) when you apply the configuration

- Before running terraform apply to check exactly what changes Terraform will make.

5. `Terraform apply`

- executes the changes required to reach the desired state of your infrastructure as defined in your configuration files. It creates, updates, or deletes resources after showing a preview and asking for your approval (unless auto-approved).

![Image6](https://github.com/gurpreet2828/Jenkins-CICD/blob/47b28cca86aff817a0d18ae3a7d99cb69b7591f3/Images/Image6.png)

If everything is set up correctly, you will see the public IP address displayed at the end.

**Jenkins Public URL:** <http://54.157.96.255:8080>

**Verifying EC2 Instance Deployment on AWS**:

Terraform will provision the required infrastructure, including the Jenkins server hosted on an EC2 instance.

You will also see the EC2 instance successfully created and running in your AWS account. To verify:

1. Log in to the [AWS Management Console](https://console.aws.amazon.com/).

2. Navigate to the **EC2 Dashboard**.

3. Under **Instances**, confirm that the newly created instance is listed and in a running state.

This EC2 instance will host your Jenkins server, and its public IP will be used to access the Jenkins

![Image35](https://github.com/gurpreet2828/Jenkins-CICD/blob/47b28cca86aff817a0d18ae3a7d99cb69b7591f3/Images/Image35.png)

## **Step 6: Access Jenkins Dashboard**

Once the infrastructure is successfully deployed and Jenkins is installed, follow these steps to connect:

### **1. Enter the public IP address with port 8080** (provided in the Terraform output).**\

Example: <http://54.157.96.255:8080>

### **2. You should see the Jenkins setup screen.**

![Image7](https://github.com/gurpreet2828/Jenkins-CICD/blob/47b28cca86aff817a0d18ae3a7d99cb69b7591f3/Images/Image7.png "Image7")

### **3. To complete the setup, you may need the **initial admin password**, which can be retrieved by running this command on your EC2 instance**

Connect to ec2-instance by running following command

```shell
ssh -i /root/.ssh/docker ec2-user@ 54.157.96.255
```

![Image8](https://github.com/gurpreet2828/Jenkins-CICD/blob/47b28cca86aff817a0d18ae3a7d99cb69b7591f3/Images/Image8.png "Image8")

### **4. On your EC2 instance, run the following command to retrieve the password:**

```shell
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

![Image9](https://github.com/gurpreet2828/Jenkins-CICD/blob/47b28cca86aff817a0d18ae3a7d99cb69b7591f3/Images/Image9.png "Image9")

### **5. Enter the Password**:

Paste the password into the setup wizard's prompt to unlock Jenkins.

### **6. Install Suggested Plugins**

Jenkins will now offer to install a set of suggested plugins. These plugins provide essential functionality for Jenkins operations.

Click on **\"Install suggested plugins\"** to proceed with the installation.

![Image10](https://github.com/gurpreet2828/Jenkins-CICD/blob/0ba3fe647030f13e6b45a1a91ff467b48ba3ab4e/Images/Image10.png)

### **7. Create the First Admin User**:

After the plugin installation is complete, Jenkins will prompt you to set up the first admin user. Fill in the required information:

**Username**: Choose a username (e.g., admin).

**Password**: Set a strong password.

**Full Name**: Enter your full name.

**Email Address**: Provide your email.

![Image11]https://github.com/gurpreet2828/Jenkins-CICD/blob/0ba3fe647030f13e6b45a1a91ff467b48ba3ab4e/Images/Image11.png)

Once the form is filled, click **\"Save and Finish\"**.

![Image12]https://github.com/gurpreet2828/Jenkins-CICD/blob/0ba3fe647030f13e6b45a1a91ff467b48ba3ab4e/Images/Image12.png)

### **8. Jenkins Dashboard**:

Once the setup is complete, you will be redirected to the Jenkins dashboard as shown in following image where you can start creating pipelines, managing jobs, and configuring Jenkins further.

![Image13](https://github.com/gurpreet2828/Jenkins-CICD/blob/0ba3fe647030f13e6b45a1a91ff467b48ba3ab4e/Images/Image13.png)

### 9. Install Docker Pipeline Plugin

**Access the Jenkins Dashboard**:

After logging into Jenkins, you will be on the Jenkins dashboard.

**Go to Manage Jenkins**:

From the left sidebar, click on **\"Manage Jenkins\"**.

**Manage Plugins**:

Under \"Manage Jenkins\", click on **\"Manage Plugins\"**.

**Search for Docker Pipeline Plugin**:

In the **Available** tab, use the search bar to search for **\"Docker Pipeline\"**.

![Image14](https://github.com/gurpreet2828/Jenkins-CICD/blob/0ba3fe647030f13e6b45a1a91ff467b48ba3ab4e/Images/Image14.png)

**Install Docker Pipeline Plugin**:

Once found, select the **Docker Pipeline** plugin and click **\"Install without restart\"**.

This will install the plugin without requiring a Jenkins restart.

**Verify Installation**:

After installation, the plugin should appear under the **Installed** tab. You can now start using Docker commands in your Jenkins pipelines.

**Restart Jenkins (Optional)**:

If necessary, you can restart Jenkins to ensure that all configurations are loaded properly. To do this, go to **Manage Jenkins** \> **Restart Jenkins**.

## **Step 7: Configure GitHub Access Key in Jenkins**

### **1. Generate Personal Access Token on GitHub**

- Go to **GitHub** \> **Settings** \> **Developer settings** \> **Personal access tokens** \> **Tokens(Classic)**

- Click on **Generate new token**.

![Image15](https://github.com/gurpreet2828/Jenkins-CICD/blob/0ba3fe647030f13e6b45a1a91ff467b48ba3ab4e/Images/Image15.png)

- Select the required scopes (e.g., repo, admin:repo_hook, etc.).

- Click **Generate token** and copy the token.

### **2. Log into Jenkins**

Open your Jenkins dashboard by accessing <http://54.157.96.255:8080> and
logging in with your admin credentials.

### **3. Go to Manage Jenkins**

From the left-hand sidebar of Jenkins, click on **\"Manage Jenkins\"**.

### **4. Configure System**

Navigate to **\"Manage Jenkins\"**, then under **System Configuration**, select **System**.

![Image17](https://github.com/gurpreet2828/Jenkins-CICD/blob/0ba3fe647030f13e6b45a1a91ff467b48ba3ab4e/Images/Image17.png)

### **5. Add GitHub Access Key**

Scroll down to the **GitHub** section (or search for \"GitHub\" in the page). Here\'s how to configure it:

- **Under GitHub Server**

Fill Following information

**Name:**

**API Url:** Keep default ( <https://api.github.com>**)**

![Image19](https://github.com/gurpreet2828/Jenkins-CICD/blob/0ba3fe647030f13e6b45a1a91ff467b48ba3ab4e/Images/Image19.png)

**Click on Add → select Jenkins → Under Kind - Select Secret text**

- Enter the following, then click Add

<!-- -->

- Secret: Enter the generated GitHub Access Key

- ID: GitHub_Key

- Description: GitHubKey

**This will display the following screen.**

![Image20](https://github.com/gurpreet2828/Jenkins-CICD/blob/0ba3fe647030f13e6b45a1a91ff467b48ba3ab4e/Images/Image20.png)

### **6. Test the Connection**

- Select **GitHubKey** from the **Credentials** drop-down, check **Manage hooks** (to enable GitHub webhooks for repository changes), and then click **Test Connection**.

![Image21](https://github.com/gurpreet2828/Jenkins-CICD/blob/0ba3fe647030f13e6b45a1a91ff467b48ba3ab4e/Images/Image21.png)

- Click **Test Connection** to ensure that Jenkins can authenticate with
  GitHub using the provided token.

- If the connection is successful, you should see a confirmation message

> ![Image22](https://github.com/gurpreet2828/Jenkins-CICD/blob/0ba3fe647030f13e6b45a1a91ff467b48ba3ab4e/Images/Image22.png)

### **7. Save the Configuration**

Once the connection is successful, scroll to the bottom and click **Save** to apply the changes.

### **8. Use GitHub Credentials in Jenkins Pipelines**

Now that Jenkins has the GitHub access token configured, you can use it in Jenkins pipelines for tasks like cloning repositories or triggering GitHub webhooks.

## **Step 8: Configure GitHub and DockerHub Credentials in Jenkins**

To allow Jenkins to securely interact with GitHub and DockerHub, add your credentials as follows:

**Go to Jenkins Dashboard**  

Navigate to your Jenkins instance.

**Click on the small arrow under your user and select Credentials**:

![Image23](https://github.com/gurpreet2828/Jenkins-CICD/blob/0ba3fe647030f13e6b45a1a91ff467b48ba3ab4e/Images/Image23.png)

- **Select System under Stores from Parent.**

![Image24](https://github.com/gurpreet2828/Jenkins-CICD/blob/0ba3fe647030f13e6b45a1a91ff467b48ba3ab4e/Images/Image24.png)

- **Then click on Global credentials (unrestricted)**

![Image25](https://github.com/gurpreet2828/Jenkins-CICD/blob/0ba3fe647030f13e6b45a1a91ff467b48ba3ab4e/Images/Image25.png)

- **Click on Add Credentials**

![Image26](https://github.com/gurpreet2828/Jenkins-CICD/blob/0ba3fe647030f13e6b45a1a91ff467b48ba3ab4e/Images/Image26.png)

- For **GitHub**:

<!-- -->

- Choose **Username with password**

- Enter your GitHub username and access token (Generated on GitHub Account)

![Image27]https://github.com/gurpreet2828/Jenkins-CICD/blob/0ba3fe647030f13e6b45a1a91ff467b48ba3ab4e/Images/Image27.png)

- For **DockerHub**:

<!-- -->

- Then select again Add Credentials from the left side menu

Add your GitHub Account by entering the following:

- **Username:** Your Docker Hub Username

- **Password:** Enter your Docker Hub Password

- **ID:** Dockerhub_login (It is important the keep the exact ID name as we are referring to it in Jenkinsfile)

- **Description:** DockerHub

![Image28](https://github.com/gurpreet2828/Jenkins-CICD/blob/0ba3fe647030f13e6b45a1a91ff467b48ba3ab4e/Images/Image28.png)

**Click OK/Save**:

This will securely store your credentials for use in Jenkins pipelines.

![Image29](https://github.com/gurpreet2828/Jenkins-CICD/blob/0ba3fe647030f13e6b45a1a91ff467b48ba3ab4e/Images/Image29.png)

## **Step 9: Create a Multibranch Pipeline project in Jenkins.**

- **Access Jenkins Dashboard**

<!-- -->

- Open your Jenkins URL (http://54.157.96.255:8080)

- Log in with your credentials.

<!-- -->

- **Create a New Item**

<!-- -->

- On the dashboard, click "New Item" (left sidebar).

- Enter a name for your project (Ex: Test-App)

- Select Multibranch Pipeline as the project type.

- Click OK.

![Image30](https://github.com/gurpreet2828/Jenkins-CICD/blob/0ba3fe647030f13e6b45a1a91ff467b48ba3ab4e/Images/Image30.png)

- **Configure the Pipeline**

<!-- -->

- **In the configuration page:**

- Under Branch Sources, click Add Source and choose GitHub

![Image31](https://github.com/gurpreet2828/Jenkins-CICD/blob/0ba3fe647030f13e6b45a1a91ff467b48ba3ab4e/Images/Image31.png)

- Provide the repository URL.

- Then Click Validate -- You will see the following

![Image33](https://github.com/gurpreet2828/Jenkins-CICD/blob/0ba3fe647030f13e6b45a1a91ff467b48ba3ab4e/Images/Image33.png)

- **Configure Scan Settings**

<!-- -->

- Set the scan interval (e.g., every 1 minute or 1 hour) under Scan Multibranch Pipeline Triggers.

- This allows Jenkins to detect new branches or updates automatically.

<!-- -->

- **Save the Configuration**

<!-- -->

- Click Save.

- Jenkins will immediately scan the repository and automatically create jobs for each branch that contains a Jenkinsfile.

![Image34](https://github.com/gurpreet2828/Jenkins-CICD/blob/0ba3fe647030f13e6b45a1a91ff467b48ba3ab4e/Images/Image34.png)

- Click on \#1 -- click console output you will see running Jenkins pipeline

![Image38](https://github.com/gurpreet2828/Jenkins-CICD/blob/0ba3fe647030f13e6b45a1a91ff467b48ba3ab4e/Images/Image38.png)

- Click **Proceed**. After a short while, the build should complete successfully, and you will be presented with the following screen.

![Image39](https://github.com/gurpreet2828/Jenkins-CICD/blob/0ba3fe647030f13e6b45a1a91ff467b48ba3ab4e/Images/Image39.png)

- Click on \#1 Build -- Pipeline Console.  You will see that your react-app has been deployed to production.

![Image41](https://github.com/gurpreet2828/Jenkins-CICD/blob/0ba3fe647030f13e6b45a1a91ff467b48ba3ab4e/Images/Image41.png)

- You can log into the server and run the following command to check the status of your running container.

```shell
docker ps
```

![Image42](https://github.com/gurpreet2828/Jenkins-CICD/blob/0ba3fe647030f13e6b45a1a91ff467b48ba3ab4e/Images/Image42.png)

- Navigate to your Docker Hub account, where you should be able to see the pushed and pulled images under **Repositories**.

![Image43](https://github.com/gurpreet2828/Jenkins-CICD/blob/0ba3fe647030f13e6b45a1a91ff467b48ba3ab4e/Images/Image43.png)

- Navigate to the public IP of your server, followed by port 1233 (e.g., \<Public IP\>:1233). The sample React application should be running.  

For example: <http://44.222.88.85:1233/>

![Image44](https://github.com/gurpreet2828/Jenkins-CICD/blob/0ba3fe647030f13e6b45a1a91ff467b48ba3ab4e/Images/Image44.png)

## **Step 10: terraform destroy**

Once you have completed the lab, it is essential to destroy the provisioned infrastructure resources to prevent any future costs.  

To do so, destroy the Terraform-managed infrastructure and confirm by typing \"yes.\"

![Image45](https://github.com/gurpreet2828/Jenkins-CICD/blob/0ba3fe647030f13e6b45a1a91ff467b48ba3ab4e/Images/Image45.png)

After typing \"yes,\" all AWS resources will be destroyed. You will see the following screen once the destruction is complete.

![Image46](https://github.com/gurpreet2828/Jenkins-CICD/blob/0ba3fe647030f13e6b45a1a91ff467b48ba3ab4e/Images/Image46.png)

To verify, log in to your AWS account, and you should see that your EC2 instance has been terminated.

![Image47](https://github.com/gurpreet2828/Jenkins-CICD/blob/0ba3fe647030f13e6b45a1a91ff467b48ba3ab4e/Images/Image47.png)


[def]: #step-7-configure-github-access-key-in-jenkins
