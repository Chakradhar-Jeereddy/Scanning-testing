Concept:
====
- **Database:** NVD: National Vulnerability database -> US offical organisation.
- **CVE:** Common vulnerability Exposure ID.
- Serval bounties/bounty programs and vendors research to find holes in the code and inform MITRE.
- CVE is managed by MITRE(Non profitable org) funded by US governament.
- If a bug/vul is found, MITRE will allocate an ID and updats in NVD database, with below metadata.
- Which version? OS, app, who is vendor, who manages it, what is the fix?
- Which upcomming version is fixing it?

How our scanning tools work?
==
- Reads the top level and low level dependencies.
- Like OS version, library name, version, etc.
- Share that wih NVD database and NVD responds with known vulnerabilites and remedy fixes.
- Then scanning tool will show the information like 3 HIGH, 3 CRITICAL, 8 MEDIUM, 9 LOW -> full info.

What is our responsibility?
==
- Setup a quality gate filter HIGH, CRITICAL first and fail the pipeline
- After those are fixed, setup for medium
- Low can be ignored.

Enabling Dependabot in Github:
==
- Code exists in github only
- Once we enable the Dependabot, it will scan the dependencies.
- It will alert in case of any bugs or vulnerabilities and provides the details/fixes.
- Click Security -> Dependabot -> click "this repository’s settings."
- Click Dependency alerts.
- Click Secuirty -> Dependabot -> Click the alert.

Example:
==
**Node-Redis potential exponential regex in monitor mode #1**
**Impact**
When a client is in monitoring mode, the regex begin used to detected monitor messages could cause exponential backtracking on some strings. This issue could lead to a denial of service.

**Patches**
The problem was fixed in commit 2d11b6d and was released in version 3.1.1.

**References**
#1569 (GHSL-2021-026)

**CVSS v3 base metrics**
- Attack vector               Network
- Attack complexity           Low
- Privileges required         None
- User interaction            None
- Scope                       Unchanged
- Confidentiality             None
- Integrity                   None
- Availability                High

Metrics
==
- **Attack vector**: More severe the more the remote (logically and physically) an attacker can be in order to exploit the vulnerability.
- **Attack complexity**: More severe for the least complex attacks.
- **Privileges required**: More severe if no privileges are required.
- **User interaction**: More severe when no user interaction is required.
- **Scope**: More severe when a scope change occurs, e.g. one vulnerable component impacts resources in components beyond its security scope.
- **Confidentiality**: More severe when loss of data confidentiality is highest, measuring the level of data access available to an unauthorized user.
- **Integrity**: More severe when loss of data integrity is the highest, measuring the consequence of data modification possible by an unauthorized user.
- **Availability**: More severe when the loss of impacted component availability is highest.

