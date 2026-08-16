# Level 19 → Level 20

**Objective:** Retrieve the password for `bandit20` stored in `/etc/bandit_pass/bandit20` using a binary file in the home directory that has Special Linux Permissions set.

**Target Account:** `bandit19`

**Host:** `bandit.labs.overthewire.org`

**Port:** `2220`

**Retrieved Password (`bandit20`):** `4pIjcunZ0fK2vmp3IwfG8Vf7VhxD6pOA`

### Terminal Execution & Proof

Bash

```
bandit19@bandit:~$ ls -l
total 16
-rwsr-x--- 1 bandit20 bandit19 14880 Jun 24 14:58 bandit20-do

bandit19@bandit:~$ ./bandit20-do
Run a command as another user.
  Example: ./bandit20-do id

bandit19@bandit:~$ ./bandit20-do cat /etc/bandit_pass/bandit20
4pIjcunZ0fK2vmp3IwfG8Vf7VhxD6pOA
```

### Understanding the Key Concepts

**1. What is `bandit20-do` and how do you spot an executable?**
When you listed the files (`ls -l`), `bandit20-do` was not a text file or directory—it was a compiled program binary. You can identify executable files by checking two main details:

- **The File Permissions (`rwsr-x---`):** The `x` and `s` bits signify that the file can be **executed** directly as a command by typing `./bandit20-do`.
- **The `file` Command Output:** Running `file bandit20-do` gives `ELF 32-bit executable`, confirming it is a program binary compiled to run directly on the operating system.

**2. Clarifying `setuid` vs. `setgid` (Permissions, Not Commands)**`setuid` (Set User ID) and `setgid` (Set Group ID) are **not standalone terminal commands**—they are **special permission settings** applied to executable files.

- Standard Linux permissions decide *who* can read, write, or run a file.
- Normally, when you run a program, it executes with **your** user permissions.
- The **`setuid` flag** changes this behavior: when you execute a `setuid` program, it runs with the permissions of the **file owner** rather than the user running it.

### How SetUID Privilege Escalation Works in This Level

```
[ You: bandit19 ]  --->  Executes ./bandit20-do  --->  [ File Owner: bandit20 ]
                                                               |
                                                  Executes 'cat /etc/bandit_pass/bandit20'
                                                               |
[ Password Displayed ] <--- Returns Output <-------------------/
```

1. **Access Restriction:** Your account (`bandit19`) cannot directly read `/etc/bandit_pass/bandit20` because Linux file permissions restrict read access exclusively to the `bandit20` user.
2. **Elevated Execution:** The `bandit20-do` file is owned by `bandit20` and has the `setuid` bit enabled (represented by the small `s` in `rwsr-x---`).
3. **Privilege Switch:** Running `./bandit20-do cat /etc/bandit_pass/bandit20` forces the system to run `cat` temporarily as the `bandit20` user, allowing you to bypass file permissions and display the target password.

### Key Takeaways

- **Spotting SetUID Files:** Look for an `s` in place of the standard `x` in the owner section of file permissions (`rwsr-x---`).
- **Running Local Executables:** The `./` prefix explicitly instructs Linux to execute a file located inside the current working directory.
- **Privilege Escalation Fundamentals:** SetUID binaries are often designed for legitimate administrative tasks (like `passwd` or `sudo`), but if misconfigured or abused, they allow lower-privileged accounts to execute commands as higher-privileged users.