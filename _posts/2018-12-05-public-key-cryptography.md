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

with a trusted third party (e.g. CA in PKI), the digital signature can be used to establish non-repudiation. If there is ever a dispute, Bob can take message, digital signature, and digital certificate to a judge and prove that Alice signed the message. The digital certificate contains the owner's public key and possibly other information, and is digitally singed by a Certification Authority (CA), thereby binding the public key to the owner. A digital signature binds a message to a public key owner and digital certificate binds a public key owner with identity (person, organisation, or web site).

![](https://fadasr.github.io/images/dig-sig-cert.png)

most public-key cryptosystems like RSA and ECC provide secure digital signature schemes like DSA, ECDSA, and EdDSA. We shall discuss the digital signatures in greater detail later.

## key transport


<!--stackedit_data:
eyJoaXN0b3J5IjpbLTEwNTI5NDczNzMsODM3MDA1NjkwLDE0Mj
I3MTgyMTksLTgzMTgwMTEwMSwtMTI2OTI5NDYxNSw3NzM1NTcx
NzcsLTMwMzA1MTYyNSwtOTEwMjc1MDIsLTcwMTQ0NjI0OSwxMD
c5MDEyODcwLDExMDM0NTQ4NjIsMjA2MzU3NDg0NiwtNTUzNTE3
MjQ3LDEwOTYyOTY3ODYsLTk1NTczNTY0MCwyMTI0NjQwNzMsLT
E4ODc4ODIwMDYsNTU1NTAxMTg0XX0=
-->