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

Check the status of Node Exporter with the following command:

```
sudo systemctl status node_exporter
```

<img width="1885" height="511" alt="image" src="https://github.com/user-attachments/assets/370d65a2-5bcb-4403-9483-ef2ed11c764a" />

If you have any issues, check logs with journalctl

```
journalctl -u node_exporter -f --no-pager
```

At this point, we have only a single target in our Prometheus. There are many different service discovery mechanisms built into Prometheus. For example, Prometheus can dynamically discover targets in AWS, GCP, and other clouds based on the labels. In the following tutorials, I’ll give you a few examples of deploying Prometheus in a cloud-specific environment. For this tutorial, let’s keep it simple and keep adding static targets. Also, I have a lesson on how to deploy and manage Prometheus in the Kubernetes cluster.

To create a static target, you need to add job_name with static_configs.

```
sudo vim /etc/prometheus/prometheus.yml
```
<img width="907" height="111" alt="image" src="https://github.com/user-attachments/assets/dc233267-58f6-4dbd-b721-067eeb2e3acf" />

prometheus.yml

<img width="1198" height="618" alt="image" src="https://github.com/user-attachments/assets/5bb094ac-c26a-466a-9e08-2097cf1901b1" />

By default, Node Exporter will be exposed on port 9100.

Since we enabled lifecycle management via API calls, we can reload the Prometheus config without restarting the service and causing downtime.

Before, restarting check if the config is valid.

```
promtool check config /etc/prometheus/prometheus.yml
```

Then, you can use a POST request to reload the config.

```
curl -X POST http://localhost:9090/-/reload
```

Check the targets section

```
http://<ip>:9090/targets
```

<img width="1918" height="722" alt="image" src="https://github.com/user-attachments/assets/0e0650ff-c308-4243-8a5d-9f3ff6995438" />

Install Grafana on Ubuntu 22.04

To visualize metrics we can use Grafana. There are many different data sources that Grafana supports, one of them is Prometheus.

First, let’s make sure that all the dependencies are installed.

```
sudo apt-get install -y apt-transport-https software-properties-common
```

<img width="1171" height="201" alt="image" src="https://github.com/user-attachments/assets/902fdacd-295e-403a-a9e0-d9d8c76889cb" />

Next, add the GPG key.

```
wget -q -O - https://packages.grafana.com/gpg.key | sudo apt-key add -
```

<img width="1162" height="157" alt="image" src="https://github.com/user-attachments/assets/6676df70-8867-4135-b0db-a29c590ad5fc" />

```
echo "deb https://packages.grafana.com/oss/deb stable main" | sudo tee -a /etc/apt/sources.list.d/grafana.list
```

After you add the repository, update and install Garafana.

```
sudo apt-get update
```
```
sudo apt-get -y install grafana
```

<img width="1543" height="622" alt="image" src="https://github.com/user-attachments/assets/268db166-113b-488f-86f4-9f16fee3b6ab" />

To automatically start the Grafana after reboot, enable the service.

```
sudo systemctl enable grafana-server
```

Then start the Grafana.

```
sudo systemctl start grafana-server
```

<img width="1543" height="622" alt="image" src="https://github.com/user-attachments/assets/f4ac32ca-ede2-4b99-baa6-c81b32fc1801" />

To check the status of Grafana, run the following command:

```
sudo systemctl status grafana-server
```

<img width="1881" height="530" alt="image" src="https://github.com/user-attachments/assets/2333433a-b8b8-44be-bc05-acd13bbd1c1b" />

Go to http://<ip>:3000 and log in to the Grafana using default credentials. The username is admin, and the password is admin as well.

```
username admin
password admin
```

<img width="1910" height="912" alt="image" src="https://github.com/user-attachments/assets/728d76bb-0d70-430e-91f0-9795b1e6d487" />

When you log in for the first time, you get the option to change the password.

To visualize metrics, you need to add a data source first.

<img width="1895" height="692" alt="image" src="https://github.com/user-attachments/assets/907b289c-02ed-4af2-abe9-917cc4ceb90b" />

<img width="1897" height="681" alt="image" src="https://github.com/user-attachments/assets/96dbbfc2-2a0b-4d9f-9c8d-941f022b2fdf" />

