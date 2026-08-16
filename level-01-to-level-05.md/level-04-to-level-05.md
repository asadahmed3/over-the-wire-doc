# Level 4 → Level 5

## Objective

Identify and read the only human-readable (ASCII text) file inside the `inhere` directory from among multiple binary or non-human-readable data files.

## Technical Details

- **Target Username:** `bandit5`
- **SSH Port:** `2220`
- **Target Directory:** `~/inhere`
- **Target File:** `~/inhere/-file07`
- **Level 5 Password:** `6C7h9GD8M6ai5nr7wo1RonrzFjj9yIrG`

## How to Solve

### Step 1: Navigate to the `inhere` Directory

Bash

```
cd inhere
```

### Step 2: Identify File Types Using `file`

Listing the directory (`ls`) reveals ten files named `-file00` through `-file09`. Rather than printing each file with `cat`, use the `file` command combined with a wildcard (`*`) to inspect their file signatures:

Bash

```
file ./*
```

**Output:**

Plaintext

```
./-file00: data
./-file01: data
./-file02: data
./-file03: data
./-file04: data
./-file05: data
./-file06: data
./-file07: ASCII text
./-file08: data
./-file09: data
```

### Step 3: Read the Human-Readable File

Notice that `./-file07` is identified as **ASCII text**, while the others are raw binary `data`. Read `./-file07` using its relative path prefix (`./`):

Bash

```
cat ./-file07
```

**Output:**

Plaintext

```
6C7h9GD8M6ai5nr7wo1RonrzFjj9yIrG
```

## Alternative Solutions

### One-Liner Automation

Instead of manually inspecting the output of `file ./*`, you can combine shell tools to automatically find and print the text file:

Bash

```
# Filter file descriptions for ASCII text
file ./* | grep "ASCII text"

# Or find and print it in a single pipe
file ./* | grep "ASCII text" | cut -d: -f1 | xargs cat
```

## Technical Breakdown & Key Takeaways

### 1. The `file` Command vs. Extensions

- In Linux, file extensions (like `.txt` or `.bin`) are optional and often omitted.
- The `file` utility inspects internal **magic bytes** (header signatures) to determine whether a file contains plain human-readable text, compiled binary, compressed archives, or raw data.

### 2. Why Binary Files "Mess Up" Terminals

- Running `cat` on a binary file dumps raw control bytes and escape codes into the shell session.
- These control characters can disrupt your terminal's character rendering mode, filling the screen with strange symbols or hiding your prompt completely.
- **Fixing Corrupted Terminals:** If your display gets scrambled after reading binary data, run the `reset` command (or type `reset` and hit Enter, even if you can't see what you are typing) to restore default settings:Bash
    
    ```
    reset
    ```