![alt diagram](assets/images/java-web-app-deployment/image1.png)

## 🚀 Project Overview
This project aims to build an advanced end-to-end CICD DevOps pipeline for a Java web application.
Our project is divided into two main parts:

1. The initial phase involves the installation and configuration of various tools and servers.

2. In the second phase, we will create an advanced end-to-end Jenkins pipeline with multiple stages.

## 🔧 Problem Statement
In the rapidly evolving landscape of software development, the implementation of Continuous Integration/Continuous Deployment (CICD) processes has become indispensable. For Java web applications, a robust CICD pipeline not only streamlines the development process but also ensures the delivery of high-quality, reliable software to end-users. This project aims to construct an advanced end-to-end CICD DevOps pipeline tailored specifically for Java web applications.

## 💽 Techonology Stack
● **AWS**

● **Git**

● **Gradle**

● **Ansible**

● **Terraform**

● **Jenkins**

● **SonarQube**

● **SonaType Nexus Repository Manager**

● **Kubernetes (kubeadm k8s cluster)**

● **Helm**

● **datree.io**

● **Slack**

## Prerequisites
1. AWS account
2. Terraform and Ansible should be installed and configured on your local computer

## Phase I: Installation and configuration
* We have used terraform config files to provision AWS resources and ansible playbooks to configure the instances as per our requirement, so you should know how to write terraform config files and ansible playbooks for understanding this phase of the project clearly. If you want to test the project then you can just clone the repo and run the commands which will automatically set up the required AWS resources.

* We need five servers to set up our CICD pipeline infrastructure. We have used Ubuntu as the base operating system for all our servers.

        1. Jenkins Server - jenkins
        ● This will host our jenkins application to implement our CI/CD pipelines.

        2.  SonarQube Server - sonar
        ● This server will host the SonarQube application. It is used for continuous inspection of code quality to perform automatic reviews with static analysis of code to detect bugs and code smells.

        3.  Nexus Repository server - nexus
        ●  Sonatype Nexus Repository Manager provides a central platform for storing build artifacts. This server will host our two private repositories one for storing docker images and another for storing helm charts.

        4. Kubernetes control plane node - k8s-master
        ●  This server will act as the control plane node for our kubernetes cluster.

        5. Kubernetes worker node - k8s-node1
        ●  The k8s-node1 server will act as a worker node for our kubernetes cluster
This concludes the brief description of the servers needed for our project. We can now proceed to Phase I.

# **Setup AWS account for Terraform**
1. To be able to set up the AWS resources with Terraform, we need to access AWS using **Access Keys**

2. Create a new user with administrator access to the AWS account

    * Goto **IAM > Users**, click on **Add users** and enter the user name and click **Next**.

    ![alt diagram](assets/images/java-web-app-deployment/image2.png)

    * On the next page, select **Attach policies directly** and attach the **AdministratorAccess** policy to the user. Click on **Next**, then click on **Create user**.

    ![alt diagram](assets/images/java-web-app-deployment/image3.png)

    ![alt diagram](assets/images/java-web-app-deployment/image4.png)

3. Create an AWS access key for the newly created user.

    * Navigate to **IAM > Users > tf-user**, select **Security credentials** and click on **Create access key**, select **Other** and click on **Next**. 

    * Enter a name for your access key and click on **Create access key** to create the key. 

    ![alt diagram](assets/images/java-web-app-deployment/image5.png)

    * Next, you will get an option to download the `.csv` file containing the access key and secret key or you can simply copy and store them in a safe place

    ![alt diagram](assets/images/java-web-app-deployment/image6.png)

4. Set environment variables on your local system for Terraform to access the AWS access key.

    * Create a file called `.envrc` in your home directory and write the commands to export the environment variables in it.

```bash

            #!/bin/bash
            ##Access_keys for AWS Terraform User##
            export TF_VAR_access_key="Enter the aws access key here"
            export TF_VAR_secret_key="Enter the secret access key here"
```

Replace the data with your AWS access and secret keys and save the file. We have used the same variables in our terraform config files so make sure the name of the environment variables are exactly as mentioned in the code block above.

* Edit your `.bashrc` file and add this line at the end `source ~/.envrc`
save the file and exit. 
    
# **Clone the Git repository**
```bash
git clone https://github.com/mandeepsingh10/cicd-setup.git
```


1. In the git repo, we have three directories.

    * **ansible_config** - contains all the ansible playbooks

    * **terraform_config** - contains all the terraform config files

    * **scripts** - contains miscellaneous scripts to automate some tasks

2. To provision AWS instances using Terraform, we need to specify a **public key** to access the resources, we can use our public key for this instead of creating a new pair as it will help us to directly access the EC2 instances without doing any additional configuration for ssh.

3. Copy your **public key** from `~/.ssh` directory to every Terraform configuration directory under `terraform_config` directory in the repository.

```bash
    cp $HOME/.ssh/id_rsa.pub $HOME/repos/cicd-setup/terraform_config/jenkins/publickey.pub
```
   My repository is present at `$HOME/repos` path, change that path as per your repo path and copy the public key as `publickey.pub` to all the directories `jenkins`, `master`, `nexus`, `node1` and `sonar` under `terraform_config` directory.
    
4. Now we will be able to provision the terraform resources by just running the `terraform apply` command.

# **Create AWS instances using Terraform**

1. First, we need to initialize all the directories where we have our Terraform configuration files.
```bash
 cd  $HOME/repos/cicd-setup/terraform_config/jenkins
 terraform init
```

We need to initialize all the directories where we have our Terraform configuration files. To do this, navigate to each directory under `cicd/terraform_config` - `jenkins`, `master`, `nexus`, `node1`, and `sonar` - and run the command `terraform init`.

2. Next, we can try provisioning any one of the instances to check if our configuration is working as expected. To do this, navigate to the `Jenkins` directory and run the command `terraform plan`. If everything is set up correctly, it should display a message similar to this at the end:

```bash
 Changes to Outputs:
   + Name                 = "jenkins"
   + ami_id               = "ami-0bcb40eb5cb6d6f9e"
   + instance_id          = (known after apply)
   + keyname              = "jenkins-key"
   + public_dns           = (known after apply)
   + public_ip            = (known after apply)
   + securityGroupDetails = (known after apply)

```

This means that our terraform configuration is correct and is working as expected.

3. To provision the jenkins server on AWS by running the command
`terraform apply --auto-approve` in the `cicd-setup/terraform_config/jenkins` directory.

4. Once the server is provisioned, the command will display details about the server, such as the hostname, `ami_id` of the image used, public and private IP addresses, similar to this:

```bash
 Apply complete! Resources: 3 added, 0 changed, 0 destroyed.

 Outputs:

 Name = "jenkins"
 ami_id = "ami-0bcb40eb5cb6d6f9e"
 instance_id = "i-09a9d9b364b692fb7"
 keyname = "jenkins-key"
 public_dns = "ec2-3-109-154-107.ap-south-1.compute.amazonaws.com"
 public_ip = "3.109.154.107"
 securityGroupDetails = "sg-02b24f714c8b6b86a"
```

5. Let's try accessing the `jenkins` server by `ssh` using it's public IP. By default, `ubuntu` user is created on all our servers, as we are using **Ubuntu** ami image.

```bash
     ssh ubuntu@3.109.154.107
     ubuntu@jenkins:~$
```

We can access the Jenkins server, which means we are good to go.

6. We can automate the provisioning of all the AWS resources using the `t_createall.sh` script located in the `cicd-setup/scripts`. While implementing Phase I for the first time, I recommend against using the `t_createall.sh` script to create all necessary resources at once. This is because our infrastructure setup utilizes `t2.medium` EC2 instances, and creating all resources simultaneously may result in higher AWS EC2 costs if instances remain idle while we configure other servers. Additionally, running a script may not provide a thorough understanding of how things work.

7. Let's move on to our next step, which is configuring our servers using Ansible playbooks. We can refer to this section whenever we need to provision the remaining servers on AWS. 

# **Configure the servers using Ansible**

I have created Ansible playbooks to configure all the servers according to the application/service we need to run on them.
The `playall.sh` script provides an option to automate the configuration management of all servers. However, as previously mentioned, solely relying on a script may not provide a comprehensive understanding of the underlying processes involved. 

### 1. **Jenkins server**
* Let's start with the `jenkins` server, but first, we need to populate our Ansible inventory file with the public IP addresses of our AWS instances so that we can run our Ansible playbooks. To do so, run the `generate_ansible_inventory.sh` from the `cicd-setup/scripts directory`.

