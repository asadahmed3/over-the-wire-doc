# Level 5 → Level 6

## Objective

Locate and read a specific file hidden deep within nested subdirectories under `inhere/` that satisfies three specific properties:

1. **Human-readable** (plain text)
2. **Exactly 1033 bytes in size**
3. **Not executable**

## Technical Details

- **Target Username:** `bandit6`
- **SSH Port:** `2220`
- **Target Directory:** `~/inhere`
- **Target File Path:** `~/inhere/maybehere07/.file2`
- **Level 6 Password:** `pXa26xhMWaC2SvDotA4r9EgZkulOeSBW`

## How to Solve

### Step 1: Navigate to the `inhere` Directory

Bash

```
cd inhere
```

Inside `inhere`, there are dozens of folders (`maybehere00` through `maybehere19`) filled with random files. Inspecting each directory manually with `cd` and `ls` would take too long.

### Step 2: Use `find` to Filter Files by Criteria

Instead of opening folders manually, use the `find` command to search the directory structure automatically based on file metadata:

Bash

```
find . -type f -size 1033c ! -executable
```

**Output:**

Plaintext

```
./maybehere07/.file2
```

### Step 3: Read the Target File

Read the contents of the file discovered by `find`:

Bash

```
cat ./maybehere07/.file2
```

**Output:**

Plaintext

```
pXa26xhMWaC2SvDotA4r9EgZkulOeSBW
```

## Technical Breakdown: Learning the `find` Command

The `find` utility is a powerful Linux command-line tool used to walk through directory trees and locate files matching specific rules.

### How the Command Works:

Bash

```
find  [where to look]  [type filter]  [size filter]  [permission filter]
```

- **`find .`**
    - Tells `find` to start searching inside the **current directory** (`.`) and search recursively through all subfolders.
- **`type f`**
    - Restricts results strictly to **regular files** (filters out directories, links, and devices).
- **`size 1033c`**
    - Filters for files that are **exactly 1033 bytes** in size. The trailing **`c`** specifies *bytes* (without `c`, Linux treats numbers as 512-byte blocks).
- **`! -executable`**
    - The exclamation mark (`!`) acts as a **NOT** operator. This condition selects files that do **not** have executable permissions.

## Key Takeaways

1. **Automation Over Manual Search:** When faced with nested folders or hundreds of files, `find` replaces tedious manual navigation (`cd`/`ls`).
2. **Size Units in `find`:**
    - `c` = Bytes (e.g., `1033c`)
    - `k` = Kilobytes
    - `M` = Megabytes
3. **Logic Operators (`!`):** You can invert any `find` condition using `!` (e.g., `! -name "*.txt"` or `! -executable`).