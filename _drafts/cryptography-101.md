---

layout: post
title: Cryptography 101 - Part 1
tags: [Security 101]

---

## Cryptography
Cryptography is the process of converting plain text into unintelligible text and vice-versa. It is a method of storing and transmitting data in a particular form so that unintended users cannot read the information.

## Cryptanalysis
The art and science of breaking the cipher text is known as cryptanalysis.

## Security Services of Cryptography
1. **Confidentiality:** It is a security service that keeps information secure from an unauthorized user. It is also reffered to as privacy.
2. **Data Integrity:** This detects any unauthorized modification to the data. This is possible when data is being transmitted from sender to receiver over the internet. This service confirms that the data is intact.
3. **Authentication:** Authentication provides the identification of the originator. It confirms to the receiver that the data received is sent by the expected sender.
4. **Non-repudiation:** This service ensures that an entity cannot refuse the ownership of a previously committed information. It is an assurance that the original creator of the data cannot deny the creation or transmission of the data.

## Components of a Cryptosystem
A cryptosystem is an implementation of a cryptographic technique and their accompanying infrastructure to provide information security services. Various components of a basic cryptosystem are as follows:
1. **Plaintext:** It is the data to be protected during transmission.
2. **Encryption Algorithm:** It is a mathematical process that produces a ciphertext for any given plaintext using encryption key.
3. **Ciphertext:** It is a scrambled version of plaintext produced by the encryption algorithm using a specific encryption key.
4. **Decryption Algorithm:** Mathematical process that converts ciphertext back to plaintext using a decryption key.
5. **Encryption Key:** This key is known to the sender and used with encryption algorithm for converting plaintext to ciphertext.
6. **Decryption Key:** This key is known to the receiver and used with decryption algorithm for converting ciphertext back to plaintext.

---

## Types of Cryptosystems
There are two main types of cryptosystems based on the manner in which encryption-decryption is performed

### Symmetric Key Encryption

The encryption mechanism where same keys are used for encryption as well as decryption of the information is known as Symmetric Key Encryption.

![11](/assets/img/posts/Security101/crypto101-1.png)

Prior to 1970, all the cryptosystems employed symmetric key encryption. Even today it's used in devices that have hardware constraints as this approach is much simpler than Asymmetric Key Encryption.

The salient features of cryptosystem based on symmetric key encryption are:
1. Sender and Receiver both use a common key for encryption and decryption.
2. In a group of n people, to enable two-party communication between the users, the number of keys required for group is *n (n - 1)/2*.
3. Length of the keys in this type of encryption is smaller and hence it is faster than asymmetric encryption.
4. Processing power required for symmetric encryption is less than that of asymmetric encryption.

### Asymmetric Key Encryption

The encryption scheme in which different keys are used for encryption and decryption of the information. Eventhough the keys are different, they are mathematically related and hence this method works. Some of the salient features of asymmetric encryption are as follows:
1. Each user in this system has two keys, a private key and a public key. While public key is shared with with all the other users, the private key is only known to the respective user. When one key from the pair is used for encryption, then the other key is used for decryption.
2. This scheme is also known as Public Key Encryption as it also involves a public key.
3. Length of keys used in this encryption scheme is large and hence the process is slower than symmetric encryption.
4. Processing power requirement is higher for asymmetric encryption.

![21](/assets/img/posts/Security101/crypto101-2.png)

## Attacks on Cryptosystems
Attacks are generally categorized into passive and active attacks. The main goal of a passive attack is to intercept and eavesdrop on the communication between sender and receiver. This type of attack does not actively modify or change any information that is being transmitted but instead is just used for collecting intel. On the other hand, active attacks involves manipulating the information that is being transmitted in some way.

