Manual installation
==
```
https://trivy.dev/docs/latest/getting-started/installation/
curl -sfL https://raw.githubusercontent.com/aquasecurity/trivy/main/contrib/install.sh | sudo sh -s -- -b /usr/local/bin v0.68.2

aquasecurity/trivy info checking GitHub for tag 'v0.68.2'
aquasecurity/trivy info found version: 0.68.2 for v0.68.2/Linux/64bit
aquasecurity/trivy info installed /usr/local/bin/trivy
```
- Build the image using a base image
- Take the packages, base image and compare it with national vulnerabilty database.
- Command to scan image using trivy
   - trivy image 406682759639.dkr.ecr.us-east-1.amazonaws.com/chakra/catalogue:1.1.0
- Output snippet, for complete output check the report file.
```
2025-12-28T21:38:09Z    INFO    [vulndb] Need to update DB
2025-12-28T21:38:09Z    INFO    [vulndb] Downloading vulnerability DB...
2025-12-28T21:38:09Z    INFO    [vulndb] Downloading artifact...        repo="mirror.gcr.io/aquasec/trivy-db:2"
79.19 MiB / 79.19 MiB [-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------] 100.00% 10.11 MiB p/s 8.0s
2025-12-28T21:38:18Z    INFO    [vulndb] Artifact successfully downloaded       repo="mirror.gcr.io/aquasec/trivy-db:2"
2025-12-28T21:38:18Z    INFO    [vuln] Vulnerability scanning is enabled
2025-12-28T21:38:18Z    INFO    [secret] Secret scanning is enabled
2025-12-28T21:38:18Z    INFO    [secret] If your scanning is slow, please try '--scanners vuln' to disable secret scanning
2025-12-28T21:38:18Z    INFO    [secret] Please see https://trivy.dev/docs/v0.68/guide/scanner/secret#recommendation for faster secret detection
2025-12-28T21:38:22Z    INFO    Detected OS     family="alpine" version="3.21.5"
2025-12-28T21:38:22Z    INFO    [alpine] Detecting vulnerabilities...   os_version="3.21" repository="3.21" pkg_num=17
2025-12-28T21:38:22Z    INFO    Number of language-specific files       num=1
2025-12-28T21:38:22Z    INFO    [node-pkg] Detecting vulnerabilities...
2025-12-28T21:38:22Z    WARN    Using severities from other vendors for some vulnerabilities. Read https://trivy.dev/docs/v0.68/guide/scanner/vulnerability#severity-selection for details.
2025-12-28T21:38:22Z    INFO    Table result includes only package filenames. Use '--format json' option to get the full path to the package file.
```

Base image scan report:
==
```
406682759639.dkr.ecr.us-east-1.amazonaws.com/chakra/catalogue:1.1.0 (alpine 3.21.5)

Total: 6 (UNKNOWN: 0, LOW: 3, MEDIUM: 3, HIGH: 0, CRITICAL: 0)

┌───────────────┬────────────────┬──────────┬────────┬───────────────────┬───────────────┬──────────────────────────────────────────────────────────────┐
│    Library    │ Vulnerability  │ Severity │ Status │ Installed Version │ Fixed Version │                            Title                             │
├───────────────┼────────────────┼──────────┼────────┼───────────────────┼───────────────┼──────────────────────────────────────────────────────────────┤
│ busybox       │ CVE-2024-58251 │ MEDIUM   │ fixed  │ 1.37.0-r13        │ 1.37.0-r14    │ In netstat in BusyBox through 1.37.0, local users can launch │
│               │                │          │        │                   │               │ of networ...                                                 │
│               │                │          │        │                   │               │ https://avd.aquasec.com/nvd/cve-2024-58251                   │
│               ├────────────────┼──────────┤        │                   │               ├──────────────────────────────────────────────────────────────┤
│               │ CVE-2025-46394 │ LOW      │        │                   │               │ In tar in BusyBox through 1.37.0, a TAR archive can have     │
│               │                │          │        │                   │               │ filenames...                                                 │
│               │                │          │        │                   │               │ https://avd.aquasec.com/nvd/cve-2025-46394                   │
├───────────────┼────────────────┼──────────┤        │                   │               ├──────────────────────────────────────────────────────────────┤
│ busybox-binsh │ CVE-2024-58251 │ MEDIUM   │        │                   │               │ In netstat in BusyBox through 1.37.0, local users can launch │
│               │                │          │        │                   │               │ of networ...                                                 │
│               │                │          │        │                   │               │ https://avd.aquasec.com/nvd/cve-2024-58251                   │
│               ├────────────────┼──────────┤        │                   │               ├──────────────────────────────────────────────────────────────┤
│               │ CVE-2025-46394 │ LOW      │        │                   │               │ In tar in BusyBox through 1.37.0, a TAR archive can have     │
│               │                │          │        │                   │               │ filenames...                                                 │
│               │                │          │        │                   │               │ https://avd.aquasec.com/nvd/cve-2025-46394                   │
├───────────────┼────────────────┼──────────┤        │                   │               ├──────────────────────────────────────────────────────────────┤
│ ssl_client    │ CVE-2024-58251 │ MEDIUM   │        │                   │               │ In netstat in BusyBox through 1.37.0, local users can launch │
│               │                │          │        │                   │               │ of networ...                                                 │
│               │                │          │        │                   │               │ https://avd.aquasec.com/nvd/cve-2024-58251                   │
│               ├────────────────┼──────────┤        │                   │               ├──────────────────────────────────────────────────────────────┤
│               │ CVE-2025-46394 │ LOW      │        │                   │               │ In tar in BusyBox through 1.37.0, a TAR archive can have     │
│               │                │          │        │                   │               │ filenames...                                                 │
│               │                │          │        │                   │               │ https://avd.aquasec.com/nvd/cve-2025-46394                   │
└─────
```
- We can update the base image version or upgrade/update packages in docker file

**Dockerfile:** Update docker file with image version and update commands to resolve the Medium Vulnerabilities.
  - RUN apk update && apk upgrade --no-cache
  - FROM node:20.19.5-alpine3.21 -> 3.22
    
Database
==
- Trivy database is downloaded in default location
   - ~/.cache/trivy/db
   - trivy.db
   - metadata.json
   - strings trivy.db| head -10


Scanning
==
- By default, trivy scans base image and all dependencies/packages.
- When dependabot is enabled in github for scanning dependencies.
- We can restrict trivy to scan only base image

**Command:**
```
trivy image \
--scanners vuln \
--severity HIGH,MEDIUM,CRITICAL \
--pkg-types os \
--exit-code 1 \
--skip-db-update \
--no-progress \
--format table \
406682759639.dkr.ecr.us-east-1.amazonaws.com/chakra/catalogue:1.1.0
```
**When exit code is 1, pipeline gets aborted.**

Pipeline
==
- After the build image stage, add a stage for scanning the image.
```
  stage('Trivy image scanning'){
   steps{
    script{
     sh""
     trivy image \
     --scanners vuln \
     --severity HIGH,MEDIUM,CRITICAL \
     --pkg-types os \
     --exit-code 1 \
     --no-progress \
     --format table \
     406682759639.dkr.ecr.us-east-1.amazonaws.com/chakra/catalogue:1.1.0
     ""
    }
   }
  }
```











