**Secure**

# 04-02 Secure Channels 1

## Confidentiality and Authenticity
### Symmetric encryption
* encrypt: E(key, message) = ciphertext
* decrypt: D(key, ciphertext) = message
* key = shared secret
  * but how to share it? unsolved
* symmetric encryption =  {{ confidentiality but not authenticity }} ... use MACs

### message authentication codes (MACs)
* goal: use shared secret _key_ to verify message origin
* MAC(key, message) = tag
* what is this similar to? --> a checksum!

### authenticated encryption
* in both encryption and MAC, given everything but the key, you should not be able to find out the value of the key
* combine symmetric encryption and MAC to achieve confidentiality and authentication

### one-direction confidentiality, asymmetric encryption
* private key has to be private; principles don't share a key
* public key is safe to give to everyone
* encrypt: PE(public key, message) = ciphertext
* decrypt: PD(private key, ciphertext) = message
* use digital signatures

### digital signatures
* sign: S(private key, message) = signature
* verify: V(public key, signature, message) = 1 ("yes, correct signature")
* knowing S, V, public key, message, signature doesn't make it too easy to find another message + signature st V(public key, other message, other signature) = 1

## Attacks and Solutions

### replay attacks
*  copy and paste a principle's signed message
* 2 principles, preventing replay attack using messages w/in that conversation -- solution: {{ nonce }}
  * (unhide this once you answer the above question) {{ nonce }} = {{ number only used once; numbering messages means a previous message cannot be resent to respond to a later message }}
  * Limitations: can't extend to replay attacks that involve other conversations where you might be able to get the correct nonce and signature
* Multiple conversations, solution: {{ nonce, signatures, and ALSO each signature includes its intended recipient }}

### TLS state machine attack
* protocol:
  * step 1: verify server identity
  * step 2: receive messages from server
* attack:
  * if server sends "here's your next message" instead of "here's my identity" then broken client ignores verifying server's identity

## Certificates + Delegating Trust

### Certificates
* A has B's public key already
* C wants B's K_pub and knows A's already
* C message A
* A can generate "certificate" message certifying B's K_pub AND Sign(A's private key, "B's public key is XXX)
* A send certificate to B
* Be sends a copy of this certificate to C
* If C trusts A, now C has B's public key

problem: why should C trust A?

### Certificate Authority (CA)
* websites go to CA with their public key and the CAs sign messages basically stating that a website's public key for foo.com is XXX
* browser uses certificates to verify website id and the website can forward certificate so browser doesn't have to contact CA directly
* other things they do:
  * maintain public db of _revoked certificates_
  * certificate transparency -- public logs of every certificate issued
  * 'CAA' records in the DNS -- indicate which CAs are allowed to issue certificates in DNS

### Certificate Chains
* certificate signed by a website / server CA which got its certificate signed by another CA ... until ultimately, trusted public keys hardcoded in OS/browser
* build a certificate hierarchy
* really brings social / economic question of who is given the authority to delegate trust

### Public-key Infrastructure
*  = ecosystem w CAs and certificates for everyone

### exercise
How should website certificates verify identity?
* {{ websites -- set by CA/Browser Forum }}

## Crypto Security Tools

### Crypto Hash
* hash(M) = X
* given X --> {{ hard to find M other than just guessing }}
* given X, M --> {{ hard to find second message so that hash(M2) = X }}
* alg ie: SHA256
  * Fast ie
* Might want slow hash alg for password hashing, ie Argon2i, scrypt, PBKDF2

### Random Numbers
* lots of keys that no one else knows and generate them in such a way that no one else could
* ideal properties:
  * attacker cannot guess (part of) number better than chance
  * knowing prior random nums doesnt help predict next random numbers
  * compromising machine shouldn't reveal older random nums

### exercise: how to generate random numbers
/dev/urandom
* collects "entropy" from hard-to-predict events
  * ie, exact timing I/O interrupts, some processor's built-in random number circuit

# 04-07

## Key Agreement
* TLS, SSH use special key agreement primitive
  * basically, symmetric communication to take advantage of speed

### Diffie-Hellman-style key agreement
* mathematical operation that generates a key share for a principle, then generate a shared secret using private value and the other principle's key share and THEN when they use that function it will generate the same shared secret
* allows time benefits of symmetric encryption
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

## TLS: Transport Layer Security

### handshake
* client and server have private values that they will never send
* use private values to generate new shared secret (or the same one as the other principle)
* NOW how do we know who this is coming from? use public key, as well as the certificate verification to confirm who sent the public key 
    * get a key share, then you get a certificate asserting the public key and containing the CA signature then sign that with website private key

* ![alt text](typical_tls_handshake.png){size=medium}
  * KeyShare = key parts for key exchange
  * Certificate = certificate (“foo.com’s public key is X” + CA signature)
  CertificateVerify = Sign(foo.com’s private key, server’s key share)
  * MAC(key made from key shares, Hash(everything so far))
  (purpose: tie new key with rest of handshake)
    * CA signature: sign(CA, private key, "foo.com's private key")

### after handshake
  * use key shares results to get several symmetric keys
  * separate keys for each direction (ie, server to client and vis versa)
  * often separate keys for encryption and MAC
  * later messages use encryption + MAC + nonces

## SSH
* works similarly but different certificate verification process