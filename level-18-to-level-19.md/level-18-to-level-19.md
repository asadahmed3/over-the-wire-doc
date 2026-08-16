# Level 18 → Level 19

**Objective:** Retrieve the password for `bandit19` inside `~/readme` when `bandit18`'s `.bashrc` automatically terminates standard interactive login sessions.

**Target Account:** `bandit18`

**Host:** `bandit.labs.overthewire.org`

**Port:** `2220`

**Retrieved Password (`bandit19`):** `KpsOfPkcP7i1FlIExk2QEjyt6dw8dxZI`

### Solution Methods Comparison

| **Approach** | **Command** | **Pros** | **Cons** | **Best Used When** |
| --- | --- | --- | --- | --- |
| **1. Direct Command** | `ssh bandit18@bandit.labs.overthewire.org -p 2220 cat readme` | • Fast & concise

• No pseudo-terminal needed | • One-off execution

• No interactive shell to navigate | You know the exact file path or command you need to run. |
| **2. Bypassing RC Hooks** | `ssh -t bandit18@bandit.labs.overthewire.org -p 2220 /bin/bash --norc` | • Full interactive `bash` prompt

• Keeps tab completion and features | • Requires `-t` flag

• Specific to `bash` | You need full interactive access on a machine with broken `.bashrc` scripts. |
| **3. Alternate Shell** | `ssh -t bandit18@bandit.labs.overthewire.org -p 2220 /bin/sh` | • Bypasses `bash` configs completely

• Works across POSIX systems | • Minimal shell prompt (`$`)

• Lacks advanced `bash` shortcuts | `bash` is restricted, locked down, or unavailable. |

### Terminal Execution & Proof

**Method 1: Direct Command Execution**

Bash

```
ssh bandit18@bandit.labs.overthewire.org -p 2220 cat readme
```

Plaintext

```
                         _                     _ _ _
                        | |__   __ _ _ __   __| (_) |_
                        | '_ \ / _` | '_ \ / _` | | __|
                        | |_) | (_| | | | | (_| | | |_
                        |_.__/ \__,_|_| |_|\__,_|_|\__|

                      This is an OverTheWire game server.
            More information on http://www.overthewire.org/wargames

backend: gibson-1
bandit18@bandit.labs.overthewire.org's password:
KpsOfPkcP7i1FlIExk2QEjyt6dw8dxZI
```

**Method 2: Interactive Bash Bypassing `.bashrc` (`--norc`)**

Bash

```
ssh -t bandit18@bandit.labs.overthewire.org -p 2220 /bin/bash --norc
```

Plaintext

```
                         _                     _ _ _
                        | |__   __ _ _ __   __| (_) |_
                        | '_ \ / _` | '_ \ / _` | | __|
                        | |_) | (_| | | | | (_| | | |_
                        |_.__/ \__,_|_| |_|\__,_|_|\__|

                      This is an OverTheWire game server.
            More information on http://www.overthewire.org/wargames

backend: gibson-1
bandit18@bandit.labs.overthewire.org's password:
bash-5.3$ ls
readme
bash-5.3$ cat readme
KpsOfPkcP7i1FlIExk2QEjyt6dw8dxZI
```

**Method 3: Alternate Shell (`/bin/sh`)**

Bash

```
ssh -t bandit18@bandit.labs.overthewire.org -p 2220 /bin/sh
```

Plaintext

```
                         _                     _ _ _
                        | |__   __ _ _ __   __| (_) |_
                        | '_ \ / _` | '_ \ / _` | | __|
                        | |_) | (_| | | | | (_| | | |_
                        |_.__/ \__,_|_| |_|\__,_|_|\__|

                      This is an OverTheWire game server.
            More information on http://www.overthewire.org/wargames

backend: gibson-1
bandit18@bandit.labs.overthewire.org's password:
$ ls
readme
$ cat readme
KpsOfPkcP7i1FlIExk2QEjyt6dw8dxZI
```

### What is `t`?

The `-t` option forces SSH to allocate a **pseudo-terminal (pseudo-tty)** on the remote server.

- Without `t`, running a remote command via SSH executes it in a non-interactive pipe (no interactive prompt, tab completion, or terminal controls).
- With `t`, the remote server treats your connection as an active interactive terminal session, allowing programs like `/bin/bash` or `/bin/sh` to display a functional prompt (`bash-5.3$` or `$`).

### Key Takeaways

- **Understanding `.bashrc` Traps:** The default interactive login sequence automatically runs `~/.bashrc`. If an `exit` command is inserted inside it, a standard login drops immediately.
- **Why Method 1 Works:** Appending a command to SSH runs it non-interactively, skipping interactive startup scripts like `.bashrc` entirely. We just want to get a file, not delve into the entire system. Since we already have the password, accessing it is simple.
- **Why Method 2 Works:** Passing `-norc` explicitly tells `bash` to ignore user configuration files (`.bashrc`) on boot.
- **Why Method 3 Works:** Launching `/bin/sh` invokes the standard POSIX shell, which ignores `bash`specific configuration files.