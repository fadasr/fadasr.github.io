# public-key cryptography

a public-key cryptograpy, aka asymmetric cryptography, uses two related keys, a public key and a private key. The two keys have the property that determining the private key from the public key is computationally infeasible. The public key may be made public, but the private key known only to the owner must remain secret. The public key enables people to encrypt messages and verify signatures. The private key allows for decrypting messages and the generation of signatures. Asymmetric algorithms are poorly suited for encrypting large messages because they are relatively slow. These algorithms are used to perform three operations: digital signatures, key transport, and key agreement.

## digital signatures

digital signatures are the public-key equivalent of MACs. Alice can generate a digital signature for a message using a message digest and her private key. To authenticate Alice as the sender, Bob generates the message digest as well and uses Alice's public key to validate the message. In contrast to handwritten signatures, the digital signature also verifies the integrity of the data. The signature validation will fail if:
- a different private key was used to generate the signature
- the data has been changed since the signature was applied

digital signatures are also used for authentication to systems or applications. A system can authenticate Alice's identity through a challenge-response protocol. The system generates a random challenge and Alice signs it. If the signature is verified with Alice's public key, it must have been signed by Alice. This type of authentication is useful for remote access to information on a server, or for gaining security access to a restricted feature.

![](https://fadasr.github.io/images/dig-sig-auth.png)

<table>
	<tr>
		<th></
	</tr>
</table>
<!--stackedit_data:
eyJoaXN0b3J5IjpbMTYxNjUwNTEwMywxMTAzNDU0ODYyLDIwNj
M1NzQ4NDYsLTU1MzUxNzI0NywxMDk2Mjk2Nzg2LC05NTU3MzU2
NDAsMjEyNDY0MDczLC0xODg3ODgyMDA2LDU1NTUwMTE4NF19
-->