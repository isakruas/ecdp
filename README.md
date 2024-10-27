# ECDP: Elliptic Curve Data Protocol

## Abstract

This document specifies the Elliptic Curve Data Protocol (ECDP), a peer-to-peer (P2P) communication protocol tailored for decentralized networks. ECDP utilizes elliptic curve cryptography (ECC) for encryption, employs the Elliptic Curve Digital Signature Algorithm (ECDSA) for message authentication, and leverages the Diffie-Hellman key exchange using elliptic curves for secure session initialization. The protocol is designed to operate over both TCP and UDP.

## 1. Introduction

P2P networks are widely used for data exchange but often face challenges related to security and data integrity. ECDP addresses these issues by implementing strong cryptographic measures and robust authentication mechanisms. ECDP is designed for scalability and security by enabling nodes to communicate indirectly through a network pool.

## 2. Terminology

The key words **"MUST"**, **"MUST NOT"**, **"REQUIRED"**, **"SHALL"**, **"SHALL NOT"**, **"SHOULD"**, **"SHOULD NOT"**, **"RECOMMENDED"**, **"MAY"**, and **"OPTIONAL"** in this document are to be interpreted as described in [RFC 2119](https://tools.ietf.org/html/rfc2119).

## 3. Goals of the Protocol

- The protocol **MUST** facilitate secure communication between nodes through a pool network.
- The protocol **MUST** ensure data integrity and confidentiality of the messages transmitted.
- The protocol **MUST** authenticate messages, ensuring their origin and integrity.

## 4. Protocol Operation

### 4.1 Message Types

- **0x01:** Handshake
- **0x02:** Data Transfer
- **0x03:** Acknowledgment (Ack)
- **0x04:** Data Request

## 5. Message Structure

### 5.1 Base Message Structure

The base structure contains fields that are common across various message types, with extensions for specific needs. Messages are routed through a pool network to enhance scalability and security.

```markdown
| Field                   | Description                                                              | Size (bytes) |
|-------------------------|--------------------------------------------------------------------------|--------------|
| Protocol Version        | Identifies the version of the ECDP protocol                              | 1            |
| Message Type            | Indicates the type of message                                            | 1            |
| Sender ECC Public Key   | Public key of the sending node                                           | 64 (2x32)    |
| Receiver ECC Public Key | Public key of the receiving node (if applicable)                         | 64 (2x32)    |
| Nonce                   | Random number to prevent replay attacks                                  | 8            |
| Encrypted Data          | Content of the message encrypted using ECC                               | Variable     |
| Signature               | Digital signature of the sender (ECDSA)                                  | 64           |
```

### 5.2 Handshake (0x01)

```markdown
| Field                   | Description                                                              | Size (bytes) |
|-------------------------|--------------------------------------------------------------------------|--------------|
| Protocol Version        | Identifies the version of the ECDP protocol                              | 1            |
| Message Type            | Set to Handshake (0x01)                                                  | 1            |
| Sender ECC Public Key   | Public key of the sending node                                           | 64 (2x32)    |
| Receiver ECC Public Key | Public key of the receiving node                                         | 64 (2x32)    |
| Nonce                   | Random number to prevent session replay                                  | 8            |
| Node Identifier         | Unique ID of the node (e.g., hash of the public key)                     | 32           |
| Node Capability         | Information about bandwidth and supported data types                     | 4            |
| Signature               | Digital signature of the sender (ECDSA)                                  | 64           |
```

### 5.3 Data Transfer (0x02)

```markdown
| Field                   | Description                                                              | Size (bytes) |
|-------------------------|--------------------------------------------------------------------------|--------------|
| Protocol Version        | Identifies the version of the ECDP protocol                              | 1            |
| Message Type            | Set to Data Transfer (0x02)                                              | 1            |
| Sender ECC Public Key   | Public key of the sending node                                           | 64 (2x32)    |
| Receiver ECC Public Key | Public key of the receiving node                                         | 64 (2x32)    |
| Nonce                   | Exclusive random number for the session                                  | 8            |
| Encrypted Data          | Message content encrypted using ECC                                      | Variable     |
| Checksum                | Cryptographic hash for data integrity verification                       | 32           |
| Signature               | Digital signature of the sender (ECDSA)                                  | 64           |
```

### 5.4 Acknowledgment (Ack) (0x03)

```markdown
| Field                   | Description                                                              | Size (bytes) |
|-------------------------|--------------------------------------------------------------------------|--------------|
| Protocol Version        | Identifies the version of the ECDP protocol                              | 1            |
| Message Type            | Set to Acknowledgment (0x03)                                             | 1            |
| Nonce                   | Random number corresponding to the session                               | 8            |
| Message Hash            | Hash of the received data message                                        | 32           |
| Status                  | Indicates Success (0x00) or Error (0x01)                                 | 1            |
```

### 5.5 Data Request (0x04)

```markdown
| Field                   | Description                                                              | Size (bytes) |
|-------------------------|--------------------------------------------------------------------------|--------------|
| Protocol Version        | Identifies the version of the ECDP protocol                              | 1            |
| Message Type            | Set to Data Request (0x04)                                               | 1            |
| Sender ECC Public Key   | Public key of the requesting node                                        | 64 (2x32)    |
| Receiver ECC Public Key | Public key of the data-holding node                                      | 64 (2x32)    |
| Nonce                   | Random number for the session                                            | 8            |
| Data Identifier         | Unique hash or ID of the requested data                                  | 32           |
| Signature               | Digital signature of the sender (ECDSA)                                  | 64           |
```

## 6. Security Considerations

ECDP employs ECC for secure communication between nodes, with messages authenticated through ECDSA. Nonces are used to prevent replay attacks. The protocol uses the Diffie-Hellman key exchange over elliptic curves for secure session initialization.

### Additional Recommendations

- Implementations **SHOULD** regularly update their cryptographic libraries to maintain security.
- Nodes **MUST** verify the signatures of incoming messages to ensure authenticity.
- It is **RECOMMENDED** that nodes securely store their private keys to prevent unauthorized access.

## 7. IANA Considerations

This document does not require any actions from IANA.

## 8. References

- [RFC 2119](https://tools.ietf.org/html/rfc2119) - Key words for use in RFCs to Indicate Requirement Levels
- [Elliptic Curve Digital Signature Algorithm (ECDSA)](https://en.wikipedia.org/wiki/Elliptic_Curve_Digital_Signature_Algorithm) - Details on ECDSA
- [Elliptic Curve Cryptography](https://en.wikipedia.org/wiki/Elliptic-curve_cryptography) - Introduction to elliptic curve cryptography
- [Diffie-Hellman Key Exchange](https://en.wikipedia.org/wiki/Diffie%E2%80%93Hellman_key_exchange) - Description of the Diffie-Hellman key exchange

## 9. Acknowledgments

The authors acknowledge the valuable contributions of cryptographic and P2P networking experts.

### Authors' Addresses

[Isak Ruas]  
[Nrolabs]
[isakruas@nrolabs.com]  
[Federative Republic of Brazil]