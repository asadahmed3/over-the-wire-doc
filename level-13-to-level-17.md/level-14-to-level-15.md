# Level 14 → Level 15

## Objective

Retrieve the password for **`bandit15`** by submitting the current level's password (`bandit14`: `aaWecNkG4FhxJQxz07uiwzVP6bJiYS65`) to **port 30000** on **`localhost`**.

## Technical Details

- **Target Username:** `bandit15`
- **Target Host:** `localhost` (`127.0.0.1`)
- **Target Port:** `30000`
- **Input Password (`bandit14`):** `aaWecNkG4FhxJQxz07uiwzVP6bJiYS65`
- **Level 15 Password:** `pbLYuZtTg4MgaqfJx8jbA9gKKGqM68A7`

## Core Concepts & Mental Models

Networking can feel abstract, but this level introduces fundamental concepts used daily in cloud engineering, container management, and system administration.

Plaintext

```
       [ Your Linux Terminal Session ]
                     │
     Sends string over TCP socket
                     │
                     ▼
┌───────────────────────────────────────────┐
│  Server: localhost (127.0.0.1)            │
│                                           │
│  ┌─────────────────────────────────────┐  │
│  │ Port 30000 (Listening Service)      │  │
│  │                                     │  │
│  │ 1. Validates input password         │  │
│  │ 2. Returns Level 15 password        │  │
│  └─────────────────────────────────────┘  │
└───────────────────────────────────────────┘
```

### 1. The "Apartment Building" Analogy

- **`localhost` (`127.0.0.1`):** Represents the local machine you are currently operating on. It is the **street address** of the building.
- **Port (`30000`):** Acts like an **apartment unit number**. An IP address identifies the machine, but the port number routes traffic to a specific application running on that machine.
- **The Service:** Inside apartment unit `30000` sits a service waiting for incoming connections. When supplied with the correct input string, it responds with the secret flag.

### 2. Why File Commands (`cat`, `grep`) Aren't Enough

Standard Linux commands like `cat` or `grep` operate on **static files saved on a storage drive**. They cannot communicate across a network socket or interact with a live process listening on a port. Tools like **Netcat (`nc`)** bridge this gap by writing and reading data across network ports.

## How to Solve

### Method 1: Interactive Netcat (`nc`) Session

Connect directly to port `30000`, paste the `bandit14` password when the blank prompt appears, and press **Enter**:

Bash

```
nc localhost 30000
```

**Terminal Session:**

Plaintext

```
aaWecNkG4FhxJQxz07uiwzVP6bJiYS65
Correct!
pbLYuZtTg4MgaqfJx8jbA9gKKGqM68A7
```

### Method 2: Pipelining File Output into Netcat (One-Liner)

Stream the contents of the local password file directly into the network socket using a pipe (`|`):

Bash

```
cat /etc/bandit_pass/bandit14 | nc localhost 30000
```

**Output:**

Plaintext

```
Correct!
pbLYuZtTg4MgaqfJx8jbA9gKKGqM68A7
```

## Alternative Solutions

Linux provides multiple methods for interacting with TCP sockets:

### 1. Using `telnet` (Legacy Network Client)

`telnet` opens a raw text-based TCP connection to a specified host and port:

Bash

```
telnet localhost 30000
```

*(Paste the `bandit14` password and hit Enter)*

### 2. Using Native Bash Pseudo-Devices (No External Tools)

The Bash shell can route streams directly into network sockets via virtual file paths under `/dev/tcp/`:

Bash

```
cat /etc/bandit_pass/bandit14 > /dev/tcp/localhost/30000
```

### 3. Programmatic Approach (Python Sockets)

Automated infrastructure and testing tools connect to network ports using socket libraries:

Python

```
import socket

# Create TCP socket and connect to port 30000
s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
s.connect(("localhost", 30000))

# Send the password followed by a newline byte
s.sendall(b"aaWecNkG4FhxJQxz07uiwzVP6bJiYS65\n")

# Receive and decode the response
response = s.recv(1024)
print(response.decode())

s.close()
```

## Why This Skill Matters in Enterprise IT & Cloud

1. **Microservice Health Checks:** When containerized services (e.g., Docker/Kubernetes) fail, engineers use `nc` or `telnet` inside the container to test whether internal ports are reachable.
2. **Firewall & Network Security Audits:** Verifying which ports are open on `localhost` ensures unnecessary services aren't exposed to unauthorized internal or external traffic.
3. **API & Service Diagnostics:** Interacting directly with raw TCP ports allows quick verification of back-end application behavior before deploying full software clients or API integrations.

## Quick Reference Summary

| **Tool / Concept** | **Function** |
| --- | --- |
| **`localhost` / `127.0.0.1`** | Loopback network interface pointing directly to the local machine. |
| **Port Numbers (`0–65535`)** | Endpoint addresses routing network traffic to specific running processes. |
| **`nc <host> <port>`** | Reads/writes raw data streams across TCP or UDP network sockets. |
| **`cat file | nc host port`** | Pipes standard text output straight into a live network socket connection. |