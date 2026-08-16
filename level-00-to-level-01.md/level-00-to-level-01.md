# Level 0 → Level 1

# Objective

Find the password hidden in the home directory while logged in as `bandit0`, then establish a new SSH session to log in as **`bandit1`**.

## Technical Details

- **Host / Server:** `bandit.labs.overthewire.org`
- **Username:** `bandit1`
- **Port:** `2220`
- **Password:** 6y2kwnwK6grgvwvpvLaa2T1cpFEKOhNR
- **Password File:** `~/readme`

## How to Solve

### Step 1: Read the Password

While logged into `bandit0`, read the contents of the `readme` file in the home directory to retrieve the password for `bandit1`:

zsh

```
cat readme
```

*Copy the outputted password to your clipboard.*

### Step 2: Connect as `bandit1`

Exit the current session and log in directly as `bandit1`:

zsh

```
# Disconnect from bandit0 session
logout

# Connect to bandit1
ssh -p 2220 bandit1@bandit.labs.overthewire.org
```

*Paste the password when prompted.*

## Common Pitfall

- **Error:** Attempting to switch users from inside the current session using `su bandit1`.
- **Cause:** OverTheWire server environments restrict or disable user switching (`su` / `sudo`) between level accounts from within an active session.
- **Key Takeaway:** Whenever you retrieve a password for a new level, open a new SSH connection using that level's username (e.g., `bandit1`) on port `2220`.