# Assignment 3 — Production Maintenance Drill (OPS Checklist)

Part of the DevOps Micro Internship (DMI) Cohort 3 with Agentic AI

---

## Purpose

In this assignment, you will treat your already deployed React application (on Ubuntu VM with Nginx) as a live production system. You will perform structured operational checks covering network validation, service health, log analysis, resource monitoring, configuration verification, and incident simulation with recovery — mirroring real on-call DevOps responsibilities.

---

# Task 1 — Server Access & Networking Validation

## Goal

Verify that the deployed React application is reachable from the browser and confirm basic network connectivity of the Ubuntu VM.

### Evidence

#### Screenshot 1 — Browser showing the React app with your Full Name visible on the UI


![Screenshot1](screenshots/W3-A2-T8-S10.png)

---

#### Screenshot 2 — Output of `ip a`

![Screenshot2](screenshots/W3-A3-T1-S2.png)

---

#### Screenshot 3 — Output of `sudo ss -tulpen`

![Screenshot2](screenshots/W3-A3-T1-S3.png)

---

#### Screenshot 4 — Output of `sudo ufw status`


![Screenshot2](screenshots/W3-A3-T1-S4.png)

---

### Notes

Answer the following in your own words:

**1. What proves Nginx is listening on 0.0.0.0:80?**

Write your answer here.

Using command sudo ss -tlnp | grep ':80'

![Screenshot2](screenshots/N1.png)

- 0.0.0.0:80: Confirms it binds to all available IPv4 interfaces on port 80.

- LISTEN: Confirms the socket status is actively listening.

- nginx: Confirms the process name holding the socket is Nginx

---

**2. What proves SSH is active on port 22?**

sing command sudo ss -tuln | grep :22

![Screenshot2](screenshots/N2.png)

- 0 / 4096: The first number shows unread data waiting in the system pipeline. The second number is the maximum queue size.

- LISTEN: The service is turned on and ready to accept visitors.

- 0.0.0.0:22: This is the listening address for IPv4 connections. The zeros mean "listen on all network adapters."

- [::]:22: This is the listening address for IPv6 connections. The double-colon represents "all adapters."

---

**3. Did you find any unexpected open ports? Explain briefly.**

No unexpected ports appear to be open. The only externally accessible services are SSH (22) for remote management and HTTP (80) served by Nginx, both of which are expected for a web server. The other ports are internal operating system services required for DNS resolution, networking, and time synchronization.

The listening ports are:

- 22/TCP – SSH
  Used for remote administration of the EC2 instance.
  Expected if you are connecting via SSH.
- 80/TCP – Nginx (HTTP)
  Used to serve your React application or web content.
  Expected since Nginx is running.

The remaining entries are standard system services:

- 53 (UDP/TCP) – systemd-resolved (DNS resolver)
- 68 (UDP) – systemd-networkd (DHCP client)
- 323 (UDP) – chronyd (time synchronization)

---

# Task 2 — Service Health & Systemd Validation (Nginx)

## Goal

Verify that Nginx is properly installed, running, enabled at boot, and safely configured.

### Evidence

#### Screenshot 1 — Output of `systemctl status nginx --no-pager`

![Screenshot1](screenshots/W3-A3-T2-S1.png)

---

#### Screenshot 2 — Output of `sudo nginx -t`

![Screenshot1](screenshots/W3-A3-T2-S2.png)

---

#### Screenshot 3 — Output of `sudo ss -lptn '( sport = :80 )'`

![Screenshot1](screenshots/W3-A3-T2-S3.png)

---

### Notes

Answer the following in your own words:

**1. What happens if Nginx fails to restart in production?**

If Nginx fails to restart, your web server stops serving traffic. You will see 502 Bad Gateway error and the website will be down.

---

**2. What's your basic rollback plan?**

Write your answer here.

---

# Task 3 — Logs & Request Trace

## Goal

Verify real traffic flow and analyze logs to understand system behavior and errors.

### Evidence

#### Screenshot 1 — Output of `sudo tail -n 30 /var/log/nginx/access.log`


![Screenshot1](screenshots/W3-A3-T3-S1.png)

---

#### Screenshot 2 — Output of `sudo tail -n 30 /var/log/nginx/error.log`

![Screenshot1](screenshots/W3-A3-T3-S2.png)

---

#### Screenshot 3 — Output of `sudo journalctl -u nginx --no-pager -n 50`


![Screenshot1](screenshots/W3-A3-T3-S3.png)

---

### Notes

Answer the following in your own words:

**1. Were there any errors in the logs?**

- If yes, mention 1–2 example error lines from the logs and explain what each one means in simple terms.
- If no, explain what it means if the error log is empty or shows no recent errors during your check.

Write your answer here.

---

**2. If there were no errors, what does that indicate about the system?**

Write your answer here.

---

**3. Based on the access logs, were your curl requests visible in the log entries? What does that prove about traffic flow?**

Write your answer here.

---

# Task 4 — System Resource Health Check (Capacity Red Flags)

## Goal

Assess server capacity and detect potential performance or failure risks.

### Evidence

#### Screenshot 1 — Output of `uptime`


