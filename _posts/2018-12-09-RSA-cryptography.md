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
3. check if the 2 numbers are coprime (the numbers have no common factors except 1)
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

real world example of 2048-bit public key, and 65537 public exponent:
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

to encrypt a message `m` using public key `(n, e)`, the sender computes the ciphertext `c = m`<sup>e</sup>`(mod n)`. To decrypt a ciphertext `c`, the receiver computes `m = c`<sup>d</sup>`(mod n)`.

the size of the message that can be encrypted using RSA is limited by the size of `n`. With encoding function (e.g. [RSA-OAEP](https://tools.ietf.org/html/rfc8017#page-18)) we cannot even use all the bits.

an academic example of RSA encryption. From the previous example, we have public key `(253, 3)` and private key `(253, 147)`. Lets encrypt a message `m = 85`.
```
c = 
c = 85^3 (mod 253) -> 
```

### RSA-OAEP


### Ferguson-Schneier encryption

[Niels Ferguson and Bruce Schneier](https://www.schneier.com/books/cryptography_engineering/) suggest a simpler method of encryption. It uses RSA to encrypt a random `r` and use a hash function (e.g. SHA-256) to derive the actual bulk encryption key `K`, thus removing any structural similarities between the actual key `K` and the random `r` encrypted by the RSA.

![](https://fadasr.github.io/images/rsa-encrypt.png)

the receiver decrypts the ciphertext `c` and hash the result to get the same key `K`.
<!--stackedit_data:
eyJoaXN0b3J5IjpbNjE1MTI1OTA0LDIxMTQxMjU3NjIsMTIzNj
YxNDk3MiwxMzU5MzU5MTU1LC0xMDg2NTg3MDU5LDg5NjQ3MTk3
NywtMTgxMjUxMDc3NSwtMTM2NjE5NTkxNSwtMTg2NDA1OTcxOS
wtNjg0ODk5MTI5LDIwNDk2NTYzOTAsLTM1MjE0ODU1MCwtMTk0
MTI3MzYxNiwtNjYwODQ3MTMsLTE0NTU4NTU1NjQsLTEyODc2Nj
I4MjNdfQ==
-->