# Level 12 → Level 13: Detailed Walkthrough

# Objective

Retrieve the password for **`bandit13`** stored inside `data.txt`. The target file is a **text hexdump** of a binary file that has been repeatedly compressed using multiple archive utilities (`gzip`, `bzip2`, and `tar`).

## Technical Details

- **Target Username:** `bandit13`
- **SSH Port:** `2220`
- **Target File:** `~/data.txt`
- **Level 13 Password:** `qQYQiHOBPR8zR61qxYqX45quvihF2uzk`

## Technical Breakdown: Core Concepts Explained

This level introduces three essential concepts that are critical for system administration, reverse engineering, and working with compiled or archived data.

### 1. Hexdumps & Reversing (`xxd`)

- **What is a Hexdump?** Raw binary files (like executables, images, or compressed archives) contain bytes that cannot be displayed cleanly as standard ASCII text. A hexdump represents those raw binary bytes as human-readable hexadecimal pairs (e.g., `47 5a 49 50`).
- **Why Reversing Matters:** Archives like `.gz` or `.tar` cannot be decompressed while stored as text hex. The command **`xxd -r`** (revert/reverse) translates hexadecimal text strings back into true raw binary bytes so decompression tools can process them.

### 2. File Signatures vs. File Extensions (`file` vs. `mv`)

- **File Signatures (Magic Bytes):** Unlike Windows, Linux does not rely on file extensions to determine what a file is. It reads header bytes inside the file (its signature). The **`file`** utility inspects these bytes to tell you the real file type.
- **Why `mv` is Needed:** While Linux knows a file is `gzip` compressed by checking its header, utility tools like `gzip` strictly enforce a `.gz` file extension before they agree to unpack it. We use **`mv`** to append the required extension to satisfy the command line tool.

### 3. Archive & Decompression Types

- **Gzip (`gzip`):** Single-file compression tool. Requires a `.gz` extension to decompress using `gzip -d`.
- **Bzip2 (`bzip2`):** High-compression tool. Decompressed using `bzip2 -d` (automatically appends `.out` to the resulting file).
- **Tar (`tar`):** Tape Archive tool used to bundle multiple files/folders into a single container. Extracted using `tar -xf` (`x` = extract, `f` = target file).

## Strategy

1. **Isolated Workspace:** Create a temporary directory in `/tmp` so we can safely extract multiple files without cluttering or running out of permissions.
2. **Reverse the Hexdump:** Convert `data.txt` from text hex back into binary data using `xxd -r`.
3. **The Loop:**
    - Run `file <filename>` to inspect what type of compression was used.
    - Rename the file using `mv` to add the correct extension (`.gz`, `.bz2`, or `.tar`).
    - Extract it using the correct tool (`gzip`, `bzip2`, or `tar`).
    - Repeat until `file` reports **ASCII text**.

## Step-by-Step Execution

### Step 1: Create a Workspace & Copy the Data

First, create a clean, private workspace inside `/tmp` using `mktemp -d`, then copy `data.txt` into it.

Bash

```
# 1. Create a hidden, unique temp folder and navigate into it
cd $(mktemp -d)

# 2. Copy the level's data file here
cp ~/data.txt .
```

### Step 2: Reverse the Hexdump (`xxd`)

`data.txt` is not a real zip or tar archive yet—it is a plain text file containing a **hexdump** (raw hexadecimal numbers written as text). We need to convert those hex characters back into actual binary bytes.

Bash

```
xxd -r data.txt > data1
```

- **What `xxd -r` does:** Reads the hexadecimal values from `data.txt` and reconstructs the raw binary file, saving it as `data1`.

### Step 3: Peel the Layers (File by File)

#### Layer 1: `data1`

Check what `data1` actually is:

Bash

```
file data1
```

> **Output:** `data1: gzip compressed data, was "data2.bin", ...`
> 

Since it's a **gzip** file, `gzip` requires the file name to end in `.gz` before it will decompress it:

Bash

```
mv data1 data2.gz
gzip -d data2.gz
```

*(This extracts a new file named `data2`)*

#### Layer 2: `data2`

Check the new file's type:

Bash

```
file data2
```

> **Output:** `data2: bzip2 compressed data, ...`
> 

It is a **bzip2** archive. `bzip2` doesn't strictly require an extension to unpack:

Bash

```
bzip2 -d data2
```

*(This extracts a new file named `data2.out`)*

#### Layer 3: `data2.out`

Check the file type:

Bash

```
file data2.out
```

> **Output:** `data2.out: gzip compressed data, ...`
> 

It's **gzip** again. Rename it with `.gz` and extract:

Bash

```
mv data2.out data3.gz
gzip -d data3.gz
```

*(This extracts `data3`)*

#### Layer 4: `data3`

Check the file type:

Bash

```
file data3
```

> **Output:** `data3: POSIX tar archive`
> 

It is a **tar archive** (tape archive). We extract tar files using `tar -xf` (`x` for extract, `f` for file):

Bash

```
tar -xf data3
```

*(This extracts a new file into your directory named `data5.bin`)*

#### Layer 5: `data5.bin`

Check the file type:

Bash

```
file data5.bin
```

> **Output:** `data5.bin: POSIX tar archive`
> 

Another **tar archive**. Extract it:

Bash

```
tar -xf data5.bin
```

*(This extracts a new file named `data6.bin`)*

#### Layer 6: `data6.bin`

Check the file type:

Bash

```
file data6.bin
```

> **Output:** `data6.bin: bzip2 compressed data, ...`
> 

It is **bzip2**. Decompress it:

Bash

```
bzip2 -d data6.bin
```

*(This extracts a file named `data6.bin.out`)*

#### Layer 7: `data6.bin.out`

Check the file type:

Bash

```
file data6.bin.out
```

> **Output:** `data6.bin.out: POSIX tar archive`
> 

Another **tar archive**. Extract it:

Bash

```
tar -xf data6.bin.out
```

*(This extracts a file named `data8.bin`)*

#### Layer 8: `data8.bin`

Check the file type:

Bash

```
file data8.bin
```

> **Output:** `data8.bin: gzip compressed data, ...`
> 

Final compression layer! It's **gzip**. Rename with `.gz` and decompress:

Bash

```
mv data8.bin data9.gz
gzip -d data9.gz
```

*(This extracts `data9`)*

### Step 4: Get the Password!

Check the final file type:

Bash

```
file data9
```

```
The password is qQYQiHOBPR8zR61qxYqX45quvihF2uzk
```

## Key Cheat Sheet

| **Command** | **Usage** | **Action** |
| --- | --- | --- |
| **`xxd -r <file> > <out>`** | Hex Reversing | Converts formatted hex text strings back into binary bytes. |
| **`file <file>`** | Signature Check | Identifies true file format by checking internal magic numbers. |
| **`gzip -d <file.gz>`** | Gzip Extract | Unpacks `.gz` archives (file **must** end in `.gz`). |
| **`bzip2 -d <file>`** | Bzip2 Extract | Unpacks `bzip2` archives. |
| **`tar -xf <file>`** | Tar Extract | Extracts files inside a `.tar` container (`x`=extract, `f`=file). |
| **`mktemp -d`** | Temp Directory | Generates a unique, world-writable directory in `/tmp`. |

## Lessons Learned

1. **Always Trust `file` over Extensions:** Filenames can be deceptive or missing extensions entirely. The `file` command provides the ground truth of what a data payload contains.
2. **Methodical CLI Debugging:** Complex challenges are just simple single-step operations stacked on top of each other. Inspecting after every extraction step prevents getting lost in nested directory structures.