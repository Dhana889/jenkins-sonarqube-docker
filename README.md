Objective: To create a CI-CD pipeline to pull code from GitHub into Jenkins and test code using SonarQube and then deploy code using docker.




Step-1: Download a freecss template and upload in GitHub repository.
Step-2: Create 3 EC2 ubuntu instances with T2.Medium family for Jenkins, Sonarqube and Docker.
  - SSH into Jenkins server using CLI terminal.
  - chmod 400 keypair.pem 
- SSH -I keypair.pem ubuntu@ipaddress.
  - sudo apt update
  - apt install openjdk-17-jdk openjdk-17-jre
  - [install jenkins LTS from jenkins documentation]
  - systemctl status jenkins
  - copy the admin token and launch jenkins webpage and install suggested plugins
  
Step-3: Pull the code from GitHub to Jenkins server.
  - Create a freestyle project in Jenkins
  - Under Configure -> Source Code Management -> Add Git repository URL
- Specify the branch name (main or feature)
  - Build Triggers -> GitHub hook trigger for GITScm polling and save the build
  - On GitHub browser, add a webhook under settings
  - Enter Jenkins IP and portnumber ‘http://43.205.203.25:8080/github-webhook/’ in Payload URL
  - Select ‘Let me select individual events’ and choose Pull requests and pushes
  - Create Webhook.

  - Now run build and check workspaces in Jenkins
  - Add a new file in main branch in GitHub and now jenkins build will run again as new changes made in main branch. Webhook activated

So far in this project, we have created a GitHub repository and whenever Developer commits a change in source code, jenkins will pull the repo automatically. Now its time to configure SonarQube.

Steps:
  - SSH into Sonarqube terminal using CLI
  - DO NOT CHANGE TO ROOT USER
  - To rename the instance use the following command:
	- sudo hostnamectl set-hostname sonarqube
  	- /bin/bash
  - sudo apt update   --> To update repository
  - Install JDK-17 ->  sudo apt install openjdk-17-jre
  - Now install Sonarqube community edition
  - wget https://binaries.sonarsource.com/Distribution/sonarqube/sonarqube-10.2.1.78527.zip
  - sudo apt install unzip
  - unzip <zipfilename>
  - cd into sonarqube folder -> cd bin/
  - cd linux-x86-64 and we can see sonar.sh bash file
  - To run -> ./sonar.sh console
  - Now login in web browser with IP address with port number 9000.
  - Make sure to enable port 9000 in in-bound security group.

On SonarQube site:  
- Default username:password is admin:admin and change password on initial login.
  - click on create project manually
  - Enter project name and key and next
  - Select previous version
  - Select GitHub DevOps platform
  - On create a Jenkinsfile option, select -> Other (for js, ts, go…)
  - Copy the sonar.project.properties code and save it in notepad for later use
  - After completing the steps, goto Administrator button and choose My Account
  - Click on security tab and generate a Token. Now copy the token id and paste it in notepad.

On Jenkins site:
  - Install 2 new plugins.
  - Sonarqube scanner for jenkins and SSH2 Easy
  - complete restart
  - Goto Tools configuration in manage jenkins
  - Add SonarQube Scanner under SonarQube Scanner installations
- Enter a name and keep Install automatically checked and save
- Go to System configuration and Add SonarQube Servers.
- Enter a name and use Sonarqube url under Server URL
- Add Server authentication token using jenkins
- Global -- secret text -- Enter token -- Enter ID and Add
- Select the token name now and save/apply.

- On the jenkins project build, go to Configure.
- Under build steps, select Execute SonarQube Scanner
- Paste the Sonar project key under Analysis properties section. Save.