![Screenshot](screenshots/W3-A3-T4-S1.png)

---

#### Screenshot 2 — Output of `free -h`

![Screenshot](screenshots/W3-A3-T4-S2.png)

---

#### Screenshot 3 — Output of `df -h`


![Screenshot](screenshots/W3-A3-T4-S3.png)

---

#### Screenshot 4 — Output of `sudo du -sh /var/* | sort -h`

![Screenshot](screenshots/W3-A3-T4-S4.png)

---

### Notes

Answer the following in your own words:

**1. Which resource looks most critical right now? (CPU/load, memory, or disk) Explain why.**

Write your answer here.

---

**2. What happens if disk becomes 100% full in a production server?**

Write your answer here.

---

# Task 5 — Configuration & Deployment Verification

## Goal

Ensure the correct React build is deployed and Nginx is serving it properly.

### Evidence

#### Screenshot 1 — Output of `ls -lah /var/www/html | head -n 20`

W3-A3-T5-S1

![Screenshot](screenshots/W3-A3-T5-S1.png)

---

#### Screenshot 2 — Output of `grep -R "Deployed by" -n /var/www/html 2>/dev/null | head`

W3-A3-T5-S2

![Screenshot](screenshots/W3-A3-T5-S2.png)

---

#### Screenshot 3 — Output of `grep -n "try_files" /etc/nginx/sites-available/default`


![Screenshot](screenshots/W3-A3-T5-S3.png)

---

### Notes

Answer the following in your own words:

**1. How do you confirm that the correct version of the application is deployed?**

Write your answer here.

---

# Task 6 — Nginx Configuration Failure Simulation

## Goal

Simulate a real-world Nginx misconfiguration and recover the service safely.

### Evidence

#### Screenshot 1 — Output of `sudo nginx -t` showing the syntax error (broken config)


![Screenshot](screenshots/W3-A3-T6-S1.png)

---

#### Screenshot 2 — Output of `sudo nginx -t` showing syntax ok (fixed config)


![Screenshot](screenshots/W3-A3-T6-S2.png)

---

#### Screenshot 3 — Output of `curl -I http://<public-ip>` confirming recovery (200 OK)


![Screenshot](screenshots/W3-A3-T6-S3.png)

---

### Notes

Answer the following in your own words:

**1. What caused the configuration failure?**

Write your answer here.

---

**2. How did you fix the issue?**

Write your answer here.

---

**3. How can you avoid this kind of issue in real production systems?**

Write your answer here.

---

# Task 7 — Web Application Failure Simulation

## Goal

Simulate missing deployment content and recover the application safely.

### Evidence

#### Screenshot 1 — Output of `curl -I http://<public-ip>` showing failure (non-200 response)

![Screenshot](screenshots/W3-A3-T7-S1.png)

---

#### Screenshot 2 — Output of `curl -I http://<public-ip>` confirming recovery (200 OK)


![Screenshot](screenshots/W3-A3-T7-S2.png)

---

### Notes

Answer the following in your own words:

**1. What caused the application to break in this scenario?**

Write your answer here

---

**2. How did you fix the issue and restore the application?**

Write your answer here.

---

**3. What steps would you take to prevent this kind of issue in real production systems?**

Write your answer here.

---

# Task 8 — Security & Reliability Review

## Goal

Review and reflect on the security and reliability practices applied during this assignment.

### Security & Reliability Notes

Answer the following in your own words:

**1. Why is SSH key-based authentication more secure than sharing passwords?**

Write your answer here.

---

**2. Why should only required ports be open on a production server?**

Write your answer here.

---

**3. Why is it important for Nginx to be enabled on boot?**

Write your answer here.

---

**4. What are the risks of sharing secrets, keys, or credentials publicly?**

Write your answer here.

---

**5. Why should cloud resources be stopped or terminated when they are no longer needed?**

Write your answer here.

---

# LinkedIn Post (Required)

## Evidence

#### LinkedIn Post URL

Paste your LinkedIn post URL here:

`__________________________`

---

#### Screenshot — Published LinkedIn post

Add your screenshot here.

---

# Submission Instructions

- Add all required screenshots in your submission
- Full name must be visible in required screenshots
- Do not expose sensitive information (keys, passwords, account IDs)

---

# Completion Checklist

- [✅] Task 1: Screenshots (browser, ip a, ss -tulpen, ufw status) + Notes answered
- [✅] Task 2: Screenshots (nginx status, nginx -t, ss port 80) + Notes answered
- [✅] Task 3: Screenshots (access log, error log, journalctl) + Notes answered
- [✅] Task 4: Screenshots (uptime, free -h, df -h, du -sh) + Notes answered
- [✅] Task 5: Screenshots (ls html, grep deployed by, grep try_files) + Notes answered
- [✅] Task 6: Screenshots (nginx -t fail, nginx -t pass, curl recovery) + Notes answered
- [✅] Task 7: Screenshots (curl failure, curl recovery) + Notes answered
- [✅] Task 8: Security & Reliability Notes answered
- [✅] LinkedIn post published and URL submitted
- [✅] Full Name visible in all required screenshots
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