# TLS-OPAQUE Secure Authentication

Password-Authenticated TLS Handshake using a Simplified OPAQUE Construction

---

# Overview

This project demonstrates how a **Password-Authenticated Key Exchange (PAKE)** protocol can be integrated into a **TLS-protected communication channel**.

The implementation enhances the TLS 1.3 handshake by introducing a simplified **OPAQUE-style authentication mechanism**. Instead of transmitting passwords, both client and server derive a shared secret using a password-based **Oblivious Pseudo-Random Function (OPRF)**.

The resulting secret is expanded using **HKDF-SHA256** and used to protect application messages via **AES-GCM authenticated encryption**.

The system illustrates how password authentication can be securely layered on top of TLS without exposing passwords to the network.

---

# Architecture

The system is composed of the following components:

Client
• Establishes a TLS connection to the server
• Executes the PAKE login protocol
• Derives symmetric keys from the shared secret
• Encrypts application messages with AES-GCM

Server
• Accepts TLS connections from clients
• Performs PAKE verification using the password
• Reconstructs the shared secret
• Decrypts and processes client messages

Supporting modules

**opaque_set.py**
Implements a simplified OPRF using HMAC to simulate the OPAQUE protocol.

**kdf_utils.py**
Uses HKDF-SHA256 to derive handshake and application keys.

**cert_utils.py**
Generates and manages self-signed TLS certificates.

---

# Protocol Flow

The authentication and message exchange process follows these steps:

1. Client opens a TCP connection to the server.
2. TLS 1.3 handshake is performed.
3. Server presents its certificate.
4. Client verifies the certificate.
5. Client performs PAKE login using its password.
6. Server reproduces the shared secret using the same password.
7. Both sides derive encryption keys via HKDF.
8. Client encrypts the application message with AES-GCM.
9. Server decrypts the message and processes it.

---

# Security Properties

Confidentiality
TLS 1.3 encrypts all communication between client and server.

Integrity
TLS record authentication and AES-GCM tags ensure messages cannot be modified.

Authentication
Server identity is verified through certificate pinning.
Client authentication is achieved through password-based PAKE.

Forward Secrecy
TLS 1.3 ephemeral Diffie-Hellman ensures session keys cannot be recovered even if long-term secrets are compromised.

---

# Repository Structure

```
tls-opaque-authentication
│
├── client.py
├── server.py
│
├── opaque_set.py
├── kdf_utils.py
├── cert_utils.py
│
├── images
│   ├── architecture.png
│   └── protocol_flow.png
│
└── README.md
```

---

# Installation

### Requirements

Python 3.10+

Install dependencies

```
pip install cryptography
```

---

# Running the Demo

Start the server

```
python server.py
```

Expected output

```
Listening on 127.0.0.1:9000 (TLS enabled)
```

Run the client

```
python client.py
```

Enter the password

```
Crypto25
```

The client performs the PAKE handshake and sends an encrypted message.

The server will display

```
Received message: Hello secure world
```

confirming successful authentication and secure communication.

---

# Key Implementation Components

## OPAQUE Module

The PAKE exchange uses a simplified OPRF implemented with HMAC:

```
shared_secret = HMAC(server_key, password)
```

This demonstrates the concept of OPAQUE without implementing full blinding and zero-knowledge proofs.

---

## Key Derivation

The shared secret is expanded using HKDF-SHA256:

```
HKDF(shared_secret) → 64 bytes
```

The output is split into two keys:

• handshake_key
• application_key

This follows the **key separation principle**.

---

## Authenticated Encryption

Application messages are encrypted with AES-GCM:

```
ciphertext = AESGCM(key).encrypt(nonce, message)
```

AES-GCM provides both confidentiality and integrity.

---

# Threat Model

The design considers attackers capable of:

Passive network monitoring
Active man-in-the-middle attacks
Message tampering and replay attempts

Security is ensured through:

• TLS encrypted transport
• PAKE-based password authentication
• AES-GCM authenticated encryption
• certificate verification

---

# Future Improvements

• Integrate a full OPAQUE protocol implementation
• Add multi-user password registration
• Support session resumption
• Formal verification using tools such as Tamarin or ProVerif
• Extend to mutual TLS authentication

---

# Author

Shristi
MS Computer Science
Arizona State University

---

TLS-OPAQUE Secure Authentication Prototype
