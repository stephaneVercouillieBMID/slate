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
itsme® is an identity checking system allowing Service Providers to use verified identities – through 4 services – for authentication and authorization on their web desktop, mobile web and in-app mobile applications:
<ul>
  <li>Login</li>
  <li>Confirm</li>
  <li>Share Data</li>
  <li>Sign</li>
</ul>

The objective of this document is to provide all the information needed to integrate the **Confirm** service using the <a href="http://openid.net/specs/openid-connect-core-1_0.html" target="blank">OpenID Connect Core 1.0 specifications</a>.

<aside class="notice">The <a href="https://belgianmobileid.github.io/slate/" target="blank">itsme® OpenID Login and Share Data</a> specifications MUST be understood first, before going further into the next paragraphs.</aside>
  
  
# 2. Integrating Confirm service

The itsme® Confirm service integration is based on the <a href="http://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth" target="blank">Authorization Code Flow</a> of OpenID Connect 1.0. However, there are some specific rules you MUST take into account during the implementation:

<ul>
  <li>The Authentication Request MUST be communicated using HTTPS POST protocol and the <code>application/x-www-form-urlencoded</code> media type. HTTPS GET calls will be refused by the Authorization Server because the Authorization Request MIGHT contain sensitive data.
  </li>
  <li>Using the <code>request</code> parameter is mandatory when forming the Authentication Request.
  </li>
  
3. The “request object” MUST be signed and encrypted by the service provider. Signed, using his private signing key and, encrypted using the itsme OP public encryption key. The later can be found, of course, in the OP JwkSet JSON file.
4. Parameters encoded in the “request object” have priority over the same parameters put as a GET query parameter. If not explicitly required by the OIDC protocol, it is mandatory to put the corresponding parameter inside the “request object” instead of putting it as a simple form parameter (because the request object is signed and encrypted).
5. Each form parameter that is eligible to be present in the request object MUST also be encoded in the request object itself (because signed and encrypted).
6. Only service codes corresponding to a pre-registered “Approval” kind of service MUST be used in the ‘service’ scope parameter. Be sure this service was already created in the system during partner onboarding.
7. The “prompt” parameter can be “login” and/or “consent”.
    
    
    
    
    
    
  <li>Confirm</li>
  <li>Share Data</li>
  <li>Sign</li>
</ul>











