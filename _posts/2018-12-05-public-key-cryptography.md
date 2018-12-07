# public-key cryptography

a public-key cryptograpy, aka asymmetric cryptography, uses two related keys, a public key and a private key. The two keys have the property that determining the private key from the public key is computationally infeasible. The public key may be made public, but the private key must remain secret. The public key enables people to encrypt messages and verify signatures. The private key allows for decrypting messages and the generation of signatures. Asymmetric algorithms are poorly suited for encrypting large messages because they are relatively slow. These algorithms are used to perform three operations: digital signatures, key transport, and key agreement.

## digital signatures

a digital signature of a message is a number dependent on private key known only to the signer, and additionally, on the  content of the message being signed. In contrast to handwritten signatures, the digital signature also verifies the integrity of the data. The signature validation will fail:
- if a different private key was used to generate the signature
- if the data has been changed since the signature was applied 
<!--stackedit_data:
eyJoaXN0b3J5IjpbNTU1NTAxMTg0XX0=
-->