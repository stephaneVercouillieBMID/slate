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


# 4. Using a JWE

The JSON Web Encryption (JWE) specification standardizes the way to represent an encrypted content in a JSON-based data structure. Like in JWS, the message to be encrypted using JWE standard needs not to be a JSON payload, it can be any content.

Following sections will show you how to generate a JWE object using the <code>RSA-OAEP</code> (RSAES-OAEP) key encryption algorithm and the <code>A128CBC-HS256</code> (AES-128-CBC-HMAC-SHA-256) content encryption algorithm, as defined in the [itsme® Discovery document](#OpenIDConfig). Note that RSAES-OAEP uses random data to generate the ciphertext; it might not be possible to exactly replicate the results in the sections below. Moreover, only the RSA public key is necessary to perform the encryption. However, for the demonstration we will use the RSA private key to allow you to validate the output.

In our example below we will use the following quotation:<q>You can trust us to stick with you through thick and to the bitter end. And you can trust us to keep any secret of yours closer than you keep it yourself. But you cannot trust us to let you face trouble alone, and go off without a word. We are your friends, Frodo.</q>

### Input factors

The following are supplied before beginning the encryption process:

<ul>
  <il>The plaintext content</li>
</ul>
```http--inline
SXTigJlzIGEgZGFuZ2Vyb3VzIGJ1c2luZXNzLCBGcm9kbywgZ29pbmcgb3V0IHlvdXIgZG9vci4gWW91IHN0ZXAgb250byB0aGUgcm9hZCwgYW5kIGlmIHlvdSBkb24ndCBrZWVwIHlvdXIgZmVldCwgdGhlcmXigJlzIG5vIGtub3dpbmcgd2hlcmUgeW91IG1pZ2h0IGJlIHN3ZXB0IG9mZiB0by4
```
<ul>
  <li>The RSA public key</li>
</ul>
```http--inline
{
     "kty": "RSA",
     "kid": "samwise.gamgee@hobbiton.example",
     "use": "enc",
     "n": "wbdxI55VaanZXPY29Lg5hdmv2XhvqAhoxUkanfzf2-5zVUxa6prHRr
         I4pP1AhoqJRlZfYtWWd5mmHRG2pAHIlh0ySJ9wi0BioZBl1XP2e-C-Fy
         XJGcTy0HdKQWlrfhTm42EW7Vv04r4gfao6uxjLGwfpGrZLarohiWCPnk
         Nrg71S2CuNZSQBIPGjXfkmIy2tl_VWgGnL22GplyXj5YlBLdxXp3XeSt
         sqo571utNfoUTU8E4qdzJ3U1DItoVkPGsMwlmmnJiwA7sXRItBCivR4M
         5qnZtdw-7v4WuR4779ubDuJ5nalMv2S66-RPcnFAzWSKxtBDnFJJDGIU
         e7Tzizjg1nms0Xq_yPub_UOlWn0ec85FCft1hACpWG8schrOBeNqHBOD
         FskYpUc2LC5JA2TaPF2dA67dg1TTsC_FupfQ2kNGcE1LgprxKHcVWYQb
         86B-HozjHZcqtauBzFNV5tbTuB-TpkcvJfNcFLlH3b8mb-H_ox35FjqB
         SAjLKyoeqfKTpVjvXhd09knwgJf6VKq6UC418_TOljMVfFTWXUxlnfhO
         OnzW6HSSzD1c9WrCuVzsUMv54szidQ9wf1cYWf3g5qFDxDQKis99gcDa
         iCAwM3yEBIzuNeeCa5dartHDb1xEB_HcHSeYbghbMjGfasvKn0aZRsnT
         yC0xhWBlsolZE",
     "e": "AQAB",
     "alg": "RSA-OAEP",
     "d": "n7fzJc3_WG59VEOBTkayzuSMM780OJQuZjN_KbH8lOZG25ZoA7T4Bx
         cc0xQn5oZE5uSCIwg91oCt0JvxPcpmqzaJZg1nirjcWZ-oBtVk7gCAWq
         -B3qhfF3izlbkosrzjHajIcY33HBhsy4_WerrXg4MDNE4HYojy68TcxT
         2LYQRxUOCf5TtJXvM8olexlSGtVnQnDRutxEUCwiewfmmrfveEogLx9E
         A-KMgAjTiISXxqIXQhWUQX1G7v_mV_Hr2YuImYcNcHkRvp9E7ook0876
         DhkO8v4UOZLwA1OlUX98mkoqwc58A_Y2lBYbVx1_s5lpPsEqbbH-nqIj
         h1fL0gdNfihLxnclWtW7pCztLnImZAyeCWAG7ZIfv-Rn9fLIv9jZ6r7r
         -MSH9sqbuziHN2grGjD_jfRluMHa0l84fFKl6bcqN1JWxPVhzNZo01yD
         F-1LiQnqUYSepPf6X3a2SOdkqBRiquE6EvLuSYIDpJq3jDIsgoL8Mo1L
         oomgiJxUwL_GWEOGu28gplyzm-9Q0U0nyhEf1uhSR8aJAQWAiFImWH5W
         _IQT9I7-yrindr_2fWQ_i1UgMsGzA7aOGzZfPljRy6z-tY_KuBG00-28
         S_aWvjyUc-Alp8AUyKjBZ-7CWH32fGWK48j1t-zomrwjL_mnhsPbGs0c
         9WsWgRzI-K8gE",
     "p": "7_2v3OQZzlPFcHyYfLABQ3XP85Es4hCdwCkbDeltaUXgVy9l9etKgh
         vM4hRkOvbb01kYVuLFmxIkCDtpi-zLCYAdXKrAK3PtSbtzld_XZ9nlsY
         a_QZWpXB_IrtFjVfdKUdMz94pHUhFGFj7nr6NNxfpiHSHWFE1zD_AC3m
         Y46J961Y2LRnreVwAGNw53p07Db8yD_92pDa97vqcZOdgtybH9q6uma-
         RFNhO1AoiJhYZj69hjmMRXx-x56HO9cnXNbmzNSCFCKnQmn4GQLmRj9s
         fbZRqL94bbtE4_e0Zrpo8RNo8vxRLqQNwIy85fc6BRgBJomt8QdQvIgP
         gWCv5HoQ",
     "q": "zqOHk1P6WN_rHuM7ZF1cXH0x6RuOHq67WuHiSknqQeefGBA9PWs6Zy
         KQCO-O6mKXtcgE8_Q_hA2kMRcKOcvHil1hqMCNSXlflM7WPRPZu2qCDc
         qssd_uMbP-DqYthH_EzwL9KnYoH7JQFxxmcv5An8oXUtTwk4knKjkIYG
         RuUwfQTus0w1NfjFAyxOOiAQ37ussIcE6C6ZSsM3n41UlbJ7TCqewzVJ
         aPJN5cxjySPZPD3Vp01a9YgAD6a3IIaKJdIxJS1ImnfPevSJQBE79-EX
         e2kSwVgOzvt-gsmM29QQ8veHy4uAqca5dZzMs7hkkHtw1z0jHV90epQJ
         JlXXnH8Q",
     "dp": "19oDkBh1AXelMIxQFm2zZTqUhAzCIr4xNIGEPNoDt1jK83_FJA-xn
         x5kA7-1erdHdms_Ef67HsONNv5A60JaR7w8LHnDiBGnjdaUmmuO8XAxQ
         J_ia5mxjxNjS6E2yD44USo2JmHvzeeNczq25elqbTPLhUpGo1IZuG72F
         ZQ5gTjXoTXC2-xtCDEUZfaUNh4IeAipfLugbpe0JAFlFfrTDAMUFpC3i
         XjxqzbEanflwPvj6V9iDSgjj8SozSM0dLtxvu0LIeIQAeEgT_yXcrKGm
         pKdSO08kLBx8VUjkbv_3Pn20Gyu2YEuwpFlM_H1NikuxJNKFGmnAq9Lc
         nwwT0jvoQ",
     "dq": "S6p59KrlmzGzaQYQM3o0XfHCGvfqHLYjCO557HYQf72O9kLMCfd_1
         VBEqeD-1jjwELKDjck8kOBl5UvohK1oDfSP1DleAy-cnmL29DqWmhgwM
         1ip0CCNmkmsmDSlqkUXDi6sAaZuntyukyflI-qSQ3C_BafPyFaKrt1fg
         dyEwYa08pESKwwWisy7KnmoUvaJ3SaHmohFS78TJ25cfc10wZ9hQNOrI
         ChZlkiOdFCtxDqdmCqNacnhgE3bZQjGp3n83ODSz9zwJcSUvODlXBPc2
         AycH6Ci5yjbxt4Ppox_5pjm6xnQkiPgj01GpsUssMmBN7iHVsrE7N2iz
         nBNCeOUIQ",
     "qi": "FZhClBMywVVjnuUud-05qd5CYU0dK79akAgy9oX6RX6I3IIIPckCc
         iRrokxglZn-omAY5CnCe4KdrnjFOT5YUZE7G_Pg44XgCXaarLQf4hl80
         oPEf6-jJ5Iy6wPRx7G2e8qLxnh9cOdf-kRqgOS3F48Ucvw3ma5V6KGMw
         QqWFeV31XtZ8l5cVI-I3NzBS7qltpUVgz2Ju021eyc7IlqgzR98qKONl
         27DuEES0aK0WE97jnsyO27Yp88Wa2RiBrEocM89QZI1seJiGDizHRUP4
         UZxw9zsXww46wy0P6f9grnYp7t8LkyDDk8eoI4KX6SNMNVcyVS9IWjlq
         8EzqZEKIA"
   }
```
<ul>
  <li>The <code>alg</code> parameter – value MUST be set to <code>RSA-OAEP</code> as defined in the <a href=" #OpenIDConfig" target="blank">itsme® Discovery document</a></li>
  <li>The <code>enc</code> parameter – value MUST be set to <code>A128CBC-HS256</code> as defined in the <a href=" #OpenIDConfig" target="blank">itsme® Discovery document</a></li>
</ul>

### Generated factors

The following are generated before encrypting:

<ul>
  <li>AES symmetric key as the Content Encryption Key (CEK); in our example it will be the one enclosed.</li>
</ul>
```http--inline
mYMfsggkTAm0TbvtlFh2hyoXnbEzJQjMxmgLN3d8xXA
```
<ul>
  <li>Initialization Vector; in our example it will be the one enclosed.</li>
</ul>
```http--inline
-nBoKLH0YkLZPSI9
```

### Encrypting the key

Performing the key encryption operation over the CEK with the RSA key produces the Encrypted Key aside.
```http--inline
rT99rwrBTbTI7IJM8fU3Eli7226HEB7IchCxNuh7lCiud48LxeolRdtFF4nzQibeYOl5S_PJsAXZwSXtDePz9hk-BbtsTBqC2UsPOdwjC9NhNupNNu9uHIVftDyu  cvI6hvALeZ6OGnhNV4v1zx2k7O1D89mAzfw-_kT3tkuorpDU-CpBENfIHX1Q58-Aad3FzMuo3Fn9buEP2yXakLXYa15BUXQsupM4A1GD4_H4Bd7V3u9h8Gkg8Bpx   KdUV9ScfJQTcYm6eJEBz3aSwIaK4T3-dwWpuBOhROQXBosJzS1asnuHtVMt2pKIIfux5BC6huIvmY7kzV7W7aIUrpYm_3H4zYvyMeq5pGqFmW2k8zpO878TRlZx7
   pZfPYDSXZyS0CfKKkMozT_qiCwZTSz4duYnt8hS4Z9sGthXn9uDqd6wycMagnQfOTs_lycTWmY-aqWVDKhjYNRf03NiwRtb5BE-tOdFwCASQj3uuAgPGrO2AWBe3
   8UjQb0lvXn1SpyvYZ3WFc7WOJYaTa7A8DRn6MC6T-xDmMuxC0G7S2rscw5lQQU06MvZTlFOt0UvfuKBa03cxA_nIBIhLMjY2kOTxQMmpDPTr6Cbo8aKaOnx6ASE5
   Jx9paBpnNmOOKH35j_QlrQhDWUN6A2Gg8iFayJ69xDEdHAVCGRzN3woEI2ozDRs
```  

### Encrypting the content

```http--inline
   {
     "alg": "RSA-OAEP",
     "kid": "samwise.gamgee@hobbiton.example",
     "enc": "A128CBC-HS256"
   }
```
First, you need to generate JWE Protected Header. It contains the <code>alg</code> parameter, a reference to the signing key (aka. <code>kid</code>) and the <code>enc</code>. An example can be visualized enclosed.

The JWE Protected Header will then be encoded using base64url to produce the string aside.
```http--inline
eyJhbGciOiJSU0EtT0FFUCIsImtpZCI6InNhbXdpc2UuZ2FtZ2VlQGhvYmJpdG9uLmV4YW1wbGUiLCJlbmMiOiJBMjU2R0NNIn0
```

Then, you can perform the content encryption operation over the plaintext - with the CEK; the Initialization Vector; and  JWE Protected Header as authenticated data - to produce the Ciphertext and the Authentication Tag as you see enclosed.

```http--inline
o4k2cnGN8rSSw3IDo1YuySkqeS_t2m1GXklSgqBdpACm6UJuJowOHC5ytjqYgRL-I-soPlwqMUf4UgRWWeaOGNw6vGW-xyM01lTYxrXfVzIIaRdhYtEMRBvBWbEw P7ua1DRfvaOjgZv6Ifa3brcAM64d8p5lhhNcizPersuhw5f-pGYzseva-TUaL8iWnctc-sSwy7SQmRkfhDjwbz0fz6kFovEgj64X1I5s7E6GLp5fnbYGLa1QUiML   7Cc2GxgvI7zqWo0YIEc7aCflLG1-8BboVWFdZKLK9vNoycrYHumwzKluLWEbSVmaPpOslY2n525DxDfWaVFUfKQxMF56vn4B9QMpWAbnypNimbM8zVOw
```

```http--inline
UCGiqJxhBI3IFVdPalHHvA
```




xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx


  
  </li>
  <li>The <code>enc</code> parameter – value MUST be set to <code>A128CBC-HS256</code> as defined in the <a href=" #OpenIDConfig" target="blank">itsme® Discovery document</a></li>
</ul>







Cryptographic algorithms and identifiers when using







