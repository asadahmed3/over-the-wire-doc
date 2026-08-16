# Level 16 → Level 17: Detailed Walkthrough

# **Objective**

Get the secret login key for **`bandit17`**. To find it, scan a specific range of ports (`31000-32000`) on the local server to find a hidden listening service. Once found, submit the current `bandit16` password over an encrypted connection to receive a private SSH key.

**Technical Details**

- **Target Username:** `bandit17`
- **SSH Port:** `2220`
- **Port Range to Scan:** `31000` to `32000`
- **Level 16 Password:** `JZj7M6j9CH9n6g3bZtV7XwQ2uX1V2W3A` *(Example)*
- **Level 17 Login Method:** Private Key File (`./bandit17.key`)

**Technical Breakdown: Core Concepts Explained**

This level teaches you how to look for open communication doors on a server, send text safely over encrypted channels, and use a file instead of a password to log into a system.

**1. Port Scanning & Service Checks (`nmap`)**

- **What is a Port Scan?** Think of a server as a large apartment building. Ports are individual apartment doors. A port scan knocks on these doors to see which ones are unlocked and listening for visitors.
- **Why Checking Versions Matters:** Multiple doors might be unlocked, but only one belongs to the bandit game. Running **`nmap -sV`** forces the scanner to look inside the door and report exactly what kind of application is running there, helping you find the right one.

**2. Encrypted Connections (`openssl s_client`)**

- **The Limit of Normal Tools:** Tools like `nc` (netcat) send text completely out in the open. If a server door expects a secure, encrypted handshake (SSL/TLS), a normal tool will fail to connect.
- **The OpenSSL Client:** **`openssl s_client`** creates a secure, encrypted tunnel to the server door. It handles all the complex security math automatically so you can safely send text back and forth.

**3. SSH Keys & File Permissions (`chmod`)**

- **Logging in Without a Password:** SSH allows users to log in using a "Private Key" file, which acts like a physical keycard. If you present the correct keycard file, the server unlocks immediately.
- **Strict Privacy Rules (`chmod 600`):** Linux is highly secure. If your keycard file is left lying around where other users on the system could read it, the SSH tool will refuse to use it. You must lock the file down so only *you* can see it.

**Strategy**

1. **Find the Target:** Scan the port range to discover which specific port is running the bandit challenge.
2. **Send the Password:** Use a pipeline (`|`) to feed your password into the secure port so the tool doesn't freeze or misinterpret your text.
3. **Save the Key:** Capture the returned text key and save it to a file inside the `/tmp` scratch folder.
4. **Download & Log In:** Download the key file to your own computer to bypass the server's rule against logging into itself.

---

# **Step-by-Step Execution**

**Step 1: Scan for the Secret Port**

Run a service scan over the required range to see what is running on the machine.

**bash**

```
nmap -p31000-32000 -sV localhost
```

Use code with caution.

> **What the results mean:**You will see a few open ports. One of them will say it is an echo server, but port **`31790`** is the correct one running the challenge.
> 

**Step 2: Get the Key File**

If you try to type your password into the secure connection manually, the tool might glitch and say `KEYUPDATE`. To fix this, pipe your password directly through `echo` so it sends automatically:

**bash**

```
echo"YOUR_BANDIT16_PASSWORD" | openssl s_client -connect localhost:31790 -quiet > /tmp/key.private
```

Use code with caution.

- **What `> /tmp/key.private` does:** Instead of printing the key on the screen, this saves the text block directly into a new file.

**Step 3: Check your Saved Key**

Make sure the file actually contains your key:

**bash**

```
cat /tmp/key.private
```

Use code with caution.

> **What it should look like:**
> 
> 
> **text**
> 
> ```
> -----BEGIN OPENSSH PRIVATE KEY-----
> b3BlbnNzaC1rZXktdjEAAAAABG5vbmUAAAAEbm9uZQAAAAAAAAABAAABlwAAAAdzc2gtcn
> ...
> -----END OPENSSH PRIVATE KEY-----
> ```
> 
> Use code with caution.
> 

**Step 4: Download the Key to Your PC and Log In**

The OverTheWire server blocks you from logging in from `localhost` to `localhost` to save computer resources. You must download the key file and connect from your own computer.

**1. Leave the Server**

Type `exit` to close the connection to Bandit 16 and return to your own terminal prompt:

**bash**

```
exit
```

Use code with caution.

**2. Download the Key via SCP**

Run this command on your own computer's terminal to pull the file down from the server:

**bash**

```
scp -P 2220 bandit16@bandit.labs.overthewire.org:/tmp/key.private ./bandit17.key
```

Use code with caution.

**3. Lock Down the File Permissions**

Tell your computer to protect the file. If you skip this, the login attempt will be blocked:

**bash**

```
chmod600 ./bandit17.key
```

Use code with caution.

**4. Log Directly into Level 17**

Use your newly downloaded key file to jump directly into the `bandit17` account:

**bash**

```
ssh -i ./bandit17.key bandit17@bandit.labs.overthewire.org -p 2220
```

Use code with caution.

---

# **Key Cheat Sheet**

| **Command** | **What it does** |
| --- | --- |
| **`nmap -p 31000-32000 -sV localhost`** | Finds open ports and identifies what is running on them. |
| **`openssl s_client -connect <host>:<port>`** | Connects to a server using a secure, encrypted connection. |
| **`echo "text" | openssl ...`** | Sends text directly into a command without having to type it manually. |
| **`chmod 600 <file>`** | Locks a file down so only you can read or write to it. |
| **`scp -P 2220 user@host:<remote_path> <local_path>`** | Copies a file from a remote server down to your own computer. |
| **`ssh -i <keyfile> user@host`** | Logs into a server using a private key file instead of a password. |

**Lessons Learned**

1. **Trust Services over Names:** Files and network ports can be named anything. Using scanning tools like `nmap -sV` tells you what a service *actually* is, not just what it claims to be.
2. **Automating Inputs Stops Errors:** When software gets confused by human keyboard inputs, using standard terminal piping (`|`) forces data through cleanly without interruption.