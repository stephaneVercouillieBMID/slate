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

The concepts of JWT, JWS and JWE are part of the JSON Object Signing and Encryption (JOSE) framework that intends to provide a method to securely transfer claims between parties.

All these technologies can be used collectively to encrypt and/or sign content using a variety of algorithms. While the full set of permutations is extremely large, and might be daunting to some, it is expected that most applications will only use a small set of algorithms to meet their needs.


# 2. Definitions

The JSON Object Signing and Encryption (JOSE) framework consists of several technologies:

<ul>
  <li>JSON Web Token (<a href=" https://tools.ietf.org/html/rfc7519" target="blank">JWT</a>) is a compact, URL-safe means of representing claims to be transferred between two parties. The claims in a JWT are encoded as a JSON object that is used as the payload of a JSON Web Signature (JWS) structure, as the plaintext of a JSON Web Encryption (JWE) structure or enclosed in another JWE or JWS structure to create a Nested JWT, enabling nested signing and encryption to be performed. In fact a JWT does not exist itself — either it has to be a JWS and/or a JWE. It’s like an abstract class — the JWS and JWE are the concrete implementations.</li>
  <li>JSON Web Signature (<a href=" https://tools.ietf.org/html/rfc7515" target="blank">JWS</a>) represents signed content using JSON data structures and base64url encoding as defined in the specifications.</li>
  <li>JSON Web Encryption (<a href=" https://tools.ietf.org/html/rfc7516" target="blank">JWE</a>) specification standardizes the way to represent an encrypted content in a JSON-based data structure.</li>
</ul>


# 3. Generating a JWS object

A signed content can be serialized in two ways: the JWS compact serialization and the JWS JSON serialization. Because the OpenID Connect specification mandates to use JWS compact serialization whenever necessary, we will not explain the JWS JSON serialization signing process in this document.

Following steps will show you how to generate a JWS Compact Serialization object:

<ol>
  <li>Build a JSON object including all the header elements, which express the cryptographic properties of the JWS object — this is known as the JWS Header. Don't forget to advertise in the JWS Header, the public key corresponding to the key used to sign the message. This can be expressed via any of these header elements:  <code>jku</code>, <code>jwk</code>, <code>kid</code>, <code>x5u</code>, <code>x5c</code>, <code>x5t</code> and <code>x5t#s256</code>.<br>An example can be found below.</br></li>
</ol>


<div class="center-column"></div>
```json--inline
{
   "alg": "RS256",
   "crit": ["exp"],
   "exp": 1363284000
}
```


Parameter | Required | Description
:-------- | :--------| :----- 
**alg** | Required | This parameter identifies the cryptographic algorithm used to secure the JWS. The <code>alg</code> value for this use can be found in the <a href="https://belgianmobileid.github.io/slate/login.html#3-1-checking-itsme-openid-provider-configuration" target="blank">itsme® Discovery document</a>, using one of these keys: <code>request_object_signing_alg_values_supported</code>, <code>id_token_signing_alg_values_supported</code>, <code>token_endpoint_auth_signing_alg_values_supported</code> or <code>userinfo_signing_alg_values_supported</code>. 
**jku** | Optionnal | This is a URI that refers to a resource for a set of JSON-encoded public keys, one of which corresponds to the key used to digitally sign the JWS.  The keys MUST be encoded as a JWK Set (JWK).
**jwk** | Optionnal | This is the public key that corresponds to the key used to digitally sign the JWS.  This key is represented as a JSON Web Key (JWK).
**kid** | Optionnal | It is a hint indicating which key was used to secure the JWS.  This parameter allows you to explicitly signal a change of key to recipients.  The structure of the <code>kid</code> value is unspecified.  Its value MUST be a case-sensitive string.
**x5u** | Optionnal | It is a URI that refers to a resource for the X.509 public key certificate or certificate chain corresponding to the key used to digitally sign the JWS.
**x5c** | Optionnal | This parameter contains the X.509 public key certificate or certificate chain corresponding to the key used to digitally sign the JWS.
**x5t** | Optionnal | It is a base64url-encoded SHA-1 thumbprint (a.k.a. digest) of the DER encoding of the X.509 certificate corresponding to the key used to digitally sign the JWS. 
**x5t#S256** | Optionnal | It is a base64url-encoded SHA-256 thumbprint (a.k.a. digest) of the DER encoding of the X.509 certificate corresponding to the key used to digitally sign the JWS.
**typ** | Optionnal | It is used by JWS applications to declare the media type of this complete JWS.
**cty** | Optionnal | It is used by JWS applications to declare the media type of the payload.
**crit** | Optionnal | It indicates that extensions to this specification and/or JWA are being used that MUST be  understood and processed.

