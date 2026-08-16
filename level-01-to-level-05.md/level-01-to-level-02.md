# Level 1 → Level 2

## Objective

Retrieve the password for **`bandit2`** from a file named `-` located in the home directory (`/home/bandit1`).

## Technical Details

- **Target Username:** `bandit2`
- **SSH Port:** `2220`
- **Target File:** `~/-`
- **Level 2 Password:** `PK8fYLZg2hnHSz83plBL1iEPKdD3QToB`

## How to Solve

### 1. Log in as `bandit1`

Connect using the credentials obtained from Level 0:

Bash

```
ssh -p 2220 bandit1@bandit.labs.overthewire.org
```

### 2. Read the Dashed Filename

To print the contents of the file named `-`, explicitly specify the relative path using `./`:

Bash

```
cat ./-
```

**Output:**

Plaintext

```
PK8fYLZg2hnHSz83plBL1iEPKdD3QToB
```

## Technical Breakdown: What Went Wrong?

### Mistake 1: Why `cat -` or `vim -` Fails

- **The Mechanics:** On Linux and Unix-like operating systems, a single dash  passed as a command argument traditionally represents **Standard Input (`stdin`)** rather than a literal filename.
- **The Result:** Running `cat -` or `vim -` tells the tool to sit and wait for manual keyboard input from the user instead of reading the actual file on disk.
- **The Fix:** Adding `./` before the filename (`./-`) forces the shell and command to treat  as a file path inside the current directory.

### Mistake 2: The Global `grep` Misconception

Running `grep` across the entire `/home` directory was a clever attempt to locate the string `"password"`, but it resulted in two main issues:

1. **Permission Denied Noise:** Most subdirectories under `/home` belong to other level users (`bandit2`, `bandit3`, etc.) which `bandit1` does not have permission to read.
2. **False Positive Result:** The `grep` command returned:Plaintext
    
    ```
    bandit0/readme:The password you are looking for is: 6y2kwnwK6grgvwvpvLaa2T1cpFEKOhNR
    ```
    
    This is the password for **Level 0**, which was already completed. Searching system-wide picked up old, leftover files rather than the target password for Level 2.
    

## Key Takeaway

- Always use `./` when interacting with files that start with special characters or dashes (e.g., `./-` or `./--spaces--`).
- Focus initial searches strictly within your own user directory (`/home/bandit1`) before searching broader system directories.