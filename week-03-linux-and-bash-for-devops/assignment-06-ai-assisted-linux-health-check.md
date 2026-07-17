# Assignment 6 — Build an AI-Assisted Linux Health Check (AI-Assisted Linux Incident Triage)

Part of the DevOps Micro Internship (DMI) Cohort 3 with Agentic AI

---

## Purpose

In this assignment, you will build a read-only Bash triage script that checks the health of your Ubuntu server and Nginx application, connect it to Claude Code as a reusable `/linux-triage` skill, simulate a controlled Nginx incident, use the skill to gather and analyze evidence, recover the service manually, and verify recovery. The workflow follows the Agentic Loop: Gather → Analyze → Human Act → Verify.

---

# Task 1 — Confirm the Healthy Baseline and Create the Workspace

## Goal

Confirm that Nginx and the React application are healthy before building the automation.

### Evidence

#### Screenshot 1 — Output of `systemctl is-active nginx`, `ss -ltn | grep ':80'`, and `curl -I http://localhost`

![Screenshot](screenshots/W3-A6-T1-S1.png)

---

#### Screenshot 2 — Output of `pwd` and `find . -maxdepth 4 -type d | sort` showing the workspace folder structure

![Screenshot](screenshots/W3-A6-T1-S2.png)

---

### Notes

Answer the following in your own words:

**1. What proves that Nginx is running?**

Nginx is running if the service status shows active (running) using commands like systemctl status nginx or systemctl is-active nginx

---

**2. What proves that the server is listening for HTTP traffic?**

The server is listening for HTTP traffic if port 80 is in LISTEN state bound to :80. A successful curl -I http://localhost response or the Nginx default page in a browser also confirms HTTP is being served.

---

**3. Why must you capture a healthy baseline before simulating an incident?**

You capture a healthy baseline first so you know what “normal” looks like before introducing a fault. That gives you a reference point for comparing symptoms, spotting exactly what changed, and proving whether your simulation affected the expected layer or something else.

---

# Task 2 — Create Project Context and Safety Rules in CLAUDE.md

## Goal

Tell Claude exactly what this project does and what it is not allowed to do.

### Evidence

#### Screenshot 3 — CLAUDE.md open in VS Code showing all four sections (Project Overview, Incident Workflow, Safety Rules, Output Rules)


![Screenshot](screenshots/W3-A6-T2-S1.png)
![Screenshot](screenshots/W3-A6-T2-S1A.png)

---

### Notes

Answer the following in your own words:

**1. Why should Claude receive project-specific operational rules?**

Claude should receive project-specific operational rules because they act as the project’s working contract to keep behavior consistent, reduce drift, and define what Claude should and should not do in that particular environment.

---

**2. Why is the human required to execute the recovery command?**

The human must execute the recovery command because recovery is a judgment-heavy, high-impact action that should stay under human control rather than being automatically triggered by the model.

---

**3. Which rule prevents Claude from making an unsupported diagnosis?**

The rule that prevents Claude from making an unsupported diagnosis is the one that says not to guess, speculate, or state a cause without evidence. It requires using the actual error or observed symptoms instead of inventing an explanation.

The rule that prevents Claude from making an unsupported diagnosis is “Do not claim a root cause unless the report contains supporting evidence.” This forces the analysis to stay evidence-based and avoids guessing.

The other relevant rule is “Use only the Bash report as the primary source of incident evidence,” which keeps the diagnosis grounded in the collected report rather than assumptions.

---

# Task 3 — Use Agentic AI to Plan Before Writing the Script

## Goal

Use Claude Code to inspect the environment and produce a read-only plan before creating any Bash code.

### Evidence

#### Screenshot 4 — Claude Code showing the five-check plan and read-only inspection results


![Screenshot](screenshots/W3-A6-T3-S1.png)

---

### Notes

Answer the following in your own words:

**1. Which part of this task represents the Gather phase?**

The Gather phase is the evidence-collection part of the Agentic Loop by reading the Bash report, checking the  information about Nginx, port 80, the HTTP response, disk usage, and available memory for gathering the facts before any recovery step is considered.

---

**2. Did Claude follow the instruction not to create files? How did you verify this?**

Claude should not have created files since the task was strictly analysis-only, and I would verify that by checking the response for any file-creation actions by running `find . -maxdepth 4 -type f | sort` Based on the workflow rules provided, the correct behavior is to stay in read-only analysis and avoid writing code or changing files until explicitly approved.

---

**3. Why is planning before coding useful in DevOps automation?**

