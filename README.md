# 🛡️ Linux Security & Systems Administration Walkthroughs

![Linux](https://img.shields.io/badge/Linux-FCC624?style=for-the-badge&logo=linux&logoColor=black)
![Bash](https://img.shields.io/badge/Shell_Scripting-4EAA25?style=for-the-badge&logo=gnu-bash&logoColor=white)
![SSH](https://img.shields.io/badge/SSH-000000?style=for-the-badge&logo=OpenSSH&logoColor=white)
![Security](https://img.shields.io/badge/Security-Red?style=for-the-badge&logo=linux-foundation&logoColor=white)
![Documentation](https://img.shields.io/badge/Documentation-007ACC?style=for-the-badge&logo=markdown&logoColor=white)

This repository documents hands-on technical solutions, security analysis, and administrative workflows developed through the **OverTheWire: Bandit** wargame challenges (Levels 0 → 20). 

It serves as technical evidence of core Linux systems administration capabilities, POSIX file permissions management, remote shell troubleshooting, and privilege boundary verification critical for modern DevOps and Infrastructure engineering roles.

---

## 🏛️ Core Competency Matrix

| Technical Category | Key Concepts Practiced | Real-World DevOps / SysAdmin Relevance |
| :--- | :--- | :--- |
| **Remote Access & Shells** | SSH port targeting, non-interactive execution, pseudo-TTY allocation (`-t`), init script bypass (`--norc`) | Remote server management, CI/CD runner execution, bastion host troubleshooting, automated SSH provisioning scripts. |
| **POSIX Access Control** | File permissions (`rwx`), ownership (`chown`/`chmod`), SetUID/SetGID execution boundaries | Securing shared host environments, container process privilege control, enforcing least privilege in production deployments. |
| **Data Stream Manipulation** | Unix pipes, stream redirections (`stdout`/`stderr`), text processing (`grep`, `find`, `sort`, `uniq`, `tr`) | Log aggregation, audit analysis, container monitoring pipelines, automated configuration parsing. |
| **Process & Environment** | Environment variables, shell configuration files (`.bashrc`, `.profile`), binary execution paths | Troubleshooting container startup failures, managing environment variables in runtime platforms, service configuration. |

---

## 📑 Walkthrough Index

| Level Range | Primary Technical Concept | Real-World DevOps Application | Documentation Link |
| :--- | :--- | :--- | :--- |
| **Level 00 → 01** | SSH Connection & Credentials | Secure remote administration over custom ports | [level-00-to-level-01.md](./level-00-to-level-01.md) |
| **Level 01 → 05** | Special Character Paths & Hidden Files | Shell parameter escaping, navigating complex file trees | [level-01-to-level-05.md](./level-01-to-level-05.md) |
| **Level 05 → 09** | Text Processing & Inspection | Log filtering, regex matching, identifying specific file attributes | [level-05-to-level-09.md](./level-05-to-level-09.md) |
| **Level 09 → 13** | Binary Analysis & Encoding | Decoding base64 data, inspecting hex dumps, archive decompression | [level-09-to-level-13.md](./level-09-to-level-13.md) |
| **Level 13 → 17** | SSH Key Auth & Local Networking | Key-based authentication, port scanning (`nc`, `nmap`), SSL/TLS wrappers | [level-13-to-level-17.md](./level-13-to-level-17.md) |
| **Level 17 → 18** | Diff Analysis & Key Handling | Comparing configuration drifts (`diff`), custom RSA key connections | [level-17-to-level-18.md](./level-17-to-level-18.md) |
| **Level 18 → 19** | Bypassing Interactive `.bashrc` Traps | Bypassing broken login scripts using non-interactive SSH & `-t` pseudo-TTY | [level-18-to-level-19.md](./level-18-to-level-19.md) |
| **Level 19 → 20** | Privilege Escalation via SetUID | Executing binary wrappers to access protected configuration files | [level-19-to-level-20.md](./level-19-to-level-20.md) |

---

---

---

## 🔍 Technical Deep-Dive Spotlights

### Spotlight 1: Bypassing Restricted Interactive Shells (Level 18 → 19)

**Problem Statement:** Standard interactive SSH login drops immediately because the target user's `~/.bashrc` file contains an automated `exit` instruction.

**Technical Analysis & Resolution:**  
When an SSH session requests an interactive shell, `bash` automatically sources initialization scripts like `.bashrc`. By appending a command directly to the SSH invocation, the request is executed as a non-interactive command, bypassing interactive RC initialization files. Alternatively, forcing a pseudo-terminal while explicitly disabling startup configs (`/bin/bash --norc`) allows full shell access.

```text
[Local Workstation] --- ( SSH Request + 'cat readme' ) ---> [Remote Server]
                                                                  |
                                                     Bypasses interactive .bashrc
                                                                  |
[ Password Returned ] <--- ( Stream Result ) ---------------------/


Execution Methods:

Bash
# Method 1: Direct Non-Interactive Command Execution
ssh bandit18@bandit.labs.overthewire.org -p 2220 cat readme

# Method 2: Force Pseudo-Terminal & Bypass RC Initialization
ssh -t bandit18@bandit.labs.overthewire.org -p 2220 /bin/bash --norc

# Method 3: Invoke POSIX Alternate Shell
ssh -t bandit18@bandit.labs.overthewire.org -p 2220 /bin/sh

```

### Spotlight 2: SetUID Execution & Privilege Boundaries (Level 19 → 20)

**Problem Statement:** The target credential file (/etc/bandit_pass/bandit20) is restricted to the bandit20 owner. The current user (bandit19) lacks read access.

**Technical Analysis & Resolution:**
The home directory contains a compiled binary (bandit20-do) with the SetUID (s) permission bit enabled (-rwsr-x---). When executed, the process inherits the effective privileges of the binary's file owner (bandit20), rather than the invoking user (bandit19). Passing target commands to this executable allows controlled execution inside the higher security boundary.

```text
[ Invoking User: bandit19 ]
           │
           ▼
Executes ./bandit20-do cat /etc/bandit_pass/bandit20
           │
           ▼
[ SetUID Bit Triggered ] ──► Process Effective UID switches to File Owner: bandit20
                                       │
                                       ▼
                     Reads restricted target file (/etc/bandit_pass/bandit20)
                                       │
                                       ▼
                     [ Returns Protected Credential ]


Execution Methods:

Bash
# Verify permissions and SetUID bit (note the 's' in the owner permissions)
bandit19@bandit:~$ ls -l bandit20-do
-rwsr-x--- 1 bandit20 bandit19 14880 Jun 24 14:58 bandit20-do

# Execute binary to read protected path under owner privilege context
bandit19@bandit:~$ ./bandit20-do cat /etc/bandit_pass/bandit20
4pIjcunZ0fK2vmp3IwfG8Vf7VhxD6pOA

```
