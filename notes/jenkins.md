Approach 4 :- Install Jenkins On Ubuntu (this is production / real time approach)

     This installation is specific to systems operating on Ubuntu.   Follow the below steps:  https://www.jenkins.io/doc/book/installing/linux/


     Step 1: Install Java

       sudo apt update
       java -version



       sudo apt install openjdk-21-jdk -y




       Step 2: Add Jenkins Repository i.e key

         sudo wget -O /etc/apt/keyrings/jenkins-keyring.asc https://pkg.jenkins.io/debian-stable/jenkins.io-2026.key



        Step 3: Add Jenkins repo to the system i.e source list

        echo "deb [signed-by=/etc/apt/keyrings/jenkins-keyring.asc]"  https://pkg.jenkins.io/debian-stable binary/ | sudo tee  /etc/apt/sources.list.d/jenkins.list > /dev/null


      Step 4: Install Jenkins


       sudo apt update
       sudo apt install jenkins


      Step 5: Verify installation


     systemctl status jenkins

     Step 6: Once Jenkins is up and running, access it from the

        link: http://localhost:8080    or the IP of virtual machine like xx.xx.xx.xx:8080




      Get the initial admin pwd from command  cat /var/lib/jenkins/secrets/initialAdminPassword

      Start, Stop & Restart Jenkins
      $ sudo service jenkins restart
      $ sudo service jenkins stop
      $ sudo service jenkins start


     http://34.131.180.95:8080/



      Install mvn on Ubuntu (GCP VM)

      sudo apt update
      sudo apt install maven -y

       mvn -version



     ☁️ Install Git on Ubuntu (GCP VM)


       ✅ Method 1: With sudo (recommended)

           sudo apt update
           sudo apt install git -y

           git --version




  ////////////--------1ST Pipeline--------------------//////////////////////


        pipeline {
    agent any

    stages {

        stage('Git Clone') {
            steps {
                git branch: 'main', url: 'https://github.com/Rakesh9439/dev43'
            }
        }

        stage('Build') {
            steps {
                sh 'javac HelloWorld.java'
            }
        }

        stage('Run') {
            steps {
                sh 'java HelloWorld'
            }
        }

    }
}



 ///////////////////////------------------------ 2nd pipeline with CI using Triger--------------///////////////////



   pipeline {
    agent any

    triggers {
        pollSCM('* * * * *')   // every 1 minute
    }

    stages {

        stage('Git Clone') {
            steps {
                git branch: 'main', url: 'https://github.com/Rakesh9439/dev43'
            }
        }

        stage('Build') {
            steps {
                sh 'javac HelloWorld.java'
            }
        }

        stage('Run') {
            steps {
                sh 'java HelloWorld'
            }
        }

    }
}





/////////////------------------------3rd pipenline----------------////////////////////////

  
   


                  pipeline {
    agent any

    triggers {
        pollSCM('* * * * *')   // every 1 minute
    }

    stages {

        stage('Git Clone') {
            steps {
                git branch: 'main', url: 'https://github.com/Rakesh9439/dev44'
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean compile'
            }
        }

        stage('Test') {
            steps {
                sh 'mvn test'
            }
        }

        stage('Package') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Deploy') {
            steps {
                sh 'java -jar target/Shopping_Cart-0.0.1-SNAPSHOT.jar --server.port=3000 &'
            }
        }

    }

    post {
        success {
            echo '✅ Build Successful'
        }
        failure {
            echo '❌ Build Failed'
        }
    }
}

      http://34.131.180.95:3000/
            
     http://34.131.180.95:8080/





     /////////////--------- Pipeline for Windows-----/////////////////////////


      pipeline {
    agent any

    triggers {
        pollSCM('* * * * *')   // every 1 minute
    }

    stages {

        stage('Git Clone') {
            steps {
                git branch: 'main', url: 'https://github.com/Rakesh9439/dev43'
            }
        }

        stage('Build') {
            steps {
                bat 'javac HelloWorld.java'
            }
        }

        stage('Run') {
            steps {
                bat 'java HelloWorld'
            }
        }

    }
}





   /////////------Assigment-------///////////////


    🔥 Assignment: Multi-Node Setup (Ubuntu Master & Mixed Slaves)
🎯 Objective

Understand how to configure architecture using Ubuntu and CentOS across environments.

🧪 Scenario 1: Ubuntu Master & Ubuntu Slave (Same Environment)



Launch in GCP

Configure:

1 as

1 as

Install Java (required for Jenkins)

Install Jenkins on Master

Setup SSH connection from Master → Slave

Add Slave node in Jenkins

Run a sample job on Slave node

Job should execute on Slave node (not on Master)

Configure labels and restrict job execution to specific node


🧪 Scenario 2: Ubuntu Master & CentOS Slave



Launch:

Ubuntu VM (Master)

CentOS VM (Slave)

Install Java on both machines (different commands for Ubuntu vs CentOS)

Setup passwordless SSH from Master → CentOS

Add CentOS node in Jenkins

Execute a pipeline job on CentOS slave

OS-level differences (apt vs yum/dnf)

Troubleshooting agent connection issues


🌍 Scenario 3: Multi-Cloud (GCP Master + AWS Slave)



Launch:

Ubuntu Master in

Slave in (Ubuntu or CentOS)

Configure

Open required ports:

SSH (22)

Jenkins (8080 or custom)

Setup SSH key exchange between GCP → AWS


Add AWS instance as Jenkins Slave


Run job from GCP Master → AWS Slave





Successful job execution across clouds


⚠️ Real-World Challenges (Ask Students to Solve)

What happens if SSH fails?

How to fix "connection refused" error?

How to handle firewall/security group issues?

What if agent goes offline?




