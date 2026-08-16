# Level 11 → Level 12

## Objective

Retrieve the password for **`bandit12`** stored in `data.txt`, which has been obfuscated using a **ROT13 (Rotate by 13 places)** substitution cipher across all uppercase and lowercase letters.

## Technical Details

- **Target Username:** `bandit12`
- **SSH Port:** `2220`
- **Target File:** `~/data.txt`
- **Level 12 Password:** `GROozWPO8QyN0mGrjUkID0WCYkZiQxrN`

## How to Solve

### Step 1: Inspect the Scrambled Text

Viewing `data.txt` with `cat` shows the obfuscated ROT13 string:

Bash

```
cat data.txt
```

### Step 2: Decode using the `tr` Command

Use the `tr` (translate) utility to shift the rotated alphabet back into normal order:

Bash

```
cat data.txt | tr 'A-Za-z' 'N-ZA-Mn-za-m'
```

*(Or passing the file directly via input redirection: `tr 'A-Za-z' 'N-ZA-Mn-za-m' < data.txt`)*

**Output:**

Plaintext

```
The password is GROozWPO8QyN0mGrjUkID0WCYkZiQxrN
```

## Technical Breakdown: ROT13 & Character Mapping

### 1. What is ROT13?

- **Caesar Cipher Variant:** ROT13 ("rotate by 13 places") replaces each letter with the letter 13 positions ahead of it in the alphabet.
- **Self-Reversing (Symmetric):** Because the English alphabet has 26 letters ($13 \times 2 = 26$), running ROT13 twice on any piece of text restores the original input string.
    - `A` $\rightarrow$ `N`, `B` $\rightarrow$ `O`, ..., `M` $\rightarrow$ `Z`
    - `N` $\rightarrow$ `A`, `O` $\rightarrow$ `B`, ..., `Z` $\rightarrow$ `M`

### 2. How `tr` Character Translation Works

The `tr` tool transforms individual characters based on two sets: a source set and a target destination set.

Bash

```
tr  'SOURCE_RANGE'  'DESTINATION_RANGE'
```

In our command:

Plaintext

```
tr 'A-Za-z' 'N-ZA-Mn-za-m'
```

- **Source Range (`A-Za-z`):** Represents standard uppercase (`A-Z`) and lowercase (`a-z`) alphabetical order.
- **Destination Range (`N-ZA-Mn-za-m`):** Specifies the rotated mapping:
    - `A-M` maps to `N-Z`
    - `N-Z` maps to `A-M`
    - `a-m` maps to `n-z`
    - `n-z` maps to `a-m`

Every character reading in from `data.txt` is swapped on-the-fly to its mapped counterpart in the destination set.

## Key Takeaways

1. **Obfuscation vs. Encryption:** ROT13 is purely text obfuscation, not secure encryption. It requires no secret key and can be instantly reversed using character mapping tools.
2. **Character Translation with `tr`:** Use **`tr`** for 1-to-1 character replacements, case manipulation (e.g., lowercase to uppercase with `tr 'a-z' 'A-Z'`), or deleting characters from a data stream.