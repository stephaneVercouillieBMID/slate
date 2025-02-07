---
title: itsme® OpenID Connect documentation

language_tabs: # must be one of https://git.io/vQNgJ
 - json: 

toc_footers:
 - <a href='#'>Sign Up for a Developer Key</a> -->
 - <a href='https://github.com/lord/slate'>Documentation Powered by Slate</a>

search: true
---
# 1. Introduction

The concepts of JWT, JWS, JWE and JWK are part of the JSON Object Signing and Encryption (JOSE) framework that intends to provide a method to securely transfer claims between parties.

All these technologies can be used collectively to encrypt and/or sign content using a variety of algorithms. While the full set of permutations is extremely large, and might be daunting to some, it is expected that most applications will only use a small set of algorithms to meet their needs.


# 2. Definitions

The JSON Object Signing and Encryption (JOSE) framework consists of several technologies:

<ul>
  <li>JSON Web Token (<a href=" https://tools.ietf.org/html/rfc7519" target="blank">JWT</a>) is a compact, URL-safe means of representing claims to be transferred between two parties. The claims in a JWT are encoded as a JSON object that is used as the payload of a JSON Web Signature (JWS) structure, as the plaintext of a JSON Web Encryption (JWE) structure or enclosed in another JWE or JWS structure to create a Nested JWT, enabling nested signing and encryption to be performed. In fact a JWT does not exist itself — either it has to be a JWS and/or a JWE. It’s like an abstract class — the JWS and JWE are the concrete implementations.</li>
  <li>JSON Web Signature (<a href=" https://tools.ietf.org/html/rfc7515" target="blank">JWS</a>) represents signed content using JSON data structures and base64url encoding as defined in the specifications.</li>
  <li>JSON Web Encryption (<a href=" https://tools.ietf.org/html/rfc7516" target="blank">JWE</a>) specification standardizes the way to represent an encrypted content in a JSON-based data structure.</li>
  <li>JSON Web Key (<a href=" https://tools.ietf.org/html/rfc7517" target="blank">JWK</a>) defines a consistent way to represent a cryptographic key in a JSON structure which is used to sign (JWS) and/or encrypt (JWE) a specific content. The JSON Web Key Set (JWKS) extension defines a consistent way to represent a set of cryptographic keys in a JSON structure.<br>As itsme® only support the RSA cryptosystem, it requires that each party exposes its public keys as a simple JWK Set document on a URI accessible to all.</br>
    <ul>
      <li>For itsme®, this URI can be retrieved from the <a href="https://belgianmobileid.github.io/slate/login.html#3-1-checking-itsme-openid-provider-configuration" target="blank">itsme® Discovery document</a>, using the <i>"jwks_uri"</i> key.</li>
      <li>Your JWK Set document MUST be accessible via the URI communicated when setting up your project in the <a href="https://brand.belgianmobileid.be/d/CX5YsAKEmVI7" target="blank">itsme® B2B portal</a>.</li>
    </ul></li>
</ul>


# 3. Creating your JWK Set URI

The JWK is nothing more than another representation of the (public) key of a certificate keypair and the JWK Set is simply a list of one or more JWK.

So, you need to have/create your certificate key pairs (a key pair consists of a public key and private key): they’ll need one pair for encryption and one pair for signing actions.

You can use your certificates or (preferably) get them through a certificate authority. 

Next, you will need to ‘convert’ the PUBLIC keys (NOT the private key. You MUST keep the private key always private) to JWK and group them in a JWK Set. There are various tools that can be used and nearly all software development language have support or extensions to be able to do that. 

The resulting JWK Set MUST contain (at least) 2 JWK: one whereby the “use” parameter is ”enc” (for encryption) and another with “sig” (for signature).

