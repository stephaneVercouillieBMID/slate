---
title: itsme® OpenID Connect documentation

language_tabs: # must be one of https://git.io/vQNgJ
 - http: HTTP

toc_footers:
 - <a href='#'>Sign Up for a Developer Key</a> -->
 - <a href='https://github.com/lord/slate'>Documentation Powered by Slate</a>

search: true
---
# 1. Introduction
The JSON Object Signing and Encryption (JOSE) is a framework intended to provide a method to securely transfer claims between parties. The JOSE framework provides a collection of specifications to serve this purpose:

<ul>
  <li><a href="https://tools.ietf.org/html/rfc7515" target="blank">JSON Web Signature</a> (JWS), defines a way to digitally sign JSON data structures. If your application needs to be able to verify the creator of a JSON data structure, you can use this specification to do so.</li>
  <li><a href="https://tools.ietf.org/html/rfc7516" target="blank">JSON Web Encryption </a> (JWE), defines a way to express encrypted content using JSON-based data structures. Basically, if you want to encrypt JSON data so that only the intended receiver can read the data, this specification tells you how to do it in an interoperable way.</li>
  <li><a href="https://tools.ietf.org/html/rfc7517" target="blank">JSON Web Key</a> (JWK), details a data structure that represents one or more cryptographic keys. If you need to express one of the many types of cryptographic key types in use today, this specification details how you do that in a standard way.</li>
  <li><a href="https://tools.ietf.org/html/rfc7518" target="blank">JSON Web Algorithms</a> (JWA), details the cryptographic algorithms and identifiers that are meant to be used with the JSON Web Signature (JWS), JSON Web Encryption (JWE), JSON Web Token (JWT), and JSON Web Key (JWK) specifications. For example, when specifying an encryption algorithm, a JSON key/value pair that has <code>alg</code> as the key may have HS256 as the value, which means HMAC using the SHA-256 hash algorithm.</li>
  <li><a href="https://tools.ietf.org/html/rfc7519" target="blank">JSON Web Token</a> (JWT), defines a way of representing claims. These claims are digitally signed and/or encrypted using either the JSON Web Signature (JWS) or JSON Web Encryption (JWE) specifications.</li>
</ul>
  
All these technologies can be used collectively to encrypt and/or sign content using a variety of algorithms. While the full set of permutations is extremely large, and might be daunting to some, it is expected that most applications will only use a small set of algorithms to meet their needs.


# 2. Practical cases
itsme® use above technologies for the following purposes:

<ul>
  <li>To enable you to verify the authenticity of ID Tokens issued by the itsme® Token Endpoint. ADD REFERENCE</li>
  <li>To enable you to verify the authenticity of claims returned at the userInfo Endpoint. ADD REFERENCE</li>
  <li>To enable your servers and itsme® endpoints to ensure confidentiality and integrity when exchanging information. ADD REFERENCE</li>
  <li>To enable the itsme® Token Endpoint to verify your credentials when exchanging the Authorization Code for an ID Token. ADD REFERENCE</li>
</ul>


# 3. Using a JWS

The JSON Web Signature (JWS) represents signed content using JSON data structures and base64url encoding as defined in the <a href=" https://tools.ietf.org/html/rfc7515" target="blank">specifications</a>. The representation consists of three parts: the JOSE Header, the JWS Payload, and the JWS Signature. The three parts are base64url-encoded for transmission, and typically represented as the concatenation of the encoded strings in that order, with the three strings being separated by period ('.') characters.

