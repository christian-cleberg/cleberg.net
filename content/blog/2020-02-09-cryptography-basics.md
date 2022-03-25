+++
date = 2020-02-09
title = "Cryptography Basics"
description = "Learn more about cryptography, a process that can change data from a readable format into an unreadable format."
+++

## Similar Article Available

If you haven't already, feel free to read my post on [AES Encryption](/blog/aes-encryption/).

## What is Cryptography?

In layman's terms, cryptography is a process that can change data from a readable format into an unreadable format (and vice-versa) through a series of processes and secrets. More technically, this is the Internet Security Glossary's definition:

> [Cryptography is] the mathematical science that deals with transforming data to render its meaning unintelligible (i.e., to hide its semantic content), prevent its undetected alteration, or prevent its unauthorized use. If the transformation is reversible, cryptography also deals with restoring encrypted data to intelligible form.
>
> \- [Internet Security Glossary (2000)](https://tools.ietf.org/html/rfc2828)

Cryptography cannot offer protection against the loss of data, it simply offers encryption methods to protect data at-rest and data in-traffic. At a high-level, encrypted is when plaintext data is encrypted to ciphertext (a secure form of text that cannot be understood unless decrypted back to plaintext). The encryption process is completed through the use of a mathematical function that utilizes one or more values called keys to encrypt or decrypt the data.

## Key Elements of Cryptographic Systems

To create or evaluate a cryptographic system, you need to know the essential pieces to the system:

- **Encryption Algorithm (Primitive):** A mathematical process that encrypts and decrypts data.
- **Encryption Key:** A string of bits used within the encryption algorithm as the secret that allows successful encryption or decryption of data.
- **Key Length (Size):** The maximum number of bits within the encryption key. It's important to remember that key size is regulated in many countries.
- **Message Digest:** A smaller, fixed-size bit string version of the original message. This is practically infeasible to reverse, which is why it's commonly used to verify integrity.

## Symmetric Systems (Secret Key Cryptography)

Symmetric cryptography utilizes a secret, bidirectional key to perform both encryption and decryption of the data. The most common implementation of symmetric cryptography is the [Advanced Encryption Standard](/blog/aes-encryption/), which uses keys that are 128 bits to 256 bits in size. This standard came after the National Institute of Standards and Technology (NIST) decided to retire the Data Encryption Standard (DES) in 2001.

Since brute force attacks strongly correlate with key length, the 56-bit key length of DES was considered insecure after it was publicly broken in under 24 hours. However, there is a modern implementation of DES called Triple DES where the DES method is applied three times to each data block.

The main advantages to symmetric systems are the ease of use, since only one key is required for both encryption and decryption, and the simplicity of the algorithms. This helps with bulk data encryption that may unnecessarily waste time and power using asymmetric systems.

However, symmetric systems have disadvantages to keep in mind. Since the key is private, it can be difficult to safely distribute keys to communication partners. Additionally, the key cannot be used to sign messages since it's necessary to keep the key private.

## Asymmetric Systems (Public Key Cryptography)

Asymmetric cryptography utilizes two keys within the system: a secret key that is privately-held and a public key that can be distributed freely. The interesting aspect of asymmetric cryptography is that either key can be used to encrypt the data, there's no rule that dictates which key must be used for encryption. Once one key is used to encrypt the data, only the other key can be used to decrypt the data. This means that if the private key encrypts the data, only the public key can decrypt the data.

An advantage of this system is that if you successfully decrypt data using one of the keys, you can be sure of the sender since only the other key could have encrypted the data.

One of the major implementations of an asymmetric system is a digital signature. A digital signature can be generated using the sender's private key, or a one-way hash function and is used to provide assurance for integrity and authenticity of the message. A couple common message digest algorithms are SHA-256 and SHA-512, which securely compress data and produce a 128-bit message digest.

It should be noted that man-in-the-middle attacks are one of the risks with digital signatures and public keys. To combat this, applications often use a public key infrastructure (PKI) to independently authenticate the validity of signatures and keys.

Due to the large key size and [inefficient mathematical functions](https://crypto.stackexchange.com/a/591) of asymmetric encryption, elliptical curve cryptography (ECC) is often used to increase security while using fewer resources.

## Applications of Cryptographic Systems

There are quite a few implementations of cryptographic systems around the world. Here are a few popular examples:

**Transport Layer Security (TLS):** One of the most famous cryptographic solutions created is TLS, a session-layered or connection-layered internet protocol that allows for secure communications between browsers and servers. Using handshakes, peer negotiation, and authentication allows TLS to prevent eavesdropping and malicious transformation of data. The major reason for TLS popularity is that a major vulnerability was found in the SSL protocol in 2014. Instead of SSL, TLS can be used with HTTP to form HTTPS and is the preferred method for modern web development due to its increased security.

**Secure Hypertext Transfer Protocol (HTTPS):** An application layer protocol that allows for secure transport of data between servers and web clients. One of the unique parts of HTTPS is that it uses a secured port number instead of the default web port address.

**Virtual Private Network (VPN):** VPNs are made to securely extend a private network across public networks by utilizing an encrypted layered tunneling protocol paired with an authentication method, such as usernames and passwords. This technology originally allowed remote employees to access their company's data but have evolved into one of the top choices for anyone who wishes to mask their sensitive personal data.

**Internet Protocol Security (IPSec):** This protocol suite facilitates communication between two or more hosts or subnets by authenticating and encrypting the data packets. IPSec is used in a lot of VPNs to establish the VPN connection through the transport and tunnel mode encryption methods. IPSec encrypts just the data portion of packets in the transport methods, but it encrypts both the data and headers in the tunnel method (introducing an additional header for authentication).

**Secure Shell (SSH):** SSH is another network protocol used to protect network services by authenticating users through a secure channel. This protocol is often used for command-line (shell) functions such as remote shell commands, logins, and file transfers.

**Kerberos:** Developed by MIT, Kerberos is a computer-network authentication protocol that works on the basis of tickets to allow nodes communicating over a non-secure network to prove their identity to one another securely. This is most commonly used in business environments when used as the authentication and encryption method for Windows Active Directory (AD).

## Cybersecurity Controls

If you're someone who needs solutions on how to control risks associated with utilizing a crytograhpic system, start with a few basic controls:

- **Policies:** A policy on the use of cryptographic controls for protection of information is implemented and is in accordance with organizational objectives.
- **Key management:** A policy on the use, protection and lifetime of cryptographic keys is implemented through the entire application lifecycle.
- **Key size:** The organization has researched the optimal key size for their purposes, considering national laws, required processing power, and longevity of the solution.
- **Algorithm selection:** Implemented algorithms are sufficiently appropriate for the business of the organization, robust, and align with recommended guidelines.
- **Protocol configuration:** Protocols have been reviewed and configured suitable to the purpose of the business.
