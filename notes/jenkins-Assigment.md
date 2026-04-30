1 🚀 Scenario: Ubuntu Master & Ubuntu Slave in GCP

We will:

Create 2 Ubuntu VMs (Master + Slave)
Connect them via SSH (passwordless)
Test communication

🧱 Step 1: Create VM Instances in Google Cloud Platform
Go to Compute Engine → VM Instances
Click Create Instance
Create Master VM
Name: master-vm
Region: (same region for both)
Image: Ubuntu 22.04 LTS
Machine type: e2-micro (free tier)
Create Slave VM
Name: slave-vm
Same configuration

👉 Important:

Allow SSH
Ensure both VMs are in same VPC network

////////////////////////----------------------////////////////////////

🧪 Scenario 2: Ubuntu Master & CentOS Slave

    🎯 Architecture

Ubuntu VM (Master - Jenkins)
↓
CentOS VM (Slave/Agent)

    ✅ Step 1: GCP me 2 VM banao

In Google Cloud Platform:

🔹 Master VM
OS: Ubuntu 22/24
Name: master-vm
🔹 Slave VM
OS: CentOS / Rocky Linux / AlmaLinux
Name: slave-centos

✅ Step 2: Master (Ubuntu) pe Jenkins install
sudo apt update
sudo apt install openjdk-21-jdk -y

# Jenkins install

wget -q -O - https://pkg.jenkins.io/debian/jenkins.io.key | sudo apt-key add -
sudo sh -c 'echo deb https://pkg.jenkins.io/debian binary/ > /etc/apt/sources.list.d/jenkins.list'

sudo apt update
sudo apt install jenkins -y

Start:

sudo systemctl start jenkins
sudo systemctl enable jenkins

👉 Browser:

http://<MASTER_IP>:8080

✅ Step 3: CentOS Slave setup

Login to slave:

ssh <user>@<SLAVE_IP>

🔹 Install Java (VERY IMPORTANT)
sudo dnf install java-21-openjdk -y

Check:

java -version

🔹 Install Git
sudo dnf install git -y

✅ Step 4: Jenkins me Agent create karo

Jenkins UI:

Manage Jenkins → Nodes → New Node

Fill:

Name: centos-agent
Type: Permanent Agent

🔹 Configuration

Remote root:

/home/centos/jenkins-agent

Labels:

centos-agent

Launch method:

👉 Launch agent via WebSocket

Save

✅ Step 5: Slave pe agent connect karo

CentOS VM pe:

mkdir -p /home/centos/jenkins-agent
cd /home/centos/jenkins-agent

🔹 Download agent
curl -O http://<MASTER_IP>:8080/jnlpJars/agent.jar

🔹 Run agent
java -jar agent.jar \
-url http://<MASTER_IP>:8080/ \
-secret <SECRET_FROM_JENKINS> \
-name "centos-agent" \
-webSocket \
-workDir "/home/centos/jenkins-agent"

🔥 Background me run
nohup java -jar agent.jar \
-url http://<MASTER_IP>:8080/ \
-secret <SECRET> \
-name "centos-agent" \
-webSocket \
-workDir "/home/centos/jenkins-agent" > agent.log 2>&1 &

✅ Step 6: Jenkins me verify

👉 Node status:

centos-agent → Online ✅

⚠️ Common Issues + Fix
❌ Java version error

👉 Install Java 21

❌ git not found

sudo dnf install git -y

❌ permission denied

chmod -R 777 /home/centos/jenkins-agent

❌ connection broken

👉 Use nohup (background run)

🧠 Key Difference (Ubuntu vs CentOS)
Task Ubuntu CentOS
Package manager apt dnf
Java install apt dnf
Default user ubuntu centos

🎯 Final Flow

Ubuntu (Master Jenkins)
↓
CentOS (Agent)
↓
Pipeline execution ✅




////////////////------------------////////////////////////////



   
🌍 Scenario 3: Multi-Cloud (GCP Master + AWS Slave)



     🌍 Architecture
GCP VM (Jenkins Master - Ubuntu)
        ↓ (Internet)
AWS EC2 (Slave/Agent - Linux)

👉 Dono cloud alag hain → connection public network se hoga



   ✅ Step 1: GCP me Master (Jenkins)

In Google Cloud Platform:

VM: Ubuntu

Jenkins install (tumne already kiya hai 👍)

Check:

sudo systemctl status jenkins

Browser:

http://<GCP_PUBLIC_IP>:8080



🔥 IMPORTANT (Networking)

👉 GCP firewall me allow karo:

Port 8080 (Jenkins UI + agent connection)



  ✅ Step 2: AWS me Slave VM banao

In Amazon Web Services:

Service: Amazon EC2
OS: Amazon Linux / Ubuntu / CentOS
Instance name: aws-agent




   🔹 Security Group (VERY IMPORTANT)

Allow:

Outbound: All traffic ✅
Inbound:
SSH (22) → your IP
(Optional) All outbound → for Jenkins connection

👉 Agent master se connect karega, isliye inbound 8080 AWS pe zaroori nahi




    ✅ Step 3: AWS Slave setup

SSH into AWS:

ssh ec2-user@<AWS_PUBLIC_IP>

🔹 Java install

Amazon Linux:

sudo dnf install java-21-amazon-corretto -y

Ubuntu:

sudo apt install openjdk-21-jdk -y

🔹 Git install

sudo dnf install git -y



✅ Step 4: Jenkins me Agent create karo

Jenkins UI:

Manage Jenkins → Nodes → New Node

Fill:

Name: aws-agent

Type: Permanent Agent

🔹 Configuration

Remote root:

/home/ec2-user/aws-agent
Label:

aws-agent
Launch method:

👉 Launch agent via WebSocket (BEST for multi-cloud)

Save


✅ Step 5: AWS pe agent connect karo
mkdir -p /home/ec2-user/aws-agent
cd /home/ec2-user/aws-agent

🔹 Download agent.jar
curl -O http://<GCP_PUBLIC_IP>:8080/jnlpJars/agent.jar



🔹 Run agent
java -jar agent.jar \
-url http://<GCP_PUBLIC_IP>:8080/ \
-secret <SECRET_FROM_JENKINS> \
-name "aws-agent" \
-webSocket \
-workDir "/home/ec2-user/aws-agent"




🔥 Background run
nohup java -jar agent.jar \
-url http://<GCP_PUBLIC_IP>:8080/ \
-secret <SECRET> \
-name "aws-agent" \
-webSocket \
-workDir "/home/ec2-user/aws-agent" > agent.log 2>&1 &



✅ Step 6: Verify

Jenkins UI:

aws-agent → Online ✅