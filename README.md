# jenkins-cicd-project
cicd project

Project1- CI/CD Project - Jenkins-GIT-GIThub, Maven, Docker Build, Docker Run
# Jenkins Server Setup in Linux VM #
Project Setup using below tools
Maven
Git Hub
Jenkins
Docker
## Step - 1 : Create Linux VM ##
1) Create Ubuntu VM using AWS EC2 (t2.medium) 
2) Enable 8080 Port Number in Security Group Inbound Rules
3) Enable 9090 Port Number in security Group Inbound Rules
4) Connect VM using MobaXterm/ connect through putty /connect through windows CMD 

## Step-2 : Install Java ##
sudo apt update
#sudo apt install  openjdk-17-jre
Or 
sudo apt install default-jre
java –version

## Step-3 : Install Jenkins ##
sudo wget -O /usr/share/keyrings/jenkins-keyring.asc \
  	https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key
echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
  	https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
  	/etc/apt/sources.list.d/jenkins.list > /dev/null
sudo apt-get update
sudo apt-get install jenkins

## Step-4 : Start Jenkins ## 
sudo systemctl enable jenkins
sudo systemctl start jenkins
## Step-5 : Verify Jenkins ##
sudo systemctl status jenkins
## Step-6 : Open jenkins server in browser using VM public ip ##
http://public-ip:8080/     >>>>>> example  https://13.43.123.218:8080/
## Step-7 : Copy jenkins admin pwd ##
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
Enter the username password for the first time in the browser
Create Admin Account & Install Required/suggested Plugins in Jenkins
Now the jenkins dashboard will be ready
## Step-8 : Create Admin Account & Install Required Plugins in Jenkins 
	
## Step-9 : ## Install Docker on  Amazon Linux VM
sudo yum update -y 
sudo yum install docker -y
sudo service docker start
sudo usermod -aG docker jenkins
sudo usermod -aG docker ec2-user
exit
## Step-9 : Install Docker In Ubuntu VM
sudo apt update
curl -fsSL get.docker.com | /bin/bash
sudo usermod -aG docker jenkins
sudo usermod -aG docker ubuntu 
sudo systemctl restart jenkins
exit
## step-10 : Verify docker installation
Restart jenkins in browser
docker -v

## Step-11 : Configure Maven as Global Tool in Jenkins
Manage Jenkins -> Tools -> Maven Installation -> Add maven
	 -  config maven named as M3 - apply and save
Step - 12 : Create Jenkins Job
Stage-1 : Clone Git Repo
Stage-2 : Maven Build
Stage-3 : Create Docker Image
Stage-4 : Create Docker Container

Pipeline {
agent any
tools {
// Define the Maven tool 'M3'
maven ‘M3’     
}
stages {
stage(‘git clone’) {
//clone the git repository
steps {
git ‘https://github.com/skarasheed72github/maven-web-app.git’
}
}

stage(‘maven install’) {
//run maven clean package
steps {
sh ‘mvn clean package’
}
}
stage(‘docker image’) {
Build docker image
steps {
 sh ‘docker build -t rasheed .’
}
}
stage(‘docker container’) {
steps {
//Run docker container
sh ‘docker run -d -p 9090:8080 –name rasheeditc rasheed’
}
}
}
}

Step - 13 : Trigger Jenkins Job 
Step - 14 : Enable host port in security group inbound rules- already done in step-1
Step - 15 : Access Application in Browser
We should be able to access our application
URL : http://public-ip:port/repo
//Instead of manual build tiger
//Configure build tigger  using poll scm with five stars * * * * * 
//this will check every minute github whether code change is committed, if  committed build will be triggered automatically
To avoid container already exist insert commands in declarative statement as below
// add this code to build second time  when pipeline successfully build image

sh ‘docker stop rasheeditc’    
sh ‘docker rm rasheeditc’
Step - 16 : After your practise, delete resources we have used in AWS Cloud to avoid billing
END—---------------------
‘’’’’’’’’’’’’’’’’’’’’’’’’’’’’’’’’’’’’’’’’’’’’’’’’’’’’’’’’’’’’’’’’’’’’’’’’’’’’’’’’
SONARQUBE  - to check the code
Step-1: To install Sonarqube follow below steps:
After running sonarqube server in ubuntu or ec2-user follow below steps
 change the directory to as below
