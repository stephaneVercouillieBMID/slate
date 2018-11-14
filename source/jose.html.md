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
  <li>JSON Web Signature <a href="https://tools.ietf.org/html/rfc7515" target="blank">(JWS)</a>, defines a way to digitally sign JSON data structures. If your application needs to be able to verify the creator of a JSON data structure, you can use this specification to do so.</li>
  <li>JSON Web Encryption <a href="https://tools.ietf.org/html/rfc7516" target="blank">(JWE)</a>, defines a way to express encrypted content using JSON-based data structures. Basically, if you want to encrypt JSON data so that only the intended receiver can read the data, this specification tells you how to do it in an interoperable way.</li>
  <li>JSON Web Key <a href="https://tools.ietf.org/html/rfc7517" target="blank">(JWK)</a>, details a data structure that represents one or more cryptographic keys. If you need to express one of the many types of cryptographic key types in use today, this specification details how you do that in a standard way.</li>
  <li>JSON Web Algorithms <a href="https://tools.ietf.org/html/rfc7518" target="blank">(JWA)</a>, details the cryptographic algorithms and identifiers that are meant to be used with the JSON Web Signature (JWS), JSON Web Encryption (JWE), JSON Web Token (JWT), and JSON Web Key (JWK) specifications. For example, when specifying an encryption algorithm, a JSON key/value pair that has <code>alg</code> as the key may have HS256 as the value, which means HMAC using the SHA-256 hash algorithm.</li>
  <li>JSON Web Token <a href="https://tools.ietf.org/html/rfc7519" target="blank">(JWT)</a>, defines a way of representing claims such as "Bob was born on November 15th, 1984". These claims are digitally signed and/or encrypted using either the JSON Web Signature (JWS) or JSON Web Encryption (JWE) specifications.</li>
  </ul>
  
  
  

  
<a name="Onboarding"></a>
# 2. Prerequisites
 
Before you can integrate your application with itsme® Login and Share Data services, you MUST set up a project in the <a href="https://brand.belgianmobileid.be/d/CX5YsAKEmVI7" target="blank">itsme® B2B portal</a> to obtain all the needed information.