Planning before coding is useful in DevOps automation because it reduces risky changes, keeps the scope clear, and helps you validate the approach before touching production-like systems. It also makes it easier to spot missing assumptions, define verification steps, and avoid unnecessary rework.

---

# Task 4 — Build the Linux Triage Bash Script

## Goal

Create one Bash script that gathers consistent Linux and Nginx health evidence.

### Evidence

#### Screenshot 5 — Top section of `linux-triage.sh` showing variables, thresholds, and the checks array

![Screenshot](screenshots/W3-A6-T4-S1.png)

---

#### Screenshot 6 — Middle section showing check functions and conditionals

![Screenshot](screenshots/W3-A6-T4-S2.png)

---

#### Screenshot 7 — Bottom section showing the loop, summary function, and exit behavior

![Screenshot](screenshots/W3-A6-T4-S3.png)

---

#### Screenshot 8 — Output of `bash -n scripts/linux-triage.sh` (no syntax errors) and `ls -l scripts/linux-triage.sh` showing executable permission

![Screenshot](screenshots/W3-A6-T4-S4.png)

---

### Notes

Answer the following in your own words:

**1. What is stored in the checks array?**

The checks array stores the names of the health-check functions

- check_service
- check_port
- check_http
- check_disk
- check_memory. 

The script later uses those names to decide which checks to run.

---

**2. How does the `for` loop use that array?**

The for loop reads each item from that array and runs it as a command with "$check_function". In other words, it iterates through the list of function names and executes them one by one.

---

**3. Why are the health checks separated into functions?**

The health checks are separated into functions to make the script easier to read, test, and maintain. Each function has one job, so you can update one check without affecting the others.


---

**4. What is the purpose of `$(...)` in this script?**

$(...) is command substitution. It runs the command inside the parentheses and replaces the expression with that command’s output, such as when the script captures pwd, date, hostname, or curl output.

---

**5. Why does the script use different exit codes for HEALTHY, WARN, and FAIL?**

The script uses different exit codes so other tools or humans can quickly tell the result from automation

-  0 means healthy
-  1 means warning
-  2 means failure. 

That makes it easier to integrate the script into monitoring, CI/CD, or incident-triage workflows.

---

# Task 5 — Run and Understand the Healthy-State Report

## Goal

Run the Bash script against the healthy server and verify that it creates a report.

### Evidence

#### Screenshot 9 — Output of `./scripts/linux-triage.sh` showing your Full Name and all five check results

![Screenshot](screenshots/W3-A6-T5-S1.png)

---

#### Screenshot 10 — Output showing the captured exit code and final summary

![Screenshot](screenshots/W3-A6-T5-S2.png)

---

### Notes

Answer the following in your own words:

**1. What is the overall status of your healthy baseline?**

The overall status of the healthy baseline is:

HEALTHY

This is supported by the report summary:

PASS: 5
WARN: 0
FAIL: 0
Overall Status: HEALTHY
Script Exit Code: 0

This indicates that all health checks completed successfully with no warnings or failures.

---

**2. Which exact Linux evidence proves the application is serving traffic?**

The strongest evidence is:

[PASS] Local HTTP check returned status 200

An HTTP 200 OK response confirms that:

Additional supporting evidence includes:

[PASS] Nginx service is active

[PASS] Port 80 is listening

---

**3. Did your script return exit code 0 or 1? Explain why.**


The script returned 0 if everything passed, and 1 if it detected a general failure. In Linux/Bash, 0 means success, while any non-zero code means failure and 1 is the common catchall for a generic error.

---

**4. What is the difference between a warning and a failure in this script?**

A warning indicates that a resource is approaching an unhealthy state but is still functioning. 

For example:
- disk usage or available memory may be close to the configured threshold
- so attention is recommended
- the application is still operational.

A failure indicates that a critical health check has failed or exceeded its failure threshold. 

Examples include :
- the Nginx service not running
- Port 80 not listening
- the HTTP check failing

A failure means immediate action is required because it can affect application availability.

In my report:

Warnings: 0 (nothing required attention)
Failures: 0 (no critical issues detected)

The system therefore remained in a HEALTHY state.

---

# Task 6 — Create and Run the /linux-triage Skill

## Goal

Turn the Bash script into a reusable, manually invoked Agentic AI workflow.

### Evidence

#### Screenshot 11 — `SKILL.md` showing the frontmatter, allowed tool restrictions, and safety rules

![Screenshot](screenshots/W3-A6-T6-S1.png)

---

#### Screenshot 12 — `/linux-triage` output for the healthy server

![Screenshot](screenshots/W3-A6-T6-S2.png)