<ol>  
  <li value="2">The JWS Header will then be encoded using UTF-8 and base64url to produce the string below.</li>
</ol>


<div class="center-column"></div>
```plaintext--inline
eyJhbGciOiJSUzI1NiIsImtpZCI6ImJpbGJvLmJhZ2dpbnNAaG9iYml0b24uZX
hhbXBsZSJ9
```


<ol>
  <li value="3">Construct the payload or the content to be signed as UTF-8 — this is known as the JWS Payload.</li>
  <li>The JWS Payload will then be encoded using base64url to produce the string below.</li>
</ol>


<div class="center-column"></div>
```plaintext--inline
SXTigJlzIGEgZGFuZ2Vyb3VzIGJ1c2luZXNzLCBGcm9kbywgZ29pbmcgb3V0IH
lvdXIgZG9vci4gWW91IHN0ZXAgb250byB0aGUgcm9hZCwgYW5kIGlmIHlvdSBk
b24ndCBrZWVwIHlvdXIgZmVldCwgdGhlcmXigJlzIG5vIGtub3dpbmcgd2hlcm
UgeW91IG1pZ2h0IGJlIHN3ZXB0IG9mZiB0by
```


<ol>
  <li value="5">Combine the JWS Header and JWS Payload, and separate them with period ('.') characters, to produce the JWS Signing Input.</li>
  <li>Complete the signing operation over the JWS Signing Input constructed in the previous step, following the signature algorithm defined by the JWS Header element <code>alg</code>. The JWS Signing Input is signed using the private key corresponding to the public key advertised in the JOSE Header. Performing the signature operation over the JWS Signing Input produces the JWS Signature.</li>
  <li>The JWS Signature will then be encoded using base64url to produce the string below.</li>
</ol>
  
  
<div class="center-column"></div>
```plaintext--inline
MRjdkly7_-oTPTS3AXP41iQIGKa80A0ZmTuV5MEaHoxnW2e5CZ5NlKtainoFmK
ZopdHM1O2U4mwzJdQx996ivp83xuglII7PNDi84wnB-BDkoBwA78185hX-Es4J
IwmDLJK3lfWRa-XtL0RnltuYv746iYTh_qHRD68BNt1uSNCrUCTJDt5aAE6x8w
W1Kt9eRo4QPocSadnHXFxnt8Is9UzpERV0ePPQdLuW3IS_de3xyIrDaLGdjluP
xUAhb6L2aXic1U12podGU0KLUQSE_oI-ZnmKJ3F4uOZDnd6QZWJushZ41Axf_f
cIe8u9ipH84ogoree7vjbU5y18kDquDg
```


<ol>
  <li value="5">Finally, you are now be able to build the JWS object by concatenating the three strings, and separate them with period ('.') characters. An example of a JWS object is given below.</li>
</ol>