```bash
  ~/repo/cicd-setup/scripts$
  ➜ ./generate_ansible_inventory.sh

  Creating inventory file for ansible ....

  Populating inventory file for ansible ....

  Done!!!
```

Check the newly created inventory file under ansible_config directory.

```bash
  ~/repo/cicd-setup$
  ➜ cat ansible_config/inventory
  jenkins ansible_host=3.109.154.107 ansible_user=ubuntu ansible_connection=ssh
  sonar ansible_host= ansible_user=ubuntu ansible_connection=ssh
  nexus ansible_host= ansible_user=ubuntu ansible_connection=ssh
  k8s-master ansible_host= ansible_user=ubuntu ansible_connection=ssh
  k8s-node1 ansible_host= ansible_user=ubuntu ansible_connection=ssh
```

We can see that the file is generated with the details required by Ansible to connect to the `jenkins` server. As we haven't provisioned the remaining servers yet, the IP details are empty for them.

* Now, to configure the `jenkins` server as per our requirements for this project, simply run the Ansible playbook located at `ansible_config/jenkins/jenkins.yaml`. You can check the Ansible playbook to understand how we are configuring the `jenkins` server. The playbook output shows us what all tasks are being performed.

```bash
  ~/repos/cicd-setup/ansible_config$ 
  ➜ ansible-playbook -i inventory jenkins/jenkins.yaml

  PLAY [Install and start Jenkins] ********************************************************************************************************************************************

  TASK [Gathering Facts] ******************************************************************************************************************************************************
  ok: [jenkins]

  TASK [ensure the jenkins apt repository key is installed] *******************************************************************************************************************
  changed: [jenkins]

  TASK [ensure the repository is configured] **********************************************************************************************************************************
  changed: [jenkins]

  TASK [Ensure java is installed] *********************************************************************************************************************************************
  changed: [jenkins]

  TASK [ensure jenkins is installed] ******************************************************************************************************************************************
  changed: [jenkins]

  TASK [ensure jenkins is running] ********************************************************************************************************************************************
  ok: [jenkins]

  PLAY [Install Helm and datree.io helm plugin] *******************************************************************************************************************************

  TASK [Gathering Facts] ******************************************************************************************************************************************************
  ok: [jenkins]

  TASK [Check if Helm is installed] *******************************************************************************************************************************************
  fatal: [jenkins]: FAILED! => {"changed": false, "cmd": "helm version", "delta": "0:00:00.002648", "end": "2023-05-12 18:31:45.516957", "msg": "non-zero return code", "rc": 127, "start": "2023-05-12 18:31:45.514309", "stderr": "/bin/sh: 1: helm: not found", "stderr_lines": ["/bin/sh: 1: helm: not found"], "stdout": "", "stdout_lines": []}
  ...ignoring

  TASK [Download Helm script] *************************************************************************************************************************************************
  changed: [jenkins]

  TASK [Install Helm] *********************************************************************************************************************************************************
  changed: [jenkins]

  TASK [Install unzip] ********************************************************************************************************************************************************
  changed: [jenkins]

  TASK [Check if Datree plugin exists] ****************************************************************************************************************************************
  ok: [jenkins]

  TASK [Install Datree plugin] ************************************************************************************************************************************************
  changed: [jenkins]

  PLAY [Install Docker] *******************************************************************************************************************************************************

  TASK [Gathering Facts] ******************************************************************************************************************************************************
  ok: [jenkins]

  TASK [Install required packages] ********************************************************************************************************************************************
  changed: [jenkins]

  TASK [Add Docker GPG key] ***************************************************************************************************************************************************
  changed: [jenkins]

  TASK [Add Docker repository] ************************************************************************************************************************************************
  changed: [jenkins]

  TASK [Install Docker] *******************************************************************************************************************************************************
  changed: [jenkins]

  TASK [Add jenkins to sudo group] ********************************************************************************************************************************************
  changed: [jenkins]

  TASK [Add jenkins to docker group] ******************************************************************************************************************************************
  changed: [jenkins]

  TASK [Add jenkins to sudoers file] ******************************************************************************************************************************************
  changed: [jenkins]

  TASK [Install kubectl] ******************************************************************************************************************************************************
  changed: [jenkins]

  TASK [Create kubectl alias for root user] ***********************************************************************************************************************************
  changed: [jenkins]

  PLAY RECAP ******************************************************************************************************************************************************************
  jenkins                    : ok=23   changed=17   unreachable=0    failed=0    skipped=0    rescued=0    ignored=1

```

* If the playbook is executed without any errors, we should be able to access the Jenkins application from your browser using the public IP address followed by port `8080`.

![alt diagram](assets/images/java-web-app-deployment/image7.png)

* Enter the initial admin password and click on continue.

* Select Installed suggested plugins.


![alt diagram](assets/images/java-web-app-deployment/image8.png)

* Now jenkins will install the suggested plugins, it will take 5-10 minutes for this to complete.

![alt diagram](assets/images/java-web-app-deployment/image9.png)

* Once the suggested plugins are installed, you will be prompted to create a user. Create the user according to your preferences.

![alt diagram](assets/images/java-web-app-deployment/image10.png)

* Next, you will see this screen, click on `Save and Finish` and then click on `Start using Jenkins` on the next screen.

![alt diagram](assets/images/java-web-app-deployment/image11.png)

![alt diagram](assets/images/java-web-app-deployment/image12.png)

* Finally, you will see the Jenkins dashboard, setup is completed.

![alt diagram](assets/images/java-web-app-deployment/image13.png)


### 2. **SonarQube Server**

* Follow the steps mentioned in **Create AWS instances using Terraform** section to provision the **SonarQube server** - `sonar`.

* Once the server is provisioned, we need to repopulate the Ansible inventory file before running the playbook to configure sonarqube server.

Run the script `cicd-setup/scripts/generate_ansible_inventory.sh`.

* Run the Ansible playbook located at `ansible_config/sonar/sonar.yaml`.

```bash
  ~/repos/cicd-setup/ansible_config$ 
  ➜ ansible-playbook -i inventory sonar/sonar.yaml

  PLAY [Install Docker and SonarQube] *****************************************************************************************************************************************

  TASK [Gathering Facts] ******************************************************************************************************************************************************
  ok: [sonar]

  TASK [Install required packages] ********************************************************************************************************************************************
  changed: [sonar]

  TASK [Add Docker GPG key] ***************************************************************************************************************************************************
  changed: [sonar]

  TASK [Add Docker repository] ************************************************************************************************************************************************
  changed: [sonar]

  TASK [Install Docker] *******************************************************************************************************************************************************
  changed: [sonar]

  TASK [Start SonarQube container] ********************************************************************************************************************************************
  changed: [sonar]

  PLAY RECAP ******************************************************************************************************************************************************************
  sonar                      : ok=6    changed=5    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```
* We have deployed our SonarQube application on a docker container and exposed it on 9000 port. We should be able to access the Jenkins application from your browser using the public IP address followed by port `9000`.

![alt diagram](assets/images/java-web-app-deployment/image14.png)


* The default username is `admin` and password is also `admin`.

* Next you will be promted to set a new password for the SonarQube application, set the new password and click on `update`.

![alt diagram](assets/images/java-web-app-deployment/image15.png)

* Now you will see the SonarQube dashboard, setup is complete.

![alt diagram](assets/images/java-web-app-deployment/image16.png)


### 3. **SonaType Nexus Server**

* Follow the steps mentioned in **Create AWS instances using Terraform** section to provision the **Nexus Repository Manager** server - `nexus`.

* Once the server is provisioned, we need to repopulate the Ansible inventory file before running the playbook to configure nexus server.

Run the script `cicd-setup/scripts/generate_ansible_inventory.sh`.

* Run the Ansible playbook located at `ansible_config/nexus/nexus.yaml`.

