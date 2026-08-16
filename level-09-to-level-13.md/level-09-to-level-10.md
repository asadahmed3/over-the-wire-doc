# Level 9 → Level 10

## Objective

Retrieve the password for **`bandit10`** stored inside a binary file (`data.txt`) as one of the few human-readable ASCII sequences, preceded by several `=` characters (e.g., `==========`).

## Technical Details

- **Target Username:** `bandit10`
- **SSH Port:** `2220`
- **Target File:** `~/data.txt`
- **Level 10 Password:** `B0s2khmbT9u0geKuOoVGW3JZKhndE3BG`

## How to Solve

### Run `strings` and Filter with `grep`

Pass `data.txt` to the `strings` utility to extract printable ASCII text, then pipe the output into `grep` to filter for the `=` character sequence:

Bash

```
strings data.txt | grep "=="
```

**Terminal Output:**

Plaintext

```
========== the
========== password
Y========== is
========== B0s2khmbT9u0geKuOoVGW3JZKhndE3BG
```

## Technical Breakdown: Why `strings` Works

### 1. The Problem with Binary Data

`data.txt` is a binary data file rather than a plain text file. Attempting to use standard text utilities like `cat` dumps raw, non-printable control characters that scramble your terminal display and prevent standard string searching.

### 2. How `strings` Extracts Text

The `strings` utility scans binary files and extracts continuous sequences of printable ASCII characters (by default, sequences of **4 or more characters** separated by non-printable binary bytes or null values).

Because non-printable binary bytes separated the words inside `data.txt`, `strings` broke each human-readable segment onto its own line:

Plaintext

```
========== the
========== password
Y========== is
========== B0s2khmbT9u0geKuOoVGW3JZKhndE3BG
```

Each sequence met the 4-character minimum and contained `==`, so `grep "=="` matched and printed all four lines.

## Pipeline Execution Workflow

Plaintext

```
[ Binary File: data.txt ]
          │
          ▼
    strings data.txt       <-- Extracts printable ASCII character sequences (4+ chars)
          │
          ▼
     grep "=="             <-- Filters extracted sequences for lines containing "=="
          │
          ▼
[ Multi-line Terminal Output containing the Password ]
```

## Useful `strings` Command Flags

- **`strings -n <length>`:** Customizes the minimum string length threshold (e.g., `strings -n 8 data.txt` only extracts sequences of 8 or more printable characters).
- **`strings -t <format>`:** Displays the byte offset location of each extracted string in decimal (`d`), hexadecimal (`x`), or octal (`o`).

## Key Takeaways

1. **Extracting Text from Binaries:** Whenever plain text is embedded inside a binary executable or raw data file, use `strings` first to convert the readable data into standard text lines.
2. **Handling Fragmented Output:** `strings` separates text chunks based on non-printable byte boundaries in the underlying file, which often causes sentences to break across multiple lines as seen in this output.