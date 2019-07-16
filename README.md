##  Jenkins Workshop

### Setup Master Environment

1. Copy ```flask-app``` content to your repository

2. Run Jenkins
```
cd jenkins;
docker-compose up -d
```

3. Open Jenkins URL
```
http://127.0.0.1:8080
```

 * Unlock Jenkins

  ```
docker ps
docker exec -it jenkins_jenkins_1 cat /var/jenkins_home/secrets/initialAdminPassword
```

  * Install Suggested Plugins, Create First Admin User and Configure Jenkins URL


4. Install Additional Plugin - BlueOcean
https://jenkins.io/doc/book/blueocean/

  * Manage Jenkins --> Manage Plugins --> Available --> BlueOcean


5. Add Jenkins Credentials to access source repository

  * Generate Jenkins SSH keys

    `ssh-keygen -t rsa -C "jenkins@lohika.com"`

  * Jenkins --> Credentials --> System -->
   Global Credentials --> Add Credentials --> SSH Username with private key

   * Open your GitHub repository settings --> Deploy keys -->
   Add new

6. Create Simple Freestyle Job

  * New Item --> FreeStyle Project
  * Source Code Management Tab --> Set Git url and Credentials
  * Build Tab --> Execute Shell --> echo "My First Jenkins Job"
  * Open Job url and Build Job


7. Add Build Step and Run Flask Application

  ```
  cd flask-app;
  docker-compose up -d --build
  ```

8. Create BlueOcean Pipeline, which run Flask App

https://jenkins.io/projects/blueocean/
https://jenkins.io/doc/book/blueocean/getting-started/

  * Jenkins --> BlueOcean --> New Pipeline
  * Provide GitHub Access Token
  * Add parallel stages
  * Add Git Checkout stage
  * Run Application

### Setup Jenkins Slave

1. Register Google Cloud Free Tier Account
https://cloud.google.com/free/

2. Create VM Instance for Jenkins Slave
https://console.cloud.google.com

  * Navigation Menu --> Compute Engine --> VM Instances --> Create Instance
  * Please use any OS type you preferred.
  For me it works on `Ubuntu 18.04`, machine type - `n1-standard-1 (1 vCPU, 3.75 GB memory)`, Disk Size - `20 GB`

3. SSH connect to created instance

```
gcloud beta compute --project "alpine-scholar-232716" ssh --zone "europe-west1-b" "jenkins-slave-1"
```

4. Create Jenkins user (later Jenkins jobs will be running from this user)

```
sudo useradd jenkins -U -m -s /bin/bash
```
5. Add Sudo Privileges to Jenkins user

```
visudo 
jenkins        ALL=(ALL)       NOPASSWD: ALL
```

6. Generate Jenkins SSH Keys

```
ssh-keygen -t rsa -C "jenkins@lohika.com"
```

7. Add public ssh key to authorized

```
sudo su - jenkins
vim .ssh/authorized_keys

chmod 600 .ssh/authorized_keys
```

8. Install docker and add Jenkins user to docker group

https://docs.docker.com/install/linux/docker-ce/ubuntu/

```
sudo usermod -a -G docker jenkins
```

9. Install Docker Compose

https://docs.docker.com/compose/install/

```
sudo curl -L "https://github.com/docker/compose/releases/download/1.24.0/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose

sudo chmod +x /usr/local/bin/docker-compose
```

10. Install Java on Jenkins slave

```
sudo apt-get install openjdk-8-jre -y
```

11. Add Slave Node to Jenkins

  * Manage Jenkins --> Manage Nodes --> New Node -->
  fill in the required fields


12. Open BlueOcean Pipeline and change label to 'slave'

13. Run BlueOcean Pipeline and test if application deployed successfully on Jenkins slave

### Run Application Tests

1. Open BluOcean Pipeline and Add 'Run Tests' stage

  * Add Step `Change Current Dir`
  * Add Child Step

  ```
docker-compose down
docker-compose build flask-app
docker-compose run flask-app pytest -v
docker-compose down
```
  * Build Job


2. Archive JUnit test results

  * Replace `run flask-app` string from previous point

  ```
  docker-compose run flask-app pytest -v --junit-xml=/var/opt/junit-report/report.xml
  ```

  * Add Step `Archive JUnit-formatted test results`
  * Select path `flask-app/junit-report/report.xml`
  * Add cleanup Step `sudo rm -rf flask-app/junit-report`


3. If tests passed - deploy new application release

4. Check Test Report from Jenkins UI

Go to the next lesson https://github.com/pavlobornia/training-ci/tree/master/lesson2
