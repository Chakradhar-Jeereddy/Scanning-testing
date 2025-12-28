Tool for scanning code
===
- SonarQube or Trivy (better to use for image scanning only)
  
Sonarqube installation
===
- Launch Ec2 instance
- AMI: SonarQube CE on AWS (market place)
- Type: T3.large
- Disk: 20GB
- Subcribe and luanch with key-pair
- Login: ssh -i <private-key> ubuntu@ip
- Take password and user name from login screen
- Default port is 9000
- Administrator -> Myaccount -> Security -> Generate Token
- Name: jenkins, Type: Global analysis Token, Expires in 30days.

Install agent SonarQube Scanner
===
- Jenkins - Manage jenkins -> Available plugins -> SonarQube Scanner -> install
- Manage -> tools -> Add SonarQube Scanner -> Name: sonar-8.0 -> Install automatically
- We install multiple scanners of different versions.
- Jenkins -> Manage -> system -> SonarQube Servers
- Name: sonar-server
- Server URL: http:ip:9000
- Server Authentication token - Add -> Kind: Secret text - Scope: Global -> Token -ID: sonar-auth and Save.
- Administration -> Mode -> Standart Experience

SonarQube scanning in jenkins pipeline
===
- We need to use a function in pipeline for agent to scan the code.
- And write a poperties file to exclude the files not be be scanned
```
        stage('Sonar Scan'){
            environment {
                def scannerHome = tool 'sonar-8.0'
            }
            steps {
                script{
                    withSonarQubeEnv('sonar-server') {
                        sh  "${scannerHome}/bin/sonar-scanner"
                    }
                }
            }
```
file: sonar-project.properties
===
```
sonar.projectKey=roboshop-catalogue
sonar.projectName="roboshop catalogue"
sonar.sources=.
sonar.exclusions=Jenkinsfile, Jenkinsfile1, Dockerfile, **/db/**, **/node_modules/**
```

SonarQube server pass analysis to jenkins
===
- It happens through webhook needs to be created in SonarQube server.
- Administration -> Configuration -> webhook -> create.
- Name: Jenkins
- URL: http://jenkins:8080/sonarqube-webhook
- Secret: null
- After code analysis stage, add code for quality gate.
```
  stage('Quality Gate') {
   steps {
         // Wait for Quality Gate, fail build if needed
         timeout(time: 1, unit: 'HOURS') {
         waitForQualityGate abortPipeline: true
   }
```
- It waits for one hour and fail the build if needed.

Create new Quality Gate
===
**Quality Gate:**
  - Analyze the scan report sent by sonar agent from jenkins. Fail the pipeline if the critera doesn't match
  - Set rules for new and overall code and evaluate the results against it and fail/pass the test.
  - We immplement rules..0 vulnerabilities, 0 bugs, 0 code smells, security rating A, maintainability rating A,
  - Reliability rating A, code coverage 80%.
**Quality Profiles:** Rules for SonarQube while scanning the code. The architects and senior developers implement it

Setup:
===
- Quality gate -> Create -> Name: Catalogue, Unlock editing
- Add conditions for new and overal code
- Add lines to cover 80%
- Click three dots and update the new gateway as default
- Run the pilepline

New code vs Overal code
===
- Overal code = c1
- Then new code is: c1-c2

Results in console output
===
- SonarQube task 'a4f28e1f-695f-4f77-8b01-2ce36b1d9ded' completed. Quality gate is 'ERROR'
- After removing server.js (SonarQube task '4ac15be5-ed82-432a-8158-a70172359478' completed. Quality gate is 'OK')