---

### Notes

Answer the following in your own words:

**1. Why does this skill have Bash, Read, and Grep, but not Write?**

The skill is designed to be read-only and safe. Each allowed tool has a specific purpose:

- Bash : Runs diagnostic commands such as the health-check script (bash scripts/linux-triage.sh).
- Read : Opens files like CLAUDE.md and the generated health report.
- Grep : Searches for specific information within files or command output if needed.

It does not include Write because the skill should never modify the system. This prevents accidental changes to configuration files, services, or logs during diagnosis.

---

**2. Why is `disable-model-invocation: true` useful for this skill?**

`disable-model-invocation: true` ensures the workflow follows the predefined instructions instead of allowing the model to improvise or delegate the task elsewhere.

This is useful because it makes the triage process:

- Consistent : Every run follows the same steps.
- Predictable : The same evidence is collected each time.
- Safe : The workflow stays within its read-only permissions.
- Auditable : The conclusions are always based on actual system evidence rather than assumptions.

---

**3. What part is performed by Bash, and what part is performed by Claude?**

Bash's responsibility:
- Execute the health-check script.
- Collect system information.
- Check Nginx, Port 80, HTTP status, disk usage, and memory.
- Generate the linux-health-report.txt report.

Claude's responsibility:
- Read the report produced by Bash.
- Interpret the evidence.
- Summarize the system health.
- Explain the likely cause of any issues.
- Recommend (but not execute) recovery actions.
- Present the findings in a clear incident report.

In short:
Bash gathers the facts.
Claude explains what those facts mean.

---

**4. Why is this better than asking Claude "Is my server healthy?" without giving it evidence?**

Without evidence, Claude has no direct access to your Linux server. It would have to guess, which could lead to incorrect conclusions.

Using this skill provides concrete evidence by:

Running real health checks.
Reading the generated report.
Basing every conclusion on actual system output.

For example, the report shows:

Nginx service is active.
Port 80 is listening.
Local HTTP check returned 200 OK.
Disk usage is 52%.
Available memory is sufficient.
Overall status is HEALTHY.

Because the assessment is based on real diagnostics rather than assumptions, the result is accurate, repeatable, and trustworthy. This evidence-based approach is much more reliable than asking a general question without providing system data.

---

# Task 7 — Simulate an Nginx Incident and Let the Skill Diagnose It

## Goal

Create a controlled service failure, gather evidence through Bash, and let Claude analyze the evidence without taking recovery action.

### Evidence

#### Screenshot 13 — Output showing Nginx is inactive and the HTTP request fails

![Screenshot](screenshots/W3-A6-T7-S1.png)

---

#### Screenshot 14 — `/linux-triage` output showing failed evidence, most likely cause, and a suggested recovery command

![Screenshot](screenshots/W3-A6-T7-S2.png)

---

#### Screenshot 15 — `incident-failure-report.txt` showing the failed checks and your Full Name

![Screenshot](screenshots/W3-A6-T7-S3.png)

---

### Notes

Answer the following in your own words:

**1. Which three checks failed?**

The report shows three failed health checks:

1.Nginx service is not active
2.Port 80 is not listening
3.Local HTTP check returned status 000 (connection refused)

These failures resulted in:

- PASS: 2
- WARN: 0
- FAIL: 3
Overall Status: FAIL

---

**2. What evidence supports the conclusion that Nginx is unavailable?**

Several pieces of evidence confirm that Nginx is unavailable:

The service status check failed: [FAIL] Nginx service is not active

Port 80 is no longer listening: [FAIL] Port 80 is not listening

The HTTP request could not connect: [FAIL] Local HTTP check returned status 000

Together, these provide clear evidence that the web server was intentionally stopped and was no longer serving requests.

---

**3. Did Claude execute the recovery command? Why is that important?**

Claude did not execute the recovery command.

Instead, it recommended: `sudo systemctl start nginx`

