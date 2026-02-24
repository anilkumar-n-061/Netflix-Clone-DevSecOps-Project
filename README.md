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

## 2B — Install Docker
```
sudo apt update
sudo apt install ca-certificates curl gnupg -y
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg
echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt update
apt-cache policy docker-ce
sudo apt install docker-ce -y 
sudo systemctl status docker
```
After the docker installation, we create a sonarqube container (Remember to add 9000 ports in the security group).
Now our sonarqube is up and running

```
docker run -d --name sonar -p 9000:9000 sonarqube:lts-community
```
<img width="1080" height="411" alt="image" src="https://github.com/user-attachments/assets/1a5eb905-3b82-4646-ada7-cad0675ab66d" />

Now our sonarqube is up and running

<img width="1342" height="532" alt="image" src="https://github.com/user-attachments/assets/ebc07a5b-f9a8-4b45-a3ec-cfdc49b9fc38" />

Enter username and password, click on login and change password

```
username admin
password admin
```
<img width="1112" height="945" alt="image" src="https://github.com/user-attachments/assets/e3644483-ae47-4b23-916b-e42f5c8e2af6" />

Update New password, This is Sonar Dashboard.

<img width="1916" height="687" alt="image" src="https://github.com/user-attachments/assets/c46bca97-c222-48ff-b294-5362d8c092d7" />

## 2C — Install Trivy

```
vi trivy.sh
```
```
sudo apt-get install wget apt-transport-https gnupg lsb-release -y
wget -qO - https://aquasecurity.github.io/trivy-repo/deb/public.key | gpg --dearmor | sudo tee /usr/share/keyrings/trivy.gpg > /dev/null
echo "deb [signed-by=/usr/share/keyrings/trivy.gpg] https://aquasecurity.github.io/trivy-repo/deb $(lsb_release -sc) main" | sudo tee -a /etc/apt/sources.list.d/trivy.list
sudo apt-get update
sudo apt-get install trivy -y
```
## Step 3: Create a TMDB API Key

Next, we will create a TMDB API key

Open a new tab in the Browser and search for TMDB

<img width="1318" height="835" alt="image" src="https://github.com/user-attachments/assets/f602c275-131c-422f-8758-cb53d9153ca4" />

Click on the first result, you will see this page.
<img width="1897" height="907" alt="image" src="https://github.com/user-attachments/assets/bbb1c94f-7589-49fd-9d02-90583ebcd90e" />

Click on the Login on the top right. You will get this page.

You need to create an account here. click on click here. I have account that’s why i added my details there.

<img width="1138" height="662" alt="image" src="https://github.com/user-attachments/assets/aae76ad8-e096-44cb-abe3-9f63e7a46793" />

once you create an account you will see this page.

<img width="1920" height="511" alt="image" src="https://github.com/user-attachments/assets/4d0e0c58-f4d9-4112-8641-86f436c7dca0" />

Let’s create an API key, By clicking on your profile and clicking settings.

<img width="1918" height="611" alt="fs" src="https://github.com/user-attachments/assets/743cc2d1-ee46-4c72-966c-fb275c80d087" />

Now click on API from the left side panel.

<img width="1920" height="831" alt="dfed" src="https://github.com/user-attachments/assets/1d1d6a7a-29c6-4dd8-b95d-0454ee6b722d" />

Now click on create

<img width="1920" height="705" alt="swf" src="https://github.com/user-attachments/assets/6181bd88-7768-42ee-8e34-28de4b472a7a" />

Provide basic details

<img width="1920" height="957" alt="image" src="https://github.com/user-attachments/assets/24a5ddbb-4c20-42fd-a278-5b14b2cc7409" />

<img width="1120" height="390" alt="SDA" src="https://github.com/user-attachments/assets/521536b4-2852-44ee-b088-af5f0706ecfc" />

Click on submit and you will get your API key.

<img width="1920" height="817" alt="SDVDS" src="https://github.com/user-attachments/assets/8b6d4ff5-ace2-494e-98b7-627fc065ff89" />

Step 4 — Install Prometheus and Grafana On the new Server

First of all, let’s create a dedicated Linux user sometimes called a system account for Prometheus. Having individual users for each service serves two main purposes:

It is a security measure to reduce the impact in case of an incident with the service.

It simplifies administration as it becomes easier to track down what resources belong to which service.

To create a system user or system account, run the following command:

