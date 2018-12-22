# elliptic curve cryptography (ECC)

[ECC](http://www.secg.org/sec1-v2.pdf) is a public-key cryptography that offers performance advantages at equal/higher security levels for a far smaller key size, compared to [RSA](https://fadasr.github.io/RSA-cryptography/). 

## what are elliptic curves

ECC makes use of elliptic curves over finite field. An elliptic curve `E(Fp)` over a prime finite field `Fp` is defined by the curve equation ([Weierstrass form](https://en.wikipedia.org/wiki/Elliptic_curve "Elliptic curve")):   

```
E : y^2 := (x^3 + a*x + b) mod p  
```

where `x`, `y`, `a`, and `b` are elements of the field `Fp` (i.e. `p` is a prime number `> 3`).

### an academic example of the curve

let's see an academic example of the curve (i.e. `secp256k1`) used in Bitcoin's public-key cryptography. The elliptic curve equation over the real numbers, where `a = 0`, and `b = 7`:

```
y^2 := x^3 + 7
```

and here is the [visualization](https://kevinmehall.net/p/equationexplorer/#%5B-17,17,-17,17%5D&i%7Cy%5E2=x%5E3+7) of the curve (over the real numbers):

![](https://fadasr.github.io/images/secp256k1-graph-real-numbers.png) 

the same elliptic curve equation over a prime finite field `Fp` can be expressed as:

```
y^2 := (x^3 + 7) mod p
```

and here is the visualization of the curve over prime finite field with `p = 17`:

![](https://fadasr.github.io/images/secp256k1-graph-prime-field.png)

the curve over prime finite field are set of points in a square matrix of size `p x p`. The finite number of unique points on the curve is known as the **order** of the curve. Let's see an example how the points are [computed](https://www.wolframalpha.com/input/?i=y%5E2+%3D+34+mod+17) using the elliptic curve equation:

```
for x = 3:
	y^2 := (3^3 + 7) mod 17 = 34  mod 17
	y := 0

for x = 5:
	y^2 := (5^3 + 7) mod 17 = 132 mod 17
	y := 8 and 9

to check if any point belong to the curve, it should satisfies the equation below:
	(y^2 - x^3 - 7)  mod 17 = 0
```

## elliptic curve algebra

elliptic curves posses an algebra with operations that allows us to create a trapdoor function useful for both DSA signatures and DH-based encryption. Point doubling (aka adding a point to itself) is defined as computing the tangent of a point on the curve and finding where it strikes the curve. There should be only one unique answer, and it is the double of the point. If we add a point `G` to itself: `G + G = 2G`. The set of points on `E(Fp)` forms a group under this operation. Furthermore the group is abelian - meaning that `G1 + G2 = G2 + G1`. Point multiplication is defined as adding a point to itself a set number of times, typically denoted as `kG` where `k` is the scalar number of times we wish to add `G` to itself. If we wrote `3G`, that is equivalent to `G + G + G`. If we cycle through all possible values of `k`, we will hit all of the points on the curve.

```
P = kG
```

where `P`, and `G` are points on the curve, and `k` is the scalar number `< p`. It is relatively easy to calculate `P` given `k` and `G`, but it is hard to determine `k` given `P` and `G`. This is called the **discrete logarithm problem** for elliptic curves. 

### example of arithmetic operations

1) install [tinyec](https://github.com/alexmgr/tinyec)

2) from the previous example, the curve equation is `y^2 = (x^3 - 7) mod 17` with `G = (15,13)`

```python
from tinyec.ec import SubGroup, Curve

field = SubGroup(p=17, g=(15, 13), n=18, h=1)
curve = Curve(a=0, b=7, field=field, name='p1707')
print('curve:', curve)

for k in range(0, 25):
    p = k * curve.g
    print(f"{k} * G = ({p.x}, {p.y})")
```

3) the result of the above point multiplications. The EC group is cyclic and the order of the EC group is `n = 18`, because starting from `k = 18`, the next points repeat the first ones.

```
curve: "p1707" => y^2 = x^3 + 0x + 7 (mod 17)
0 * G = (None, None)
1 * G = (15, 13)
2 * G = (2, 10)
3 * G = (8, 3)
4 * G = (12, 1)
5 * G = (6, 6)
6 * G = (5, 8)
7 * G = (10, 15)
8 * G = (1, 12)
9 * G = (3, 0)
10 * G = (1, 5)
11 * G = (10, 2)
12 * G = (5, 9)
13 * G = (6, 11)
14 * G = (12, 16)
15 * G = (8, 14)
16 * G = (2, 7)
17 * G = (15, 4)
18 * G = (None, None)
19 * G = (15, 13)
20 * G = (2, 10)
21 * G = (8, 3)
22 * G = (12, 1)
23 * G = (6, 6)
24 * G = (5, 8)
```

## elliptic curve parameters

Six parameters describe any curve over the prime finite fields:

```
T = (p, a, b, G, n, h)
```

- modulus `p`: defines the field in which the curve lies
- parameters `a` and `b`: define the curve in the finite field
- base point `G`
- order of the curve `n`: defines the order of `G`
- cofactor `h = #E(Fp) / n`

elliptic curve parameters over `Fp` precisely specify an elliptic curve and base point. This is necessary to precisely define public-key cryptographic schemes based on ECC. The base/generator point `G` can generate any other point in its group by multiplying `G` by some integer in the range `[0…n]`. The base point has order `n`, which is a large prime. The number of points on the curve is `hn` for some integer `h` (the cofactor) which is not divisible by `n`. To foster interoperability it is strongly recommended that we use the parameters specified in the standard (e.g . [SEC 2](https://safecurves.cr.yp.to/refs.html#2010/certicom-sec2)). Each of these standards tries to ensure that the elliptic-curve discrete-logarithm problem (ECDLP) is difficult. It is recommended to [choose curves](https://safecurves.cr.yp.to/index.html) that allow simple implementations to be secure implementations.

### example curve parameters from the standards

```
secp256k1: y^2 = x^3 + 0x + 7
	p = 115792089237316195423570985008687907853269984665640564039457584007908834671663
	  = 0xfffffffffffffffffffffffffffffffffffffffffffffffffffffffefffffc2f
	  = 2^256 - 2^32 - 977
	G = (55066263022277343669578718895168534326250603453777594175500187360389116729240,
	    32670510020758816978083085130507043184471273380659243275938904335757337482424)
	  = (0x79be667ef9dcbbac55a06295ce870b07029bfcdb2dce28d959f2815b16f81798,
	    0x483ada7726a3c4655da4fbfc0e1108a8fd17b448a68554199c47d08ffb10d4b8)
	n = 115792089237316195423570985008687907852837564279074904382605163141518161494337
	  = 0xfffffffffffffffffffffffffffffffebaaedce6af48a03bbfd25e8cd0364141
	  = 2^256 - 432420386565659656852420866394968145599

Curve25519 [rfc7748]: y^2 = x^3 + 486662x^2 + x
	p = 57896044618658097711785492504343953926634992332820282019728792003956564819949
	  = 0x7fffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffed
	  = 2^255 - 19
	G = (9,
	    14781619447589544791020593568409986887264606134616475288964881837755586237401)
	  = (0x9,
	    0x20ae19a1b8a086b4e01edd2c7748d14c923d4d7e6d7c61b229e9c5a27eced3d9)
	n = 7237005577332262213973186563042994240857116359379907606001950938285454250989
	  = 0x1000000000000000000000000000000014def9dea2f79cd65812631a5cf5d3ed
	  = 2^252 + 27742317777372353535851937790883648493
```

## elliptic curve key pairs

elliptic curve key pairs should be generated as follows:

```
input: valid parameters T = (p, a, b, G, n, h)
output: an elliptic curve key pair (d, Q) associated with T
actions:
	1. randomly select an integer d in the interval [1, n-1]
	2. compute Q = dG
	3. Output (d, Q)  
```

### example key pairs generation

```python
from tinyec import registry
import secrets

curve = registry.get_curve('secp192r1')

privKey = secrets.randbelow(curve.field.n)
pubKey = privKey * curve.g
print("private key:", privKey)
print("public key:", pubKey)
```

the output:

```
private key: 4225655318977962031264230130242180748818603147467615868902
public key: (5396030834456770190396776530938374882273836179487834152291, 3422160588166914010077732710830109086004758012634997793937) on "secp192r1" => y^2 = x^3 + 6277101735386680763835789423207666416083908700390324961276x + 2455155546008943817740293915197451784769108058161191238065 (mod 6277101735386680763835789423207666416083908700390324961279)
```

## elliptic curve digital signature algorithm (ECDSA)

the ECDSA signing algorithm is based on the [ElGamal signature scheme](https://en.wikipedia.org/wiki/ElGamal_signature_scheme). For each signature generation, ECDSA requires a fresh random value `k`. 

```
input:
	d: private key (integer)
	n: order of the curve
	m: message to be signed
output: (r, s): the signature
signature generation:
	1. select a random integer k in the range [1, n-1]
	2. compute point P = (x, y) = kG and r = x mod n. If r = 0 then goto step 1
	3. compute h = H(m), where H is one of the SHA-2 or SHA-3 hash functions
	4. compute s = k^-1(h + dr) mod n. If s = 0 the goto step 1
	5. the signature of message m is the pair (r, s)
```

For Bob to authenticate Alice's signature, he must have a copy of her public-key and public domain parameters. Bob is presented with Alice's message and digital signature:

```
input:
	n: order of the curve
	m: signed message
	G: base point for the curve
	Q: signers public key 
	(r, s): the signature
output: validity of signature
signature verification:
	1. verify that r and s in the range [1, n-1]
	2. compute h = H(m)
	3. compute w = s^-1 mod n
	4. compute the point X = (x', y') = (hw)G + (rw)Q. If X = 0 then reject the signature
	5. compute r' = x' mod n
	6. if r equals r' then return valid
```

It is possible to turn ECDSA into deterministic schemes by using a deterministic process for generating the "random" value k. [Deterministic ECDSA](https://tools.ietf.org/html/rfc6979) only deal with the need for randomness at the time of signature generation. The key pair generation still requires a source of randomness.

### example digital signature

install first [pycoin](https://github.com/richardkiss/pycoin)

```python
from pycoin.ecdsa import generator_secp256k1, sign, verify
import hashlib, secrets

def sha3_256Hash(msg):
    hashBytes = hashlib.sha3_256(msg.encode("utf8")).digest()
    return int.from_bytes(hashBytes, byteorder="big")

def signECDSAsecp256k1(msg, privKey):
    msgHash = sha3_256Hash(msg)
    signature = sign(generator_secp256k1, privKey, msgHash)
    return signature

def verifyECDSAsecp256k1(msg, signature, pubKey):
    msgHash = sha3_256Hash(msg)
    valid = verify(generator_secp256k1, pubKey, msgHash, signature)
    return valid
```

the ECDSA signature, generated by the pycoin library by default is deterministic. Now let's sign a message and verify its signature:

```python
# ECDSA sign message (using the curve secp256k1 + SHA3-256)
msg = "Message for ECDSA signing"
privKey = secrets.randbelow(generator_secp256k1.order())
signature = signECDSAsecp256k1(msg, privKey)
print("Message:", msg)
print("Private key:", hex(privKey))
print("Signature: r=" + hex(signature[0]) + ", s=" + hex(signature[1]))

# ECDSA verify signature (using the curve secp256k1 + SHA3-256)
pubKey = (generator_secp256k1 * privKey).pair()
valid = verifyECDSAsecp256k1(msg, signature, pubKey)
print("\nMessage:", msg)
print("Public key: (" + hex(pubKey[0]) + ", " + hex(pubKey[1]) + ")")
print("Signature valid?", valid)
```

the output:

```
Message: Message for ECDSA signing
Private key: 0x79afbf7147841fca72b45a1978dd7669470ba67abbe5c220062924380c9c364b
Signature: r=0xb83380f6e1d09411ebf49afd1a95c738686bfb2b0fe2391134f4ae3d6d77b78a, s=0x6c305afcac930a3ea1721c04d8a1a979016baae011319746323a756fbaee1811

Message: Message for ECDSA signing
Public key: (0x3804a19f2437f7bba4fcfbc194379e43e514aa98073db3528ccdbdb642e240, 0x6b22d833b9a502b0e10e58aac485aa357bccd1df6ec0fa4d398908c1ac1920bc)
Signature valid? True
```

in ECDSA it is possible to recover the public key from signature:

```python
from pycoin.ecdsa import possible_public_pairs_for_signature

def recoverPubKeyFromSignature(msg, signature):
    msgHash = sha3_256Hash(msg)
    recoveredPubKeys = possible_public_pairs_for_signature(
        generator_secp256k1, msgHash, signature)
    return recoveredPubKeys

msg = "Message for ECDSA signing"
recoveredPubKeys = recoverPubKeyFromSignature(msg, signature)
print("\nMessage:", msg)
print("Signature: r=" + hex(signature[0]) + ", s=" + hex(signature[1]))
for pk in recoveredPubKeys:
    print("Recovered public key from signature: (" +
          hex(pk[0]) + ", " + hex(pk[1]) + ")")
```

the output:

```
Message: Message for ECDSA signing
Signature: r=0xd034c98af3274ad93f3c8ce944bbc17b11b6aa170c5f097ed98687fa0d93347c, s=0xa2318ceea2002caba38efbba3bf8ef8d43236a6edc33c040734d8eb2ed77f608
Recovered public key from signature: (0x1353fd26a6cb6110980cfd2bb5eca3b3cc3e08c930ad5991395dd826a250c79, 0xba6825142e230ee1fa2b406f3f9158a47ee49daca8ac47898c5fd92d805a101e)
Recovered public key from signature: (0x10b5d9028ec828a0f9111e36f046afa5a0c677357351093426bcec10c663db7d, 0x271763c56fcd87b72d59ceaa5b9c3fd2122788fe344751a9bde373f903e5bb20)
```

now let's use [eth_keys](https://github.com/ethereum/eth-keys/) library that generates extended ECDSA signatures `{r, s, v}` and supports internally the public key recovery.

```python
import eth_keys, os

# Generate the private + public key pair (using the secp256k1 curve)
signerPrivKey = eth_keys.keys.PrivateKey(os.urandom(32))
signerPubKey = signerPrivKey.public_key
print('Private key (64 hex digits):', signerPrivKey)
print('Public key (uncompressed, 128 hex digits):', signerPubKey)

# ECDSA sign message (using the curve secp256k1 + Keccak-256)
msg = b'Message for signing'
signature = signerPrivKey.sign_msg(msg)
print('Message:', msg)
print('Signature: [r = {0}, s = {1}, v = {2}]'.format(
    hex(signature.r), hex(signature.s), hex(signature.v)))

# ECDSA public key recovery from signature + verify signature
# (using the curve secp256k1 + Keccak-256 hash)
msg = b'Message for signing'
recoveredPubKey = signature.recover_public_key_from_msg(msg)
print('Recovered public key (128 hex digits):', recoveredPubKey)
print('Public key correct?', recoveredPubKey == signerPubKey)
valid = signerPubKey.verify_msg(msg, signature)
print("Signature valid?", valid)
```

the output:

```
Private key (64 hex digits): 0x68abc765746a33272e47b0a96a0b4184048f70354221e04219fbc223bfe79794
Public key (uncompressed, 128 hex digits): 0x30a6dc572da312587144e7ccda1e9abd901323adebe7091bb4985e1202c2a10bc25f681b3d2e1a671438f0b125287b473c09ca345c5583cd627232b536b9ca0a
Message: b'Message for signing'
Signature: [r = 0x4cddf146c578d20a31fa6128e5d9afe6ac666e5ef5899f2767cacb56a42703cc, s = 0x3847036857aa3f077a2e142eee707e5af2653baa99b9d10764a0be3d61595dbb, v = 0x0]
Recovered public key (128 hex digits): 0x30a6dc572da312587144e7ccda1e9abd901323adebe7091bb4985e1202c2a10bc25f681b3d2e1a671438f0b125287b473c09ca345c5583cd627232b536b9ca0a
Public key correct? True
Signature valid? True
```

## Edwards-Curve Digital Signature Algorithm (EdDSA)

[EdDSA](https://tools.ietf.org/html/rfc8032) is based on performance-optimized elliptic curves, such as the 255-bit curve [Curve25519](https://tools.ietf.org/html/rfc7748). It is a variant of Schnorr's signature system with (possibly twisted) Edwards curves. It uses small public keys (32 or 57 bytes) and signatures (64 or 114 bytes) for Ed25519 and Ed448, respectively.
<!--stackedit_data:
eyJoaXN0b3J5IjpbODc1MjI3ODgsLTIxMzEzODYwNzEsLTgzOT
IzNTAxNSwtMTY0MTA1NzQ2NSwtNjE5Nzk3NTczLDIwNDI5ODI5
OTUsLTM4OTgxMDUwNCwtMTUyNzQ5ODIzMCwxNTIxOTAyNSwtMT
E4NjgzNDI1NCw5OTA4MjIyNDhdfQ==
-->