<div class="center-column"></div>
```plaintext--inline
eyJhbGciOiJSUzI1NiIsImtpZCI6ImJpbGJvLmJhZ2dpbnNAaG9iYml0b24uZX
hhbXBsZSJ9
.
SXTigJlzIGEgZGFuZ2Vyb3VzIGJ1c2luZXNzLCBGcm9kbywgZ29pbmcgb3V0IH
lvdXIgZG9vci4gWW91IHN0ZXAgb250byB0aGUgcm9hZCwgYW5kIGlmIHlvdSBk
b24ndCBrZWVwIHlvdXIgZmVldCwgdGhlcmXigJlzIG5vIGtub3dpbmcgd2hlcm
UgeW91IG1pZ2h0IGJlIHN3ZXB0IG9mZiB0by4
.
MRjdkly7_-oTPTS3AXP41iQIGKa80A0ZmTuV5MEaHoxnW2e5CZ5NlKtainoFmK
ZopdHM1O2U4mwzJdQx996ivp83xuglII7PNDi84wnB-BDkoBwA78185hX-Es4J
IwmDLJK3lfWRa-XtL0RnltuYv746iYTh_qHRD68BNt1uSNCrUCTJDt5aAE6x8w
W1Kt9eRo4QPocSadnHXFxnt8Is9UzpERV0ePPQdLuW3IS_de3xyIrDaLGdjluP
xUAhb6L2aXic1U12podGU0KLUQSE_oI-ZnmKJ3F4uOZDnd6QZWJushZ41Axf_f
cIe8u9ipH84ogoree7vjbU5y18kDquDg
```


An example illustrating the signing process is available at <a href="https://tools.ietf.org/html/rfc7520#section-4.1" target="blank">https://tools.ietf.org/html/rfc7520#section-4.1</a>.


# 4. Producing a JWE object

An encrypted content can be serialized in two ways: the JWE compact serialization and the JWE JSON serialization. Because the OpenID Connect specification mandates to use JWE compact serialization whenever necessary, we will not explain the JWE JSON serialization signing process in this document.

With the JWE compact serialization, a JWE object is built with five key components, each separated by a period (.): JWE Header, JWE Encrypted Key, JWE Initialization Vector, JWE Additional Authentication Data (AAD), JWE Ciphertext and JWE Authentication Tag. The following steps will show you how to generate a JWE Compact Serialization object:

<ol>
  <li>Build a JSON object including all the header elements. The structure of the JWE Header is the same, as we discussed under JWS other than couple of exceptions. The JWE specification introduces two new elements (<code>enc</code> and <code>zip</code>), which are included in the JWE Header of the JWE object, in addition to what’s defined by the JSON Web Signature (JWS) specification.<br>An example use can be found below.</br></li>
</ol>


<div class="center-column"></div>
```json--inline
{
    "alg": "RSA-OAEP",
    "kid": "samwise.gamgee@hobbiton.example",
    "enc": "A128CBC-HS256"
}
```


Parameter | Required | Description
:-------- | :--------| :----- 
**alg** | Required | This parameter has the same meaning, syntax, and processing rules as the <code>alg</code> defined in the JWS section, except that it defines the cryptographic algorithm used to encrypt the CEK. The <code>alg</code> value for this use can be found in the <a href="https://belgianmobileid.github.io/slate/login.html#3-1-checking-itsme-openid-provider-configuration" target="blank">itsme® Discovery document</a>, using one of these keys: <code>request_object_encryption_alg_values_supported</code>, <code>id_token_encryption_alg_values_supported</code>, or <code>userinfo_encryption_alg_values_supported</code>. 
**enc** | Required | It identifies the content encryption algorithm used to perform authenticated encryption on the plaintext to produce the ciphertext and the Authentication Tag. The <code>enc</code> value can be retrieved from the <a href="https://belgianmobileid.github.io/slate/login.html#3-1-checking-itsme-openid-provider-configuration" target="blank">itsme® Discovery document</a>, using one of these keys: <code>request_object_encryption_enc_values_supported</code>, <code>id_token_encryption_enc_values_supported</code>, or <code>userinfo_encryption_enc_values_supported</code>. 
**zip** | Optionnal | 
**jku** | Optionnal | This parameter has the same meaning, syntax, and processing rules as the <code>jku</code> parameter defined in the JWS section, except that the JWK Set resource contains the public key to which the JWE was encrypted; this can be used to determine the private key needed to decrypt the JWE.
**jwk** | Optionnal | This parameter has the same meaning, syntax, and processing rules as the <code>jwk</code> parameter defined in the JWS section, except that the key is the public key to which the JWE was encrypted; this can be used to determine the private key needed to decrypt the JWE.
**kid** | Optionnal | This parameter has the same meaning, syntax, and processing rules as the <code>kid</code> parameter defined in the JWS section, except that the key hint references the public key to which the JWE was encrypted; this can be used to determine the private key needed to decrypt the JWE.
**x5u** | Optionnal | This parameter has the same meaning, syntax, and processing rules as the <code>x5u</code> parameter defined in the JWS section, except that the X.509 public key certificate or certificate chain contains the public key to which the JWE was encrypted; this can be used to determine the private key needed to decrypt the JWE.
**x5c** | Optionnal | This parameter has the same meaning, syntax, and processing rules as the <code>x5c</code> parameter defined in the JWS section, except that the X.509 public key certificate or certificate chain contains the public key to which the JWE was encrypted; this can be used to determine the private key needed to decrypt the JWE.
**x5t** | Optionnal | This parameter has the same meaning, syntax, and processing rules as the <code>x5t</code> parameter defined in the JWS section, except that the certificate referenced by the thumbprint contains the public key to which the JWE was encrypted; this can be used to determine the private key needed to decrypt the JWE.
**x5t#S256** | Optionnal | This parameter has the same meaning, syntax, and processing rules as the <code>x5t#S256</code> parameter defined in the JWS section, except that the certificate referenced by the thumbprint contains the public key to which the JWE was encrypted; this can be used to determine the private key needed to decrypt the JWE.
**typ** | Optionnal | It is used by JWS applications to declare the media type of this complete JWE.
**cty** | Optionnal | It is used by JWS applications to declare the media type of the secured content (the plaintext).
**crit** | Optionnal | This parameter has the same meaning, syntax, and processing rules as the </code>crit</code> parameter defined in the JWS section, except that parameters for a JWE are being referred to, rather than parameters for a JWS.

