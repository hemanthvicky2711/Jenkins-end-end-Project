**Agenda - Deploying an application in TOMCAT application server using CI/CD**

**Tools**

GIT - for source code from developer
MAVEN - to build the source code into WAR file
SONAR - to check code quality to analyse if there are any bugs
NEXUS - to store the WAR file in artifactory
TOMCAT - for web application server to deploy the WAR file

*deploy - installation of a application on a web-application server

**STEP - 1**

Launch 4 instances with the following :

1. JENKINS - t2.micro
2. TOMCAT  - t2.micro
3. SONAR   - t3.medium with 20GB of EBS( volume )
4. NEXUS   - t3.medium with 20GB of EBS

Setup all 4 servers along with their required dependencies.

**STEP - 2**

Login into Jenkins dashboard and install these following plugins

1. Sonar Scanner : to scan the code
2. Nexus Artifactory Uploaded : to store the files in nexus
3. SSH Agent : to send these files from one server to another server

**STEP - 3**

Create a jenkins pipeline job and write a Jenkins file for deploy a web application, usually we have 2 types of pipelines:
1. Declarative
2. Scripted

Here i am using scripted pipeline for the Jenkins file

**Stage 1 : GET THE CODE FROM GITHUB TO CI-SERVER**

node {
    stage ("Code"){
        git"https://github.com/devops0014/one.git"
    }

**Stage 2 :  BUILD THE SOURCE CODE**

GO TO MANAGE JENKINS >> GLOBAL TOOL CONFIGURATION >> MAVEN >> ADD INSTALLER WITH THE NAME OF maven WITH VERISON (3.9.6) *any latest version is okay. Need not to remember the below code/syntax, in real time this will be provided by developers

stage ("Build") {
        def mavenHome = tool name:"maven", type:"maven"
        def mavenCMD ="${mavenHome}/bin/mvn"
        sh "${mavenCMD} clean package"
    }

**Stage 3 : SCAN THE SOURCE CODE**

1.LOGIN INTO SONAR >> GO TO MY ACCOUNT >> SECURITY >> ENTER A TOKEN NAME AND GENERATE A TOKEN
2. NOW INTEGRATE THE SONAR TO JENKINS : MANAGE JENKINS >> CONFIGURE SYSTEM >> SONAR SERVER :
NAME: anyname 
Url: PublicIP:9000/
credentials: —— (username with secretkey)

------

stage ("CQA"){
        withSonarQubeEnv('mysonar'){
            def mavenHome = tool name:"maven", type:"maven"
        def mavenCMD ="${mavenHome}/bin/mvn"
        sh "${mavenCMD} sonar:sonar"
    }

**Stage 4 : UPLOAD WAR FILE INTO ARTIFACTORY:**

CREATE A REPO IN NEXUS :
Name: hemanth-releases
formar: maven2 hosted
Version policy: releases
Deployment policy: allow redeploy
INSTALL NEXUS ARTIFACTORY UPLOAD PLUGIN In JENKINS
To GENERATE THE PIPELINE SYNTAX, WE NEED TO USE NEXUS ARTIFACTORY UPLOADER IN PIPELINE
SYNTAX AND GIVE ALL INPUTS AND GENERATE PIPELINE SYNTAX

--------

 stage ("Nexus"){
        nexusArtifactUploader artifacts: [[artifactId: 'myweb', classifier: '', file: 'target/myweb-8.7.1.war', type: 'war']], credentialsId: 'nexus', groupId: 'in.javahome', nexusUrl: '54.146.245.177:8081', nexusVersion: 'nexus3', protocol: 'http', repository: 'vicky-repo', version: '8.6.0'
    }

**Stage 5 :  DEPLOY THE APPLICATION INTO TOMCAT WEB SERVER**

USE PIPELINE SYNTAX
sshAgent:SSHAgent >> ADD >> Global Credentials >> SSH Username with privatekey >> Any username > enterdirectly >> paste your .pem key >> Generate 

stage ("Deployment"){
        sshagent(['8ac57613-36f5-4b12-992d-3335c4d39090']) {
            sh 'scp -o StrictHostKeyChecking=no target/*.war ec2-user@http://13.218.54.69:/home/ec2-user/apache-tomcat-9.0.117/webapps/'
        }

----------
Breakdown of the script : 

'scp -o StrictHostKeyChecking=no target/*.war ec2-user@http://13.218.54.69:/home/ec2-user/apache-tomcat-9.0.117/webapps/'

scp -o StrictHostKeyChecking=no  --> cmd to connect two servers 
target/*.war                     --> file location
ec2-user@http://13.218.54.69     --> default user in server and ip address on tomcat server public/private 
/home/ec2-user/apache-tomcat-9.0.117/webapps/  -- > file path 
