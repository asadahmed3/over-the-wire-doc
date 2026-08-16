# Level 15 → Level 16

## Objective

Retrieve the password for **`bandit16`** by submitting the `bandit15` password (`pbLYuZtTg4MgaqfJx8jbA9gKKGqM68A7`) to **port 30001** on `localhost` over an SSL/TLS encrypted connection.

## Technical Details

- **Target Username:** `bandit16`
- **Target Host:** `localhost` (`127.0.0.1`)
- **Target Port:** `30001`
- **Encryption Scheme:** TLS 1.3
- **Input Password (`bandit15`):** `pbLYuZtTg4MgaqfJx8jbA9gKKGqM68A7`
- **Retrieved Password (`bandit16`):** `kS0Hf0u5HiXFwKMKFqXvPdOTNGGa0X8V`

## Terminal Execution Record

Bash

```
bandit15@bandit:~$ openssl s_client -connect localhost:30001
Connecting to 127.0.0.1
CONNECTED(00000003)
...
New, TLSv1.3, Cipher is TLS_AES_256_GCM_SHA384
Protocol: TLSv1.3
...
pbLYuZtTg4MgaqfJx8jbA9gKKGqM68A7
Correct!
kS0Hf0u5HiXFwKMKFqXvPdOTNGGa0X8V
closed
```

## Deep Technical Analysis & Execution Mechanics

Connecting to an encrypted port requires completing two distinct setup phases before application data can travel: establishing the underlying **Layer 4 TCP Connection** followed by negotiating the **Layer 6/7 TLS Handshake**.

```
+-------------------------------------------------------------------+
|                        1. TCP 3-Way Handshake                     |
|  Client (bandit15)  ---------------------------->  Server (30001) |
|                     <----------------------------                 |
|                     ---------------------------->                 |
+-------------------------------------------------------------------+
|                        2. TLS 1.3 Handshake                       |
|  - Client Hello (supported ciphers, TLS versions)                 |
|  - Server Hello + Certificate (CN=SnakeOil)                       |
|  - Key Exchange (TLS_AES_256_GCM_SHA384)                          |
+-------------------------------------------------------------------+
|                        3. Encrypted Data Transfer                 |
|  [Standard Input (stdin)] ---> [TLS Encryption] ---> [Socket]     |
|  [Standard Output (stdout)] <-- [TLS Decryption] <-- [Socket]     |
+-------------------------------------------------------------------+
```

1. **TCP Socket Allocation:**
    
    The line `CONNECTED(00000003)` confirms the creation of a raw TCP socket assigned to file descriptor `3`.
    
2. **TLS 1.3 Handshake:**
    
    `openssl` negotiates encryption parameter sets (`TLS_AES_256_GCM_SHA384`).
    
3. **Self-Signed Certificate Exception:**
    
    `Verify return code: 18 (self-signed certificate)` flags an untrusted internal certificate (`CN=SnakeOil`). `openssl s_client` allows the connection to proceed regardless, permitting interaction with non-production microservices.
    
4. **Session Tickets:**
    
    `read R BLOCK` messages indicate post-handshake session tickets dispatched by the remote service for optional session resumption.
    

## Execution Methods & Underlying Linux Mechanics

### 1. Interactive Shell Mode

Bash

```
openssl s_client -connect localhost:30001
```

- **Mechanism:** OpenSSL opens a interactive stream binding your terminal's Standard Input (`stdin`) and Standard Output (`stdout`) directly to the encrypted SSL socket.
- **Use Case:** Manual inspection of SSL certificate chains, protocol version support, and initial service discovery.

### 2. Direct Pipeline (`echo` + Pipe Operator)

Bash

```
echo "pbLYuZtTg4MgaqfJx8jbA9gKKGqM68A7" | openssl s_client -connect localhost:30001 -quiet
```

- **Mechanism:** `echo` writes the string followed by a newline (`\n`) to `stdout`. The pipe (`|`) connects `echo`'s `stdout` directly into `openssl`'s `stdin`.
- **`quiet` Flag:** Suppresses handshake logs, certificate banners, and session ticket output, leaving only the application response on terminal output.
- **Use Case:** Clean output formatting in non-interactive shell scripts.

### 3. File Descriptor Input Redirection

Bash

```
openssl s_client -connect localhost:30001 -quiet < /etc/bandit_pass/bandit15
```

- **Mechanism:** The `<` operator redirects the contents of the password file directly into the program's `stdin` file descriptor without spawning an intermediary shell process.

### 4. Modern Socket Handling via `ncat --ssl`

Bash

```
ncat --ssl localhost 30001 < /etc/bandit_pass/bandit15
```

- **Mechanism:** Standard Netcat (`nc`) fails on encrypted ports because it transmits unencrypted TCP payloads without performing a TLS handshake. `ncat` with the `-ssl` flag wraps the outgoing socket in a transparent TLS layer automatically.

## Summary Command Matrix

| **Method** | **Protocol Wrapper** | **Output Verbosity** | **Primary Use Case** |
| --- | --- | --- | --- |
| **Interactive** | `openssl s_client` | Full Certificate / Cipher Details | Debugging TLS connections & certificates |
| **Pipe (`echo`)** | `openssl s_client -quiet` | Data Only | Automation & one-line command scripts |
| **File Redirection** | `openssl s_client -quiet <` | Data Only | Reading stored key/password files directly |
| **`ncat` Client** | `ncat --ssl` | Data Only | Modern lightweight alternative to netcat |

## Key Takeaways

- **Netcat vs. OpenSSL:** Standard `nc` sends raw TCP traffic. When connecting to an SSL/TLS listener, `openssl s_client` or `ncat --ssl` must be used to perform the cryptographic handshake before data exchange can occur.
- **Unix I/O Redirection:** `s_client` accepts standard input from keyboards, pipes (`|`), or redirected files (`<`), adhering to the Unix philosophy that standard input handles file descriptors uniformly regardless of origin.