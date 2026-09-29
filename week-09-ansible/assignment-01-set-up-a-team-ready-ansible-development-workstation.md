# Assignment 01 — Set Up a Team-Ready Ansible Development Workstation

Part of the DevOps Micro Internship (DMI) with Agentic AI

---

## Purpose

In this assignment, you will prepare an isolated and reusable Ansible development workstation.

You will install Ansible and supporting tools inside a Python virtual environment, configure VS Code, prepare SSH access, configure Git and pre-commit hooks, and document the complete setup.

This workstation will be used as the Ansible controller in upcoming assignments.

---

# Task 1 — Create and Initialize the Ansible Workspace

## Goal

Create the assignment workspace, initialize a Git repository, prepare the required directories, and add Git ignore rules for local and sensitive files.

### Evidence

#### Screenshot 1 — Terminal showing the `ansible-onboarding` path, `ls -la` output, and `git status` confirming the Git repository is on the `main` branch

![alt text](screenshots/W9-A1-T1-S1.png)

---

# Task 2 — Create the Virtual Environment and Install Ansible Tools

## Goal

Create an isolated Python virtual environment and install Ansible and the required validation tools without modifying the system Python environment.

### Evidence

#### Screenshot 2 — Terminal showing the active `(.venv)` environment, `which ansible`, `ansible --version`, `ansible-lint --version`, `yamllint --version`, and `pre-commit --version`

![alt text](screenshots/W9-A1-T2-S2.png)

---

# Task 3 — Configure VS Code for Ansible Development

## Goal

Configure Visual Studio Code to use the project’s Python virtual environment and provide validation support for Python, YAML, and Ansible files.

### Evidence

#### Screenshot 3 — VS Code Extensions panel showing the Ansible, YAML, and Python extensions installed

![alt text](screenshots/W9-A1-T3-S3.png)

---

#### Screenshot 4 — VS Code showing `.vscode/settings.json` and `.editorconfig` open side by side, with the required settings clearly visible

![alt text](screenshots/W9-A1-T3-S4.png)

---

# Task 4 — Create the Baseline Ansible Configuration

## Goal

Create a reusable `ansible.cfg` file containing the default settings that will be used in this workspace and upcoming Ansible assignments.

### Evidence

#### Screenshot 5 — `ansible.cfg` open in VS Code or another editor, showing the complete configuration

![alt text](screenshots/W9-A1-T4-S5.png)

---

#### Screenshot 6 — Terminal showing `ansible --version` with the `ansible.cfg` path and the output of `ansible-config dump --only-changed`

![alt text](screenshots/W9-A1-T4-S6.png)

---

# Task 5 — Configure SSH Readiness

## Goal

Prepare SSH key authentication, load the key into the SSH agent, configure reusable SSH client settings, and understand how trusted host fingerprints are stored.

### Evidence

#### Screenshot 7 — Terminal showing `ssh-add -l` with the ED25519 key loaded and the SSH configuration verification output

![alt text](screenshots/W9-A1-T5-S7.png)

---

# Task 6 — Configure Git Identity and Pre-commit Hooks

## Goal

Configure your Git identity and install pre-commit hooks that validate YAML and Ansible files before commits are created.

### Evidence

#### Screenshot 8 — Terminal showing your Git full name, Git email, default branch, successful `pre-commit install` output, and `.git/hooks/pre-commit`

![alt text](screenshots/W9-A1-T6-S8.png)

---

# Task 7 — Test the Complete Workstation Setup

## Goal

Verify that Ansible, the linting tools, Git hooks, SSH agent, and Git ignore rules are working correctly.

### Evidence

#### Screenshot 9 — Terminal showing `pre-commit run --all-files` completing successfully

![alt text](screenshots/W9-A1-T7-S9.png)

---

#### Screenshot 10 — Terminal showing `ansible --version` with the project configuration path and `ssh-add -l` with the ED25519 key loaded

![alt text](screenshots/W9-A1-T7-S10.png)

---

# Task 8 — Create the README and Onboarding Checklist

## Goal

Document the completed Ansible workstation setup and create a reusable checklist for preparing another workstation in the future.

### Evidence

#### Screenshot 11 — Terminal showing the final `ansible-onboarding` project structure

![alt text](screenshots/W9-A1-T8-S11.png)

---

#### Screenshot 12 — VS Code Markdown preview showing your full name, project summary, and part of the “New Machine? Do This” checklist

![alt text](screenshots/W9-A1-T8-S12A.png)
![alt text](screenshots/W9-A1-T8-S12B.png)

---

# Assignment Questions

Answer the following in your own words:

**1. What is one feature that makes your workstation setup team-friendly?**

One team-friendly feature is the use of a Python virtual environment together with requirements.txt. The virtual environment keeps Ansible and its dependencies isolated from the system Python installation, while requirements.txt records the installed package versions. This allows another team member to recreate a similar development environment without affecting their system packages.

---

**2. What is one pitfall you avoided while completing the setup?**

