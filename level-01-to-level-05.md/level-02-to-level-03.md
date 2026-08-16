# Level 2 → Level 3

# Objective

Retrieve the password for **`bandit3`** stored in a file titled `--spaces in this filename--` located in the home directory (`/home/bandit2`).

## Technical Details

- **Target Username:** `bandit3`
- **SSH Port:** `2220`
- **Target File:** `~/\-\-spaces in this filename\-\-`
- **Level 3 Password:** `7ZZ2LFrykP2zEyvBl4m3clcL7tGYJPME`

## How to Solve

### Method 1: Explicit Path Prefix (Recommended)

Prepend `./` to force the shell and program to interpret the input strictly as a file path, and wrap the filename in quotes to handle spaces:

Bash

```
cat "./--spaces in this filename--"
```

### Method 2: End-of-Options Delimiter (`-`)

Use `--` to tell the command line parser to stop scanning for command flags. Anything after `--` is treated strictly as positional arguments (filenames):

Bash

```
cat -- "--spaces in this filename--"
```

**Output for both commands:**

Plaintext

```
7ZZ2LFrykP2zEyvBl4m3clcL7tGYJPME
```

## Technical Breakdown: The Two Obstacles

When dealing with a filename like `--spaces in this filename--`, two distinct shell parsing rules cause standard commands to fail:

### 1. Leading Dashes (`-`) Interfere with Flag Parsing

- **The Problem:** Linux commands rely on POSIX standards where a single dash () or double dash (`-`) introduces command options/flags (e.g., `ls -l` or `grep --help`).
- **Why `cat --spaces...` Fails:** Running `cat --spaces in this filename--` causes `cat` to interpret `-spaces` as an invalid option flag, throwing an error like `cat: unrecognized option '--spaces'`.
- **The Solutions:**
    - **Explicit Relative Path (`./`):** By writing `./--spaces...`, the argument starts with a dot (`.`), so `cat` sees a path rather than a option flag.
    - **Option Delimiter (`-`):** In POSIX utilities, passing a standalone `-` signals the **end of command-line flags**. Every argument following `-` is treated purely as data or a file argument.

### 2. Spaces Split Arguments

- **The Problem:** The shell uses whitespace (spaces and tabs) as **argument delimiters**.
- **Why Unquoted Commands Fail:** Running `cat ./--spaces in this filename--` causes the shell to split the string into 4 separate arguments: `./--spaces`, `in`, `this`, and `filename--`. `cat` will try (and fail) to open four separate non-existent files.
- **The Solution:** Enclose the full path in double quotes (`"..."`), single quotes (`'...'`), or escape each individual space using a backslash (`\` ):Bash
    
    ```
    cat ./\-\-spaces\ in\ this\ filename\-\-
    ```
    

## Key Takeaways

- Use **quotes** or **backslash escapes** (`\` ) whenever filenames contain whitespace.
- Use **`./`** or the **`-` option delimiter** whenever filenames start with dashes to prevent commands from treating filenames as configuration flags.