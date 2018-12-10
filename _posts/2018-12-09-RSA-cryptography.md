# RSA cryptography

the [RSA](https://tools.ietf.org/html/rfc8017) is one of the most widely used public-key cryptosystem in the world. The RSA algorithm provides:
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

an academic example of RSA key generation:

1. choose public exponent and prime numbers
	> e = 3, p = 11, q = 23
2. calculate modulus
	> n = pq = 253, t = (p-1)(q-1) = 220
3. check if the 2 numbers are coprime
	> gcd(e, t) == 1?
4. calculate private exponent using Extended Euclidean Algorithm
	> d = 147
5. verify the private exponent
	> ed = 3*147 = 441 = 1 mod 220

(253, 3) is the public key and (253, 147) is the private key. In the next example we will use the keys for encryption and decryption of a 