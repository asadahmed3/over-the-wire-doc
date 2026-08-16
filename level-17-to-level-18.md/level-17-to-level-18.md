# Level 17 → Level 18: Detailed Walkthrough

**Objective**

Retrieve the password for **`bandit18`** stored inside `passwords.new`. The password is the only line that has been modified or updated when comparing it to an older version of the file named `passwords.old`.

**Technical Details**

- **Target Username:** `bandit18`
- **SSH Port:** `2220`
- **Files to Inspect:** `~/passwords.old` and `~/passwords.new`
- **Level 18 Password:** `OQxXZjELndr90zuhOTDYBEomI0SZITXI`

**Technical Breakdown: Core Concepts Explained**

This level introduces file comparison concepts, which are vital for tracking system changes, inspecting updates, or finding configuration drift in text files.

**1. File Differences (`diff`)**

- **What is a File Comparison?** When working with massive text assets like configuration maps or password registries, manually reading lines to look for a change is impossible.
- **The Utility Check:** The **`diff`** command looks at two text files line-by-line and highlights every added, deleted, or altered line instantly. It saves you from having to filter through data chunks yourself.

**2. Output Formatting Indicators (`<` vs `>`)**

- **Left Side (`<`):** Tells you the text was unique to the first file you listed in your command (the "old" reference point).
- **Right Side (`>`):** Tells you the text is unique to the second file you listed in your command (the "new" file update). Since the objective asks for the modified line inside `passwords.new`, you only care about the string flagged with the `>` indicator.

---

**Strategy**

1. **Verify Files:** Check the home directory to confirm both text assets are available.
2. **Compare Content:** Run a file difference tool to highlight lines that do not match across both objects.
3. **Isolate the Password:** Look at the changes marked as belonging strictly to the newer document to pull your target password.

---

**Step-by-Step Execution**

**Step 1: Check the Directory**

List the contents of the home directory to see the two password tracking lists.

**bash**

```
ls -la
```

Use code with caution.

**Step 2: Compare the Two Files**

Run the file difference command, passing the old baseline file first, followed by the updated new file:

**bash**

```
diff passwords.old passwords.new
```

Use code with caution.

> **What the results mean:**You will see a small block of output text highlighting the exact line where the files split apart:
> 
> 
> **text**
> 
> ```
> 42c42
> < old_expired_password_string
> ---
> > qOg5pVOjPx9x9VccyYBADiT4xxyoUB8D
> ```
> 
> Use code with caution.
> 
> The string right after the **`>`** marker is the modified entry living in `passwords.new`. This is your password for Level 18!
> 

---

**Key Cheat Sheet**

| **Command** | **What it does** |
| --- | --- |
| **`diff <file1> <file2>`** | Compares two files line-by-line and lists all of the differences. |
| **`grep <pattern> <file>`** | Searches through files for text matching specific words or structures. |
| **`cat <file>`** | Prints the entire text content of a file straight onto your screen. |

**Lessons Learned**

1. **Automate Comparisons:** Never read files manually to find changes. Command-line comparisons eliminate human error when scrubbing through massive data sets.
2. **Understand Directional Tags:** Paying attention to `diff` arrow directions (`<` or `>`) lets you instantly tell whether data was removed from an asset or freshly introduced by an update.

---