<ol>  
  <li value="2">The JWE Header will then be encoded using using UTF-8 and base64url to produce the string below.</li>
</ol>


<div class="center-column"></div>
```plaintext--inline
eyJhbGciOiJSU0ExXzUiLCJraWQiOiJmcm9kby5iYWdnaW5zQGhvYmJpdG9uLm
V4YW1wbGUiLCJlbmMiOiJBMTI4Q0JDLUhTMjU2In0
```


<ol>
  <li value="3">Generate a random Content Encryption Key (CEK).</li>
  <li>Compute the CEK with the algorithm defined in the <code>alg</code> parameter, to produce the JWE Encrypted Key.</li>
  <li>The JWE Encrypted Key will then be encoded using base64url to produce the string below.</li>
</ol>


<div class="center-column"></div>
```plaintext--inline
laLxI0j-nLH-_BgLOXMozKxmy9gffy2gTdvqzfTihJBuuzxg0V7yk1WClnQePF
vG2K-pvSlWc9BRIazDrn50RcRai__3TDON395H3c62tIouJJ4XaRvYHFjZTZ2G
Xfz8YAImcc91Tfk0WXC2F5Xbb71ClQ1DDH151tlpH77f2ff7xiSxh9oSewYrcG
TSLUeeCt36r1Kt3OSj7EyBQXoZlN7IxbyhMAfgIe7Mv1rOTOI5I8NQqeXXW8Vl
zNmoxaGMny3YnGir5Wf6Qt2nBq4qDaPdnaAuuGUGEecelIO1wx1BpyIfgvfjOh
MBs9M8XL223Fg47xlGsMXdfuY-4jaqVw
```
 
<ol>
  <li value="6">Generate a random JWE Initialization Vector.</li>
  <li>The JWE Initialization Vector will then be encoded using base64url to produce the string below.</li>
</ol>


<div class="center-column"></div>
```plaintext--inline
bbd5sTkYwhAIqfHsx8DayA
```


<ol>
  <li>Compute the ASCII value of the encoded JWE Header to get the Additional Authenticated Data (AAD).</li>
  <li>Encrypt the JSON payload using the Content Encryption Key (CEK), the JWE initialization vector and the Additional Authentication Data (AAD) value, with the encryption algorithm defined by the <code>enc</code> parameter. It will produce the JWE Ciphertext and Authentication Tag.</li>
  <li>Base64url-encode the JWE Ciphertext.</li>
  <li>Base64url-encode the JWE Authentication Tag.</li>
  <li>Assemble the final representation by concatenating the five strings, and separate them with period ('.') characters. An example of a JWE object is given below.</li>