cd /home/ubuntu/
sudo wget https://binaries.sonarsource.com/Distribution/sonarqube/sonarqube-9.9.4.87374.zip
sudo unzip sonarqube-9.9.4.87374.zip
cd /sonarqube-9.9.4.87374/bin/linux-x86-64/
./sonar.sh console
or
docker run -d --name sonarqube -p 9000:9000 -v sonarqube_data:/opt/sonarqube/data sonarqube
docker volume create sonarqube_data

Step-2: in the browser type
 Ip:9000    >>>>>>>>>>>>>> enable inbound rule with 9000 port first
User and password as admin admin then enter
It will as for to change password- complete it
The dash board will be ready
Click on project- enter project name and enter setup button
Click on locally button
Generate token -click on generate - then one token will be generate 
Copy the token code and save in notepad in local machine

Step-3: go to jenkins dashboard  -got to manage jenkins- plugins-type sonarqube scanner and install this and restart jenkins
Check plugin is installed successfully
to go to jenkins- manage jenkins- credentials-  system.global- add new credentials- choose secret text- copy and paste code in secret- give name of id and description as sonar-token-id- ten click on crea
Again open managed jenkins and open system - go down here need to config sonarqube
Checkbox environment variable
Give name as Sonar
Server url : http://ip:9000/
Credentials - choose sonar-token-id   and then apply-save
Go to manage jenkins - tools - scroll down - will see sonarqube scanner- give name as sonar-scanner
Apply and save
Again go to sonarqube dash board
Click on continue

SELECT MAVEN
COPY THE CODE GENERATED
Go to jenkins code paste the above code
Make some change add ssh in front of maven installation

======================================================================================
This code is to install github clone, maven, docker build, docker run, finally executive the progreamme

pipeline {
    agent any
    tools {
        maven 'M3'
    }

    stages {
     stage('git clone') {
    steps {
        git 'https://github.com/skarasheed72github/maven-web-app.git'
    }
}
stage('maven install') {
    steps {
        sh 'mvn clean package'
    }
}
stage('docker image') {
    steps {
        sh 'docker build -t bhaskar .'
    }
}
stage('docker container') {
    steps {
        sh 'docker stop rasheeditc'
        sh 'docker rm rasheeditc'
        sh 'docker run -d -p 9090:8080 --name rasheeditc bhaskar'
    }
}
    }
}
======================================================================================
This code is to install github clone, maven, sonarcube analysis, docker build, docker run, finally executive the progreamme

pipeline {
    agent any
    tools {
        maven 'M3'
    }

    stages {
     stage('git clone') {
    steps {
        git 'https://github.com/skarasheed72github/maven-web-app.git'
    }
}
 stage('sonar analysis') {
    steps {
       sh "mvn clean verify sonar:sonar \
  -Dsonar.projectKey=sonar \
  -Dsonar.projectName='sonar' \
  -Dsonar.host.url=http://15.207.84.45:9000 \
  -Dsonar.token=sqp_79e466e33e2cde5118aa8bc6d0c27831a1526baf"
    }
}
stage('maven install') {
    steps {
        sh 'mvn clean package'
    }
}
stage('docker image') {
    steps {
        sh 'docker build -t bhaskar .'
    }
}
stage('docker container') {
    steps {
        sh 'docker stop rasheeditc'
        sh 'docker rm rasheeditc'
        sh 'docker run -d -p 9090:8080 --name rasheeditc bhaskar'
    }
}
    }
}

======================================================================================
This code is to executive terraform script (which is going to provision ec2 instance)
Note: first install jenkins, terraform on ec2 / local machine
Install the plugin of terraform in jenkins
Create a job in jenkins
Copy the jenkins script and paste in the jenkins
Set the global credentials  -new credentials- enter the secret and ID.


 

