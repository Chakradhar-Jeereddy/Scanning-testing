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

Security implementation in Org:
===
1. Collected the list of project codes, app codes -> roboshop, catalogue
2. Enabled sonar scan for them, without applying quality gates to failed the build.
3. Provided 6 months timeframe to clear High, Critical issues on overall code.
     * Overall code
     * New code
4. One month to clear High, Critical issues on new code.
5. Anounced that Quality gates will be applied on new code after one month.
6. It will fail the build if the new code is not good.
7. Finally it will be enabled on overall code after 6 months.

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

### Jenkins Setup

Installation commands from Jenkins official doc
```
sudo curl -o /etc/yum.repos.d/jenkins.repo \
    https://pkg.jenkins.io/redhat-stable/jenkins.repo
sudo rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io-2023.key
# Add required dependencies for the jenkins package
sudo yum install fontconfig java-21-openjdk -y
sudo yum install jenkins -y
sudo systemctl daemon-reload
sudo systemctl start jenkins
sudo systemctl enable jenkins
```

#### Plugins:
* Stage view
* AWS Steps
* SonarQube scanner
* Pipeline utility steps

#### Master Agent Architecture
* Create credentials to connect agent
* Install Java in agent
* Select Non verification strategy

#### Credentials
* SSH authentication
* AWS credentials
* Sonar token

# Sonar

### 🐞 Bugs

An issue representing a coding error that will likely cause unexpected behavior or application failure. This seems fine but sometimes it may break in production.

### 🔐 Vulnerabilities
A security-related holes that can be exploited by attackers. This can be hacked

### 👃 Code Smells
A maintainability issue that does not break the app but makes the code hard to understand, change, or maintain. This works today, but hurts tomorrow

**Technical Debt:**
It is the estimated time required to fix all maintainability issues (code smells) in the codebase. That future pain = technical debt

**Example:**
Your project has:
100 code smells
Each smell estimated as 10 minutes to fix
```
Technical Debt = 100 × 10 min = 1000 minutes ≈ 16.6 hours
```

Example timings SonarQube assumes: <br/>
Rename variable → 2 min <br/>
Reduce complexity → 30 min <br/>
Remove duplication → 1h

### 📋 Duplication
Percentage or blocks of identical or near-identical code across files. copy-paste risk. Should be made as function and reuse it.

### 📊 Coverage

Percentage of code that is executed by unit tests.
```
Coverage = (Lines covered by tests / Lines to cover) × 100
```

Unit testing will give us a JSON report
* Total test cases executed
* How many passed, How many failed

This report should be uploaded to SonarQube server through agent.

### 🔒 Security Rating
Rating based on severity of vulnerabilities

**Ratings logic:**
A → No vulnerabilities
B–E → Based on highest severity found

Example:
1 Critical vulnerability → Rating becomes E <br/>
Security Rating = worst vulnerability decides

### 🛠️ Maintainability Rating

Rating based on Technical Debt Ratio
Technical Debt Ratio formula:
```
(Total remediation cost / Development cost) × 100
```
1️⃣ Total Remediation Cost (SonarQube)
What it means
Example:
Total time required to fix all Code Smells in the codebase.
| Code Smell        | Fix Time |
| ----------------- | -------- |
| Long method       | 30 min   |
| Duplicate code    | 1 hour   |
| Bad variable name | 2 min    |

```
Total remediation cost = 1h 32m
```

2️⃣ Development Cost (SonarQube)
What it means?
Estimated time it would take to write the existing code from scratch.
SonarQube uses a fixed heuristic:
Development cost = Lines of Code × 30 minutes
(30 minutes per line is SonarQube’s default assumption)

Example

Lines of Code: 1,000
```
Development cost = 1,000 × 30 min = 30,000 min ≈ 500 hours
```

**Real Example:**
Lines of Code: 2,000

Development cost = 2,000 × 30 min = 1,000 hours
Total remediation cost = 50 hours
```
Technical Debt Ratio = (50 / 1000) × 100 = 5%
```
**Ratings:**
A → ≤ 5%

B → 6–10%

C → 11–20%

D → 21–50%

E → > 50%

Driven mainly by:

Code smells

Complexity

Duplication

➡️ Maintainability Rating = future change effort

### 🎯 Reliability Rating

Rating based on Bugs severity

Blocker/Critical bugs push rating down fast

➡️ Reliability = stability of the app


