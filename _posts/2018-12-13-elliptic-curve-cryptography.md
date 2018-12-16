# elliptic curve cryptography (ECC)

[ECC](http://www.secg.org/sec1-v2.pdf) is a public-key cryptography that offers performance advantages at equal/higher security levels for a far smaller key size, compared to [RSA](https://fadasr.github.io/RSA-cryptography/). 

## what are elliptic curves

ECC makes use of elliptic curves over finite field. An elliptic curve `E(Fp)` over a prime finite field `Fp` is defined by the curve equation:   

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

### e

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
<!--stackedit_data:
eyJoaXN0b3J5IjpbNzMxMjQ4ODM5LDE1MjE5MDI1LC0xMTg2OD
M0MjU0LDk5MDgyMjI0OF19
-->