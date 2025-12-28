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