and clearly instructed:`"Please review the recovery command above and execute it when ready."

This is important because the Linux triage skill is designed to be read-only. It explicitly states:

- Do not edit files.
- Do not use sudo.
- Do not start, stop, restart, install, delete, or modify anything.
- Never execute the recovery command.
- Ask the human to review and run any recovery action manually.

This prevents an AI from making unintended changes to a production system and keeps the human in the loop of remediation.

---

**4. Which phase of the Agentic Loop is represented by the Bash report?**

The Bash report represents the *Gather* phase of the Agentic Loop.

During this phase, the script:
 
- Collects recent Nginx logs.
- Checks whether Nginx is running.
- Verifies Port 80.
- Checks disk usage.
- Checks available memory.
- Produces a structured health report.

Its role is to gather objective evidence from the system without interpreting it.

---

**5. Which phase is represented by Claude's explanation?**

Claude's explanation represents the *Verify* phase of the Agentic Loop.

After reading the Bash report, Claude:

- Interprets the collected evidence.
- Determines that the overall status is FAIL.
- Identifies the most likely cause (Nginx was intentionally stopped).
- Explains why the failures occurred.
- Recommends a recovery command without executing it.

In summary:

- Gather (Bash): Collects facts and system evidence.
- Verify (Claude): Analyzes those facts, explains the issue, and recommends the appropriate next action while leaving execution to the human.
- Act (Human): Performs execution to resolve the issue

---

# Task 8 — Recover Manually, Verify Again, and Write the Incident Summary

## Goal

Recover the service as the human operator and prove that the system is healthy again.

### Evidence

#### Screenshot 16 — Output showing Nginx is active and `curl -I http://localhost` returns 200 OK

![Screenshot](screenshots/W3-A6-T8-S1.png)

---

#### Screenshot 17 — Second `/linux-triage` output showing successful recovery with no FAIL results

![Screenshot](screenshots/W3-A6-T8-S2.png)

---

#### Screenshot 18 — Output of `ls -lah reports` showing both `incident-failure-report.txt` and `recovery-report.txt`

W3-A6-T8-S2
![Screenshot](screenshots/W3-A6-T8-S2.png)

---

#### Screenshot 19 — `incident-summary.md` showing all required sections and your Full Name


![Screenshot](screenshots/W3-A6-T8-S3.png)

---

### Notes

Answer the following in your own words:

**1. What action did you execute manually?**

I manually restarted the Nginx service using: `sudo systemctl start nginx.

This follows the recovery recommendation provided by Claude. After starting the service, I verified it using:'systemctl is-active nginx'

which returned: 'active'

---

**2. What evidence proves that the service recovered?**

Several pieces of evidence confirm that the service recovered successfully:

- Nginx is active:`systemctl is-active nginx`
Output:active

- HTTP requests succeed again:`curl -I http://localhost`
Output includes:

`HTTP/1.1 200 OK
Server: nginx/1.28.3 (Ubuntu)
The second Linux triage report shows:
Overall Status: HEALTHY
PASS: 5/5
WARN: 0
FAIL: 0`

Claude also reports:

- "The service recovered successfully and is currently running with all health indicators normal."
- These results confirm that Nginx is running, serving traffic, and all health checks have passed.

---

**3. Why is the second triage run necessary?**

The second triage run is necessary to verify that the recovery action actually resolved the issue.

Rather than assuming that restarting Nginx fixed the problem, the second run collects fresh evidence by checking:

- Nginx service status
- Port 80 availability
- HTTP response
- Disk usage
- Available memory

This confirms the system has returned to a healthy state and that no additional issues remain.

---

**4. What could go wrong if an AI agent automatically restarted every failed service?**

Automatically restarting every failed service could create several problems:

- It could hide the real root cause instead of identifying it.
- A restart might interrupt active users or critical workloads.
- The service could repeatedly crash, creating a restart loop.


Keeping the human in the loop for executing recovery actions provides an important safety check before changes are made.

---

**5. In one sentence, explain the difference between using AI as a chatbot and using AI in this agentic workflow.**

 An chatbot answers questions, while an agentic workflow uses AI to follow a structured process to gather real evidence, analyze it, and recommend actions without making unauthorized changes to the system.

---

# Incident Summary

Fill in all seven sections below in your own words.

**Full Name:** Jacquelina Shalinie Stanley
**Date:** 17/07/2026

---

**1. Reported Symptom**

The react application hosted on the EC2 instance over the ubuntuVM became unavailable after the Nginx Web Server was manually stopped. 

This was identified with requesto to https://localhost failed and the Linux health-check script reported that the server was no longer serving web traffic.

---

**2. Evidence Collected**

The health report identified threee failed checks 
- Nginx service was not active 
- Port 80 was not listening 
- Local HTTP health check returned status 000 (connection refused)

---

**3. Most Likely Cause**

The Nginx service had been intentionally stopped, making the web server unavailable. The service logs showed a normal shutdown with no errors, indicating the outage was caused by manual intervention rather than a service failure.

---

**4. Human-Approved Recovery Action**

After reviewing Claude's output for the /linux-triage
I manually restarted the service using: `sudo systemctl start ngix`

The recovery action was executed by me rathern than the AI,following the read-only design of the Linux triage workflow.

