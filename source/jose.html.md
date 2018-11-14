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
  <li><a href="https://tools.ietf.org/html/rfc7519" target="blank">JSON Web Token</a> (JWT), defines a way of representing claims such as "Bob was born on November 15th, 1984". These claims are digitally signed and/or encrypted using either the JSON Web Signature (JWS) or JSON Web Encryption (JWE) specifications.</li>
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

More information on how to generate JWS objects can be found <a href="https://tools.ietf.org/html/rfc7520#section-4." target="blank">here</a>.



<ol>
  <li>The JOSE Header describes the signature method and parameters related to the Encoded JWS Payload and optionally additional properties of the JWS. The Header Parameter Names within this object MUST be unique. The JOSE Header MUST contain an <code>alg</code> parameter, the value of which is a string that unambiguously identifies the algorithm used to sign the JOSE Header and the JWS Payload to produce the JWS Signature.</li>
  <li>The JWS Payload is the message content to be secured.</li>
  <li>The JWS Signature ensures the integrity of both the JWS Header and the JWS Payload.</li>
</ol>

A signed Request Object can be serialized using the JWS compact serialization. Following lists out the signing process of a JWS Request Object under the **compact serialization**:

<ul>
   <li>Build a JOSE header containing the `alg` parameter – value MUST be set to ` RS256`  as defined in the itsme® <a href="https://merchant.itsme.be/oidc/.well-known/openid-configuration" target="blank">Discovery document</a> and a reference to the signing key using the `kid`. This value SHOULD BE retrieved from your own JWKSet.</li>
   <li>Compute the base64url-encoded value against the UTF-8 encoded JOSE header from the 1st step, to produce the 1st element of the JWS token.</li>
   <li>Construct the payload or the content to be signed. This is known as the JWS payload.</li>
   <li>Compute the base64url-encoded value of the JWS payload from the previous step to produce the 2nd element of the JWS token.</li>
   <li>Build the message to compute the digital signature or the Mac. The message is constructed as ASCII(BASE64URL-ENCODE(UTF8(JOSE Header)) ‘.’ BASE64URL-ENCODE(JWS Payload)).</li>
   <li>Compute the signature over the message constructed in the previous step, following the signature algorithm defined by the JOSE header element `alg`.</li>
   <li>Compute the base64url encoded value of the JWS signature produced in the previous step, which is the 3rd element of the serialized JWS Request Object.</li>
</ul>





Cryptographic algorithms and identifiers when using







