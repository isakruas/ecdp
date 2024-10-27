---
title: "ECDP: Elliptic Curve Data Protocol"
abbrev: "ECDP"
category: std

docname: draft-ruas-cfrg-ecdp-latest
submissiontype: IETF
number:
date:
consensus: true
v: 3
area: "IRTF"
workgroup: "Crypto Forum"
keyword:
 - peer-to-peer
 - encryption
 - cryptography
 - data protocol

venue:
  group: "Crypto Forum"
  type: "Research Group"
  mail: "cfrg@ietf.org"
  arch: "https://mailarchive.ietf.org/arch/browse/cfrg"
  github: "isakruas/ecdp"
  latest: "https://isakruas.github.io/ecdp/draft-ruas-cfrg-ecdp.html"

author:
  -
    fullname: Isak Ruas
    organization: Nrolabs
    email: isakruas@nrolabs.com
    country: BR

normative:
  RFC2119:
  RFC5639:
  RFC4492:
  RFC6979:

--- abstract

This document specifies the Elliptic Curve Data Protocol (ECDP), a peer-to-peer (P2P) communication protocol tailored for decentralized networks. ECDP utilizes elliptic curve cryptography (ECC) for encryption, employs the Elliptic Curve Digital Signature Algorithm (ECDSA) for message authentication, and leverages the Diffie-Hellman key exchange using elliptic curves for secure session initialization. The protocol supports a variety of elliptic curves (e.g., secp256k1, secp384r1) with varying key sizes, and is designed to operate over both TCP and UDP. Additionally, it utilizes SHA-256 or SHA-512 for cryptographic hash verification to ensure message integrity.

--- middle

# Introduction

P2P networks are widely used for data exchange but often face challenges related to security and data integrity. ECDP addresses these issues by implementing strong cryptographic measures and robust authentication mechanisms. ECDP is designed for scalability and security by enabling nodes to communicate indirectly through a network pool.

To enhance the security and integrity of the messages exchanged, the protocol employs elliptic curve cryptography (ECC) with various well-established curves, as well as the SHA-256 and SHA-512 algorithms for cryptographic hash functions.

The protocol dynamically supports different ECC key lengths, ensuring flexibility and forward compatibility with future developments in elliptic curve cryptography.

# Terminology

The key words **"MUST"**, **"MUST NOT"**, **"REQUIRED"**, **"SHALL"**, **"SHALL NOT"**, **"SHOULD"**, **"SHOULD NOT"**, **"RECOMMENDED"**, **"MAY"**, and **"OPTIONAL"** in this document are to be interpreted as described in {{RFC2119}}.

# Goals of the Protocol

- The protocol **MUST** facilitate secure communication between nodes through a pool network.
- The protocol **MUST** ensure data integrity and confidentiality of the messages transmitted.
- The protocol **MUST** authenticate messages using elliptic curve signature schemes, ensuring their origin and integrity.
- The protocol **MUST** be extensible and support future advances in cryptographic techniques.
- The protocol **SHOULD** be scalable, able to operate in decentralized large-scale P2P networks.
- The protocol **MUST** support multiple key sizes for flexible elliptic curve operations.

# Supported Algorithms

ECDP relies on a variety of standardized and widely used algorithms to ensure secure, authenticated, and efficient communication.

## 4.1 Cryptography

### 4.1.1 Supported Elliptic Curves

ECDP supports the following elliptic curves for encryption, Diffie-Hellman key exchange, and signing operations (ECDSA):

- **secp192k1**
- **secp192r1**
- **secp224k1**
- **secp224r1**
- **secp256k1**
- **secp256r1**
- **secp384r1**
- **secp521r1**

Each curve corresponds to a distinct key size. To accommodate different security levels, the protocol includes a parameter for indicating the key size used in message operations. Implementations **MUST** support **secp256k1**, **secp256r1**, and **secp384r1** as a minimum requirement. Support for other curves is **RECOMMENDED** for greater flexibility and stronger cryptography when necessary.

### 4.1.2 Supported Cryptographic Hash Algorithms

ECDP uses cryptographic hash functions for various integrity checks, including **checksums** and **digital signatures**.

Supported hash functions include:

- **SHA-256** (RECOMMENDED)
- **SHA-512** (OPTIONAL for longer data integrity verification)

Implementations **MUST** support **SHA-256** as a minimum requirement.

# Protocol Operation

## Message Types

- **0x01:** Handshake
- **0x02:** Data Transfer
- **0x03:** Acknowledgment (Ack)
- **0x04:** Data Request

# Message Structure

## 5.1 Base Message Structure

The base structure contains fields that are common across various message types, with extensions for specific needs. Messages are routed through a pool network to enhance scalability and security.

| Field                   | Description                                                              | Size (bytes) |
|-------------------------|--------------------------------------------------------------------------|--------------|
| Protocol Version        | Identifies the version of the ECDP protocol                              | 1            |
| Message Type            | Indicates the type of message                                            | 1            |
| Key Size                | Indicates the ECC key size used (e.g., 192, 224, 256, 384, 521 bits)      | 2            |
| Sender ECC Public Key   | Public key of the sending node                                           | Variable (based on key size) |
| Receiver ECC Public Key | Public key of the receiving node (if applicable)                         | Variable (based on key size) |
| Nonce                   | Random number to prevent replay attacks                                  | 8            |
| Encrypted Data          | Content of the message encrypted using ECC                               | Variable     |
| Signature               | Digital signature of the sender (ECDSA)                                  | Variable (based on key size)  |

