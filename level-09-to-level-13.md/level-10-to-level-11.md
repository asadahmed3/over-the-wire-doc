# Level 10 → Level 11

## Objective

Decode the contents of `data.txt`, which contains **Base64-encoded data**, to retrieve the password for **`bandit11`**.

## Technical Details

- **Target Username:** `bandit11`
- **SSH Port:** `2220`
- **Target File:** `~/data.txt`
- **Level 11 Password:** `pYfOY6HwUsDj5rL9UvyhU7MCmv8vN5Ro`

## How to Solve

### Step 1: Inspect the Encoded File (Optional)

If you view `data.txt` using `cat`, you will see a randomized block of ASCII text padded with an equals sign (`=`):

Bash

```
cat data.txt
```

### Step 2: Decode Using the `base64` Command

Use the `base64` utility with the `-d` (decode) flag to translate the encoded string back into plain text:

Bash

```
base64 -d data.txt
```

*(Or via pipe: `cat data.txt | base64 -d`)*

**Output:**

Plaintext

```
The password is pYfOY6HwUsDj5rL9UvyhU7MCmv8vN5Ro
```

## Technical Breakdown: What is Base64?

### 1. Encoding vs. Encryption

- **Base64 is an encoding scheme, not encryption.** It translates binary data or unformatted text into a safe subset of 64 printable ASCII characters (`A-Z`, `a-z`, `0-9`, `+`, `/`). This allows data to transfer across networks and protocols (like email or HTTP) without being corrupted.
- Because Base64 is purely visual transformation and not encrypted, it requires **no secret key** to decode.

### 2. Identifying Base64 Data Visual Indicators

- **Alphanumeric mix:** High density of upper and lower case letters mixed with digits.
- **Padding (`=`):** Base64 strings frequently end with one or two equals signs (`=`) used to align output byte blocks.

### 3. Key `base64` Flags

- **`base64 -d` (or `-decode`):** Decodes Base64 data into original text or binary.
- **`base64` (default):** Encodes input text into Base64 format.