For the URL, enter localhost:9090 and click Save and test. You can see Data source is working.

```
<public-ip:9090>
```

<img width="1901" height="902" alt="image" src="https://github.com/user-attachments/assets/77853884-e1f6-46cc-b19a-9ebe450d1420" />

Click on Save and Test.

<img width="1305" height="897" alt="image" src="https://github.com/user-attachments/assets/d74d6c2d-23fe-4785-b126-1a6691017e82" />

Let’s add Dashboard for a better view

<img width="1907" height="717" alt="image" src="https://github.com/user-attachments/assets/2e9607bd-9db4-453f-a487-d2fadbe65548" />

Click on Import Dashboard paste this code 1860 and click on load

<img width="1512" height="497" alt="image" src="https://github.com/user-attachments/assets/31abcf10-0042-49fa-8fd9-4faa5644f348" />

Select the Datasource and click on Import

<img width="1440" height="870" alt="image" src="https://github.com/user-attachments/assets/360c15c3-99da-470f-bfe6-796185826c14" />

You will see this output

<img width="1905" height="920" alt="image" src="https://github.com/user-attachments/assets/1704a3d9-e605-46d6-9fd1-7ac3692c9409" />

Step 5 — Install the Prometheus Plugin and Integrate it with the Prometheus server
Let’s Monitor JENKINS SYSTEM

Need Jenkins up and running machine

Goto Manage Jenkins –> Plugins –> Available Plugins

Search for Prometheus and install it



Step 6 — Email Integration With Jenkins and Plugin Setup
Install Email Extension Plugin in Jenkins

<img width="1846" height="427" alt="image" src="https://github.com/user-attachments/assets/433299c6-253c-46fd-80a0-019616b4217c" />

Go to your Gmail and click on your profile

Then click on Manage Your Google Account –> click on the security tab on the left side panel you will get this page(provide mail password).

<img width="1902" height="913" alt="image" src="https://github.com/user-attachments/assets/9010084c-363d-4fbe-8659-69d03d77af93" />

2-step verification should be enabled.

Search for the app in the search bar you will get app passwords like the below image

<img width="1728" height="627" alt="image" src="https://github.com/user-attachments/assets/442d7071-0f77-4970-8d31-c3a5261ac419" />

<img width="1462" height="822" alt="image" src="https://github.com/user-attachments/assets/1d28dbbf-2b47-4ab6-b0ca-875c38969bb2" />

Click on other and provide your name and click on Generate and copy the password

<img width="742" height="612" alt="image" src="https://github.com/user-attachments/assets/0fdb8aee-7e16-4dba-9ff1-c7a60c76e2a3" />

In the new update, you will get a password like this

Once the plugin is installed in Jenkins, click on manage Jenkins –> configure system there under the E-mail Notification section configure the details as shown in the below image

<img width="1891" height="835" alt="image" src="https://github.com/user-attachments/assets/f177acce-86fb-4ee4-84fe-8674c8c14e18" />

<img width="1811" height="669" alt="image" src="https://github.com/user-attachments/assets/08930325-89a1-40d4-99f0-b0125bd16e0f" />

Click on Apply and save.

Click on Manage Jenkins–> credentials and add your mail username and generated password

<img width="1902" height="905" alt="image" src="https://github.com/user-attachments/assets/872d136e-238a-4aac-86ac-94409fadeadb" />

This is to just verify the mail configuration

Now under the Extended E-mail Notification section configure the details as shown in the below images

<img width="1907" height="833" alt="image" src="https://github.com/user-attachments/assets/32e5a735-e3a7-4be1-baf7-4f92a0b3202d" />

<img width="1758" height="323" alt="image" src="https://github.com/user-attachments/assets/ad0687ee-c968-45db-9a9f-d9743670bd9f" />

<img width="1892" height="906" alt="image" src="https://github.com/user-attachments/assets/a7da2814-20b0-4e42-b21f-f4c1f54fe060" />

Click on Apply and save.

```
post {
     always {
        emailext attachLog: true,
            subject: "'${currentBuild.result}'",
            body: "Project: ${env.JOB_NAME}<br/>" +
                "Build Number: ${env.BUILD_NUMBER}<br/>" +
                "URL: ${env.BUILD_URL}<br/>",
            to: 'postbox.aj99@gmail.com',  #change Your mail
            attachmentsPattern: 'trivyfs.txt,trivyimage.txt'
        }
    }
```

