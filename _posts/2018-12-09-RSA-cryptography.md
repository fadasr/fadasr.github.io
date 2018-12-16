# RSA cryptography

the [RSA](https://tools.ietf.org/html/rfc8017) system is one of the most widely used public-key cryptosystem in the world. The RSA algorithm provides:

- key-pair generation: random keys typically of size `1024-4096` bits
- encryption: encrypt a message in the range `[0...n]` using the public key and decrypt using the private key
- digital signature: sign a message with the private key and verify using the public key

the key length of `3072` bits and above are considered secure. Longer keys provide higher security but consume more computing time.

## key generation

the foundation of RSA's security is based on the difficulty of factoring large numbers (aka the trapdoor). Given `n`, it is considered a hard problem to determine it's prime factors, `p` and `q`.

```
n = pq
```

where `n` is a modulus, `p` and `q` are two large prime number

the pair `(n, e)` forms the public key, while the pair `(n, d)` forms the private key. The number `e` is called public key exponent and typically is equal to 3, 5, 17, or 65537. The number `d` is called private key exponent. The requirement for `e` and `d` is that:

```
ed = 1 mod (p-1)(q-1)
```

key generation flow diagram:

![](https://fadasr.github.io/images/rsa-keygen.png)

### an academic example of RSA key generation

1) choose public exponent and prime numbers

```
e = 3, p = 11, q = 23
```

2) calculate modulus

```
n = pq = 253, t = (p-1)(q-1) = 220
```

3) check if the [gcd(3,220)](https://www.wolframalpha.com/input/?i=gcd(3,220)) is coprime (the numbers have no common factors except 1)

```
gcd(e, t) == 1?
gcd(3, 220) = 1
```

4) calculate private exponent (e.g. using Extended Euclidean Algorithm)

```
ed = 1 (mod t) - in this case 1 < t, it can also be expressed like this: ed = i*t + 1, where i is an integer. In other word, find d that (i*t + 1) is divisible by e.
i = 1:
	ed = 1*220 + 1 = 221 -> not divisible by 3
i = 2:
	ed = 2*220 + 1 = 441 -> is divisible by 3
so d = 441 / 3 = 147
```

`(253, 3)` is the public key and `(253, 147)` is the private key. In the next example we will use the keys for encryption and decryption of a message.

### real world example of key pair

2048-bit public key with 65537 public exponent:

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

the size of the message that can be encrypted using RSA is limited by the size of `n`. With encoding function (e.g. [RSAES-OAEP](https://tools.ietf.org/html/rfc8017#page-18)), it places more limits on the size of the plaintext you can encrypt. If you encrypt a very small message `m` with small public exponent `e` (e.g. `e = 5`), where <code>m<sup>5</sup> < n</code>, so no modular reduction takes place. The attacker can recover `m` by simply taking the fifth root of <code>m<sup>5</sup></code>.

### an academic example of RSA encryption

From the previous example, we have public key `(253, 3)` and private key `(253, 147)`. Lets encrypt a message `m = 85`.

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

RSAES-OAEP can operate on messages of length up to `k - 2hLen - 2` octets, where `hLen` is the length of the output from the underlying hash function and `k` is the length in octets of the recipient's RSA modulus. For hybrid mode, the typical largest message would be `32` bytes corresponding to a `256-bit` AES key. With RSA `2048-bit` modulus, the message limit for OAEP can be calculated:

```
SHA-256: 
	message limit = 256 - (2*32) - 2 = 190 octets

SHA-512:
	message limit = 256 - (2*64) - 2 = 126 octets
```

the RSAEP primitive performs the public key RSA transform by raising the integer to `e` modulo `n`. The RSADP function is the inverse of RSAEP. The PKCS#1 standard also specifies how RSADP can be performed with the Chinese Remainder Theorem (CRT) to speed up the operation.

### example of RSAES-OAEP using python

1) install first [PyCryptodome](https://github.com/Legrandin/pycryptodome)

2) generate RSA keys `1024-bit` in PKCS#8 PEM ASN.1 format

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

3) the default public exponent is `65537. Each generation will generates different random RSA key-pair. Use this [website](https://lapo.it/asn1js/) to decode the PEM-format.

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

4) encrypt and decrypt a message. The OAEP padding algorithm adds some randomness with the padding.

```python
msg = b'A message for encryption'
encryptor = PKCS1_OAEP.new(pubKey)
encrypted = encryptor.encrypt(msg)
print("Encrypted:", binascii.hexlify(encrypted))

decryptor = PKCS1_OAEP.new(keyPair)
decrypted = decryptor.decrypt(encrypted)
print('Decrypted:', decrypted)
```

5) here is the output:

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

### example using python

1) generate RSA keys 1024-bit

```python
from Crypto.PublicKey import RSA