```bash
  ~/repos/cicd-setup/ansible_config$ 
  ➜ ansible-playbook -i inventory nexus/nexus.yaml

  PLAY [Install Nexus] ********************************************************************************************************************************************************

  TASK [Gathering Facts] ******************************************************************************************************************************************************
  ok: [nexus]

  TASK [Install Java 8] *******************************************************************************************************************************************************
  changed: [nexus]

  TASK [Download Nexus] *******************************************************************************************************************************************************
  changed: [nexus]

  TASK [Extract Nexus] ********************************************************************************************************************************************************
  changed: [nexus]

  TASK [Start Nexus] **********************************************************************************************************************************************************
  changed: [nexus]

  PLAY RECAP ******************************************************************************************************************************************************************
  nexus                      : ok=5    changed=2    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

* Nexus takes some time to load so wait for 2-3 minutes and then access it using the public IP address followed by port `8081`.

![alt diagram](assets/images/java-web-app-deployment/image17.png)

* Click on "Sign In" and follow the on-screen instructions to get the initial admin password for Nexus. You will be prompted to set a new password for **Nexus**. Make sure to store this password as we will need it in Phase II of our project where we integrate different applications together.

* On the next screen make sure `Enable anonymous` access is selected and then click on Next.

![alt diagram](assets/images/java-web-app-deployment/image18.png)

* Click on Finish, the setup is completed.

### 4. **Kubernetes Cluster**

* In this section we will setup and configure a kubernetes cluster on AWS using kubeadm tool.

* We will be deploying two k8s nodes, one control plane node `k8s-master` and one worker node `k8s-node1`

* Follow the steps mentioned in **Create AWS instances using Terraform** section to provision the `k8s-master` and `k8s-node1` servers for our kubernetes cluster.

* Once both the servers are provisioned, we need to repopulate the Ansible inventory file before running the playbook to setup and configure our kubernetes cluster.

Run the script `cicd-setup/scripts/generate_ansible_inventory.sh`.

* Run the Ansible playbook located at `ansible_config/k8s/k8s_cluster_setup.yaml`.

```bash
  ~/repos/cicd-setup/ansible_config$ 
  ➜ ansible-playbook -i inventory k8s/k8s_cluster_setup.yaml

  PLAY [Add private IP of k8s instances to init scripts] **********************************************************************************************************************

  TASK [Gathering Facts] ******************************************************************************************************************************************************
  ok: [localhost]

  TASK [Run add_k8s_ip.sh] ****************************************************************************************************************************************************
  changed: [localhost]

  PLAY [Setup k8s Control Plane (master) node] ********************************************************************************************************************************

  TASK [Gathering Facts] ******************************************************************************************************************************************************
  ok: [k8s-master]

  TASK [Copy master.sh script to remote server] *******************************************************************************************************************************
  changed: [k8s-master]

  TASK [Configure Control Plane (master) node] ********************************************************************************************************************************
  changed: [k8s-master]

  TASK [Get kubeadm join command] *********************************************************************************************************************************************
  changed: [k8s-master]

  TASK [Generate token to join the k8s cluster] *******************************************************************************************************************************
  changed: [k8s-master]

  TASK [Create join_cluster.sh] ***********************************************************************************************************************************************
  changed: [k8s-master -> localhost]

  TASK [Create kubectl alias] *************************************************************************************************************************************************
  changed: [k8s-master]

  PLAY [Configure kubectl for ubuntu user] ************************************************************************************************************************************

  TASK [Gathering Facts] ******************************************************************************************************************************************************
  ok: [k8s-master]

  TASK [Create .kube directory for ubuntu user] *******************************************************************************************************************************
  changed: [k8s-master]

  TASK [Copy admin.conf to ubuntu's .kube directory] **************************************************************************************************************************
  changed: [k8s-master]

  TASK [Change ownership of .kube/config file] ********************************************************************************************************************************
  ok: [k8s-master]

  TASK [Create kubectl alias for ubuntu] **************************************************************************************************************************************
  changed: [k8s-master]

  PLAY [Setup k8s worker node] ************************************************************************************************************************************************

  TASK [Gathering Facts] ******************************************************************************************************************************************************
  ok: [k8s-node1]

  TASK [Copy nodes.sh script to remote server] ********************************************************************************************************************************
  changed: [k8s-node1]

  TASK [Configure Worker node] ************************************************************************************************************************************************
  changed: [k8s-node1]

  TASK [Copy join_cluster.sh] *************************************************************************************************************************************************
  changed: [k8s-node1]

  TASK [Join k8s cluster] *****************************************************************************************************************************************************
  changed: [k8s-node1]

  PLAY [Copy the kubeconfig form k8s-master] **********************************************************************************************************************************

  TASK [Gathering Facts] ******************************************************************************************************************************************************
  ok: [k8s-master]

  TASK [Fetch the file from the k8s-master to localhost] **********************************************************************************************************************
  changed: [k8s-master]

  PLAY [Jenkins User kubectl Setup] *******************************************************************************************************************************************

  TASK [Gathering Facts] ******************************************************************************************************************************************************
  ok: [jenkins]

  TASK [create directory and set ownership of .kube directory for jenkins user] ***********************************************************************************************
  changed: [jenkins]

  TASK [Copy the file from localhost to jenkins] ******************************************************************************************************************************
  changed: [jenkins]

  PLAY RECAP ******************************************************************************************************************************************************************
  jenkins                    : ok=3    changed=2    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0  
  k8s-master                 : ok=14   changed=10   unreachable=0    failed=0    skipped=0    rescued=0    ignored=0  
  k8s-node1                  : ok=5    changed=4    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0  
  localhost                  : ok=2    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

* Once the playbook is successfully executed, we can login to our master nodes to verify the setup and configuration of our k8s cluster.

```bash
  ssh ubuntu@65.0.21.196

  ubuntu@k8s-master:~$ kubectl get nodes
  NAME         STATUS   ROLES           AGE     VERSION
  k8s-master   Ready    control-plane   11m     v1.27.1
  k8s-node1    Ready    <none>          9m39s   v1.27.1

  ubuntu@k8s-master:~$ kubectl get pods -n kube-system
  NAME                                 READY   STATUS    RESTARTS      AGE
  coredns-5d78c9869d-blhx8             1/1     Running   0             21m
  coredns-5d78c9869d-tfzj6             1/1     Running   0             21m
  etcd-k8s-master                      1/1     Running   0             21m
  kube-apiserver-k8s-master            1/1     Running   0             21m
  kube-controller-manager-k8s-master   1/1     Running   0             21m
  kube-proxy-qsp48                     1/1     Running   0             19m
  kube-proxy-rv87l                     1/1     Running   0             21m
  kube-scheduler-k8s-master            1/1     Running   0             21m
  weave-net-mx4lj                      2/2     Running   0             19m
  weave-net-zddmr                      2/2     Running   1 (20m ago)   21m
```

* We can see that both nodes are in a Ready state, and all pods in the kube-system namespace are up and running, indicating that our k8s cluster has been deployed properly.

### **With this we have successfully completed Phase I of our project, the required infrastructure for the CICD pipeline is successfully provisioned and configured.**
**Note:**

<mark style="background-color: #FFFF00" >When implementing Phase I for the first time, it may take a considerable amount of time to complete. If you need to take a break or are unable to continue for a few days, it is not advisable to keep the AWS resources running during this time as it can result in significantly higher charges.</mark >

For such scenarios or if you make mistakes during Phase I and need to start over but don't want to repeat the same steps again, you can use the following scripts in the cicd-setup/scripts directory:

    1. t_createall.sh: provisions all EC2 instances

    2.t_destroyall.sh: destroys all EC2 instances

    3. playall.sh: automates the configuration management of all servers

    4. phase1.sh: automates both provisioning and configuration management processes

After automating the provisioning and configuration management processes, you will still need to complete certain manual steps in the initial setup of applications such as Jenkins, SonarQube, and Nexus. To do this, you can access the dashboards of each application using the public IP followed by the port in your web browser.

That's it for Phase I, let's proceed to Phase II.

## **Phase II: The CICD Pipeline**

* In this phase we will do everything from integrating our Jenkins, SonarQube, Nexus Repository Manager, Kubernetes cluster, Helm, datree.io applications with each other to deploying our Java web application on the K8s cluster using Helm charts based on pull request triggers. Let's begin.


### **Clone the git repository**
* We will be updating and pushing the changes to the repository so we need to clone the repository via ssh to do so first create a fork of the repository and then

clone it using the ssh URL.

```bash
  git clone git@github.com:mandeepsingh10/java-gradle-app.git
```

* Currently we only have `main` branch in the repository, so we will create a new branch called `dev` as it is not a good practice to modify the `main` branch.

```bash
  java-gradle-app on main via 🅶 v7.1.1 via ☕ 
  ➜ git branch
  * main
  java-gradle-app on main via 🅶 v7.1.1 via ☕ 
  ➜ git checkout -b dev
  Switched to a new branch 'dev'
  java-gradle-app on dev via 🅶 v7.1.1 via ☕ 
  ➜ git branch
  * dev
    main
```

### **Creating a pipeline job**
* Go to the jenkins dashboard and click on `New Item`, select the type as `Pipeline` and Enter a name for the pipeline. Click on `OK`.

![alt diagram](assets/images/java-web-app-deployment/image19.png)


* Navigate to the `Pipeline` section of the job and select `Pipeline script from SCM`, select `SCM` as `Git`, enter the repository URL, leave the `Credentials` field empty as our repo is public. In the `Branches to build` section, select `Branch Specifier` as the newly created dev branch. Click on `Save`.


![alt diagram](assets/images/java-web-app-deployment/image20.png)

* Now, let's create a `Jenkinsfile` in the dev branch with a simple one stage pipeline to check if our code is getting pulled properly or not. Open your favorite code editor and create the `Jenkinsfile` in the root directory of the repo, make sure you are checked out to `dev` branch.

```bash
  pipeline{
      agent any
      stages{
          stage("Git Test"){
              steps{
                  echo "Executing git connection test"
              }
              post{
                  success{
                      echo "git repository cloned successfully"
                  }
                  failure{
                      echo "git clone action failed"
                  }
              }
          }
      }
      post{
          success{
              echo "========pipeline executed successfully ========"
          }
          failure{
              echo "========pipeline execution failed========"
          }
      }
  }
```

* Add the Jenkinsfile to dev branch and commit the changes.

```bash
  java-gradle-app on  dev  🛤️ ×1via 🅶 v7.1.1 via ☕ 
  ➜ git branch
  * dev
    main

  java-gradle-app on  dev  🛤️ ×1via 🅶 v7.1.1 via ☕ 
  ➜ git status
  On branch dev
  Untracked files:
    (use "git add <file>..." to include in what will be committed)
      Jenkinsfile

  nothing added to commit but untracked files present (use "git add" to track)

  java-gradle-app on  dev  🛤️ ×1via 🅶 v7.1.1 via ☕ 
  ➜ git add .

  java-gradle-app on  dev  🗃️×1via 🅶 v7.1.1 via ☕ 
  ➜ git commit -m "Added Jenkinsfile"
  [dev 5b6b195] Added Jenkinsfile
   1 file changed, 26 insertions(+)
   create mode 100644 Jenkinsfile

  java-gradle-app on  dev via 🅶 v7.1.1 via ☕ 
  ➜ git push
  fatal: The current branch dev has no upstream branch.
  To push the current branch and set the remote as upstream, use

      git push --set-upstream origin dev

  java-gradle-app on  dev via 🅶 v7.1.1 via ☕ 
  ✖  git push --set-upstream origin dev
  Enumerating objects: 4, done.
  Counting objects: 100% (4/4), done.
  Delta compression using up to 8 threads
  Compressing objects: 100% (3/3), done.
  Writing objects: 100% (3/3), 459 bytes | 459.00 KiB/s, done.
  Total 3 (delta 1), reused 0 (delta 0), pack-reused 0
  remote: Resolving deltas: 100% (1/1), completed with 1 local object.
  remote: 
  remote: Create a pull request for 'dev' on GitHub by visiting:
  remote:      https://github.com/mandeepsingh10/java-gradle-app/pull/new/dev
  remote: 
  To github.com:mandeepsingh10/java-gradle-app.git
   * [new branch]      dev -> dev
  Branch 'dev' set up to track remote branch 'dev' from 'origin'.
```

We received an error saying `The current branch dev has no upstream branch`. This was because we created a new branch but didn't set the remote upstream for it.
We ran the command `git push --set-upstream origin dev` to push the changes after setting the upstream.

* Now, let's try building our pipeline job in jenkins. Goto Jenkins dashboard and then select `java-gradle-app` and click on `Build Now`.

![alt diagram](assets/images/java-web-app-deployment/image21.png)

* Our test build was successfull. Let's check the logs, click on the `#1` build and then select `Console Output`.

![alt diagram](assets/images/java-web-app-deployment/image22.png)

We can see the commands that were executed during the execution and some other details, this completes our test run, now let's integrate SonarQube with our Jenkins server.

## **STAGE I : SonarQube Integration**

* In this step, we will integrate our SonarQube server with our Jenkins server so that we can perform Code Quality checks, code smells etc.

* To start, we'll need to install some jenkins plugins, to do so, navigate to
`Manage Jenkins` > `Manage Plugins` > `Available` and install the plugins listed below:

    1. SonarQube Scanner for Jenkins

    2. Sonar Gerrit

    3. Sonar Quality Gates

    4. SonarQube Generic Coverage

    5.Quality Gates

    6. Docker

    7. Docker Pipeline

    8. docker-build-step

Click on `Install without restart`, it will take some time to install the plugins.

* Next, we will establish connection between SonarQube and Jenkins Server for that we will need an authentication token from SonarQube.
Goto SonarQube dashboard, goto `Administration` > `Security`, generate a token for administrator user by clicking on the icon below the Tokens column.

![alt diagram](assets/images/java-web-app-deployment/image23.png)

Choose name as jenkins and leave the `Expires in` field at the default value of 30 days and click on `Generate`.

![alt diagram](assets/images/java-web-app-deployment/image24.png)

* Copy this token, we will create a Jenkins secret using this token which will used to authenticate jenkins while connecting with sonarqube.
Goto `Manage Jenkins` > `Security` > `Credentials`. Click on `global` and then `add credentials`, select `Kind` as Secret text and `ID` as `sonar-token`

![alt diagram](assets/images/java-web-app-deployment/image25.png)

* Now, we need to configure SonarQube settings, Goto `Manage Jenkins` > `Configure System` . There will be a section SonarQube servers, we need to update the details in this section. Click on `Add SonarQube`, Enter the name as `sonarserver`, Server URL is `http://IP:PORT`, select `Server authentication` token as `sonar-token`. Also enable `Environment variables`.

![alt diagram](assets/images/java-web-app-deployment/image26.png)

* We need to use this token in our Jenkinsfile so we will need to generate a pipeline script for that, navigate to the pipeline job and click on `Pipeline Syntax` and generate sonarqube pipeline script from the **Snippet Generator**.
Select `Sample Step` as `withSonarQubeEnv` , select `Server authentication token` as `sonar-token`, click on `Generate Pipeline Script`.

```bash
  withSonarQubeEnv {
      // some block
  }
```

This is the pipeline script generated, similarly whenever we need to generate any script we can use the `Snipper Generator`.

* Now add this pipeline script to the Jenkinsfile. Remove the previous test stages and create a new Stage `Sonar Quality Check` where we will perform code analysis by querying SonarQube.

```bash
  pipeline{
      agent any
      environment{
          VERSION = "{env.BUILD_ID}"
      }
      stages{
          stage("Sonar Quality Check"){
              steps{
                  script{
                      withSonarQubeEnv(credentialsId: 'sonar-token') {
                          sh 'chmod +x gradlew'
                          sh './gradlew sonarqube --info'
                      }
                  }
              }
          }
      }
  }
```

* We have give execute permission to gradlew and then we have executed `./gradlew sonarqube` which helps us in pushing the code to sonarQube, where we will validate our checks against the sonar rules.

* Add the JenkinsFile to the git repo and push the changes.

* Go to the Jenkins dashboard and build the pipeline job and see if it builds successfully.

![alt diagram](assets/images/java-web-app-deployment/image27.png)

* We can see that the job was a success, we can also check the SonarQube Dashboard.

![alt diagram](assets/images/java-web-app-deployment/image28.png)

* Now, we will we also need to create a webhook to have connection between jenkins and sonarqube.

    1. To create a webhook navigate to sonarqube dashboard then `administration` > `configuration` > `webhooks`.

    2. Enter the URL of your jenkins host with port suffixed with
       /sonarqube-webhook/ - The URL will be `http://jenkins_ip:8080/sonarqube-webhook/`

![alt diagram](assets/images/java-web-app-deployment/image29.png)

* The data that will be sent to jenkins via the sonarqube webhook can be seen by clicking the small icon beside the time in the `Last delivery` column (it will be available after we start a new build after creating the webhook).
Go back to jenkins dashboard and click on `Build Now`. Once the build is completed successfully, go back to SonarQube Dashboard and check the Last Delivery Data.


![alt diagram](assets/images/java-web-app-deployment/image30.png)


* We are interested in the qualityGate section of the Last Delivery data.

![alt diagram](assets/images/java-web-app-deployment/image31.png)


```bash
  "qualityGate": {
              "name": "Sonar way",
              "status": "OK",
```

* If the status is `ok` then only our pipeline will move on to the next stage otherwise the build will fail.

* Now, we will add a block to our `Sonar Quality Check` stage which will wait for 15 minutes for the Quality Gate status to chaneg to `ok` state.

```bash
  timeout(time: 15, unit: 'MINUTES') {
      def qg = waitForQualityGate()
      if (qg.status != 'OK') {
          error "Pipeline aborted due to quality gate failure: ${qg.status}"
          }
      }
```
```bash
  pipeline{
      agent any
      environment{
          VERSION = "{env.BUILD_ID}"
      }
      stages{
          stage("Sonar Quality Check"){
              steps{
                  script{
                      withSonarQubeEnv(credentialsId: 'sonar-token') {
                          sh 'chmod +x gradlew'
                          sh './gradlew sonarqube --info'
                      }
                      timeout(time: 15, unit: 'MINUTES') {
                          def qg = waitForQualityGate()
                          if (qg.status != 'OK') {
                              error "Pipeline aborted due to quality gate failure: ${qg.status}"
                          }
                      }
                  }
              }
          }
      }
  }
```

* Commit and push the changes to the dev branch and then run the build.

![alt diagram](assets/images/java-web-app-deployment/image32.png)


* This concludes the SonarQube Integration.

## **STAGE II : Build docker images and push to Nexus**


1. ### **Create a private repository in Nexus Repository Manager**

    * We will create a private repository to store our docker images on the Nexus Repository Manager.

        * Go to Nexus dashboard > `Repositories` > `Create Repositor`y Select Recipe as `docker-hosted` Select HTTP port as `8083` Click on Create repository



![alt diagram](assets/images/java-web-app-deployment/image33.png)


* Next, we need to configure this repository on our jenkins server as an insecure registry to so that we can push our docker images to this repo.
To do so, go to the jenkins server and create or edit the file `/etc/docker/daemon.json`

```bash
  vim /etc/docker/daemon.json
  { "insecure-registries":["nexus_machine_ip:8083"] }
```

It will look like this `{ "insecure-registries":["13.235.91.151:8083"] }`

* Now restart the docker service using `systemctl restart docker.service` and check if the insecure registry is added properly.

```bash
  root@jenkins:~# docker info | grep Insecure -A1
   Insecure Registries:
    13.235.91.151:8083
```

* We have successfully created and configured the nexus repository for storing our docker images, now let's try to login to the repository.

```bash
  root@jenkins:~# docker login -u admin 13.235.91.151:8083
  Password: 
  WARNING! Your password will be stored unencrypted in /root/.docker/config.json.
  Configure a credential helper to remove this warning. See
  https://docs.docker.com/engine/reference/commandline/login/#credentials-store
  Login Succeeded
```

* Next, we need to add the password for our nexus repository as a secret in jenkins so that we can use it in our pipeline stages.
Goto `Manage Jenkins` > `Security` > `Credentials`. Click on `global` and then `add credentials`, select kind as `secret text`. Click on `Create`.


![alt diagram](assets/images/java-web-app-deployment/image34.png)


2. ### **Creating a multi-stage Dockerfile**

* This Dockerfile is creating a Docker image that deploys a Java web application to Tomcat using Docker multi-stage build.

```bash
  FROM openjdk:11 as base

  WORKDIR /app

  COPY . .

  RUN chmod +x gradlew

  RUN ./gradlew build

  FROM tomcat:9

  WORKDIR webapps/

  COPY --from=base /app/build/libs/sampleWeb-0.0.1-SNAPSHOT.war .

  RUN rm -rf ROOT && mv sampleWeb-0.0.1-SNAPSHOT.war ROOT.war
```

* Here is a breakdown of what is happening in each step:

    1. `FROM openjdk:11 as base` - This line sets the base image for the build process to the official OpenJDK 11 image.

    2. `WORKDIR /app` - This line sets the working directory for the Docker container to /app.

    3. `COPY . .` - This line copies the current directory (where the Dockerfile is located) into the /app directory in the Docker container.

    4. `RUN chmod +x gradlew` - This line makes the gradlew script executable.

    5. `RUN ./gradlew build` - This line runs the gradlew script to build the Java application.

    6. `FROM tomcat:9` - This line sets the base image to the official Tomcat 9 image for the second stage of our multi stage Dockerfile

    7. `WORKDIR webapps/` - This line sets the working directory for the Docker container to /usr/local/tomcat/webapps, where Tomcat looks for web applications.

    8. `COPY --from=base /app/build/libs/sampleWeb-0.0.1-SNAPSHOT.war .` - This line copies the built Java web application from the "base" image to the current directory in the second image which is our final image.

    9. `RUN rm -rf ROOT && mv sampleWeb-0.0.1-SNAPSHOT.war ROOT.war` - This line removes the existing ROOT web application and renames the copied application to ROOT.war so that it becomes the default application served by Tomcat.

* We can check if our docker file is working as expected or not, go to the jenkins server and navigate to the path `/var/lib/jenkins/workspace/sampleWeb_app`, here we will have all our project files from the git repo. We can create our Dockerfile here and try to build test images.

```bash
  docker build -t test-tomcat .

  jenkins@jenkins:~/workspace/java-gradle-app$ docker images 
  REPOSITORY    TAG       IMAGE ID       CREATED          SIZE
  test-tomcat   latest    0589959663b8   25 minutes ago   514MB
```

```bash
  docker run -itd -p 7777:8080 test-tomcat

  jenkins@jenkins:~/workspace/java-gradle-app$ docker run -itd -p 7777:8080 test-tomcat
  584f79d32cfa20f8b7428cdc9d2ca80928f717e14fb93ee59ee0bd7019ce2372
  jenkins@jenkins:~/workspace/java-gradle-app$ docker ps
  CONTAINER ID   IMAGE         COMMAND             CREATED         STATUS         PORTS                                       NAMES
  584f79d32cfa   test-tomcat   "catalina.sh run"   5 seconds ago   Up 3 seconds   0.0.0.0:7777->8080/tcp, :::7777->8080/tcp   nice_mcclintock
```

* We can access the web application by going to `jenkins_ip:7777` in our web browser.

* Make sure to delete the containers and images we created for our testing purpose in the previous test after confirming that our container is running as expected


3. ### **Adding Stage II : Build docker images and push to Nexus to Jenkinsfile**

    * We need to do four steps to build our image and push the image to the nexus repository.

        1. ### **Tag and build docker image**

            * We can tag our image using the command
             `docker build -t nexus_server_ip:8083/myapp:$VERSION`

            * `VERSION` needs to be unique as every change to the application will trigger a new job and will create a new image so we need a variable which we can use as tag, we can use the build number of our jenkins job as a tag. The image created by the job will have the build number as tag.

            * For this to work, first we need to define `$VERSION` in the pipeline, it's value will be build number of the jenkins job.

            * Define the `VERSION` in the pipeline using environment variable which will be availabe for use during the execution of the pipeline.
            
            ```bash
              environment{
              VERSION = "${env.BUILD_ID}"
              }
            ```

        2. ### **Login to the Nexus repo**

            * This can be done using the command
              `docker login -u admin -p $nexus_pass_var nexus_server_ip:8083`

            * `$nexus_pass_var` is variable through which we access the jenkins credential that we created for the storing the admin password of the nexus repository.

            * We will use a withCredentials block to access the credential `nexus_pass`.

            ```bash
              withCredentials([string(credentialsId: 'nexus_pass', variable: 'nexus_pass_var')]) {
              sh '''
              docker build -t nexus_server_ip:8083/myappapp:${VERSION} .
              docker login -u admin -p $nexus_docker_repo_pass_var nexus_server_ip:8083
            ```

        3. ### **Push the docker image to the nexus repo**

            ```bash
              #Use this command to push the image to nexus repo
              docker push nexus_server_ip:8083/myappapp:${VERSION}
            ```

        4. ### **Remove the image from the server after the image is pushed to nexus**

            ```bash
              docker rmi nexus_server_ip:8083/myapp:${VERSION}
            ```

        * The final code of this stage of pipeline will look like this:
          We've also created a new environment variable in the pipeline called DOCKER_HOSTED_EP which will declare the value of nexus_machine_ip:8083 as an variable which will be available to the pipeline through all the stages.

            ```bash
                    pipeline{
            agent any
            environment{
                VERSION = "${env.BUILD_ID}"
                DOCKER_HOSTED_EP = "13.235.91.151:8083" 
            }
            stages{
                stage("Sonar Quality Check"){
                    steps{
                        script{
                            withSonarQubeEnv(credentialsId: 'sonar-token') {
                                sh 'chmod +x gradlew'
                                sh './gradlew sonarqube --info'
                            }
                            timeout(time: 1, unit: 'MINUTES') {
                                def qg = waitForQualityGate()
                                if (qg.status != 'OK') {
                                    error "Pipeline aborted due to quality gate failure: ${qg.status}"
                                }
                            }
                        }
                    }
                }
                stage("Build docker images and push to Nexus"){
                    steps{
                        script{
                            withCredentials([string(credentialsId: 'nexus_pass', variable: 'nexus_pass_var')]) {
                                sh '''
                                docker build -t $DOCKER_HOSTED_EP/javawebapp:${VERSION} .
                                docker login -u admin -p $nexus_pass_var $DOCKER_HOSTED_EP
                                docker push $DOCKER_HOSTED_EP/javawebapp:${VERSION}
                                docker rmi $DOCKER_HOSTED_EP/javawebapp:${VERSION}
                                '''
                                 }
                            }
                        }
                    }
                }            
            }
            ```

            * Once the build is successfully completed, check the nexus repository
              `docker-hosted` to see if the docker images were pushed to it.

            
        ![alt diagram](assets/images/java-web-app-deployment/image35.png)  

        * Build was successfull, build number is **9**, let's see if we have docker images in the nexus repository with tag as **9**.

        ![alt diagram](assets/images/java-web-app-deployment/image36.png)


        * **Docker image successfully pushed to the nexus repository!!!!!**

        * This concludes Stage II of our Pipeline, let's move on to Stage III.


## **STAGE III : Identify misconfigurations in Helm charts using datree.io**

* In this stage we will identify the misconfigurations in our HELM charts.

* We have already created the required YAML files for deployments, services and helm charts. These are present in the kubernetes/myapp folder in the root directory of the repo.

* First we will need a token from our [datree.io](https://www.datree.io) account in start analzing helm charts based on the policies/rules set in our [datree.io](https://www.datree.io) account.

    1. Go to [datree.io](https://www.datree.io) and login using your github or gmail account, after successful login, we will see the [datree.io](https://www.datree.io) dashboard


    ![alt diagram](assets/images/java-web-app-deployment/image37.png)


    2. The policies/rules against which our helm chart will be analzed can se accesed by navigating to **Policies > Active Rules**.

    ![alt diagram](assets/images/java-web-app-deployment/image38.png)


    3. Next, go to **SETTINGS > TOKEN MANAGEMENT**, click on `Create Token`

    ![alt diagram](assets/images/java-web-app-deployment/image39.png)


    * Enter a name in the `Label` section and select `write` in the `Type`
      section, click on Generate Token.

    ![alt diagram](assets/images/java-web-app-deployment/image40.png)


    4. Copy the token and create a Jenkins credential datree-token of Kind Secret text, enter the token as secret, click on `Create`.

    ![alt diagram](assets/images/java-web-app-deployment/image41.png)


    5. Our helm charts are in the kubernetes/myapp directory so we have to perform three steps sop that we can do static code analysis of our helm charts using datree.

        1. Change the working directory to `kubernetes/`

        ```bash
          dir('kubernetes/') {
          }
        ```

        2. Set datree token from the value of jenkins secret credential `datree-token`.

        ```bash
          dir('kubernetes/') {
          withCredentials([string(credentialsId: 'datree-token', variable: 'datree_token_var')]) {                
          sudo helm datree config set token $datree_token_var
            }    
          }
        ```

        3. Run the command `helm datree test myapp/`

        ```bash
          dir('kubernetes/') {
          withCredentials([string(credentialsId: 'datree-token', variable: 'datree_token_var')]) {
          sh '''
          sudo helm datree config set token $datree_token_var
          sudo helm datree test myapp/
          '''
            }    
          }
        ```

        4. Now, the complete pipeline for this stage will look like this:

        ```bash
         stage('Identifying the misconfiguration in HELM charts using datree'){
        steps{
         script{
             dir('kubernetes/') {
                 withCredentials([string(credentialsId: 'datree-token', variable: 'datree_token_var')]) {
                     sh '''
                     sudo helm datree config set token $datree_token_var
                     sudo helm datree test myapp/
                     '''
                         }    
                     }
                 }
             }
         }
        ```

        5. Next, we wil edit the jenkins file and commit the changes to the dev branch and then start a new build.

        ![alt diagram](assets/images/java-web-app-deployment/image42.png)


        6. Build was successful, let's check the console output for more details.

        ```bash
             Pipeline] { (Identify misconfigurations in HELM charts using datree.io)
            [Pipeline] script
            [Pipeline] {
            [Pipeline] dir
            Running in /var/lib/jenkins/workspace/java-gradle-app/kubernetes
            [Pipeline] {
            [Pipeline] withCredentials
            Masking supported pattern matches of $datree_token_var
            [Pipeline] {
            [Pipeline] sh
            + sudo helm datree config set token ****
            + sudo helm datree test myapp/

            [K
            | Loading... WWWWWWWWWWWWWW[K[K[K[K[K[K[K[K[K[K[K[K[K[K
            [K
            / Loading... WWWWWWWWWWWWWW[K[K[K[K[K[K[K[K[K[K[K[K[K[K
            [K
            - Loading... WWWWWWWWWWWWWW[K[K[K[K[K[K[K[K[K[K[K[K[K[K
            [K
            \ Loading... WWWWWWWWWWWWWW[K[K[K[K[K[K[K[K[K[K[K[K[K[K
            [K
            | Loading... WWWWWWWWWWWWWW[K[K[K[K[K[K[K[K[K[K[K[K[K[K
            [K
            / Loading... WWWWWWWWWWWWWW[K[K[K[K[K[K[K[K[K[K[K[K[K[K
            [K
            - Loading... WWWWWWWWWWWWWW[K[K[K[K[K[K[K[K[K[K[K[K[K[K
            [K
            \ Loading... WWWWWWWWWWWWWW[K[K[K[K[K[K[K[K[K[K[K[K[K[K
            [K
            | Loading... WWWWWWWWWWWWWW[K[K[K[K[K[K[K[K[K[K[K[K[K[K
            [K
            / Loading... WWWWWWWWWWWWWW[K[K[K[K[K[K[K[K[K[K[K[K[K[K
            [K
            - Loading... WWWWWWWWWWWWWW[K[K[K[K[K[K[K[K[K[K[K[K[K[K
            [K
            \ Loading... WWWWWWWWWWWWWW[K[K[K[K[K[K[K[K[K[K[K[K[K[K
            [K
            | Loading... WWWWWWWWWWWWWW[K[K[K[K[K[K[K[K[K[K[K[K[K[K
            [K
            / Loading... WWWWWWWWWWWWWW[K[K[K[K[K[K[K[K[K[K[K[K[K[K
            [K
            - Loading... WWWWWWWWWWWWWW[K[K[K[K[K[K[K[K[K[K[K[K[K[K
            [K
            \ Loading... WWWWWWWWWWWWWW[K[K[K[K[K[K[K[K[K[K[K[K[K[K
            [K
            | Loading... WWWWWWWWWWWWWW[K[K[K[K[K[K[K[K[K[K[K[K[K[K
            [K
            / Loading... WWWWWWWWWWWWWW[K[K[K[K[K[K[K[K[K[K[K[K[K[K
            [K
            - Loading... WWWWWWWWWWWWWW[K[K[K[K[K[K[K[K[K[K[K[K[K[K
            [K
            \ Loading... WWWWWWWWWWWWWW[K[K[K[K[K[K[K[K[K[K[K[K[K[K
            [K
            | Loading... WWWWWWWWWWWWWW[K[K[K[K[K[K[K[K[K[K[K[K[K[K
            [K
            / Loading... WWWWWWWWWWWWWW[K[K[K[K[K[K[K[K[K[K[K[K[K[K
            [K
            (Summary)

            - Passing YAML validation: 1/1

            - Passing Kubernetes (1.24.0) schema validation: 1/1

            - Passing policy check: 1/1

            +-----------------------------------+-----------------------+
            | Enabled rules in policy "Starter" | 0                     |
            | Configs tested against policy     | 2                     |
            | Total rules evaluated             | 0                     |
            | [36mTotal rules skipped[0m               | [36m0[0m                     |
            | [91mTotal rules failed[0m                | [91m0[0m                     |
            | [32mTotal rules passed[0m                | [32m0[0m                     |
            | See all rules in policy           | https://app.datree.io |
            +-----------------------------------+-----------------------+
            [Pipeline] }
            [Pipeline] // withCredentials
            [Pipeline] }
            [Pipeline] // dir
            [Pipeline] }
            [Pipeline] // script
            [Pipeline] }
            [Pipeline] // stage
            [Pipeline] }
            [Pipeline] // withEnv
            [Pipeline] }
            [Pipeline] // withEnv
            [Pipeline] }
            [Pipeline] // node
            [Pipeline] End of Pipeline
            Finished: SUCCESS
        ```

        7. We can see that datree plugin worked as expected. This concludes Stage III.


## **STAGE IV : Push Helm Charts to Nexus Repo**

1. ### **Create a private repository in Nexus Repository Manager**

    * We will create a private repository to store our Helm charts on the Nexus Repository Manager and

        * Go to Nexus dashboard > `Repositories` > `Create Repository` Select Recipe as `helm-hosted`. Click on `Create repository`.


        ![alt diagram](assets/images/java-web-app-deployment/image43.png)

        * We can see our `helm-hosted` repository listed under repositories.

        ![alt diagram](assets/images/java-web-app-deployment/image44.png)


        * Next step is to create the Stage in Jenkins Pipeline for pushing the helm charts to Nexus repo, for this no other configuration is need in Jenkins host because we will use Nexus repo api to push the helm charts.


        ```bash
          curl -u admin:$nexus_pass_var http://nexus_machine_ip:8081/repository/helm-hosted/ --upload-file myapp-${helmchartversion}.tgz -v
        ```

        * We will create a new environment variable in the pipeline called `HELM_HOSTED_EP` which will replace hard coded value for `nexus_machine_ip:8081` same as when we pushed the docker images and used the environment variable `DOCKER_HOSTED_EP`.

        * Next, we need to package our helm according to the helm chart version, this can be done by simply running the command `helm package myapp`.

        ```bash
          jenkins@jenkins:~/workspace/java-gradle-app/kubernetes$ cat myapp/Chart.yaml | grep 'version:'
          version: 0.1.0

          jenkins@jenkins:~/workspace/java-gradle-app/kubernetes$ helm package myapp/

          Successfully packaged chart and saved it to: /var/lib/jenkins/workspace/java-gradle-app/kubernetes/myapp-0.1.0.tgz
        ```

        * Helm package command will package the helm charts accoding to the chart `version` mentioned in `myapp/Chart.yaml`.

        * Our final code for this stage of the pipeline will look like this

        ```bash
        environment{
                VERSION = "${env.BUILD_ID}"
                DOCKER_HOSTED_EP = "13.235.91.151:8083" 
                HELM_HOSTED_EP = "13.235.91.151:8081"
            }
        ...
        ...
            stage("Push Helm Charts to Nexus Repo"){
            steps{
                script{
                dir('kubernetes/'){
                    withCredentials([string(credentialsId: 'nexus_pass', variable: 'nexus_pass_var')]) {
                            sh '''
                            helmchartversion=$(helm show chart myapp/ | grep version | awk '{print $2}')
                            helm package myapp/
                            curl -u admin:$nexus_pass_var http://$HELM_HOSTED_EP/repository/helm-hosted/ --upload-file myapp-${helmchartversion}.tgz -v
                                    '''
                                }   
                            }
                        }
                    }
                }
        ```

        * Let's run the build now.


        ![alt diagram](assets/images/java-web-app-deployment/image45.png)


        * The build was successful, now let's check if there were any images pushed to the helm repository.


        ![alt diagram](assets/images/java-web-app-deployment/image46.png)


        * Helm Charts pushed to the helm-hosted Nexus repo with the same tag as the helm chart version.

        * This concludes the Stage IV of our pipeline.


## **Stage V: Deploy application on k8s cluster**


* To deploy our applicatiojn on k8s-cluster we need to perform some additional steps.

    1. ### **Configuring the Jenkins servers to access the kubernetes cluster and run administrative commands.**

        * This was already done by the magic of our ansible playbooks, the requirement was to install kubectl utility on the Jenkins node (implemented in `jenkins.yaml`) and copy the the kubeconfig file i.e `/etc/kubernetes/admin.conf` or `/root/.kube/config`, both of them are the same.

        * We've copied the `/root/.kube/config` file from the `k8s-master` node to the `$HOME` directory of the jenkins user `/var/lib/jenkins/.kube/config` on the Jenkins node.

        * These steps were performed by these plays in the `k8s_cluster_setup.yaml`.

        ```bash
        #### Plays to copy kubeconfig to jenkins server for jenkins user #### Required for Stage V & VI : Deploying application on k8s cluster #####

        ##### PLAY 1 : COPY kubeconfig from k8s-master node to localhost #####
        -   name: Copy the kubeconfig from k8s-master 
            hosts: k8s-master
            become: true
            tasks:
            - name: Fetch the file from the k8s-master to localhost
                run_once: yes
                fetch:
                src: /root/.kube/config
                dest: buffer/
                flat: yes
        ##### PLAY 2 : INSTALL KUBECTL, CREATE .kube direcoty in $HOME of jenkins user, copy kubeconfig from localhost buffer to Jenkins server and sets the permission to 0600 and ownership to jenkins:jenkins#####
        -   name: Jenkins User kubectl Setup
            hosts: jenkins
            become: true
            tasks:
                - name: Install kubectl  
                snap: 
                    name: kubectl
                    classic: true
                    state: present
                - name: Create directory and set ownership of .kube directory for jenkins user
                file:
                    path: /var/lib/jenkins/.kube
                    state: directory
                    owner: jenkins
                    group: jenkins
                - name: Copy the file from localhost to jenkins
                copy:
                    src: buffer/config
                    dest: /var/lib/jenkins/.kube/config
                    mode: "0600"
                    owner: jenkins
                    group: jenkins
        ```

        * We decided to add these plays to the `k8s_cluster_setup.yaml` instead of a standalone Ansible playbook because they have a dependency on the K8s cluster being set up.

        * If we created a standalone playbook and someone tried to run it before the cluster was provisioned, they would encounter errors. By including the plays in the `k8s_cluster_setup.yaml`, we ensure that all necessary configurations for the Jenkins node to communicate with the K8s cluster are completed after the cluster is provisioned.

        * We can now sit back and relax knowing that once the cluster is provisioned in the earlier stages, everything is set up correctly.

    2. ### **Adding the docker-hosted Nexus repository on both the k8s cluster nodes**

        1. We are using containerd (CRI) so we cannot install docker runtime and configure insecure repo for docker as it will cause many issues in our cluster.

        2. We will have to configure our nexus repo as an insecure repository for containerd. The below mentioned steps need to be performed on both the k8s-nodes (we are only scheduling our deployments on k8s-node1 but it's a good practice to keep the configuration consisitent so we will do it for both the nodes).

            1. Set the default config file for **container.d** as `/etc/containerd/config.toml`

            ```bash
                 mkdir -p /etc/containerd
                 containerd config default>/etc/containerd/config.toml
                 systemctl restart containerd
                 systemctl status containerd.service
            ```

            2. Edit the `/etc/containerd/config.toml`

                * Find the line [plugins."io.containerd.grpc.v1.cri".registry.configs]

                * Add these six lines below it
            
            ```bash
              [plugins."io.containerd.grpc.v1.cri".registry.configs."13.235.91.151:8083"]     
                      [plugins."io.containerd.grpc.v1.cri".registry.configs."13.235.91.151:8083".tls]
                            ca_file = ""
                            cert_file = ""
                            insecure_skip_verify = true
                            key_file = ""
            ```

                Our docker-hosted repository is at 13.235.91.151:8083 so we are using this IP:PORT combination. Replace the IP address with the public ip of the Nexus Repository Manager and if you specified a different port for docker-hosted while confguring then use that.

            * Find the line `[plugins."io.containerd.grpc.v1.cri".registry.mirrors]`

            * Add these two lines below it

            ```bash
              [plugins."io.containerd.grpc.v1.cri".registry.mirrors."13.235.91.151:8083"]
              endpoint = ["http://13.235.91.151:8083"]
            ```

            * It will look like this after all the changes.

            ```bash
              [plugins."io.containerd.grpc.v1.cri".registry.configs]      [plugins."io.containerd.grpc.v1.cri".registry.configs."13.235.91.151:8081"]       [plugins."io.containerd.grpc.v1.cri".registry.configs."13.235.91.151:8081".tls]
                  ca_file = ""
                  cert_file = ""
                  insecure_skip_verify = true
                  key_file = ""    [plugins."io.containerd.grpc.v1.cri".registry.headers]      [plugins."io.containerd.grpc.v1.cri".registry.mirrors]
               [plugins."io.containerd.grpc.v1.cri".registry.mirrors."13.235.91.151:8083"]
                    endpoint = ["http://13.235.91.151:8083"]
            ```

            * Restart `containerd` service and try pulling an image from the `docker-hosted` Nexus repo.

            ```bash
              root@k8s-master:~# systemctl restart containerd.service

              root@k8s-master:~# crictl pull 13.235.91.151:8083/javawebapp:11
              Image is up to date for sha256:603cc7300cb07bf4ec156ddfcdaa72698fbeb441bda60bbc84704505e3c1428e

              root@k8s-master:~# crictl images | grep javawebapp
              13.235.91.151:8083/javawebapp             11                  603cc7300cb07       287MB
            ```

            * We are able to pull the container images from our private nexus repository docker-hosted.

            * Repeat the steps on the k8s-node1, our application will be deployed on the worker node as by default k8s control plane node has a taint on it which forbids us to deploy any pods on it except the kube-system ones.
            Taints: node-role.kubernetes.io/control-plane:NoSchedule

            * Once these steps are completed on the k8s worker node as well then we are good to go.

    3. ### **Configure Mail Server and add post block in Jenkins Pipeline**

        * Goto `Manage Jankins` > `Manage Plugins` > `Installed` and make sure that the `Email Extension Plugin` is installed.

        * Goto `Manage Jankins` > `Configure Systems` > `Email Notification` Let's configure smtp config for our gmail account and try sending a test email.



        ![alt diagram](assets/images/java-web-app-deployment/image47.png)


        * To configure these setting we'll need to go to our gmail account settings > `Manage Your Google Account`. Now we need to create an app password so that our jenkins app can send emails to our gmail account.

        * **Important**: To create an app password, you need 2-Step Verification on your Google Account.

        If you use 2-Step-Verification and get a "password incorrect" error when you sign in, you can try to use an app password.

        1. Go to your Google Account.

        2. Select **Security**

        3. Under "**Signing in to Google**," select **2-Step Verification**.

        4. At the bottom of the page, select **App passwords**.

        5. Enter a name that helps you remember where you’ll use the app password.

        6. Select **Generate**.

        7. To enter the app password, follow the instructions on your screen. The app password is the 16-character code that generates on your device.

        8. Select **Done**.

        * Use the created app password in the Email configuration for Jenkins (Step 2) After filling the required fields, it will look similar to this. Click on `Test Configuration`.

        ![alt diagram](assets/images/java-web-app-deployment/image48.png)



    * Our test config is working properly, let's setup the email now.

      First, we have to create a credential to use the app password in the jenkins pipeline to allow access jenkins to send emails.

        1. Goto `Manage Jenkins` > `Credentials` > `select global`, select kind as `Username with password`.


        ![alt diagram](assets/images/java-web-app-deployment/image49.png)


        2. Goto `Manage Jenkins` > `Configure Systems` > `Extended E-mail Notification`.


        ![alt diagram](assets/images/java-web-app-deployment/image50.png)


        3. Add a post block to the jenkins pipeline after the stages block.

        ```bash
         post {
            always {
                mail bcc: '', body: "<br>Project: ${env.JOB_NAME} <br>Build Number: ${env.BUILD_NUMBER} <br>Build URL: ${env.BUILD_URL}", cc: '', charset: 'UTF-8', from: '', mimeType: 'text/html', replyTo: '', subject: "${currentBuild.result} CI: Project name -> ${env.JOB_NAME}", to: "mandeepsingh1018@gmail.com";
                }
            }
        ```

        4. Also make sure that you have filled the Email Notification section as well in addition to the Extended Email Notification, if this is missed then the build will fail.

        ![alt diagram](assets/images/java-web-app-deployment/image51.png)


        5. Now build the job again, this time you'll recieve an email about the Status of the job.

        ![alt diagram](assets/images/java-web-app-deployment/image52.png)


    4. ### **Configure Slack notifications**

        * Download Slack plugin

        * Goto `Manage Jenkins` > `Manage Plugins` > `Available`, search for slack and download the plugin

        * Login to your slack account and create a channel for jenkins notifications.

        ![alt diagram](assets/images/java-web-app-deployment/image53.png)


        * On next screen, select `Public` and then click on `Create`.

        ![alt diagram](assets/images/java-web-app-deployment/image54.png)


        * Goto https://my.slack.com/services/new/jenkins-ci


        ![alt diagram](assets/images/java-web-app-deployment/image55.png)



        * Select the newly created slack channel for jenkins and click on Add `Jenkins CI Integration`.

        * Follow the on-screen instructions in step4 and get the **workspace (team subdomain)**, and **Integration Token Credential ID**

        * Create a secret text credential with **Integration Token Credential ID**


        ![alt diagram](assets/images/java-web-app-deployment/image56.png)



        * Goto `Manage Jenkins` > `Configure Systems`, Search for `Slack` settings.


        ![alt diagram](assets/images/java-web-app-deployment/image57.png)


        * Add this to the post block in your pipeline, you can also choose to change the frequency to always, success, or failure etc, I have added it in the same always block where mail is configured.

        ```bash
          slackSend channel: '#jenkins-cicd', message: "Project: ${env.JOB_NAME} \n Build Number: ${env.BUILD_NUMBER} \n Status: *${currentBuild.result}* \n More info at: ${env.BUILD_URL}"
        ```

        ```bash
            post {
                always {
                    mail bcc: '', body: "<br>Project: ${env.JOB_NAME} <br>Build Number: ${env.BUILD_NUMBER} <br>Build URL: ${env.BUILD_URL}", cc: '', charset: 'UTF-8', from: '', mimeType: 'text/html', replyTo: '', subject: "${currentBuild.result} CI: Project name -> ${env.JOB_NAME}", to: "mandeepsingh1018@gmail.com";
                    slackSend channel: '#jenkins-cicd', message: "Project: ${env.JOB_NAME} \n Build Number: ${env.BUILD_NUMBER} \n Status: *${currentBuild.result}* \n More info at: ${env.BUILD_URL}"
                    }
                }
        ```

        * Make sure to change the TeamDomain and channel name.

        * Make the changes to Jenkinsfile and push it to the dev branch.

        * Now, let's build the job and check if we get any slack notifications.


        ![alt diagram](assets/images/java-web-app-deployment/image58.png)

    
    5. ### **Deploy Helm Charts on k8s cluster**

        1. Let's start by checking if we have any release created on our cluster.

        ```bash
         jenkins@jenkins:~$ helm list
         NAME    NAMESPACE    REVISION    UPDATED    STATUS    CHART    APP VERSION
        ```

        We don't have any helm releases

        2. Let's add a new stage to our jenkins pipeline to create a release

        ```bash
        stage('Deploy application on k8s-cluster') {
          steps {
            script{
              dir ("kubernetes/"){  
                  sh 'helm upgrade --install --set image.repository="$DOCKER_HOSTED_EP/javawebapp" --set image.tag="${VERSION}" jwa1 myapp/ ' 
                     }   
                 }
             }
         }
         ```

        * The `steps` block contains a `script` block that changes the working directory to `kubernetes/` using the `dir` directive. This is where the Helm chart and the Kubernetes deployment configuration files are stored.

        * The command is a Helm CLI command that upgrades an existing Kubernetes deployment or installs a new one using a Helm chart located in the `myapp/` directory.

        * The command is broken down as follows:

            1. `helm upgrade`: This command upgrades a Helm release if it already exists, or installs a new one if it does not exist.

            2. `--install`: This flag indicates that a new release should be installed if it does not already exist.

            3. `--set`: This flag sets one or more values in the Helm chart's values file. In this case, it sets the `image.repository` and `image.tag` values to the specified values.

            4. `image.repository="13.235.91.151:8083/javawebapp"`: This sets the Docker image repository for the application to `13.235.91.151:8083/javawebapp`.

            5. `image.tag="${VERSION}"`: This sets the Docker image tag for the application to a value stored in the `${VERSION}` environment variable defined in the pipeline

            6. `jwa1`: This is the name of the Helm release.

            7. `myapp/`: This is the path to the Helm chart directory containing the `values.yaml` file and any other necessary files.

        3. Create a a kubernetes secret for nexus docker-hosted repo

        ```bash
         kubectl create secret docker-registry registry-secret --docker-server=13.235.91.151:8083 --docker-username=admin --docker-password=msx@9797 --docker-email=not-needed@yolo.com
        ```

        We need this secret to authenticate to the nexus repo hosted on our nexus server

        This is a kubectl command that creates a `Kubernetes secret` named `registry-secret` of type docker-registry. This secret is used to store credentials for authenticating with a Docker registry located at the specified IP address and port number

        `(13.235.91.151:8083)`. The `--docker-username`, `--docker-password`, and `--docker-email` flags are used to set the corresponding authentication credentials for the Docker registry.

        When we run the commands mentioned in **step2**, the image.tag will get the value of $VERSION variable that is build number and it will also be replaced in the `values.yaml` file in helm charts directory




























        