This JWK Set is nothing more than a plain text file (“JSON” format) and you should place that on a publicly available website (https off course) and share that link with us when registering your project in the [itsme® B2B portal](#Onboarding). As these are the PUBLIC keys, there is no problem to share this information publicly (in other words, it should not be placed behind a password or login screen).

Some more (technical) documentation on <a href="https://tools.ietf.org/html/rfc7517" target="blank">https://tools.ietf.org/html/rfc7517</a>.


# 4. Signing, encrypting and decrypting

## 4.1. Signing

A signed content can be serialized in two ways: the JWS compact serialization and the JWS JSON serialization. Because the OpenID Connect specification mandates to use JWS compact serialization whenever necessary, we will not explain the JWS JSON serialization signing process in this document.

Following steps will show you how to generate a JWS Compact Serialization object:

<ol>
  <li>Build a JSON object including the below header elements, which express the cryptographic properties of the JWS object — this is known as the JWS Header.</li>
</ol>

Parameter | Required | Description
:-------- | :--------| :----- 
**alg** | Required | This parameter identifies the cryptographic algorithm used to secure the JWS. This MUST be set to <i>"RS256"</i>.
**kid** | Optionnal | It is a hint indicating which key was used to secure the JWS. The structure of the <i>"kid"</i> value is unspecified. In case there are multiple signing keys referenced in your JWK Set document than it MUST be a case-sensitive string.

<ol>  
  <li value="2">The JWS Header will then be encoded using UTF-8 and base64url to produce the string below.</li>
</ol>

<code style=display:block;white-space:pre-wrap>eyJhbGciOiJSUzI1NiIsImtpZCI6ImJpbGJvLmJhZ2dpbnNAaG9iYml0b24uZXhhbXBsZSJ9</code>

<ol>
  <li value="3">Construct the payload or the content to be signed as UTF-8 — this is known as the JWS Payload.</li>
  <li>The JWS Payload will then be encoded using base64url to produce the string below.</li>
</ol>

<code style=display:block;white-space:pre-wrap>SXTigJlzIGEgZGFuZ2Vyb3VzIGJ1c2luZXNzLCBGcm9kbywgZ29pbmcgb3V0IHlvdXIgZG9vci4gWW91IHN0ZXAgb250byB0aGUgcm9hZCwgYW5kIGlmIHlvdSBkb24ndCBrZWVwIHlvdXIgZmVldCwgdGhlcmXigJlzIG5vIGtub3dpbmcgd2hlcmUgeW91IG1pZ2h0IGJlIHN3ZXB0IG9mZiB0by</code>

<ol>
  <li value="5">Combine the JWS Header and JWS Payload, and separate them with period ('.') characters, to produce the JWS Signing Input.</li>
  <li>Complete the signing operation over the JWS Signing Input with the algorithm defined in the <i>"alg"</i> parameter, to produce the JWS Signature. The JWS Signing Input is signed using your private key corresponding to the public key referenced in your JWK Set document. This information SHOULD be made available via the URI you communicated when setting up your project in the <a href="https://brand.belgianmobileid.be/d/CX5YsAKEmVI7" target="blank">itsme® B2B portal</a>.</li>
  <li>The JWS Signature will then be encoded using base64url to produce the string below.</li>
</ol>

<code style=display:block;white-space:pre-wrap>MRjdkly7_-oTPTS3AXP41iQIGKa80A0ZmTuV5MEaHoxnW2e5CZ5NlKtainoFmKZopdHM1O2U4mwzJdQx996ivp83xuglII7PNDi84wnB-BDkoBwA78185hX-Es4JIwmDLJK3lfWRa-XtL0RnltuYv746iYTh_qHRD68BNt1uSNCrUCTJDt5aAE6x8wW1Kt9eRo4QPocSadnHXFxnt8Is9UzpERV0ePPQdLuW3IS_de3xyIrDaLGdjluPxUAhb6L2aXic1U12podGU0KLUQSE_oI-ZnmKJ3F4uOZDnd6QZWJushZ41Axf_fcIe8u9ipH84ogoree7vjbU5y18kDquDg</code>

<ol>
  <li value="5">Finally, you are now be able to build the JWS object by concatenating the three strings, and separate them with period ('.') characters. An example of a JWS object is given below.</li>
</ol>

<code style=display:block;white-space:pre-wrap>eyJhbGciOiJSUzI1NiIsImtpZCI6ImJpbGJvLmJhZ2dpbnNAaG9iYml0b24uZXhhbXBsZSJ9
.
SXTigJlzIGEgZGFuZ2Vyb3VzIGJ1c2luZXNzLCBGcm9kbywgZ29pbmcgb3V0IHlvdXIgZG9vci4gWW91IHN0ZXAgb250byB0aGUgcm9hZCwgYW5kIGlmIHlvdSBkb24ndCBrZWVwIHlvdXIgZmVldCwgdGhlcmXigJlzIG5vIGtub3dpbmcgd2hlcmUgeW91IG1pZ2h0IGJlIHN3ZXB0IG9mZiB0by4
.
MRjdkly7_-oTPTS3AXP41iQIGKa80A0ZmTuV5MEaHoxnW2e5CZ5NlKtainoFmKZopdHM1O2U4mwzJdQx996ivp83xuglII7PNDi84wnB-BDkoBwA78185hX-Es4JIwmDLJK3lfWRa-XtL0RnltuYv746iYTh_qHRD68BNt1uSNCrUCTJDt5aAE6x8wW1Kt9eRo4QPocSadnHXFxnt8Is9UzpERV0ePPQdLuW3IS_de3xyIrDaLGdjluPxUAhb6L2aXic1U12podGU0KLUQSE_oI-ZnmKJ3F4uOZDnd6QZWJushZ41Axf_fcIe8u9ipH84ogoree7vjbU5y18kDquDg</code>


## 4.2. Encryption

Now that our content is signed, we will encapsulate this JWS object into a JWE object so that it becomes encrypted as well.

An encrypted content can be serialized in two ways: the JWE compact serialization and the JWE JSON serialization. Because the OpenID Connect specification mandates to use JWE compact serialization whenever necessary, we will not explain the JWE JSON serialization signing process in this document.

With the JWE compact serialization, a JWE is the concatenation of 5 elements:

1. The **JWE Header** contains information about the encryption algorithms used.
2. The encrypted **Content Encryption Key (CEK)** contains the key needed to decrypt the payload.
3. The **Initialization Vector** is used to introduce randomness in the encryption process.
4. The **Cipher text** is the encrypted payload itself. In this case, the payload is our JWS object.
5. The **Authentication Tag** is used for integrity checks.

Here are the steps to generate those elements:

1. Create the JWS object, as described in the section above.</li>
2. Create the JWE Header: it is a JSON object containing 4 parameters.

```json
## JWE Header Example

{
    "alg": "RSA-OAEP",
    "enc": "A128CBC-HS256",
    "cty": "JWT",
    "kid": "samwise.gamgee@hobbiton.example"
}
```

Parameter | Required | Description
:-------- | :--------| :----- 
**alg** | Required | Defines the algorithm used to encrypt the Content Encryption Key (CEK). This MUST be set to *"RSA-OAEP"*.
**enc** | Required | Defines the algorithm used to perform authenticated encryption on the payload to produce the Ciphertext and the Authentication Tag. This MUST be set to *"A128CBC-HS256"*.
**cty** | Required | Defines the “content type” of the payload. In this case, the value MUST be *"JWT"*, to indicate that a nested JWT (= our JWS) is carried inside this JWT.
**kid** | Optionnal | This parameter has the same meaning, syntax, and processing rules as the "kid" parameter defined in the JWS section, except that the key hint references the public key with which the JWE was encrypted; this can be used to determine the private key needed to decrypt the JWE.

<ol>  
  <li value="3">Base64url encode the (UTF-8 encoded) JWE Header, to obtain a string like:</li>
</ol>

<code style=display:block;white-space:pre-wrap>eyJhbGciOiJSU0ExXzUiLCJraWQiOiJmcm9kby5iYWdnaW5zQGhvYmJpdG9uLmV4YW1wbGUiLCJlbmMiOiJBMTI4Q0JDLUhTMjU2In0</code>

<ol>
  <li value="4">Generate a random 32 bytes string to be used as CEK</li>
  <li>Encrypt the CEK using RSA-OAEP algorithm (<a href="https://tools.ietf.org/html/rfc7518#section-4.3">RFC7518</a>).
      The key to be used for this encryption is itsme® public key. This key is published in itsme® JWKset with parameter "use" set on "enc". Our JWKset can be found at the jwks_uri URL given in <a href="https://belgianmobileid.github.io/slate/login.html#3-1-check-itsme-openid-provider-configuration">itsme® Discovery document</a></li>
  <li>Base64url encode the (UTF-8 encoded) encrypted CEK, to obtain a string like:</li>
</ol>

<code style=display:block;white-space:pre-wrap>laLxI0j-nLH-_BgLOXMozKxmy9gffy2gTdvqzfTihJBuuzxg0V7yk1WClnQePFvG2K-pvSlWc9BRIazDrn50RcRai__3TDON395H3c62tIouJJ4XaRvYHFjZTZ2GXfz8YAImcc91Tfk0WXC2F5Xbb71ClQ1DDH151tlpH77f2ff7xiSxh9oSewYrcGTSLUeeCt36r1Kt3OSj7EyBQXoZlN7IxbyhMAfgIe7Mv1rOTOI5I8NQqeXXW8VlzNmoxaGMny3YnGir5Wf6Qt2nBq4qDaPdnaAuuGUGEecelIO1wx1BpyIfgvfjOhMBs9M8XL223Fg47xlGsMXdfuY-4jaqVw
</code>
 
<ol>
  <li value="7">Generate a random 128 bits string to be used as Initialization Vector.</li>
  <li>Base64url encode the Initialization Vector to obtain a string like:</li>
</ol>

<code style=display:block;white-space:pre-wrap>bbd5sTkYwhAIqfHsx8DayA</code>

<ol>
  <li value="9">Compute the ASCII values of the characters of the encoded JWE Header to get the Additional Authenticated Data (AAD). In our example, that would give:</li>
</ol>

<code style=display:block;white-space:pre-wrap>101 121 74 104 98 71 99 105 79 105 74 83 85 48 69 120 88 122 85 105 76 67 74 114 97 87 81 105 79 105 74 109 99 109 57 107 98 121 53 105 89 87 100 110 97 87 53 122 81 71 104 118 89 109 74 112 100 71 57 117 76 109 86 52 89 87 49 119 98 71 85 105 76 67 74 108 98 109 77 105 79 105 74 66 77 84 73 52 81 48 74 68 76 85 104 84 77 106 85 50 73 110 48
</code>

<ol>
  <li value="10">Encrypt the JWS using A128CBC-HS256 algorithm. The algorithm takes as input four strings: the CEK, the AAD, the JWE Initialization Vector and the payload (= the JWS) which were computed in the previous steps. The algorithm returns two string outputs: the JWE Ciphertext and the Authentication Tag.
  <li>Base64url-encode the JWE Ciphertext.</li>
  <li>Base64url-encode the JWE Authentication Tag.</li>
  <li>Assemble the final representation by concatenating the five base64url encoded strings, and separate them with period ('.') characters. An example of a JWE object is given below.</li>
</ol>

<code style=display:block;white-space:pre-wrap>eyJhbGciOiJSU0ExXzUiLCJraWQiOiJmcm9kby5iYWdnaW5zQGhvYmJpdG9uLmV4YW1wbGUiLCJlbmMiOiJBMTI4Q0JDLUhTMjU2In0
.
laLxI0j-nLH-_BgLOXMozKxmy9gffy2gTdvqzfTihJBuuzxg0V7yk1WClnQePFvG2K-pvSlWc9BRIazDrn50RcRai__3TDON395H3c62tIouJJ4XaRvYHFjZTZ2GXfz8YAImcc91Tfk0WXC2F5Xbb71ClQ1DDH151tlpH77f2ff7xiSxh9oSewYrcGTSLUeeCt36r1Kt3OSj7EyBQXoZlN7IxbyhMAfgIe7Mv1rOTOI5I8NQqeXXW8VlzNmoxaGMny3YnGir5Wf6Qt2nBq4qDaPdnaAuuGUGEecelIO1wx1BpyIfgvfjOhMBs9M8XL223Fg47xlGsMXdfuY-4jaqVw
.
bbd5sTkYwhAIqfHsx8DayA
.
0fys_TY_na7f8dwSfXLiYdHaA2DxUjD67ieF7fcVbIR62JhJvGZ4_FNVSiGc_raa0HnLQ6s1P2sv3Xzl1p1l_o5wR_RsSzrS8Z-wnI3Jvo0mkpEEnlDmZvDu_k8OWzJv7eZVEqiWKdyVzFhPpiyQU28GLOpRc2VbVbK4dQKPdNTjPPEmRqcaGeTWZVyeSUvf5k59yJZxRuSvWFf6KrNtmRdZ8R4mDOjHSrM_s8uwIFcqt4r5GX8TKaI0zT5CbL5Qlw3sRc7u_hg0yKVOiRytEAEs3vZkcfLkP6nbXdC_PkMdNS-ohP78T2O6_7uInMGhFeX4ctHG7VelHGiT93JfWDEQi5_V9UN1rhXNrYu-0fVMkZAKX3VWi7lzA6BP430m
.
kvKuFBXHe5mQr4lqgobAUg</code>



## 4.3. Decrypting

To validate a Nested JWT object, you will first need to decrypt the JWE object, then extract the signed JWS Payload and verify the JWS Signature. 

**Decrypting the JWE object**

The decryption process is the reverse of the encryption process:

<ol>
  <li>Parse the JWE object to extract the serialized values of the base64url-encoded JWE Header, the base64url-encoded JWE Encrypted Key, the base64url-encoded JWE  Initialization Vector, the base64url-encoded JWE Ciphertext, and the base64url-encoded JWE Authentication Tag.</li>
  <li>Base64url decode the encoded representations of the JWE Header, the JWE Encrypted Key, the JWE Initialization  Vector, the JWE Ciphertext, the JWE Authentication Tag, and the JWE Additional Authenticated Data (AAD).</li>
  <li>Verify that the octet sequence resulting from decoding the encoded JWE Header is a UTF-8-encoded representation of a completely valid JSON object.</li>
  <li>Determine the algorithm specified by the <i>"alg"</i> parameter in the JWE Header.</li>
  <li>Decrypt the JWE Encrypted Key with the algorithm defined in the <i>"alg"</i> parameter, to produce the Content Encryption Key (CEK). The JWE Encrypted Key is decoded using your private key corresponding to the public key referenced in your JWK Set document. This information SHOULD be made available via the URI you communicated when setting up your project in the <a href="https://brand.belgianmobileid.be/d/CX5YsAKEmVI7" target="blank">itsme® B2B portal</a>.</li></li> 
  <li>Let the Additional Authenticated Data (AAD) encryption parameter be the octets of the ASCII representation of the encoded JWE Header value.</li>
  <li>Decrypt the JWE Ciphertext with the encryption algorithm defined by the <i>"enc"</i> parameter in the JWE Header. It will return the decrypted JWS object.</li>
</ol>

**Extracting the payload**

<ol>
  <li>Parse the JWS object to extract the JWS components serialized values, such as in the example below.</li>
</ol>

<code style=display:block;white-space:pre-wrap>jws_header = 
"eyJhbGciOiJSUzI1NiIsImtpZCI6ImJpbGJvLmJhZ2dpbnNAaG9iYml0b24uZXhhbXBsZSJ9"
payload = 
"SXTigJlzIGEgZGFuZ2Vyb3VzIGJ1c2luZXNzLCBGcm9kbywgZ29pbmcgb3V0IHlvdXIgZG9vci4gWW91IHN0ZXAgb250byB0aGUgcm9hZCwgYW5kIGlmIHlvdSBkb24ndCBrZWVwIHlvdXIgZmVldCwgdGhlcmXigJlzIG5vIGtub3dpbmcgd2hlcmUgeW91IG1pZ2h0IGJlIHN3ZXB0IG9mZiB0by4"
signature = 
"MRjdkly7_-oTPTS3AXP41iQIGKa80A0ZmTuV5MEaHoxnW2e5CZ5NlKtainoFmKZopdHM1O2U4mwzJdQx996ivp83xuglII7PNDi84wnB-BDkoBwA78185hX-Es4JIwmDLJK3lfWRa-XtL0RnltuYv746iYTh_qHRD68BNt1uSNCrUCTJDt5aAE6x8wW1Kt9eRo4QPocSadnHXFxnt8Is9UzpERV0ePPQdLuW3IS_de3xyIrDaLGdjluPxUAhb6L2aXic1U12podGU0KLUQSE_oI-ZnmKJ3F4uOZDnd6QZWJushZ41Axf_fcIe8u9ipH84ogoree7vjbU5y18kDquDg"</code>

<ol>
  <li value="2">Base64url-decode the encoded representation of the JWS Header.</li>
  <li>Verify that the resulting octet sequence is a UTF-8-encoded representation of a completely valid JSON object.</li>
</ol>

<code style=display:block;white-space:pre-wrap>{
   "alg":"RS256",
   "kid":"3466d51f7dd0c780565688c183921816c45889ad"
}</code>

<ol>
  <li value="4">Base64url-decode the encoded representation of the JWS Payload.</li>
</ol>

<code style=display:block;white-space:pre-wrap>{
   "sub":"joe",
   "aud":"im_oic_client",
   "jti":"uf90SK4wscFhctUT6Dtvb2",
   "iss":"https:\/\/localhost:9031",
   "iat":1394060853,
   "exp":1394061153,
   "nonce":"e957ffba-9a78-4ea9-8eca-ae8c4ef9c856",
   "at_hash":"wfgvmE9VxjAudsl9lc6TqA"
}</code>

<ol>
  <li value="5">Base64url-decode the encoded representation of the JWS Signature.</li>
</ol>

<code style=display:block;white-space:pre-wrap>XiyNdHHHoYqarDZGkhln5sF_SQNNVvV67SZsFAk7yo8NreJjzVw7LmtkwpiUQe87-Km39PeIwf1W_PqEH9RqjA</code>

You have decoded the JWS object. The next step is to validate the JWS Signature.

**Checking the JWS Signature**

<ol>
  <li>Determine the signing algorithm <i>"alg"</i> and the key identifier <i>"kid"</i> from the JWS Header.</li>
  <li>Validate the JWS Signature in the manner defined for the algorithm being used, which MUST be accurately represented by the value of the <i>"alg"</i> parameter. The signature is verified using the public key of itsme®, which can be retrieved from the <a href="https://belgianmobileid.github.io/slate/login.html#3-1-checking-itsme-openid-provider-configuration" target="blank">itsme® Discovery document</a>, using the <i>"jwks_uri"</i> key. If the returned itsme® JSON Web Key (JWK) contains an array of keys, you MUST use the one corresponding to the value given by the <i>"kid"</i> parameter from the JWS Header.</li>
</ol>
    