In this structure:
- **Key Size** is a 2-byte field that indicates the key size in bits (e.g., 192, 224, 256, 384, 521). This allows both endpoints to use flexible key sizes as per their security requirements.

## 5.2 Handshake (0x01)

| Field                   | Description                                                              | Size (bytes) |
|-------------------------|--------------------------------------------------------------------------|--------------|
| Protocol Version        | Identifies the version of the ECDP protocol                              | 1            |
| Message Type            | Set to Handshake (0x01)                                                  | 1            |
| Key Size                | Indicates the ECC key size used                                          | 2            |
| Sender ECC Public Key   | Public key of the sending node                                           | Variable     |
| Receiver ECC Public Key | Public key of the receiving node                                         | Variable     |
| Nonce                   | Random number to prevent session replay                                  | 8            |
| Node Identifier         | Unique ID of the node (e.g., hash of the public key)                     | 32           |
| Node Capability         | Information about bandwidth and supported data types                     | 4            |
| Signature               | Digital signature of the sender (ECDSA)                                  | Variable     |

## 5.3 Data Transfer (0x02)

| Field                   | Description                                                              | Size (bytes) |
|-------------------------|--------------------------------------------------------------------------|--------------|
| Protocol Version        | Identifies the version of the ECDP protocol                              | 1            |
| Message Type            | Set to Data Transfer (0x02)                                              | 1            |
| Key Size                | Indicates the ECC key size used                                          | 2            |
| Sender ECC Public Key   | Public key of the sending node                                           | Variable     |
| Receiver ECC Public Key | Public key of the receiving node                                         | Variable     |
| Nonce                   | Exclusive random number for the session                                  | 8            |
| Encrypted Data          | Message content encrypted using ECC                                      | Variable     |
| Checksum                | Cryptographic hash (SHA-256 or SHA-512) for data integrity verification  | 32 or 64     |
| Signature               | Digital signature of the sender (ECDSA)                                  | Variable     |

## 5.4 Acknowledgment (Ack) (0x03)

| Field                   | Description                                                              | Size (bytes) |
|-------------------------|--------------------------------------------------------------------------|--------------|
| Protocol Version        | Identifies the version of the ECDP protocol                              | 1            |
| Message Type            | Set to Acknowledgment (0x03)                                             | 1            |
| Nonce                   | Random number corresponding to the session                               | 8            |
| Message Hash            | Hash of the received data message (SHA-256 or SHA-512)                   | 32 or 64     |
| Status                  | Indicates Success (0x00) or Error (0x01)                                 | 1            |

## 5.5 Data Request (0x04)

| Field                   | Description                                                              | Size (bytes) |
|-------------------------|--------------------------------------------------------------------------|--------------|
| Protocol Version        | Identifies the version of the ECDP protocol                              | 1            |
| Message Type            | Set to Data Request (0x04)                                               | 1            |
| Key Size                | Indicates the ECC key size used                                          | 2            |
| Sender ECC Public Key   | Public key of the requesting node                                        | Variable     |
| Receiver ECC Public Key | Public key of the data-holding node                                      | Variable     |
| Nonce                   | Random number for the session                                            | 8            |
| Data Identifier         | Unique hash or ID of the requested data (using SHA-256, SHA-512)         | 32 or 64     |
| Signature               | Digital signature of the sender (ECDSA)                                  | Variable     |

# Security Considerations

ECDP employs ECC for secure communication between nodes, with messages authenticated through ECDSA. The inclusion of a **Key Size** field allows nodes to negotiate and use varying key lengths based on the curve chosen, ensuring flexibility in security levels.

Nonces are used to prevent replay attacks. The protocol uses the Diffie-Hellman key exchange over elliptic curves for secure session initialization. The following elliptic curves are supported, with corresponding key sizes:

- secp192k1 (192-bit)
- secp192r1 (192-bit)
- secp224k1 (224-bit)
- secp224r1 (224-bit)
- secp256k1 (256-bit) (**RECOMMENDED**)
- secp256r1 (256-bit) (**RECOMMENDED**)
- secp384r1 (384-bit) (**RECOMMENDED**)
- secp521r1 (521-bit)

## Additional Recommendations

- Implementations **SHOULD** regularly update their cryptographic libraries to maintain security against evolving threats.
- Nodes **MUST** verify the signatures of incoming messages to ensure authenticity.
- Nonce sizes **MUST** be sufficient (8 bytes at minimum) to prevent replay attacks.
- It is **RECOMMENDED** that nodes securely store their private keys using encryption and protected hardware when possible to prevent unauthorized access.

# IANA Considerations

This document has no actions directed at IANA at this time. However, if future protocol updates require additional message types, code points, or other attributes, a registration with IANA **MAY** be required.

--- back

# Acknowledgments
{:numbered="false"}

The authors acknowledge the valuable contributions of cryptographic and P2P networking experts. Special thanks to those who provided feedback on elliptic curve performance and secure key storage recommendations.
