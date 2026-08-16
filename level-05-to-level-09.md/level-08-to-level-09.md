# Level 8 → Level 9

## Objective

Find the password for **`bandit9`** stored inside `data.txt`, which is identified as the **only line of text that occurs exactly once** among thousands of duplicate lines.

## Technical Details

- **Target Username:** `bandit9`
- **SSH Port:** `2220`
- **Target File:** `~/data.txt`
- **Level 9 Password:** `EjmOSvuAu7sGAHqHVcBDPirRe9T03kxl`

## How to Solve

### Run the Pipeline

Combine `cat` (or direct input), `sort`, and `uniq -u` using pipes (`|`):

Bash

```
sort data.txt | uniq -u
```

*(Or using `cat`: `cat data.txt | sort | uniq -u`)*

**Output:**

Plaintext

```
EjmOSvuAu7sGAHqHVcBDPirRe9T03kxl
```

## Technical Breakdown: Understanding `sort` and `uniq`

Finding unique or duplicate items in Linux requires a two-step process using `sort` followed by `uniq`.

### 1. The `uniq` Command (The Catch)

The `uniq` utility filters out or flags adjacent repeated lines from a text stream.

- **Critical Rule:** `uniq` **ONLY detects duplicate lines if they are directly next to each other.**
- If duplicate lines are scattered throughout a file (e.g., line 5 and line 800 are identical), `uniq` will treat them as separate, unique lines and will fail to filter them out.

### 2. Why `sort` is Required First

Before passing data to `uniq`, you **must** pass it through `sort`.

`sort` rearranges all lines alphabetically/numerically. This clusters every identical line together into continuous blocks, allowing `uniq` to scan and evaluate their frequency accurately.

## Command Arguments Explained

### `sort` Options & Behavior

- **Default behavior (`sort`):** Reads input line-by-line and sorts it in standard ASCII alphabetical order.
- **Useful Flags for Future Use:**
    - `n` — Sort numerically (`1, 2, 10` instead of `1, 10, 2`).
    - `r` — Reverse the sort order (Z to A, 10 to 1).
    - `u` — Sort and remove duplicates in one step (equivalent to `sort | uniq`).

### `uniq` Options & Behavior

- **`uniq -u` (Unique):** Prints **ONLY** lines that are completely unique (lines that appear exactly once in the input).
- **`uniq -d` (Duplicates):** Prints **ONLY** lines that occur more than once (repeated lines).
- **`uniq -c` (Count):** Prefixes each line with the number of times it occurred in the file (e.g., `100 lineA`, `1 lineB`).

## Workflow Summary

Plaintext

```
Unsorted File (data.txt) ---> [ sort ] ---> Grouped Duplicates ---> [ uniq -u ] ---> Single Unique Line
```

1. **`sort data.txt`** groups identical strings together in memory.
2. **`uniq -u`** discards all the grouped repeated blocks and outputs only the single line that has no matches.

## Key Takeaways

- **`uniq` requires sorted input.** Always pipe into `sort` before piping into `uniq`.
- Use **`uniq -u`** to extract rare/unique entries (like a hidden flag or an isolated log event).
- Use **`uniq -c`** to analyze frequency distribution across large datasets.