One pitfall I avoided was installing Ansible globally with sudo pip. Instead, I installed Ansible and its supporting tools inside the project's .venv. This reduces the risk of dependency conflicts with Ubuntu's system Python packages. I also kept the project inside the native WSL filesystem under /home/cyberindian/ instead of /mnt/c/, which helps avoid WSL permission issues that can cause Ansible to ignore a project-level ansible.cfg.

---

**3. Why should Ansible be installed inside a Python virtual environment?**

Ansible should be installed inside a Python virtual environment because it keeps the project's Python packages separate from the operating system's Python environment. This allows the project to use its own Ansible and dependency versions without affecting other projects or system packages. It also improves reproducibility because team members can recreate the environment using the dependency list in requirements.txt

---

**4. Why must SSH private keys and `.venv/` remain outside version control?**

SSH private keys must remain outside version control because they are sensitive authentication credentials. If a private key is committed and exposed, someone could potentially use it to access systems where that key is trusted. The .venv/ directory should also remain outside Git because it contains machine-specific installed packages and a large number of generated dependency files. Instead of committing .venv/, the project stores dependencies in requirements.txt so the environment can be recreated safely. The assignment explicitly requires .gitignore to exclude both the virtual environment and common private-key file types.

---

# Required Files

Confirm that the following files are included in your assignment workspace:

- [/] `README.md`
- [/] `requirements.txt`
- [/] `.gitignore`
- [/] `.editorconfig`
- [/] `.vscode/settings.json`
- [/] `ansible.cfg`
- [/] `.pre-commit-config.yaml`
- [/] `inventories/`
- [/] `roles/`

---

# Submission Instructions

- Add all required screenshots in your submission.
- Full Name must be visible in required screenshots.
- All screenshots must be readable.
- Answer all assignment questions clearly in your own words.
- Do not expose SSH private-key contents, passwords, access tokens, API keys, credentials, private certificates, or other sensitive information.

---

# Completion Checklist

- [/] Task 1: `ansible-onboarding` workspace created
- [/] Task 1: Git initialized on the `main` branch
- [/] Task 1: `.gitignore` created
- [/] Task 2: Python virtual environment created
- [/] Task 2: Virtual environment activated
- [/] Task 2: Ansible installed inside `.venv`
- [/] Task 2: `ansible-lint`, `yamllint`, and `pre-commit` installed
- [/] Task 2: `requirements.txt` created
- [/] Task 3: Required VS Code extensions installed
- [/] Task 3: VS Code uses the Python interpreter from `.venv`
- [/] Task 3: `.vscode/settings.json` created
- [/] Task 3: `.editorconfig` created
- [/] Task 4: `ansible.cfg` created
- [/] Task 4: Ansible loads `ansible.cfg` from the project directory
- [/] Task 5: ED25519 SSH key exists
- [/] Task 5: SSH private key has not been exposed
- [/] Task 5: SSH key loaded into the SSH agent
- [/] Task 5: `~/.ssh/config` contains the required settings
- [/] Task 5: `~/.ssh/known_hosts` exists
- [/] Task 6: Git identity configured correctly
- [/] Task 6: Pre-commit hooks installed
- [/] Task 7: `pre-commit run --all-files` completes successfully
- [/] Task 8: `README.md` contains your full name and workstation details
- [/] Task 8: “New Machine? Do This” checklist contains 10–12 items
- [/] All 12 required screenshots are included
- [/] Assignment questions are answered
- [/] No sensitive information is exposed

---

## About DMI & CloudAdvisory

DevOps Micro Internship (DMI) is a project-based DevOps program run by Pravin Mishra and The CloudAdvisory, focused on real-world execution, systems thinking, and career readiness.

It helps learners build strong DevOps foundations with hands-on experience.

---

## Resources

- DMI Official Website: [https://dmi.pravinmishra.com?utm_source=github&utm_medium=readme](https://dmi.pravinmishra.com?utm_source=github&utm_medium=readme)
- University: [https://university.pravinmishra.com?utm_source=github&utm_medium=readme](https://university.pravinmishra.com?utm_source=github&utm_medium=readme)
- Discord Community: [https://discord.pravinmishra.com?utm_source=github&utm_medium=readme](https://discord.pravinmishra.com?utm_source=github&utm_medium=readme)
- Blog: [https://dmi.pravinmishra.com/blog?utm_source=github&utm_medium=readme](https://dmi.pravinmishra.com/blog?utm_source=github&utm_medium=readme)
- YouTube Playlist: [https://www.youtube.com/playlist?list=PLFeSNDtI4Cho](https://www.youtube.com/playlist?list=PLFeSNDtI4Cho)
- Pravin Mishra LinkedIn: [https://www.linkedin.com/in/pravin-mishra-aws-trainer/](https://www.linkedin.com/in/pravin-mishra-aws-trainer/)
- CloudAdvisory LinkedIn: [https://www.linkedin.com/company/thecloudadvisory/](https://www.linkedin.com/company/thecloudadvisory/)

---

_This submission is part of DevOps Micro Internship (DMI) — Agentic AI Track._