</ol>


<div class="center-column"></div>
```plaintext--inline
eyJhbGciOiJSU0ExXzUiLCJraWQiOiJmcm9kby5iYWdnaW5zQGhvYmJpdG9uLm
V4YW1wbGUiLCJlbmMiOiJBMTI4Q0JDLUhTMjU2In0
.
laLxI0j-nLH-_BgLOXMozKxmy9gffy2gTdvqzfTihJBuuzxg0V7yk1WClnQePF
vG2K-pvSlWc9BRIazDrn50RcRai__3TDON395H3c62tIouJJ4XaRvYHFjZTZ2G
Xfz8YAImcc91Tfk0WXC2F5Xbb71ClQ1DDH151tlpH77f2ff7xiSxh9oSewYrcG
TSLUeeCt36r1Kt3OSj7EyBQXoZlN7IxbyhMAfgIe7Mv1rOTOI5I8NQqeXXW8Vl
zNmoxaGMny3YnGir5Wf6Qt2nBq4qDaPdnaAuuGUGEecelIO1wx1BpyIfgvfjOh
MBs9M8XL223Fg47xlGsMXdfuY-4jaqVw
.
bbd5sTkYwhAIqfHsx8DayA
.
0fys_TY_na7f8dwSfXLiYdHaA2DxUjD67ieF7fcVbIR62JhJvGZ4_FNVSiGc_r
aa0HnLQ6s1P2sv3Xzl1p1l_o5wR_RsSzrS8Z-wnI3Jvo0mkpEEnlDmZvDu_k8O
WzJv7eZVEqiWKdyVzFhPpiyQU28GLOpRc2VbVbK4dQKPdNTjPPEmRqcaGeTWZV
yeSUvf5k59yJZxRuSvWFf6KrNtmRdZ8R4mDOjHSrM_s8uwIFcqt4r5GX8TKaI0
zT5CbL5Qlw3sRc7u_hg0yKVOiRytEAEs3vZkcfLkP6nbXdC_PkMdNS-ohP78T2
O6_7uInMGhFeX4ctHG7VelHGiT93JfWDEQi5_V9UN1rhXNrYu-0fVMkZAKX3VW
i7lzA6BP430m
.
kvKuFBXHe5mQr4lqgobAUg
```


An example illustrating the signing process is available at <a href="https://tools.ietf.org/html/rfc7520#section-5.2" target="blank">https://tools.ietf.org/html/rfc7520#section-5.2</a>.


# 4. Validating a Nested JWT object

To validate a Nested JWT object, you will first need to decrypt the JWE object, then extract the signed JWS Payload and verify the JWS Signature. 

**Decrypting the JWE object**

The decryption process is the reverse of the encryption process:

<ol>
  <li>Parse the JWE object to extract the serialized values of the base64url-encoded JWE Header, the base64url-encoded JWE Encrypted Key, the base64url-encoded JWE  Initialization Vector, the base64url-encoded JWE Ciphertext, and the base64url-encoded JWE Authentication Tag.</li>
  <li>Base64url decode the encoded representations of the JWE Header, the JWE Encrypted Key, the JWE Initialization  Vector, the JWE Ciphertext, the JWE Authentication Tag, and the JWE Additional Authenticated Data (AAD).</li>
  <li>Verify that the octet sequence resulting from decoding the encoded JWE Header is a UTF-8-encoded representation of a completely valid JSON object.</li>
  <li>Determine the algorithm specified by the <code>alg</code> parameter.</li>
  <li>Decrypt the JWE Encrypted Key with the algorithm defined in the <code>alg</code> parameter, to produce the Content Encryption Key (CEK).</li>
  <li>Let the Additional Authenticated Data (AAD) encryption parameter be the octets of the ASCII representation of the encoded JWE Header value.</li>
  <li>Decrypt the JWE Ciphertext using the Content Encryption Key (CEK), the JWE initialization vector and the Additional Authentication Data (AAD) value, with the encryption algorithm defined by the <code>enc</code> parameter. It will return the decrypted JWS object.</li>
