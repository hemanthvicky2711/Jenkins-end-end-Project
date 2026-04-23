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


