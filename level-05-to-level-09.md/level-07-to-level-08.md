# Level 7 → Level 8

## Objective

Retrieve the password for **`bandit8`** stored inside `data.txt` directly adjacent to the word **`millionth`**.

## Technical Details

- **Target Username:** `bandit8`
- **SSH Port:** `2220`
- **Target File:** `~/data.txt`
- **Level 8 Password:** `VR1ljMayciFxbnUokuQmJFw6QC9VKtub`

## How to Solve

### Step 1: Inspect and Search `data.txt`

The `data.txt` file is huge and contains thousands of lines of text. To extract the specific line containing `millionth`, pipe the output of `cat` into `grep`:

Bash

```
cat data.txt | grep "millionth"
```

**Alternative Direct Command (More Efficient):**

You can also pass `data.txt` directly as an argument to `grep` without needing `cat` or a pipe:

Bash

```
grep "millionth" data.txt
```

**Output:**

Plaintext

```
millionth   VR1ljMayciFxbnUokuQmJFw6QC9VKtub
```

## Technical Breakdown

### 1. How `grep` Works

- **Pattern Matching:** `grep` (Global Regular Expression Print) processes text line-by-line and prints only the lines that match a specified search string or pattern.
- **Case Sensitivity:** By default, `grep` is case-sensitive. Searching for `"millionth"` will match `millionth`, but not `Millionth`. You can pass `i` to perform a case-insensitive search (`grep -i "millionth" data.txt`).

### 2. Piping (`|`) vs. Direct Argument

- **`cat data.txt | grep "millionth"`**
    - `cat` reads the file and streams its content through Standard Output (`stdout`).
    - The pipe (`|`) redirects `cat`'s output to serve as the Standard Input (`stdin`) for `grep`.
- **`grep "millionth" data.txt`**
    - Opens and reads `data.txt` directly. This avoids creating an extra `cat` process, making it slightly more efficient and idiomatic in shell scripting (often referred to as avoiding *Useless Use of Cat* or *UUOC*).

## Key Takeaways

- Use **`grep`** to instantly extract specific strings from massive text or log files.
- Learn to combine commands with **pipes (`|`)** to build simple, effective text-processing pipelines.