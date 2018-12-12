# RSA cryptography

the [RSA](https://tools.ietf.org/html/rfc8017) system is one of the most widely used public-key cryptosystem in the world. The RSA algorithm provides:
- key-pair generation: random keys typically of size 1024-4096 bits
- encryption: encrypt a message in the range [0-key_length] using the public key and decrypt using the private key
- digital signature: sign a message with the private key and verify using the public key

the key length of 3072 bits and above are considered secure. Longer keys provide higher security but consume more computing time.

## key generation

the foundation of RSA's security is based on the difficulty of factoring large numbers (aka the trapdoor). Given `n`, it is considered a hard problem to determine it's prime factors, `p` and `q`.
```
n = pq
```
where `n` is a modulus, `p` and `q` are two large prime number

the pair `(n, e)` forms the public key, while the pair `(n, d)` forms the private key. The number `e` is called _public key exponent_ and typically is equal to 3, 5, 17, or 65537. The number `d` is called _private key exponent_. The requirement for `e` and `d` is that:
```
ed = 1 mod (p-1)(q-1)
```

![](https://fadasr.github.io/images/rsa-keygen.png)

an academic example of RSA key generation:

1. choose public exponent and prime numbers
```
e = 3, p = 11, q = 23
```
2. calculate modulus
```
n = pq = 253, t = (p-1)(q-1) = 220
```
3. check if the [gcd(3,220)](https://www.wolframalpha.com/input/?i=gcd(3,220)) is coprime (the numbers have no common factors except 1)
```
gcd(e, t) == 1?
gcd(3, 220) = 1
```
4. calculate private exponent (e.g. using Extended Euclidean Algorithm)
```
ed = 1 (mod t) - in this case 1 < t, it can also be expressed like this: ed = i*t + 1, where i is an integer. In other word, find d that (i*t + 1) is divisible by e.
i = 1:
	ed = 1*220 + 1 = 221 -> not divisible by 3
i = 2:
	ed = 2*220 + 1 = 441 -> is divisible by 3
so d = 441 / 3 = 147
```

`(253, 3)` is the public key and `(253, 147)` is the private key. In the next example we will use the keys for encryption and decryption of a message.

other real world example of 2048-bit public key, and 65537 public exponent:
```
n = 0xa709e2f84ac0e21eb0caa018cf7f697f774e96f8115fc2359e9cf60b1dd8d4048d974cdf8422bef6be3c162b04b916f7ea2133f0e3e4e0eee164859bd9c1e0ef0357c142f4f633b4add4aab86c8f8895cd33fbf4e024d9a3ad6be6267570b4a72d2c34354e0139e74ada665a16a2611490debb8e131a6cffc7ef25e74240803dd71a4fcd953c988111b0aa9bbc4c57024fc5e8c4462ad9049c7f1abed859c63455fa6d58b5cc34a3d3206ff74b9e96c336dbacf0cdd18ed0c66796ce00ab07f36b24cbe3342523fd8215a8e77f89e86a08db911f237459388dee642dae7cb2644a03e71ed5c6fa5077cf4090fafa556048b536b879a88f628698f0c7b420c4b7
e = 0x010001
```

and the corresponding 2048-bit private key:
```
n = 0xa709e2f84ac0e21eb0caa018cf7f697f774e96f8115fc2359e9cf60b1dd8d4048d974cdf8422bef6be3c162b04b916f7ea2133f0e3e4e0eee164859bd9c1e0ef0357c142f4f633b4add4aab86c8f8895cd33fbf4e024d9a3ad6be6267570b4a72d2c34354e0139e74ada665a16a2611490debb8e131a6cffc7ef25e74240803dd71a4fcd953c988111b0aa9bbc4c57024fc5e8c4462ad9049c7f1abed859c63455fa6d58b5cc34a3d3206ff74b9e96c336dbacf0cdd18ed0c66796ce00ab07f36b24cbe3342523fd8215a8e77f89e86a08db911f237459388dee642dae7cb2644a03e71ed5c6fa5077cf4090fafa556048b536b879a88f628698f0c7b420c4b7
d = 0x10f22727e552e2c86ba06d7ed6de28326eef76d0128327cd64c5566368fdc1a9f740ad8dd221419a5550fc8c14b33fa9f058b9fa4044775aaf5c66a999a7da4d4fdb8141c25ee5294ea6a54331d045f25c9a5f7f47960acbae20fa27ab5669c80eaf235a1d0b1c22b8d750a191c0f0c9b3561aaa4934847101343920d84f24334d3af05fede0e355911c7db8b8de3bf435907c855c3d7eeede4f148df830b43dd360b43692239ac10e566f138fb4b30fb1af0603cfcf0cd8adf4349a0d0b93bf89804e7c2e24ca7615e51af66dccfdb71a1204e2107abbee4259f2cac917fafe3b029baf13c4dde7923c47ee3fec248390203a384b9eb773c154540c5196bce1
```

## encryption

to encrypt a message `m` using public key `(n, e)`, the sender computes the ciphertext <code>c = m<sup>e</sup> (mod n)</code>. To decrypt a ciphertext `c`, the receiver computes <code>m = c<sup>d</sup> (mod n)</code>.

the size of the message that can be encrypted using RSA is limited by the size of `n`. With encoding function (e.g. [RSAES-OAEP](https://tools.ietf.org/html/rfc8017#page-18)), it places more limits on the size of the plaintext you can encrypt.

an academic example of RSA encryption. From the previous example, we have public key `(253, 3)` and private key `(253, 147)`. Lets encrypt a message `m = 85`.
```
x mod y = x - (y * floor(x/y))
c = 85^3 (mod 253) = 85^3 - (253 * 2427) = 94
```

to decrypt the ciphertext `c = 94` back to `m`. See [the calculation](https://www.wolframalpha.com/input/?i=94%5E147+-+(253*floor((94%5E147)%2F253))) here.
```
m = 94^147 (mod 253) = 85
```

### RSAES-OAEP

RSAES-OAEP combines the RSAEP and RSADP primitives with the EME-OAEP encoding method. The encoding technique involves a mask generation function (MGF) based on a hash function and there is no obvious structure in the encoded block, unlike the PKCS#1v1.5 encoding method. The provable security of RSAES-OAEP relies on the random nature of the output of the MGF, which in turn relies on the random nature of the underlying hash. Here is EME-OAEP encoding operation:

![](https://fadasr.github.io/images/eme-oaep-encode.png)

RSAES-OAEP can operate on messages of length up to `k - 2hLen - 2` octets, where `hLen` is the length of the output from the underlying hash function and `k` is the length in octets of the recipient's RSA modulus. For hybrid mode, the typical largest message would be 32 bytes corresponding to a 256-bit AES key. With RSA 2048-bit modulus, the message limit for OAEP can be calculated:
```
SHA-256: 
	message limit = 256 - (2*32) - 2 = 190 octets

SHA-512:
	message limit = 256 - (2*64) - 2 = 126 octets
```

the RSAEP primitive performs the public key RSA transform by raising the integer to `e` modulo `n`. The RSADP function is the inverse of RSAEP. The PKCS#1 standard also specifies how RSADP can be performed with the Chinese Remainder Theorem (CRT) to speed up the operation.

example of RSAES-OAEP using python:
install first [PyCryptodome](https://github.com/Legrandin/pycryptodome)

1. generate RSA keys 1024-bit in PKCS#8 PEM ASN.1 format
```python
from Crypto.PublicKey import RSA
from Crypto.Cipher import PKCS1_OAEP
import binascii

keyPair = RSA.generate(1024)

pubKey = keyPair.publickey()
print(f"Public key:  (n={hex(pubKey.n)}, e={hex(pubKey.e)})")
pubKeyPEM = pubKey.exportKey()
print(pubKeyPEM.decode('ascii'))

print(f"Private key: (n={hex(pubKey.n)}, d={hex(keyPair.d)})")
privKeyPEM = keyPair.exportKey()
print(privKeyPEM.decode('ascii'))
```

the default public exponent is 65537. Each generation will generates different random RSA key-pair. Use this [website](https://lapo.it/asn1js/) to decode the PEM-format.
```
Public key: (n=0x9a11485bccb9569410a848fb1afdf2a81b17c1fa9f9eb546fd1deb873b49b693a4edf20eb8362c085cd5b28ba109dbad2bd257a013f57f745402e245b0cc2d553c7b2b8dbba57ebda7f84cfb32b7d9c254f03dbd0188e4b8e40c47b64c1bd2572834b936ffc3da9953657ef8bee80c49c2c12933c8a34804a00eb4c81248e01f, e=0x10001)
-----BEGIN PUBLIC KEY-----
MIGfMA0GCSqGSIb3DQEBAQUAA4GNADCBiQKBgQCaEUhbzLlWlBCoSPsa/fKoGxfB
+p+etUb9HeuHO0m2k6Tt8g64NiwIXNWyi6EJ260r0legE/V/dFQC4kWwzC1VPHsr
jbulfr2n+Ez7MrfZwlTwPb0BiOS45AxHtkwb0lcoNLk2/8PamVNlfvi+6AxJwsEp
M8ijSASgDrTIEkjgHwIDAQAB
-----END PUBLIC KEY-----
Private key: (n=0x9a11485bccb9569410a848fb1afdf2a81b17c1fa9f9eb546fd1deb873b49b693a4edf20eb8362c085cd5b28ba109dbad2bd257a013f57f745402e245b0cc2d553c7b2b8dbba57ebda7f84cfb32b7d9c254f03dbd0188e4b8e40c47b64c1bd2572834b936ffc3da9953657ef8bee80c49c2c12933c8a34804a00eb4c81248e01f, d=0x318ab12be3cf0d4a1b7921cead454fcc42ba070462639483394d6fb9529547827e9c8d23b294a8e01f8a1019da34e350f2307740e06a270bef1fe646e6ad213e31b528fdd5f5d03e633c07c44755ed622a629d79e822c095ebdf9cc80e517b5566dd3d3e5b16ec737987337a0e497fdba4b5ad97af41c1c3cdd87542a4637d81)
-----BEGIN RSA PRIVATE KEY-----
MIICXAIBAAKBgQCaEUhbzLlWlBCoSPsa/fKoGxfB+p+etUb9HeuHO0m2k6Tt8g64
NiwIXNWyi6EJ260r0legE/V/dFQC4kWwzC1VPHsrjbulfr2n+Ez7MrfZwlTwPb0B
iOS45AxHtkwb0lcoNLk2/8PamVNlfvi+6AxJwsEpM8ijSASgDrTIEkjgHwIDAQAB
AoGAMYqxK+PPDUobeSHOrUVPzEK6BwRiY5SDOU1vuVKVR4J+nI0jspSo4B+KEBna
NONQ8jB3QOBqJwvvH+ZG5q0hPjG1KP3V9dA+YzwHxEdV7WIqYp156CLAlevfnMgO
UXtVZt09PlsW7HN5hzN6Dkl/26S1rZevQcHDzdh1QqRjfYECQQDGDUIQXlOiAcGo
d5YqAGpWe0wzJ0UypeqZcqS9MVe9OkjjopCkkYntifdN/1oG7S/1KUMtLoGHqntb
c428zOO/AkEAxyV0cmuJbFdfM0x2XhZ+ge/7putIx76RHDOjBpM6VQXpLEFj54kB
qGLAB7SXr7P4AFrEjfckJOp2YMI5BreboQJAb3EUZHt/WeDdJLutzpKPQ3x7oykM
wfQkbxXYZvD16u96BkT6WO/gCb6hXs05zj32x1/hgfHyRvGCGjKKZdtwpwJBAJ74
y0g7h+wwoxJ0S1k4Y6yeQikxUVwCSBxXLCCnjr0ohsaJPJMrz2L30YtVInFkHOlL
i/Q4AWZmtDDxWkx+bYECQG8e6bGoszuX5xjvhEBslIws9+nMzMuYBR8HvhLo58B5
N8dk3nIsLs3UncKLiiWubMAciU5jUxZoqWpRXXwECKE=
-----END RSA PRIVATE KEY-----
```

2. encrypt and decrypt a message. The OAEP padding algorithm adds some randomness with the padding.
```python
msg = b'A message for encryption'
encryptor = PKCS1_OAEP.new(pubKey)
encrypted = encryptor.encrypt(msg)
print("Encrypted:", binascii.hexlify(encrypted))

decryptor = PKCS1_OAEP.new(keyPair)
decrypted = decryptor.decrypt(encrypted)
print('Decrypted:', decrypted)
```

here is the output:
```
Encrypted: b'99b331c4e1c8f3fa227aacd57c85f38b7b7461574701b427758ee4f94b1e07d791ab70b55d672ff55dbe133ac0bea16fc23ea84636365f605a9b645e0861ee11d68a7550be8eb35e85a4bde6d73b0b956d000866425511c7920cdc8a3786a4f1cb1986a875373975e158d74e11ad751594de593a35de765fe329c0d3dfbbfedc'
Decrypted: b'A message for encryption'
```

### Ferguson-Schneier encryption

[Niels Ferguson and Bruce Schneier](https://www.schneier.com/books/cryptography_engineering/) suggest a simpler method of encryption. It uses RSA to encrypt a random `r` and use a hash function (e.g. SHA-256) to derive the actual content-encryption key `K`, thus removing any structural similarities between the actual key `K` and the random `r` encrypted by the RSA.

![](https://fadasr.github.io/images/rsa-encrypt.png)

the receiver decrypts the ciphertext `c` and hash the result to get the same key `K`.

## signatures

to sign a message `m`, the owner of the private key computes <code>s = m<sup>d</sup> (mod n)</code>. The pair `(m, s)` is now a signed message. To verify the signature, anyone who knows the public key can verify that <code>m = s<sup>e</sup> (mod n)</code>.

### RSASSA-PSS

RSASSA-PSS combines the RSASP1 and RSAVP1 primitives with the EMSA-PSS encoding method. In contrast to the RSASSA-PKCS1-v1_5 signature scheme, a hash function identifier is not embedded in the EMSA-PSS encoded message, therefore, it is recommended that the EMSA-PSS mask generation function be based on the same hash function. Here is the EMSA-PSS encoding operation:

![](https://fadasr.github.io/images/EMSA-PSS-encode.png)

the signature is verified by first applying RSAVP1 to the signature, which returns the value of EM. We then look for the 0xBC octet and check if 
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTE4OTU1ODI3OTgsLTE5ODU0ODY3ODIsLT
ExNTgwMTQ1OTAsLTgxNDE4NTYwNiwxOTMwMDc4NjcsLTExNDgz
NjY3NjcsLTE2MDcyMTUwNzksNTUyNjA3NTQ5LC04NTE1NTk5Mj
MsLTMxNDkzNjIzMywxNDYyNDY2ODUyLDg4MTY5NDkzNiwxNzM1
MzAzMjg1LC02NTI5NDQ5NzgsNDk1MDM4NzY2LDEzOTI5NzAyOT
UsLTc5MDYyNTI0LDIxMTQxMjU3NjIsMTIzNjYxNDk3MiwxMzU5
MzU5MTU1XX0=
-->