# Level 3 → Level 4

# Objective

Retrieve the password for **`bandit4`** stored in a hidden file inside the `inhere` directory located in the home folder (`/home/bandit3/inhere`).

## Technical Details

- **Target Username:** `bandit4`
- **SSH Port:** `2220`
- **Target Directory:** `~/inhere`
- **Target File:** `~/inhere/...Hiding-From-You`
- **Level 4 Password:** `xzTXq1rDJQVVAzdv5cHq1TQytTWufAMq`

## How to Solve

### Step 1: Navigate to the Directory

Change into the `inhere` directory:

Bash

```
cd inhere
```

### Step 2: Reveal Hidden Files

Running a standard `ls` command displays nothing because the file is hidden. Use `ls` with the `-a` (all) flag to reveal hidden files and directories:

Bash

```
ls -a
```

**Directory Listing:**

Plaintext

```
.  ..  ...Hiding-From-You
```

### Step 3: Read the Hidden Password File

Print the contents of the hidden file `...Hiding-From-You`:

Bash

```
cat ...Hiding-From-You
```

**Output:**

Plaintext

```
xzTXq1rDJQVVAzdv5cHq1TQytTWufAMq
```

## Technical Breakdown

### What Makes a File "Hidden" in Linux?

- **Dotfiles:** In Linux and Unix-like operating systems, any file or folder whose name begins with a period (`.`) is considered a hidden file (often called a *dotfile*).
- **Default `ls` Behavior:** The standard `ls` command deliberately filters out files starting with `.` to keep directory listings clean and avoid cluttering views with configuration files.
- **The `a` Flag:** Passing `a` (or `-all`) explicitly overrides this default behavior and instructs `ls` not to ignore entries starting with `.`.

> **Note on Navigation:**
> 
> - `.` represents the **current directory**.
> - `..` represents the **parent directory** (one level up).
> - Files like `...Hiding-From-You` are valid hidden files because they begin with a dot, regardless of how many dots follow.