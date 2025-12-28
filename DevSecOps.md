Moved from DevOps to DevSecOps
===
1. Dependabot (Scan dependencies)
2. Sonar Qube (Scan code)
3. Quality gate (Evaluate the results)
4. Trivy (Scan images and base images)

**Dependabot:**
- Enabled it in github, to scan high and low level dependencies
- It generates alets if a block of code can be exploited.
- The alerts are caputred by Jenkins pipeline and based on the severity the pipeline execution is altered.
- Github used webhook and post the infomration to jenkins endpoint.
- The pipleline uses the github API, gets the alerts, transform it into JSON response.
- It reads the JSON reponse, if the alert is Critical/High, it will halt/stop the pipeline/buid.

**Sonar Qube:**
- We use SonarQube to find the weakness in code that can be exploited by an attacker.
- SonarQube agent plugin is installed in jenkins, it will send the scanned results to SonarQube Server.
- The agent communicated to SonarQube server using the server URL and token, configured under manag-jenkins - system.
- A scanner tool v8.0 is installed in jenkins and manage -> tools.
- For agent to read pipeline we infuse the credentials and run the scan.
- A file sonar-project.properties file is created and Jenkins, Dockerfile are expluded.
- Now when the Scanning is done duing the pipeline execution, the results are sent to Sonarqube server.

  Quality gate
  ===
  - It is confugured in sonarqube server, we define the rules that needs to be evaluated agaist the scan results.
  - If the results meet the criteria defined by the rule, the result is marked as passed.
  - The pipeline will have a quality gate stage which will evaluate the results and block the pipeline if the results status is failed.
  - For Sonarqube to send result status to Jenkins, a webhook is created in Sonarqube server, it sends the data to jenkins endpoint.

  Trivy
  ===
  - We installed Trivy on jenkins agent.
  - It scans the image and if the results find vulnerability, we mark the results as faiure.
