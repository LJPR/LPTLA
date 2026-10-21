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
        - Publish step gives error on duplicate global attrib. After some reading, resolved by setting `GenerateTargetFrameworkAttribute` to `false` in `OrderService.csproj`.
    3. Scanned with trivy. There were many OS findings.
        - added 13.01 to repair, confirmed resolved (the app level)
        - confirmed running as appuser

> Why do we care if the container runs as root, and what did you have to change to make USER appuser actually work?

Running as a user whenever possible is a foundational security control throughout modern computing. A container running as root opens up greater risk of privilege escalation and similar security concerns, and that significantly compromises one of the major benefits of containers: isolation.

***

## 3 - GH Actions
    1. I foolishly did not read all of the *code* included, so I spent longer than I ought considering superlinter, etc. before seeing the example using only dotnet tools.
    2. I should have taken a break sooner when troubleshooting format-test. Originally thought it was a syntax issue but couldn't resolve; then thought $PATH var may resolve it, but I verified the path on the runner and redirecting locally with the dotnet command was not working either. Stepping away and returning to figure it out locally first, it was, in fact, a syntax error with the dotnet CLI.
***

## 4 - Deploy

> What's the actual risk of docker run with no restart policy and no health-based rollback, and what's the smallest change that reduces it?

Docker run without restart policy or health based rollback means no automatic or scalable built-in resilience to crashes, shutdowns or bad updates. The smallest change to reduce it would be running some sort of watchdog or monitoring solution with the ability to notify you- grafana is an obvious solution, a quick search shows me watchdock, an MIT licensed option that runs as an 11MB container for a very lightweight solution. The likely lowest *effort* solution would be a paid cloud monitoring/observability suite.

***

## 5 - Written

> Format check, test, security scan -- if all three could run in parallel instead of sequentially, would you? What do you gain, what do you risk?

For all three, I would not. You would gain speed of iteration, with major tradeoffs

But testing and security scanning together means a failure introduces the potential for someone to think "it already passed the security scan, I only changed the formatting..." The potential for human error, even for people who know should better, cannot be overlooked.

Additionally, separating them all can be clearer when walking back through the process after an issue: three smaller pieces of pipeline you can potentially narrow your troubleshooting scope to.

> Where do secrets live in your pipeline? What's the blast radius if one leaks into a log line?

Secrets live in

> Your Trivy gate uses `--ignore-unfixed`. A base-image CVE that had no fix gets one next month. Nothing in your pipeline catches that automatically. What's the gap, how do you close it?

A published artifact on its own will not hit that Trivy gate again.

> If this needed 3 replicas behind a load balancer instead of one docker run, what's the smallest realistic next step -- and what would you explicitly not try to solve with a dockerfile change alone?