Next, we will log in to Jenkins and start to configure our Pipeline in Jenkins

Step 7 — Install Plugins like JDK, Sonarqube Scanner, NodeJs, OWASP Dependency Check
7A — Install Plugin
Goto Manage Jenkins →Plugins → Available Plugins →

Install below plugins

1 → Eclipse Temurin Installer (Install without restart)

2 → SonarQube Scanner (Install without restart)

3 → NodeJs Plugin (Install Without restart)

<img width="1902" height="642" alt="image" src="https://github.com/user-attachments/assets/1150b30b-f042-4ffd-928c-d6e4eabe7596" />

7B — Configure Java and Nodejs in Global Tool Configuration
Goto Manage Jenkins → Tools → Install JDK(17) and NodeJs(16)→ Click on Apply and Save

<img width="1890" height="682" alt="image" src="https://github.com/user-attachments/assets/353eae73-97e8-449c-9d49-27ba2b946977" />

<img width="1890" height="842" alt="image" src="https://github.com/user-attachments/assets/12479632-c64b-4fd4-87eb-5983e6484047" />

7C — Create a Job
create a job as Netflix Name, select pipeline and click on ok.

Step 8 — Configure Sonar Server in Manage Jenkins
Grab the Public IP Address of your EC2 Instance, Sonarqube works on Port 9000, so <Public IP>:9000. Goto your Sonarqube Server. Click on Administration → Security → Users → Click on Tokens and Update Token → Give it a name → and click on Generate Token

<img width="1918" height="758" alt="image" src="https://github.com/user-attachments/assets/243f3b89-fd6f-4416-9a06-3ba5bd4aee76" />

click on update Token

<img width="1913" height="612" alt="image" src="https://github.com/user-attachments/assets/1802aad4-ddd3-475c-a150-bcd79c50a2ab" />

Create a token with a name and generate

<img width="1752" height="710" alt="image" src="https://github.com/user-attachments/assets/aa74394d-04f1-42df-9970-6ed9e6f884ec" />

copy Token

Goto Jenkins Dashboard → Manage Jenkins → Credentials → Add Secret Text. It should look like this

<img width="1910" height="903" alt="image" src="https://github.com/user-attachments/assets/635cac63-cee2-48d4-924f-39d297f91b0e" />

You will this page once you click on create

Now, go to Dashboard → Manage Jenkins → System and Add like the below image.

<img width="1897" height="901" alt="image" src="https://github.com/user-attachments/assets/2fde4b0a-028d-4a96-bfdd-ce1d7ce3fbcb" />

Click on Apply and Save

The Configure System option is used in Jenkins to configure different server

Global Tool Configuration is used to configure different tools that we install using Plugins

We will install a sonar scanner in the tools.

<img width="1917" height="898" alt="image" src="https://github.com/user-attachments/assets/f48c672e-4fdd-4f96-af9e-0e9fd6120271" />

In the Sonarqube Dashboard add a quality gate also

Administration–> Configuration–>Webhooks

<img width="1895" height="701" alt="image" src="https://github.com/user-attachments/assets/0ba75ff8-3264-49f7-80a1-f104ad7d4f78" />

Click on Create

<img width="1918" height="463" alt="image" src="https://github.com/user-attachments/assets/71268fa5-0c2e-413e-aae8-ee50ac05485b" />

Add details

```
#in url section of quality gate
<http://jenkins-public-ip:8080>/sonarqube-webhook/
```

Let’s go to our Pipeline and add the script in our Pipeline Script.

