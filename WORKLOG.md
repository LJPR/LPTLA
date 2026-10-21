# WORKLOG
### Threatlocker Jr Devops Eng Assessment - Lidia Pereira

***

## Preparatory Steps
    - Read the asssignment fully
    - Accidentally found NavilH's github when checking if there was an accepted worklog format to follow
    - Set up proj folder, this doc, added all questions to this doc, and git init'd
    - Verified other installs, installed .NET 8 SDK for OpenSUSE Leap
    - Reviewed Trivy's "Getting Started" and reviewed syntax for .NET CLI, etc.

***

## 1 - Scaffolding
    1. Left name as OrderService for simplicity's sake.
    2. `dotnet new webapi` with hint flags, `eza` to verify. Opened in editor `kate OrderService/Program.cs`
    3. Wrote Endpoints.
        - Everything worked once I found a stray bracket.
        - defaulted to listening on 5242 on my machine.
        - cURL to both, in dev and with env set.

***

## 2 - Vulnerability Scan
    1. Ran `dotnet list package...`, added NewtonSoft 9 and reran to confirm. High Severity shows.
    2. Writing dockerfile:
        - COPY changed to explicit OrderService/Ordrservice.csproj from wildcard since my folder structure has an extra step of depth
        - Started receiving errors on build, had to `dotnet clean OrderService` to resolve.
        - With build resolved, publish now gives error: `

> Why do we care if the container runs as root, and what did you have to change to make USER appuser actually work?

Running as a user whenever possible is a foundational security control throughout modern computing. A container running as root opens up greater risk of privilege escalation and similar security concerns, and that significantly compromises one of the major benefits of containers: isolation.

Dockerfile reference states: if there is no usergroup associated  with the user, runner defaults to root. Used the -U flag on adduser.

***

## 3 - GH Actions


***

## 4 - Deploy

> What's the actual risk of docker run with no restart policy and no health-based rollback, and what's the smallest change that reduces it?

Docker run without restart policy or health based rollback means no automatic or scalable built-in resilience to crashes, shutdowns or bad updates. The smallest change to reduce it would be running some sort of watchdog or monitoring solution with the ability to notify you- grafana is an obvious solution, a quick search shows me watchdock, an MIT licensed option that runs as an 11MB container for a very lightweight solution. The likely lowest *effort* solution would be a paid cloud monitoring/observability suite.

***

## 5 - Written

> Format check, test, security scan -- if all three could run in parallel instead of sequentially, would you? What do you gain, what do you risk?

For those three, I would not. In the absence of compute costs, it saves time and I would assume is the preferred dev experience.

However, any fail not only means you need to rerun three tasks instead of one, it also introduces the potential for someone to think "it already passed the security scan, I only changed the formatting..." The potential for human error, even for people who know should better, cannot be overlooked.

Additionally, running them together (and therefore presumable having a single, simultaneous enforcement point) can cloud the source of an issue in a published artifact, when walking back through the process after a failure. Three separate gates means three smaller pieces of pipeline you can potentially narrow your troubleshooting scope to.

> Where do secrets live in your pipeline? What's the blast radius if one leaks into a log line?

(I had to look this one up)

> Your Trivy gate uses `--ignore-unfixed`. A base-image CVE that had no fix gets one next month. Nothing in your pipeline catches that automatically. What's the gap, how do you close it?

A published artifact on its own will not hit that Trivy gate again.

> If this needed 3 replicas behind a load balancer instead of one docker run, what's the smallest realistic next step -- and what would you explicitly not try to solve with a dockerfile change alone?
