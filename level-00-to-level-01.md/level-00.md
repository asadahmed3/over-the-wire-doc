# Level 0

## Objective

Establish an initial SSH connection to the OverTheWire `bandit` game server to access **Level 0**.

## Technical Details

- **Host / Server:** `bandit.labs.overthewire.org`
- **Username:** `bandit0`
- **Password:** `bandit0`
- **Port:** `2220`

## How to Solve

Run the following SSH command in your terminal:

Bash

```
ssh -p 2220 bandit0@bandit.labs.overthewire.org
```

> **Note:** If host name resolution fails, you can replace `bandit.labs.overthewire.org` with its direct IP address. But I don’t think it’s available.
> 

## Common Pitfall

- **Error:** Connection refused or timing out when running `ssh bandit0@bandit.labs.overthewire.org`.
- **Cause:** Omitting the `p` flag causes SSH to default to **Port 22**. The OverTheWire servers listen on non-standard ports (**Port 2220** for the Bandit wargame).
- **Key Takeaway:** Always verify and specify the target port number using the `p <port>` flag when connecting to non-standard remote servers.