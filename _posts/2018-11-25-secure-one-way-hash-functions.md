## secure one-way hash functions

hash functions aka pseudo-random functions (PRF) take as an input an arbitrary length message and produce a fixed size result. The hash result is also known as the digest or the fingerprint.

![](https://fadasr.github.io/images/hash.png)

two properties hash functions must have to be useful:

- one-way function: a function that can be computed but cannot be inverted. aka pre-image-attack-resistance function: given only the digest `h`, finding the message `m` is nontrivial
- collision resistance: a collision is two different inputs <code>m<sub>1</sub></code> and <code>m<sub>2</sub></code> for which <code>H(m<sub>1</sub>) = H(m<sub>2</sub>)</code>. Finding another input that produces the same digest is nontrivial

almost all real-life hash functions are iterative hash functions. This structure allows you to start computing the hash of an input as soon as you have the first part of it. So in applications where a stream of data is to be hashed, the message can be hashed on the fly without ever storing the data.

![](https://fadasr.github.io/images/hash-process.png)

hash functions are used in many scenarios. Here are their most common applications:

- file manifests
- storing password
- generate unique Id: commit hashes in git, bitcoin addresses
- pseudo-random number generation
- proof-of-work algorithm

here are list of modern cryptographic hash algorithm that are secure enough for most application:

### SHA-2 

SHA-256, SHA-384, and SHA-512 are based on the cryptographic concept "merkle-damgard" construction. SHA-256 is widely used by developers (e.g. in the bitcoin blockchain). SHA-512 uses 128-byte blocks instead of the 64-byte blocks SHA-256 use.

### SHA-3

Keccak is the winner of the SHA-3 NIST competition and is based on the cryptographic concept "sponge" construction. Unlike SHA-2, the SHA-3 family are not vulnerable to the "length extension" attack. SHAKE128 and SHAKE256 are variants of the SHA-3 

### BLAKE2

BLAKE2 is a family of fast hash functions and is one of the finalists at the SHA-3 NIST competition. BLAKE2s is optimized for 32-bit microprocessors and BLAKE2b is optimized for 64-bit

### calculating cryptographic hash functions in python

```python
import hashlib, binascii

text = 'hello'
data = text.encode("utf8")

sha256hash = hashlib.sha256(data).digest()
print("SHA-256:   ", binascii.hexlify(sha256hash))

sha3_256 = hashlib.sha3_256(data).digest()
print("SHA3-256:  ", binascii.hexlify(sha3_256))

blake2s = hashlib.new('blake2s', data).digest()
print("BLAKE2s:   ", binascii.hexlify(blake2s))
```

the output:

```
SHA-256:    b'2cf24dba5fb0a30e26e83b2ac5b9e29e1b161e5c1fa7425e73043362938b9824'
SHA3-256:   b'3338be694f50c5f338814986cdf0686453a888b84f424d792af4b9202398f392'
BLAKE2s:    b'19213bacc58dee6dbde3ceb9a47cbb330b3d86f8cca8997eb00be456f140ca25'
```
<!--stackedit_data:
eyJoaXN0b3J5IjpbMTU5Mjk3MzczMiwzNzA1OTE0OTUsMjAxOT
Q3ODEzMiwtMTMzNTE0MjU0OSw2MjAyMDUyODJdfQ==
-->