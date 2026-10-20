# WORKLOG
### Threatlocker Jr Devops Eng Assessment - Lidia Pereira

## Preparatory Steps
    - Read the asssignment fully
    - Accidentally found NavilH's github when checking if there was an accepted worklog format to follow
    - Set up proj folder, this doc, added all questions to this doc, and git init'd
    - Verified Docker version, installed .NET 8 SDK for OpenSUSE Leap,
    - Reviewed Trivy's "Getting Started" and reviewed syntax for .NET CLI, docker et c.


## 1 - Scaffolding

## 2 - Vulnerability Scan

> Why do we care if the container runs as root, and what did you have to change to make USER appuser actually work?

## 3 - GH Actions

## 4 - Deploy

> What's the actual risk of docker run with no restart policy and no health-based rollback, and what's the smallest change that reduces it?

## 5 - Written

> Format check, test, security scan -- if all three could run in parallel instead of sequentially, would you? What do you gain, what do you risk?

> Where do secrets live in your pipeline? What's the blast radius if one leaks into a log line?

> Your Trivy gate uses `--ignore-unfixed`. A base-image CVE that had no fix gets one next month. Nothing in your pipeline catches that automatically. What's the gap, how do you close it?

> If this needed 3 replicas behind a load balancer instead of one docker run, what's the smallest realistic next step -- and what would you explicitly not try to solve with a dockerfile change alone?
