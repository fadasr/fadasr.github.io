# public-key cryptography

a public-key cryptograpy, aka asymmetric cryptography, uses two related keys, a public key and a private key. The two keys have the property that determining the private key from the public key is computationally infeasible. The public key may be made public, but the private key known only to the owner must remain secret. The public key enables people to encrypt messages and verify signatures. The private key allows for decrypting messages and the generation of signatures. Asymmetric algorithms are poorly suited for encrypting large messages because they are relatively slow. These algorithms are used to perform three operations: digital signatures, key transport, and key agreement.

## digital signatures
cryptographic goals provided by digital signatures are:
 - [x] authentication
 - [x] integrity
 - [x] non-repudiation

digital signatures are the public-key equivalent of MACs. Alice can generate a digital signature for a message using a message digest and her private key. To authenticate Alice as the sender, Bob generates the message digest as well and uses Alice's public key to validate the message. The generic setting is shown below. In contrast to handwritten signatures, the digital signature also verifies the integrity of the data. The signature validation will fail if:
- a different private key was used to generate the signature
- the data has been changed since the signature was applied

![](https://fadasr.github.io/images/dig-sig.png)

digital signatures are also used for authentication to systems or applications. A system can authenticate Alice's identity through a challenge-response protocol. The system generates a random challenge and Alice signs it. If the signature is verified with Alice's public key, it must have been signed by Alice. This type of authentication is useful for remote access to information on a server, or for gaining security access to a restricted feature.

![](https://fadasr.github.io/images/dig-sig-auth.png)

with a trusted third party (e.g. CA in PKI), the digital signature can be used to establish non-repudiation. If there is ever a dispute, Bob can take message, digital signature, and digital certificate to a judge and prove that Alice signed the message. The digital certificate contains the owner's public key and possibly other information, and is digitally signed by a Certification Authority (CA), a trusted third party, thereby binding the public key to the owner. A digital signature binds a message to a public key owner and digital certificate binds a public key owner with identity (person, organisation, or web site).

![](https://fadasr.github.io/images/dig-sig-cert.png)

most asymmetric algorithms like RSA and ECC provide secure digital signature schemes like DSA, ECDSA, and [EdDSA](https://tools.ietf.org/html/rfc8032). We shall discuss the digital signatures in greater detail later.

## key transport

in practice asymmetric algorithms are never used to encrypt large amounts of data, because they are much slower than symmetric key algorithm. Additionally, some of them (like ECC) do not provide directly encryption primitives, so more complex schemes should be used. However they are perfectly suited to encrypting small amounts of data - such as symmetric key. The following example might described an electronic mail message from Alice to Bob:

![](https://fadasr.github.io/images/key-trans.png) 

examples of such asymmetric encryption schemes are: [RSA-OAEP](https://tools.ietf.org/html/rfc8017), [RSA-KEM](https://tools.ietf.org/html/rfc5990), and ECIES-KEM.

## key agreement

Diffie-Hellman (DH) is a key agreement algorithm used by two parties to agree on a shared secret. The secret may then be converted into cryptographic key material for other symmetric algorithms. DH is resistant to sniffing attacks, but it is vulnerable to man-in-the-middle (MITM) attacks. Elliptic Curve Diffie-Hellman (ECDH) uses ECC point multiplication instead of modular exponentiation.

The authenticated key agreement schemes authenticate the identities of parties involved in the key exchange and thus prevent MITM attacks by use of digitally signed keys (e.g. PKI certificate), or password-authenticated key agreement.
<!--stackedit_data:
eyJoaXN0b3J5IjpbMTczNjgxNDA2MywtNzMyMTI3MTUxLC0xND
I3NDQ3NzEwLC03NTEyNzgxNjEsLTUyMTI5NTEwOCwtMTQ5MDc2
MTg4MiwtNDMxNzEwNzc5LDEzNDg4NDUxNDIsLTE1NDkyNjk3MD
UsMTczMzAyNzk0NCwxNTczNTMyMTI0LDUxNTc2MzM3Miw5MTM1
ODA5MjgsODM3MDA1NjkwLDE0MjI3MTgyMTksLTgzMTgwMTEwMS
wtMTI2OTI5NDYxNSw3NzM1NTcxNzcsLTMwMzA1MTYyNSwtOTEw
Mjc1MDJdfQ==
-->