Following sections will show you how to generate a JWS object using the <code>RS256</code> (RSASSA-PKCS1-v1_5 with SHA-256) algorithm, as defined in the [itsme® Discovery document](#OpenIDConfig). For the demonstration we will use the following quotation:<q>It's a dangerous business, Frodo, going out your door. You step onto the road, and if you don't keep your feet, there's no knowing where you might be swept off to.</q>


### Input factors

The following are supplied before beginning the signing operation:

<ul>
  <il>A payload content serialized as UTF-8, and then encoded using base64url to produce JWS Payload enclosed</li>
</ul>
```http--inline
SXTigJlzIGEgZGFuZ2Vyb3VzIGJ1c2luZXNzLCBGcm9kbywgZ29pbmcgb3V0IHlvdXIgZG9vci4gWW91IHN0ZXAgb250byB0aGUgcm9hZCwgYW5kIGlmIHlvdSBkb24ndCBrZWVwIHlvdXIgZmVldCwgdGhlcmXigJlzIG5vIGtub3dpbmcgd2hlcmUgeW91IG1pZ2h0IGJlIHN3ZXB0IG9mZiB0by4
```
<ul>
  <li>The RSA 2048-Bit private key illustrated enclosed.</li>
</ul>
```http--inline
{
     "kty": "RSA",
     "kid": "bilbo.baggins@hobbiton.example",
     "use": "sig",
     "n": "n4EPtAOCc9AlkeQHPzHStgAbgs7bTZLwUBZdR8_KuKPEHLd4rHVTeT
         -O-XV2jRojdNhxJWTDvNd7nqQ0VEiZQHz_AJmSCpMaJMRBSFKrKb2wqV
         wGU_NsYOYL-QtiWN2lbzcEe6XC0dApr5ydQLrHqkHHig3RBordaZ6Aj-
         oBHqFEHYpPe7Tpe-OfVfHd1E6cS6M1FZcD1NNLYD5lFHpPI9bTwJlsde
         3uhGqC0ZCuEHg8lhzwOHrtIQbS0FVbb9k3-tVTU4fg_3L_vniUFAKwuC
         LqKnS2BYwdq_mzSnbLY7h_qixoR7jig3__kRhuaxwUkRz5iaiQkqgc5g
         HdrNP5zw",
     "e": "AQAB",
     "d": "bWUC9B-EFRIo8kpGfh0ZuyGPvMNKvYWNtB_ikiH9k20eT-O1q_I78e
         iZkpXxXQ0UTEs2LsNRS-8uJbvQ-A1irkwMSMkK1J3XTGgdrhCku9gRld
         Y7sNA_AKZGh-Q661_42rINLRCe8W-nZ34ui_qOfkLnK9QWDDqpaIsA-b
         MwWWSDFu2MUBYwkHTMEzLYGqOe04noqeq1hExBTHBOBdkMXiuFhUq1BU
         6l-DqEiWxqg82sXt2h-LMnT3046AOYJoRioz75tSUQfGCshWTBnP5uDj
         d18kKhyv07lhfSJdrPdM5Plyl21hsFf4L_mHCuoFau7gdsPfHPxxjVOc
         OpBrQzwQ",
     "p": "3Slxg_DwTXJcb6095RoXygQCAZ5RnAvZlno1yhHtnUex_fp7AZ_9nR
         aO7HX_-SFfGQeutao2TDjDAWU4Vupk8rw9JR0AzZ0N2fvuIAmr_WCsmG
         peNqQnev1T7IyEsnh8UMt-n5CafhkikzhEsrmndH6LxOrvRJlsPp6Zv8
         bUq0k",
     "q": "uKE2dh-cTf6ERF4k4e_jy78GfPYUIaUyoSSJuBzp3Cubk3OCqs6grT
         8bR_cu0Dm1MZwWmtdqDyI95HrUeq3MP15vMMON8lHTeZu2lmKvwqW7an
         V5UzhM1iZ7z4yMkuUwFWoBvyY898EXvRD-hdqRxHlSqAZ192zB3pVFJ0
         s7pFc",
     "dp": "B8PVvXkvJrj2L-GYQ7v3y9r6Kw5g9SahXBwsWUzp19TVlgI-YV85q
         1NIb1rxQtD-IsXXR3-TanevuRPRt5OBOdiMGQp8pbt26gljYfKU_E9xn
         -RULHz0-ed9E9gXLKD4VGngpz-PfQ_q29pk5xWHoJp009Qf1HvChixRX
         59ehik",
     "dq": "CLDmDGduhylc9o7r84rEUVn7pzQ6PF83Y-iBZx5NT-TpnOZKF1pEr
         AMVeKzFEl41DlHHqqBLSM0W1sOFbwTxYWZDm6sI6og5iTbwQGIC3gnJK
         bi_7k_vJgGHwHxgPaX2PnvP-zyEkDERuf-ry4c_Z11Cq9AqC2yeL6kdK
         T1cYF8",
     "qi": "3PiqvXQN0zwMeE-sBvZgi289XP9XCQF3VWqPzMKnIgQp7_Tugo6-N
         ZBKCQsMf3HaEGBjTVJs_jcK8-TRXvaKe-7ZMaQj8VfBdYkssbu0NKDDh
         jJ-GtiseaDVWt7dcH0cfwxgFUHpQh7FoCrjFJ6h6ZEpMF6xmujs4qMpP
         z8aaI4"
   }
```
<ul>
  <li>The <code>alg</code> parameter – value MUST be set to <code>RS256</code> as defined in the <a href=" #OpenIDConfig" target="blank">itsme® Discovery document</a></li>
</ul>


### Signing Operation

```http--inline
{
     "alg": "RS256",
     "kid": "bilbo.baggins@hobbiton.example"
}
```
A JWS Protected Header is generated to complete the signing operation. It contains the <code>alg</code> parameter and a reference to the signing key (aka. <code>kid</code>). An example can be visualized enclosed.

The JWS Protected Header will then be encoded using base64url to produce the string enclosed.
```http--inline
SXTigJlzIGEgZGFuZ2Vyb3VzIGJ1c2luZXNzLCBGcm9kbywgZ29pbmcgb3V0IHlvdXIgZG9vci4gWW91IHN0ZXAgb250byB0aGUgcm9hZCwgYW5kIGlmIHlvdSBkb24ndCBrZWVwIHlvdXIgZmVldCwgdGhlcmXigJlzIG5vIGtub3dpbmcgd2hlcmUgeW91IG1pZ2h0IGJlIHN3ZXB0IG9mZiB0by4
```

The JWS Protected Header and JWS Payload are combined to produce the JWS Signing Input.
```http--inline
eyJhbGciOiJSUzI1NiIsImtpZCI6ImJpbGJvLmJhZ2dpbnNAaG9iYml0b24uZXhhbXBsZSJ9
.
SXTigJlzIGEgZGFuZ2Vyb3VzIGJ1c2luZXNzLCBGcm9kbywgZ29pbmcgb3V0IHlvdXIgZG9vci4gWW91IHN0ZXAgb250byB0aGUgcm9hZCwgYW5kIGlmIHlvdSBkb24ndCBrZWVwIHlvdXIgZmVldCwgdGhlcmXigJlzIG5vIGtub3dpbmcgd2hlcmUgeW91IG1pZ2h0IGJlIHN3ZXB0IG9mZiB0by4
```

Performing the signature operation over the JWS Signing Input, following the signature algorithm defined by the JOSE header element <code>alg</code>, produces the enclosed JWS Signature.
```http--inline
MRjdkly7_-oTPTS3AXP41iQIGKa80A0ZmTuV5MEaHoxnW2e5CZ5NlKtainoFmKZopdHM1O2U4mwzJdQx996ivp83xuglII7PNDi84wnB-BDkoBwA78185hX-Es4JIwmDLJK3lfWRa-XtL0RnltuYv746iYTh_qHRD68BNt1uSNCrUCTJDt5aAE6x8wW1Kt9eRo4QPocSadnHXFxnt8Is9UzpERV0ePPQdLuW3IS_de3xyIrDaLGdjluPxUAhb6L2aXic1U12podGU0KLUQSE_oI-ZnmKJ3F4uOZDnd6QZWJushZ41Axf_fcIe8u9ipH84ogoree7vjbU5y18kDquDg
```

### Output results

The resulting JWS object using the JWS Compact Serialization can be visualized aside. 
```http--inline
eyJhbGciOiJSUzI1NiIsImtpZCI6ImJpbGJvLmJhZ2dpbnNAaG9iYml0b24uZXhhbXBsZSJ9
.
SXTigJlzIGEgZGFuZ2Vyb3VzIGJ1c2luZXNzLCBGcm9kbywgZ29pbmcgb3V0IHlvdXIgZG9vci4gWW91IHN0ZXAgb250byB0aGUgcm9hZCwgYW5kIGlmIHlvdSBkb24ndCBrZWVwIHlvdXIgZmVldCwgdGhlcmXigJlzIG5vIGtub3dpbmcgd2hlcmUgeW91IG1pZ2h0IGJlIHN3ZXB0IG9mZiB0by4
.
MRjdkly7_-oTPTS3AXP41iQIGKa80A0ZmTuV5MEaHoxnW2e5CZ5NlKtainoFmKZopdHM1O2U4mwzJdQx996ivp83xuglII7PNDi84wnB-BDkoBwA78185hX-Es4JIwmDLJK3lfWRa-XtL0RnltuYv746iYTh_qHRD68BNt1uSNCrUCTJDt5aAE6x8wW1Kt9eRo4QPocSadnHXFxnt8Is9UzpERV0ePPQdLuW3IS_de3xyIrDaLGdjluPxUAhb6L2aXic1U12podGU0KLUQSE_oI-ZnmKJ3F4uOZDnd6QZWJushZ41Axf_fcIe8u9ipH84ogoree7vjbU5y18kDquDg
```

The resulting JWS object using the general JWS JSON Serialization is slightly different as you can see enclosed.
```http--inline
   {
     "payload": "SXTigJlzIGEgZGFuZ2Vyb3VzIGJ1c2luZXNzLCBGcm9kbywg
         Z29pbmcgb3V0IHlvdXIgZG9vci4gWW91IHN0ZXAgb250byB0aGUgcm9h
         ZCwgYW5kIGlmIHlvdSBkb24ndCBrZWVwIHlvdXIgZmVldCwgdGhlcmXi
         gJlzIG5vIGtub3dpbmcgd2hlcmUgeW91IG1pZ2h0IGJlIHN3ZXB0IG9m
         ZiB0by4",
     "signatures": [
       {
         "protected": "eyJhbGciOiJSUzI1NiIsImtpZCI6ImJpbGJvLmJhZ2
             dpbnNAaG9iYml0b24uZXhhbXBsZSJ9",
         "signature": "MRjdkly7_-oTPTS3AXP41iQIGKa80A0ZmTuV5MEaHo
             xnW2e5CZ5NlKtainoFmKZopdHM1O2U4mwzJdQx996ivp83xuglII
             7PNDi84wnB-BDkoBwA78185hX-Es4JIwmDLJK3lfWRa-XtL0Rnlt
             uYv746iYTh_qHRD68BNt1uSNCrUCTJDt5aAE6x8wW1Kt9eRo4QPo
             cSadnHXFxnt8Is9UzpERV0ePPQdLuW3IS_de3xyIrDaLGdjluPxU
             Ahb6L2aXic1U12podGU0KLUQSE_oI-ZnmKJ3F4uOZDnd6QZWJush
             Z41Axf_fcIe8u9ipH84ogoree7vjbU5y18kDquDg"
       }
     ]
   }
```


Cryptographic algorithms and identifiers when using







