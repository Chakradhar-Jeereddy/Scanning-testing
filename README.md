# Scanning-testing

Types of testing:
===
* ***Unit testing:*** Tests individual functions or methods in isolation. Smallest building blocks of an application (like bricks).
* ***Funtional/Component testing:*** Tests a single feature or component of the application to ensure 
                                   it works as expected, without focusing on internal code structure.
* ***Integration testing:*** Tests interaction between multiple components or services (eg: is catalogue reaching mongodb?)
* ***Regression testing:*** Ensures that existing functionality still works after new changes, bug fixes, or enhancements.
* ***Sanity check:*** A quick, focused check after a small change or bug fix to verify that the core functionality works.
* ***End-to-End:*** Tests the entire application flow from start to finish as a real user would.

--
* Unit         → Smallest code piece
* Functional   → Feature works
* Integration  → Components talk
* Regression   → Old stuff still works
* Sanity       → Quick confidence check
* E2E          → User jour

Test case to check functionality of a login function
===
```
login(string username, string password){
	boolean isSuccess = checkWithDB(username, password)
	if(isSuccess){
		showDashBoard()
	}
	if(failure){
		showError()
	}
}
```

Code coverage
===
- Out of 10 functions, if we write unit test cases for 9 functions, code coverage is 90%
- Valid tests -> If you pass valid parameters the test should succeed.
- Failure test -> If incorrect parameters passed, it should fail.

Types Scanning/analysis
===
1. Static source code analysis  -> sonarqube
2. Static application security testing - sonarqube
3. Open source vulnerability scans -> Nexus IQ, Blackduck (comertial tools)
4. Image scanning -> ECR scans and Trivy
5. Dynamic application security testing -> Veracode
   
- We install agents in jenkins, agents will access and scan the code.
- The results of the scan will be sent to Scanning central server.
- The central server analyze the results and send it back to jenkins (quality gates/controls).
- It sends errors, jenkins will stop the build.
- We enforce this scans and ask developers to fix the vulnerabilities

***Vulnerabilites:*** A block of code or file that can be exploited. A hole through which a robot can be sent to encrypt or access data.

Jenkins vs Github Actions
===
- Jenkins need to clone the repo
- Code is already there in github, we can write the pipeline there only using github actions.
- No extra components like jenkins

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