### Cryptographic Attacks
Based on the methodology used, attacks on the cryptosystems are categorized as:
1. **Ciphertext Only Attacks:** In this method, the attacker only has access to a set of ciphertexts. The attack is considered successful when the adversary can obtain the plaintext corresponding to the ciphertext and maybe the encryption key as well.
2. **Known Plaintext Attack:** In this method, the adversary knows some part of plaintext for some of the ciphertext. The attack is considered successful if rest of the ciphertext can be decrypted without the secret key.
3. **Chosen Plaintext Attack:** In this attack, the adversary gets the ciphertext corresponding to the plain text of her choice. This simplifies her task of identifying the encryption key.
4. **Dictionary Attack:** This attack is a simple attack where the attacker builds a dictionary of mapping between plaintext and corresponding ciphertext and use that for decrypting the ciphertext.
5. **Brute Force Attack:** In this attack the adversary tries all the possible combination of keys. For instance if the key is 8 bit then all the possible keys will be 256.
6. **Man in Middle Attack:** The targets of this attack are mostly public key cryptosystems where key exchange is involved before communication takes place. The attacker in the middle intercepts all the communication between sender and receiver.
7. **Side Channel Attack:** This type of attack exploits weakness in physical implementation of cryptosystem and leaks secret of the encryption algorithm. For instance cache side channel attack.
8. **Power Analysis Attacks:** These attacks measure the amount of power consumption is used to obtain information about the nature of the underlying computations.

---

## Traditional Ciphers
In this section we will talk about some simple traditional ciphers. While being simple, they are intuitive as well.

### Caesar Cipher
It is a mono-alphabetic cipher wherein each letter of the plaintext is substituted by another letter to form the ciphertext. This cryptosystem is generally reffered to as the Shift Cipher. The main idea is to replace each alphabet with another alphabet which is shifted by some fixed number for encryption. And for decryption, the cipher text is shifted back by the same amount to obtain the plain text. For instance a mapping as mentioned below can be used for encryption.

| Plaintext Alphabet | Ciphertext Alphabet |
|:------------------:|:-------------------:|
|          a         |          D          |
|          b         |          E          |
|          c         |          F          |
|          d         |          G          |
|          e         |          H          |
|          f         |          I          |
|          g         |          J          |
|          h         |          K          |
|          i         |          L          |
|          j         |          M          |
|          k         |          N          |
|          l         |          O          |
|          m         |          P          |
|          n         |          Q          |
|          o         |          R          |
|          p         |          S          |
|          q         |          T          |
|          r         |          U          |
|          s         |          V          |
|          t         |          W          |
|          u         |          X          |
|          v         |          Y          |
|          w         |          Z          |
|          x         |          A          |
|          y         |          B          |
|          z         |          C          |

Ceasar Cipher is not a secure cryptosystem because there are only 26 possible keys to try out.

### Simple Substitution Cipher
Here unlike Ceaser Cipher, this scheme uses some permutation of the letters in alphabet. With 26 letters in alphabet, the possible permutation are 26!.

| Plaintext Alphabet | Ciphertext Alphabet |
|:------------------:|:-------------------:|
|          a         |          F          |
|          b         |          T          |
|          c         |          B          |
|          d         |          W          |
|          e         |          E          |
|          f         |          V          |
|          g         |          N          |
|          h         |          A          |
|          i         |          O          |
|          j         |          Y          |
|          k         |          J          |
|          l         |          Z          |
|          m         |          R          |
|          n         |          D          |
|          o         |          P          |
|          p         |          S          |
|          q         |          G          |
|          r         |          K          |
|          s         |          X          |
|          t         |          C          |
|          u         |          Q          |
|          v         |          I          |
|          w         |          U          |
|          x         |          M          |
|          y         |          H          |
|          z         |          L          |

Simple substituion cipher is a considerable improvement over Caesar Cipher and even the modern computing systems are not yet powerful enough to comfortably brute force.

### One Time Pad
This is an encryption technique where a one-time pre-shared key is used which is of the same length as that of the plain text. The algorithm combines each character of the plaintext with each character of the key to obtain the cipher text. If the chosen shared secret key is truely random then the obtained cipher text is as well. The operation used for obtaining cipher text can be something like modular addition.

This encryption cannot be cracked but relies on only one-time pre-shared key which might not be reliable in realistic scenarios or recurring communication. As the size of the plaintext grows, it becomes computationally impossible to break this scheme by brute force.

---

## Conclusion
Since this is the first post on Security 101, it is a simple post. Once we have covered some basics in the coming weeks, we can move forward and dive into some more complicated topics. In the next week we will talk about modern ciphers and go into the methodology of each of those ciphers.

---
