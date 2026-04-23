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

Setup all 4 servers alon with thier required dependencies.

**STEP - 2**