```
sudo useradd \
    --system \
    --no-create-home \
    --shell /bin/false prometheus
```

<img width="374" height="116" alt="image" src="https://github.com/user-attachments/assets/e70b15b2-a04b-4d2f-8b64-6334bd21a934" />


–system – Will create a system account.
–no-create-home – We don’t need a home directory for Prometheus or any other system accounts in our case.
–shell /bin/false – It prevents logging in as a Prometheus user.
Prometheus – Will create a Prometheus user and a group with the same name.

Let’s check the latest version of Prometheus from the download page.

You can use the curl or wget command to download Prometheus.

```
wget https://github.com/prometheus/prometheus/releases/download/v2.47.1/prometheus-2.47.1.linux-amd64.tar.gz
```

<img width="1920" height="513" alt="image" src="https://github.com/user-attachments/assets/92378331-1f78-4b7e-b65a-2e1441dcd2df" />

```
tar -xvf prometheus-2.47.1.linux-amd64.tar.gz
```

<img width="758" height="327" alt="image" src="https://github.com/user-attachments/assets/8f3ba022-8072-46b1-a7ed-260eaa9b3e81" />

Usually, you would have a disk mounted to the data directory. For this tutorial, I will simply create a /data directory. Also, you need a folder for Prometheus configuration files.

```
sudo mkdir -p /data /etc/prometheus
```

<img width="552" height="74" alt="image" src="https://github.com/user-attachments/assets/82da67f3-3b0c-4247-8c21-be00557b5f4e" />


Now, let’s change the directory to Prometheus and move some files.

```
cd prometheus-2.47.1.linux-amd64/
```

<img width="619" height="224" alt="image" src="https://github.com/user-attachments/assets/220dcc3d-ce25-42e7-bcad-85a11f89f17a" />

First of all, let’s move the Prometheus binary and a promtool to the /usr/local/bin/. promtool is used to check configuration files and Prometheus rules.

```
sudo mv prometheus promtool /usr/local/bin/
```
<img width="961" height="123" alt="image" src="https://github.com/user-attachments/assets/6cda91d2-c474-4d93-bd5e-0c5adcaf2ff3" />

Optionally, we can move console libraries to the Prometheus configuration directory. Console templates allow for the creation of arbitrary consoles using the Go templating language. You don’t need to worry about it if you’re just getting started.

```
sudo mv consoles/ console_libraries/ /etc/prometheus/
```

<img width="1009" height="93" alt="image" src="https://github.com/user-attachments/assets/75cfc4ef-eaf1-47d4-9309-7b6d360ff85b" />

To avoid permission issues, you need to set the correct ownership for the /etc/prometheus/ and data directory.

<img width="1134" height="91" alt="image" src="https://github.com/user-attachments/assets/501b3f4f-5103-4ec5-b204-71b9dca5eaca" />

```
cd
rm -rf prometheus-2.47.1.linux-amd64.tar.gz
```

<img width="703" height="325" alt="image" src="https://github.com/user-attachments/assets/1341d2f8-0ad9-47cb-93e3-d502175526f3" />

```
prometheus --help
```

We’re going to use some of these options in the service definition.

We’re going to use Systemd, which is a system and service manager for Linux operating systems. For that, we need to create a Systemd unit configuration file.

Prometheus.service

```
[Unit]
Description=Prometheus
Wants=network-online.target
After=network-online.target
StartLimitIntervalSec=500
StartLimitBurst=5
[Service]
User=prometheus
Group=prometheus
Type=simple
Restart=on-failure
RestartSec=5s
ExecStart=/usr/local/bin/prometheus \
  --config.file=/etc/prometheus/prometheus.yml \
  --storage.tsdb.path=/data \
  --web.console.templates=/etc/prometheus/consoles \
  --web.console.libraries=/etc/prometheus/console_libraries \
  --web.listen-address=0.0.0.0:9090 \
  --web.enable-lifecycle
[Install]
WantedBy=multi-user.target
```

****<img width="573" height="448" alt="image" src="https://github.com/user-attachments/assets/57eb801d-6724-47d8-9d61-b08ed2543c93" />