---

**5. Verification**

To perform the verification, I reviewed the claude outcome's for verification commands. The recommendation informed `sudo systemctl start ngix` and `curl -s  http://localhost > /dev/null && echo "Nginx still responding"`

From Claude : 
*Run this in 5-10 minutes to confirm sustained health: 
systemctl is-active nginx && curl -s  http://localhost > /dev/null && echo "Nginx still responding"*

Running the Linux triage script a second time reported:
-PASS: 5
-WARN: 0
-FAIL: 0
Overall Status: HEALTHY

This confirmed the application had recovered successfully.

---

**6. Safety Decision**

After restarting Nginx:

The AI did not make any changes to the system. It collected evidence, analyzed the report, and recommended a recovery command, leaving the final decision and execution to the human operator. This approach reduces the risk of unintended changes and keeps the human in control of production systems.

---

**7. Agentic Loop Mapping**

- Observe: Bash collected system evidence by checking the Nginx service, Port 80, HTTP response, disk usage, memory, and service logs.

- Gather: Claude analyzed the health report, identified the root cause, and explained the failed checks.

- Act: The human reviewed Claude's recommendation and manually restarted the Nginx service.

- Verify: The health-check script was run again to confirm that all five checks passed and the system had returned to a healthy state.

---

# LinkedIn Post (Required)

## Evidence

#### LinkedIn Post URL

Paste your LinkedIn post URL here:

`Add your URL here`

---

#### Screenshot — Published LinkedIn post

Add your screenshot here.

---

# GitHub Repository URL

Paste the URL of your GitHub folder or repository containing the assignment files here:

`Add your URL here`

---

# Submission Instructions

- Add all required screenshots in your submission
- Full Name must be visible in required screenshots and the Bash report
- All written answers must be in your own words
- Do not expose sensitive information (keys, passwords, AWS account IDs, tokens)
- GitHub URL must be included in this document

---

# Completion Checklist

- [✅] Task 1: Healthy baseline confirmed, workspace created (Screenshots 1–2, Notes answered)
- [✅] Task 2: CLAUDE.md created with all four sections (Screenshot 3, Notes answered)
- [✅] Task 3: Five-check plan produced by Claude using read-only tools (Screenshot 4, Notes answered)
- [✅] Task 4: `linux-triage.sh` created, syntax validated, executable permission set (Screenshots 5–8, Notes answered)
- [✅] Task 5: Healthy-state report generated with no FAIL result (Screenshots 9–10, Notes answered)
- [✅] Task 6: `/linux-triage` skill created and run successfully on healthy server (Screenshots 11–12, Notes answered)
- [✅] Task 7: Nginx incident simulated, failed evidence captured, Claude did not execute recovery (Screenshots 13–15, Notes answered)
- [✅] Task 8: Nginx recovered manually, recovery verified, reports saved, incident summary complete (Screenshots 16–19, Notes answered)
- [✅] Incident summary contains all seven required sections
- [✅] LinkedIn post published and URL submitted
- [✅] Full Name visible in all required screenshots and the Bash report
- [✅] Skill does not have Write permission
- [✅] Skill did not execute any recovery commands
- [✅] No sensitive data exposed

---

## 📌 About DMI & CloudAdvisory

DevOps Micro Internship (DMI) is a project-based DevOps program run by Pravin Mishra (The CloudAdvisory) focused on real-world execution, systems thinking, and career readiness.

It helps learners build strong DevOps foundations with hands-on experience.

---

## 📌 Resources

- 🌐 DMI Official Website: https://pravinmishra.com/dmi  
- 🎓 DevOps for Beginners (Udemy): https://www.udemy.com/course/devops-for-beginners-docker-k8s-cloud-cicd-4-projects/  
- 🎓 Agentic AI DevOps with Claude Code: https://www.udemy.com/course/ultimate-agentic-ai-devops-with-claude-code/  
- 🎓 DevOps with Claude Code: Terraform, EKS, ArgoCD & Helm: https://www.udemy.com/course/devops-with-claude-code-terraform-eks-argocd-helm/  
- ▶️ YouTube Playlist: https://www.youtube.com/playlist?list=PLFeSNDtI4Cho  
- 🔗 Pravin Mishra (LinkedIn): https://www.linkedin.com/in/pravin-mishra-aws-trainer/  
- 🏢 CloudAdvisory (LinkedIn): https://www.linkedin.com/company/thecloudadvisory/

---

*This submission is part of DevOps Micro Internship (DMI) Cohort 3 — Agentic AI Track.*