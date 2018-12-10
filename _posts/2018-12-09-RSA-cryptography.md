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
3. check if the 2 numbers are coprime
```
gcd(e, t) == 1?
```
4. calculate private exponent using Extended Euclidean Algorithm
```
d = 147
```
5. verify the private exponent
```
ed = 3*147 = 441 = 1 mod 220
```
(253, 3) is the public key and (253, 147) is the private key. In the next example we will use the keys for encryption and decryption of a message.

real world example of 2048-bit public key, and 65537 public exponent:
```
n = 0xa709e2f84ac0e21eb0caa018cf7f697f774e96f8115fc2359e9cf60b1dd8d4048d974cdf8422bef6be3c162b04b916f7ea2133f0e3e4e0eee164859bd9c1e0ef0357c142f4f633b4add4aab86c8f8895cd33fbf4e024d9a3ad6be6267570b4a72d2c34354e0139e74ada665a16a2611490debb8e131a6cffc7ef25e74240803dd71a4fcd953c988111b0aa9bbc4c57024fc5e8c4462ad9049c7f1abed859c63455fa6d58b5cc34a3d3206ff74b9e96c336dbacf0cdd18ed0c66796ce00ab07f36b24cbe3342523fd8215a8e77f89e86a08db911f237459388dee642dae7cb2644a03e71ed5c6fa5077cf4090fafa556048b536b879a88f628698f0c7b420c4b7
e = 0x010001
```

<!--stackedit_data:
eyJoaXN0b3J5IjpbLTEzNDQyODEzMzksLTY2MDg0NzEzLC0xND
U1ODU1NTY0LC0xMjg3NjYyODIzXX0=
-->