Let’s go over a few of the most important options related to Systemd and Prometheus. Restart – Configures whether the service shall be restarted when the service process exits, is killed, or a timeout is reached.
RestartSec – Configures the time to sleep before restarting a service.
User and Group – Are Linux user and a group to start a Prometheus process.
–config.file=/etc/prometheus/prometheus.yml – Path to the main Prometheus configuration file.
–storage.tsdb.path=/data – Location to store Prometheus data.
–web.listen-address=0.0.0.0:9090 – Configure to listen on all network interfaces. In some situations, you may have a proxy such as nginx to redirect requests to Prometheus. In that case, you would configure Prometheus to listen only on localhost.
–web.enable-lifecycle — Allows to manage Prometheus, for example, to reload configuration without restarting the service.

To automatically start the Prometheus after reboot, run enable.

```
sudo systemctl enable prometheus
```

<img width="1115" height="61" alt="image" src="https://github.com/user-attachments/assets/46681b2e-0295-4e03-bde7-5ef02c76065f" />

Then just start the Prometheus.

```
sudo systemctl start prometheus
```

<img width="819" height="109" alt="image" src="https://github.com/user-attachments/assets/fc778e30-150e-4a90-b830-f5044fdcb728" />

```
sudo systemctl status prometheus
```
<img width="1896" height="409" alt="image" src="https://github.com/user-attachments/assets/be7ab503-3de2-4cc9-9095-ebc79a218fc3" />

Suppose you encounter any issues with Prometheus or are unable to start it. The easiest way to find the problem is to use the journalctl command and search for errors.

```
journalctl -u prometheus -f --no-pager
```

Now we can try to access it via the browser. I’m going to be using the IP address of the Ubuntu server. You need to append port 9090 to the IP.

<img width="1920" height="500" alt="image" src="https://github.com/user-attachments/assets/5f84de30-bae2-4462-9f9a-270b098d3e56" />

If you go to targets, you should see only one – Prometheus target. It scrapes itself every 15 seconds by default.

Install Node Exporter on Ubuntu 22.04
Next, we’re going to set up and configure Node Exporter to collect Linux system metrics like CPU load and disk I/O. Node Exporter will expose these as Prometheus-style metrics. Since the installation process is very similar, I’m not going to cover as deep as Prometheus.

First, let’s create a system user for Node Exporter by running the following command:

```
sudo useradd \
    --system \
    --no-create-home \
    --shell /bin/false node_exporter
```

<img width="785" height="151" alt="image" src="https://github.com/user-attachments/assets/057ccf34-601e-485f-9ad6-13ed64547856" />

You can download Node Exporter from the same page.

Use the wget command to download the binary.

```
wget https://github.com/prometheus/node_exporter/releases/download/v1.6.1/node_exporter-1.6.1.linux-amd64.tar.gz
```

<img width="1907" height="442" alt="image" src="https://github.com/user-attachments/assets/465ea633-acf6-4d96-8714-b7f1b5f7c187" />

Extract the node exporter from the archive.

```
tar -xvf node_exporter-1.6.1.linux-amd64.tar.gz
```
Move binary to the /usr/local/bin.

```
sudo mv \
  node_exporter-1.6.1.linux-amd64/node_exporter \
  /usr/local/bin/
```

<img width="1677" height="84" alt="image" src="https://github.com/user-attachments/assets/0b2f6b37-9d75-40ff-b883-7ccf91dec417" />

Clean up, and delete node_exporter archive and a folder.

```
rm -rf node_exporter*
```

Verify that you can run the binary.

```
node_exporter --version
```

<img width="930" height="246" alt="image" src="https://github.com/user-attachments/assets/58379033-127d-4688-8419-77194ef90783" />

Node Exporter has a lot of plugins that we can enable. If you run Node Exporter help you will get all the options.

```
node_exporter --help

```

–collector.logind We’re going to enable the login controller, just for the demo.

Next, create a similar systemd unit file.

```
sudo vim /etc/systemd/system/node_exporter.service
```

### node_exporter.service

```
[Unit]
Description=Node Exporter
Wants=network-online.target
After=network-online.target
StartLimitIntervalSec=500
StartLimitBurst=5
[Service]
User=node_exporter
Group=node_exporter
Type=simple
Restart=on-failure
RestartSec=5s
ExecStart=/usr/local/bin/node_exporter \
    --collector.logind
[Install]
WantedBy=multi-user.target
```

<img width="503" height="344" alt="image" src="https://github.com/user-attachments/assets/af15b8c9-ad6b-4986-b5a4-431da92e74f3" />

```
sudo systemctl enable node_exporter
```

Then start the Node Exporter.

```
sudo systemctl start node_exporter
```
