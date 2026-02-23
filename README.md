# Netflix Clone DevSecOps: CI/CD Pipeline with Jenkins, Docker, Kubernetes & Security

![Netflix Clone Project](https://github.com/user-attachments/assets/7c97e322-5b45-4b01-a836-a623aa8d3f90)

In this guide, we will walk through the end-to-end deployment of a Netflix clone using a modern DevOps pipeline. We’ll leverage Jenkins for CI/CD automation, containerize the application with Docker, and orchestrate the deployment on a Kubernetes cluster. To ensure system reliability, we will implement a robust monitoring stack using Prometheus, Grafana, and Node Exporter to track real-time metrics across Jenkins and our K8s nodes.

CLICK HERE FOR GITHUB REPOSITORY : 

Steps:-

Step 1 — Launch an Ubuntu(22.04) m7i-flex.large Instance

Step 2 — Install Jenkins, Docker and Trivy. Create a Sonarqube Container using Docker.

Step 3 — Create a TMDB API Key.

Step 4 — Install Prometheus and Grafana On the new Server.

Step 5 — Install the Prometheus Plugin and Integrate it with the Prometheus server.

Step 6 — Email Integration With Jenkins and Plugin setup.

Step 7 — Install Plugins like JDK, Sonarqube Scanner, Nodejs, and OWASP Dependency Check.

Step 8 — Create a Pipeline Project in Jenkins using a Declarative Pipeline

Step 9 — Install OWASP Dependency Check Plugins

Step 10 — Docker Image Build and Push

Step 11 — Deploy the image using Docker

Step 12 — Kubernetes master and slave setup on Ubuntu (20.04)

Step 13 — Access the Netflix app on the Browser.

Step 14 — Terminate the AWS EC2 Instances.

## Now, let’s get started and dig deeper into each of these steps:-

Start by launching a m7i-flex.large instance on AWS using the Ubuntu AMI. You have the choice of generating a new key pair or selecting an existing one. In the Security Group configuration, ensure that HTTP and HTTPS traffic is enabled. Additionally, for experimental purposes, open all ports—though keep in mind this is only for learning and isn't a recommended security practice.

<img width="1562" height="223" alt="image" src="https://github.com/user-attachments/assets/4264e3ea-5063-43f8-b613-3d89669f9739" />

## Step 2 — Install Jenkins, Docker and Trivy

Connect to your console, and enter these commands to Install Jenkins

```
vi jenkins.sh #make sure run in Root (or) add at userdata while ec2 launch
```
```
#!/bin/bash
#!/bin/bash
sudo apt update
sudo apt install fontconfig openjdk-21-jre -y
java -version

sudo wget -O /etc/apt/keyrings/jenkins-keyring.asc \
  https://pkg.jenkins.io/debian-stable/jenkins.io-2026.key
echo "deb [signed-by=/etc/apt/keyrings/jenkins-keyring.asc]" \
  https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null
sudo apt update
sudo apt install jenkins -y

sudo systemctl enable jenkins
sudo systemctl start jenkins
sudo systemctl status jenkins
```
```
sudo chmod 777 jenkins.sh
./jenkins.sh    # this will installl jenkins
```

Once Jenkins is installed, you will need to go to your AWS EC2 Security Group and open Inbound Port 8080, since Jenkins works on Port 8080

```
<EC2 Public IP Address:8080>
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```
<img width="1915" height="1012" alt="image" src="https://github.com/user-attachments/assets/56375f93-2306-4fa3-9056-d2517b9ef446" />

Unlock Jenkins using an administrative password and install the suggested plugins.

img 

Jenkins will now get installed and install all the libraries.

img

Create a user click on save and continue.

Jenkins Getting Started Screen.

<img width="1912" height="1017" alt="image" src="https://github.com/user-attachments/assets/7eadd627-95d8-4792-948b-265bb5bbf744" />