</ol>

**Extracting the payload**

<ol>
  <li>Parse the JWS object to extract the JWS components serialized values, such as in the example below.</li>
</ol>


<div class="center-column"></div>
```plaintext--inline
jws_header = 
"eyJhbGciOiJSUzI1NiIsImtpZCI6ImJpbGJvLmJhZ2dpbnNAaG9iYml0b24uZX
hhbXBsZSJ9"
payload = 
"SXTigJlzIGEgZGFuZ2Vyb3VzIGJ1c2luZXNzLCBGcm9kbywgZ29pbmcgb3V0IH
lvdXIgZG9vci4gWW91IHN0ZXAgb250byB0aGUgcm9hZCwgYW5kIGlmIHlvdSBk
b24ndCBrZWVwIHlvdXIgZmVldCwgdGhlcmXigJlzIG5vIGtub3dpbmcgd2hlcm
UgeW91IG1pZ2h0IGJlIHN3ZXB0IG9mZiB0by4"
signature = 
"MRjdkly7_-oTPTS3AXP41iQIGKa80A0ZmTuV5MEaHoxnW2e5CZ5NlKtainoFmK
ZopdHM1O2U4mwzJdQx996ivp83xuglII7PNDi84wnB-BDkoBwA78185hX-Es4J
IwmDLJK3lfWRa-XtL0RnltuYv746iYTh_qHRD68BNt1uSNCrUCTJDt5aAE6x8w
W1Kt9eRo4QPocSadnHXFxnt8Is9UzpERV0ePPQdLuW3IS_de3xyIrDaLGdjluP
xUAhb6L2aXic1U12podGU0KLUQSE_oI-ZnmKJ3F4uOZDnd6QZWJushZ41Axf_f
cIe8u9ipH84ogoree7vjbU5y18kDquDg"
```


<ol>
  <li value="2">Base64url-decode the encoded representation of the JWS Header.</li>
  <li>Verify that the resulting octet sequence is a UTF-8-encoded representation of a completely valid JSON object.</li>
</ol>


<div class="center-column"></div>
```javascript--inline
{
   "alg":"RS256",
   "kid":"3466d51f7dd0c780565688c183921816c45889ad"
}
```


<ol>
  <li value="4">Base64url-decode the encoded representation of the JWS Payload.</li>
</ol>


<div class="center-column"></div>
```javascript--inline
{
   "sub":"joe",
   "aud":"im_oic_client",
   "jti":"uf90SK4wscFhctUT6Dtvb2",
   "iss":"https:\/\/localhost:9031",
   "iat":1394060853,
   "exp":1394061153,
   "nonce":"e957ffba-9a78-4ea9-8eca-ae8c4ef9c856",
   "at_hash":"wfgvmE9VxjAudsl9lc6TqA"
} 
```


<ol>
  <li value="5">Base64url-decode the encoded representation of the JWS Signature.</li>
</ol>


<div class="center-column"></div>
```plaintext--inline
XiyNdHHHoYqarDZGkhln5sF_SQNNVvV67SZsFAk7yo8NreJjzVw7LmtkwpiUQe87-Km39PeIwf1W_PqEH9RqjA
```


You have decoded the JWS object. The next step is to validate the JWS Signature.

**Checking the JWS Signature**

<ol>
  <li>Determine the signing algorithm <code>alg</code> and the key identifier <code>kid</code> from the JWS Header.</li>
  <li>Retrieve the value of the <code>jwks_uri</code> key in the <a href="https://belgianmobileid.github.io/slate/login.html#3-1-checking-itsme-openid-provider-configuration" target="blank">itsme® Discovery document</a>. As the returned JSON Web Key (JWK) contains an array of keys, you MUST use the one corresponding to the value given by the <code>kid</code> parameter from the JWS Header.</li>
  <li>Use one of the available <a href="https://jwt.io/" target="blank">cryptographic libraries</a> to validate the the JWS Signature in the manner defined for the algorithm being used, which MUST be accurately represented by the value of the <code>alg</code> parameter.</li>
</ol>
    








