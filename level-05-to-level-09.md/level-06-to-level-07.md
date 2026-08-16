# Level 6 → Level 7

## Objective

Locate and read a password file stored **somewhere on the entire server** that satisfies three specific ownership and size properties:

1. Owned by **user `bandit7`**
2. Owned by **group `bandit6`**
3. Exactly **33 bytes in size**

## Technical Details

- **Target Username:** `bandit7`
- **SSH Port:** `2220`
- **Search Starting Directory:** `/` (root directory)
- **Target File Path:** `/var/lib/dpkg/info/bandit7.password`
- **Level 7 Password:** `Bmnnvf82KzQlfxgAI2d1zYbr1u9pr3E3`

## How to Solve

### Run the System-Wide `find` Command

Search the root filesystem (`/`) while filtering out error noise:

Bash

```
find / -user bandit7 -group bandit6 -size 33c 2>/dev/null
```

**Output:**

Plaintext

```
/var/lib/dpkg/info/bandit7.password
```

### Read the Target File

Print the contents of the located password file:

Bash

```
cat /var/lib/dpkg/info/bandit7.password
```

**Output:**

Plaintext

```
Bmnnvf82KzQlfxgAI2d1zYbr1u9pr3E3
```

## Command Breakdown

Bash

```
find / -user bandit7 -group bandit6 -size 33c 2>/dev/null
```

- **`find /`** — Searches the entire server starting from the root directory (`/`).
- **`user bandit7`** — Filters for files owned by the user account `bandit7`.
- **`group bandit6`** — Filters for files assigned to the group `bandit6`.
- **`size 33c`** — Filters for files exactly `33` bytes (`c` = bytes) in size.
- **`2>/dev/null`** — Redirects Standard Error (`stderr`) to the null device to hide `Permission denied` errors.

## Common Pitfalls & Lessons Learned

### 1. Hiding "Permission Denied" Errors (`2>/dev/null`)

- **The Problem:** Searching the root directory (`/`) as a low-privileged user (`bandit6`) causes `find` to try to scan restricted system folders (like `/root`, `/etc/sudoers`, or `/proc`). This floods your screen with hundreds of `Permission denied` errors, making the real result impossible to spot.
- **The Fix (`2>/dev/null`):**
    - `2` represents **Standard Error** (`stderr`).
    - `>` is the **redirection operator**.
    - `/dev/null` is a special system file (the Linux "black hole") that discards all data written to it.
    - Adding `2>/dev/null` silences error messages so only valid, accessible results print to your screen.

### 2. Typing Accuracy in User/Group Names

- **The Mistake:** Using `banit7` instead of `bandit7`.
- **The Result:** The system checks if a user named `banit7` exists. Since no such user exists on the system, `find` silently returns nothing without explicitly warning you about the typo.
- **Takeaway:** If a `find` query returns zero results when you expect one, always double-check the spelling of flags, username strings, and group names.

## Key Takeaways

1. **Root Directory Searches (`/`):** Use `/` as the target directory when a file could be located anywhere on the operating system rather than just your current folder.
2. **Stderr Redirection:** `2>/dev/null` is an essential Linux trick when searching system-wide directories as a non-root user.