**Secure**

* Diffie-Hellman-style key agreement
  * High-level: way for two parties to agree on a shared secret over a public channel
      * public/private-key-like
        * "key share" (public (parties use public math))
        * private (random) value (parties keep one secret value)
        * key share = GenerateKeyShare(private value) (both parties end up computing the same shared key)
  * Steps:
    1. Public values
    2. Each party picks a secret
    3. Compute public values
    4. Compute shared secret

* TLS: Transport Layer Security
* ![alt text](typical_tls_handshake.png){size=medium}
  * KeyShare = key parts for key exchange
  * Certificate = certificate (“foo.com’s public key is X” + CA signature)
  CertificateVerify = Sign(foo.com’s private key, server’s key share)
  * MAC(key made from key shares, Hash(everything so far))
  (purpose: tie new key with rest of handshake)
    * CA signature: sign(CA, private key, "foo.com's private key")
* After handshake
  * use key shares results to get several symmetric keys
  * separate keys for each direction (ie, server to client and vis versa)
  * often separate keys for encryption and MAC
  * later messages use encryption + MAC + nonces

**i missed everything oops**

**Pipeline**

*Exercise 2: throughput/latency*
* ![alt text](ex_throughput_latency.png){size=medium}
double number of pipeline stages (to 10) + decrease cycle time from 500 ps to 250 ps — throughput?
1. 1 instr/100 ps
2. 1 instr/250 ps
3. 1 instr/1000 ps (answer)
4. 1 instr/5000 ps
5. something else

*Diminishing returns*

1. Register delays
2. Uneven split
   * ![alt text](uneven_split1.png){size=medium}
   * ![alt text](uneven_split2.png){size=medium}
   * ![alt text](uneven_split3.png){size=medium}

*Data hazard*

1. Read registers 8 and 9
2. Read registers 9 and 8 while adding 8 and 9

So we're reading the old value instead of the value ISA says was just written

_Solutions:_
1. Compiler solution: change the ISA, make it the compiler's job
   * Overview:
       * Basically saying the manual is wrong
       * All addqs take effect three instructions later (add instruction doesn't write value right away)
       * So compiler takes the value from our perspective
       * Basically using dummy instructions
       * Practical solution

2. Hardware solution: hardware adds nops, _stalling_
   * Overview:
     * Hardware inserts 2 nops (no operations, which means its slow)
     * Works, but it's slow
   * Stalling/nop pipeline diagram
       * 
       * 