keyPair = RSA.generate(bits=1024)
print(f"Public key:  (n={hex(keyPair.n)}, e={hex(keyPair.e)})")
print(f"Private key: (n={hex(keyPair.n)}, d={hex(keyPair.d)})")
```

2) here is the output:

```
Public key:  (n=0xf51518d30754430e4b89f828fd4f1a8e8f44dd10e0635c0e93b7c01802729a37e1dfc8848d7fbbdf2599830268d544c1ecab4f2b19b6164a4ac29c8b1a4ec6930047397d0bb93aa77ed0c2f5d5c90ff3d458755b2367b46cc5c0d83f8f8673ec85b0575b9d1cea2c35a0b881a6d007d95c1cc94892bec61c2e9ed1599c1e605f, e=0x10001)
Private key: (n=0xf51518d30754430e4b89f828fd4f1a8e8f44dd10e0635c0e93b7c01802729a37e1dfc8848d7fbbdf2599830268d544c1ecab4f2b19b6164a4ac29c8b1a4ec6930047397d0bb93aa77ed0c2f5d5c90ff3d458755b2367b46cc5c0d83f8f8673ec85b0575b9d1cea2c35a0b881a6d007d95c1cc94892bec61c2e9ed1599c1e605f, d=0x165ecc9b4689fc6ceb9c3658977686f8083fc2e5ed75644bb8540766a9a2884d1d82edac9bb5d312353e63e4ee68b913f264589f98833459a7a547e0b2900a33e71023c4dedb42875b2dfdf412881199a990dfb77c097ce71b9c8b8811480f1637b85900137231ab47a7e0cbecc0b011c2c341b6de2b2e9c24d455ccd1fc0c21)
```

3) sign a hashed message using the private key `(n, d)`. In Python we have modular exponentiation as built in function [`pow(x, y, n)`](https://docs.python.org/3/library/functions.html#pow):

```python
# RSA sign the message
msg = b'A message for signing'
from hashlib import sha512
hash = int.from_bytes(sha512(msg).digest(), byteorder='big')
signature = pow(hash, keyPair.d, keyPair.n)
print("Signature:", hex(signature))
```

4) here is the 1024-bit Signature generated:

```
Signature: 0x650c9f2e6701e3fe73d3054904a9a4bbdb96733f1c4c743ef573ad6ac14c5a3bf8a4731f6e6276faea5247303677fb8dbdf24ff78e53c25052cdca87eecfee85476bcb8a05cb9a1efef7cb87dd68223e117ce800ac46177172544757a487be32f5ab8fe0879fa8add78be465ea8f8d5acf977e9f1ae36d4d47816ea6ed41372b
```

5) verify the Signature by decrypting the Signature using the public key `(n, e)` and comparing the hashes:

```python
# RSA verify signature
msg = b'A message for signing'
hash = int.from_bytes(sha512(msg).digest(), byteorder='big')
hashFromSignature = pow(signature, keyPair.e, keyPair.n)
print("Signature valid:", hash == hashFromSignature)
```

6) here is the output:

```
Signature valid: True
```

### RSASSA-PSS

RSASSA-PSS combines the RSASP1 and RSAVP1 primitives with the EMSA-PSS encoding method. In contrast to the RSASSA-PKCS1-v1_5 signature scheme, a hash function identifier is not embedded in the EMSA-PSS encoded message, therefore, it is recommended that the EMSA-PSS mask generation function be based on the same hash function. Here is the EMSA-PSS encoding operation:

![](https://fadasr.github.io/images/EMSA-PSS-encode.png)

the signature is verified by first applying RSAVP1 to the signature, which returns the value of `EM`. We then look for the `0xBC` octet and check if the upper `8*emLen - emBits` bits of the leftmost octet are zero. If either test fails, the signature is invalid. After further decoding, we can re-compute `H` from the `salt` and compare it against the `H` we extracted from `EM`. If they match, the signature is valid.

to demonstrate the PKCS#1 RSA digital signatures, we shall use the following code, based on the `pycryptodome` Python library, which implements RSA sign/verify, following the PKCS#1 v1.5 specification:

```python
from Crypto.PublicKey import RSA
from Crypto.Signature.pkcs1_15 import PKCS115_SigScheme
from Crypto.Hash import SHA256
import binascii

# Generate 1024-bit RSA key pair (private + public key)
keyPair = RSA.generate(bits=1024)

# Sign the message using the PKCS#1 v1.5 signature scheme (RSASP1)
msg = b'A message for signing'
hash = SHA256.new(msg)
signer = PKCS115_SigScheme(keyPair)
signature = signer.sign(hash)
print("Signature:", binascii.hexlify(signature))

# Verify valid PKCS#1 v1.5 signature (RSAVP1)
msg = b'A message for signing'
hash = SHA256.new(msg)
signer = PKCS115_SigScheme(keyPair)
try:
    signer.verify(hash, signature)
    print("Signature is valid.")
except:
    print("Signature is invalid.")
```

here is the output:

```
Signature: b'243b9ed6561ab3bddead98508af0ac34b4567b1358011ace24db71ce2bc7f1a2e942b6231aa84cb07bae85b668d7c7cd0bc40cdda6f8162de57f0ee842e589c58f94aa4f96d51355f8aa395d7db950ebb9d375fca3124b6222699a645e93287bc6f5eb5b750fc0b470588f949a887dff75ed42cf01d9642a5d497f609b8cd043'
Signature is valid.
```



<!--stackedit_data:
eyJoaXN0b3J5IjpbMTcxNTU2MzcyNCwxOTczNzY3NTI0LDk5Nz
g4MzA5OCwtNTcwMDUyMzAsLTE0MTM1MDkwMTAsMTIyMTczNDAy
LC03MjMwODcyMTAsLTEwMTQ3NTcwMTQsOTY1Mjg1MjAzLC0xOT
g1NDg2NzgyLC0xMTU4MDE0NTkwLC04MTQxODU2MDYsMTkzMDA3
ODY3LC0xMTQ4MzY2NzY3LC0xNjA3MjE1MDc5LDU1MjYwNzU0OS
wtODUxNTU5OTIzLC0zMTQ5MzYyMzMsMTQ2MjQ2Njg1Miw4ODE2
OTQ5MzZdfQ==
-->