```
pipeline{
    agent any
    tools{
        jdk 'jdk17'
        nodejs 'node16'
    }
    environment {
        SCANNER_HOME=tool 'sonar-scanner'
    }
    stages {
        stage('clean workspace'){
            steps{
                cleanWs()
            }
        }
        stage('Checkout from Git'){
            steps{
                git branch: 'main', url: 'https://github.com/Aj7Ay/Netflix-clone.git'
            }
        }
        stage("Sonarqube Analysis "){
            steps{
                withSonarQubeEnv('sonar-server') {
                    sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=Netflix \
                    -Dsonar.projectKey=Netflix '''
                }
            }
        }
        stage("quality gate"){
           steps {
                script {
                    waitForQualityGate abortPipeline: false, credentialsId: 'Sonar-token'
                }
            }
        }
        stage('Install Dependencies') {
            steps {
                sh "npm install"
            }
        }
    }
    post {
     always {
        emailext attachLog: true,
            subject: "'${currentBuild.result}'",
            body: "Project: ${env.JOB_NAME}<br/>" +
                "Build Number: ${env.BUILD_NUMBER}<br/>" +
                "URL: ${env.BUILD_URL}<br/>",
            to: 'postbox.aj99@gmail.com',
            attachmentsPattern: 'trivyfs.txt,trivyimage.txt'
        }
    }
}
```

Click on Build now, you will see the stage view like this

<img width="1467" height="552" alt="image" src="https://github.com/user-attachments/assets/5f023a6b-4aa4-43d0-9cc3-44db788989ba" />

<img width="1280" height="208" alt="image" src="https://github.com/user-attachments/assets/7592e5ac-b584-445d-a749-0c7ec5441797" />


You can see the report has been generated and the status shows as passed. You can see that there are 3.2k lines it scanned. To see a detailed report, you can go to issues.

Step 9 — Install OWASP Dependency Check Plugins
GotoDashboard → Manage Jenkins → Plugins → OWASP Dependency-Check. Click on it and install it without restart.

<img width="1902" height="401" alt="image" src="https://github.com/user-attachments/assets/85c55179-40c7-462a-a1c4-02b843e62320" />

First, we configured the Plugin and next, we had to configure the Tool

Goto Dashboard → Manage Jenkins → Tools →

<img width="1892" height="910" alt="image" src="https://github.com/user-attachments/assets/fbd57f10-3870-4fd3-be69-c07b640f5b76" />

Click on Apply and Save here.

Now go configure → Pipeline and add this stage to your pipeline and build.

```
stage('OWASP FS SCAN') {
            steps {
                dependencyCheck additionalArguments: '--scan ./ --disableYarnAudit --disableNodeAudit', odcInstallation: 'DP-Check'
                dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }
        stage('TRIVY FS SCAN') {
            steps {
                sh "trivy fs . > trivyfs.txt"
            }
        }
```

The stage view would look like this,

<img width="1461" height="371" alt="image" src="https://github.com/user-attachments/assets/c81cc712-4448-4093-b8d8-f099b43fbf4a" />

You will see that in status, a graph will also be generated and Vulnerabilities.

<img width="1451" height="703" alt="image" src="https://github.com/user-attachments/assets/b87e7f37-1119-423e-964c-945ca0e40ace" />

Step 10 — Docker Image Build and Push
We need to install the Docker tool in our system, Goto Dashboard → Manage Plugins → Available plugins → Search for Docker and install these plugins

Docker

Docker Commons

Docker Pipeline

Docker API

docker-build-step

and click on install without restart

<img width="1882" height="916" alt="image" src="https://github.com/user-attachments/assets/0996df16-4e98-4a8d-b51d-1787aa96a4eb" />

Now, goto Dashboard → Manage Jenkins → Tools →

<img width="1918" height="908" alt="image" src="https://github.com/user-attachments/assets/2e488cf9-2bc1-4f28-9840-abf8aa8b2ddc" />

Add DockerHub Username and Password under Global Credentials

<img width="1916" height="918" alt="image" src="https://github.com/user-attachments/assets/a045c550-b26c-4a6c-bcb3-844071bb39f2" />

Add this stage to Pipeline Script

```
stage("Docker Build & Push"){
            steps{
                script{
                   withDockerRegistry(credentialsId: 'docker', toolName: 'docker'){
                       sh "docker build --build-arg TMDB_V3_API_KEY=Aj7ay86fe14eca3e76869b92 -t netflix ."
                       sh "docker tag netflix anilkumar061/netflix:latest "
                       sh "docker push anilkumar061/netflix:latest "
                    }
                }
            }
        }
        stage("TRIVY"){
            steps{
                sh "trivy image anilkumar061/netflix:latest > trivyimage.txt"
            }
        }
```
