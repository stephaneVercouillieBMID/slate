 
---
title: itsme(r) OpenID Connect documentation
 
language_tabs: # must be one of https://git.io/vQNgJ
- json: JSON
- http: HTTP
 
toc_footers:
<!-- - <a href='#'>Sign Up for a Developer Key</a> -->
- <a href='https://github.com/lord/slate'>Documentation Powered by Slate</a>
 
search: true
---
# 1. Introduction
## 1.1. Who should read this document
This document is intended to be read by Service Provider developers who are in charge of developing and integrating the itsme(r) services. In this document, we speak directly to the developers, using 'you' to designate them.
 
This documentation describes our OAuth 2.0 implementation of
**itsme® Login**, which conforms to  the <a href="http://openid.net/specs/openid-connect-core-1_0.html" target="_blank"> OpenID Connect 1.0 </a> specifications. 
## 1.2. Objective
The objective of this document is to provide all the information needed to integrate the itsme(r) services using OpenID Connect protocol.
## 1.3. The itsme® services in a few words
 
itsme® offers 3 services, which act as strong enablers for every process digitalization project. Further information about itsme(r) services is provided on our B2B portal:

- [**itsme®Login**](https://brand.belgianmobileid.be/d/CX5YsAKEmVI7/documentation#/documentation/general-information/login-with-itsme)
- [**itsme®Confirm**](https://brand.belgianmobileid.be/d/CX5YsAKEmVI7/documentation#/documentation/general-information/confirm-with-itsme)
 - [**itsme®Shared Data**](https://brand.belgianmobileid.be/d/CX5YsAKEmVI7/documentation#/documentation/general-information/shared-data)

# 2. <a name id="Onboarding"></a> On boarding Process
 
Our on boarding process consists of two main steps:
- The configuration of your Sandbox
- The integration of itsme(r) services
 
## 2.1. Configuration of your Sandbox
Before you can start working on the integration of itsme(r) services, we need to create your Sandbox in which the integration takes place. Your company will provide us both functional and technical information we will use to create your Sandbox. These information include (not exhaustively):
- Information to customize the user consent screen
- Redirect URIs associated to your instances of itsme(r) services (to which the user will be redirected after the autentication)
- JWKset URL, and associated SSL/TLS certificate
You can consult [our B2B portal](https://brand.belgianmobileid.be/d/CX5YsAKEmVI7) for more information about this step.
 
<aside class="success"> What is the use of our Certificate? Certificate is used to retrieve your JWKSet. We don't need client certificate but we need the root, the intermediate CA and the final public certificate.  
For testing, you could use a self signed certificate like that you could define validity period.</aside>

<aside class="success">Should I warn BMID if the certificate is changed?Yes, we need to be aware on time</aside>

<aside class="success">What are the consequences of a change label in Sandbox environment? At the moment it's done, all pending approval/login will result in a "Wrong PIN".</aside>

<aside class="success"> Can a same service point to separate SSL Certificates?

Currently, the certificate is used to protect the JWKSet and it is not directly linked to the SSL certificate, so for one Partner code you have maximum 1 JWKSet.</aside>
 
<aside class="success"> Can I use a self signed certificate for my integration? In Sandbox, yes but not for production where we need the certificate & its intermediate chain.</aside>

<aside class="success"> What should I do once I changed my JWKSet URL? You must notify Support and create a new certificate accordingly.</aside>

<aside class="success"> Is it possible to register multiple valid callback URIs/jwkset URIs for one environment, or will they have to change this every time they switch URIs? While it is possible to register several redirect urls, it is not possible to register several JWKSet URLs. 
The redirect URL is specified in each Authorization request, the User will be redirected to that URL after the (failed or successful) authentication in the itsme App. You need to specify it in the onboarding file because it needs to be whitelisted by our F5. We can whitelist several urls. The jwkset urls, however, is used by our BE for the decryption and signature verification of the JWTokens present in the OpenID Connect flow. Our BE must know what URL to contact, and thus we can register only one
JWKSet URL.</aside>

<aside class="success">
Once your Sandbox is created, you will receive:
<ul> 
<li>Your Partner Code, which corresponds to the OpenID <b>client_id</b> </li>
<li>Your Service Codes, which are the identifiers of your instances of itsme(r) services. </li>
</ul>
</aside>

Please consult <a name="ServiceCode"></a>[Service Code Concept](#ServiceCode) for more information about the notion of Service Code. 
<aside class="success">What are the consequences of a changing label in sandbox environment?</aside>All pending approval/login will result in a "Wrong PIN".

## 2.2. Integration of itsme® services
Once your Sandbox has been created, you can start working on the integration itself. All the information needed to do so is the object of this
document.
### 2.2.1. itsme® OpenID Configuration
The OpenID Connect protocol requires the use of multiple endpoints for authenticating users, and for requesting resources including tokens, user information and public keys.
 
To simplify implementations and increase flexibility, OpenID Connect allows the use of a "Discovery document", a JSON document found at well-known location containing key-value pairs which provide details about the OpenID Connect provider's configuration including, 
- URIs of the authorization, 
- token, 
- userinfo, 
- supported claims 
- [JWKSet](#jwks) to interact with it. 
 
<aside class="success">The Discovery document for itsme® service may be retrieved from: <a href="https://merchant.itsme.be/oidc/.well-known/openid-configuration">https://merchant.itsme.be/oidc/.well-known/openid-configuration</a></aside>
Field  names and meanings in this document are defined in [OpenID Connect Discovery 1.0](https://openid.net/specs/openid-connect-discovery-1_0.html).

<aside class="success"> Can we re-use the same service for another environment?
No, We will create new services for the OIDC.
</aside>

<aside class="success">We want to integrate itsme(r) but we only have 1 development machine & so no unique IP for the back-end in test due to it isn't unique & runs in local on each machine. </aside>

# 3. Authenticating the User

**itsme® Login** is based on the [Authorization Code Flow](http://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth) of OpenID Connect 1.0. The Authorization Code Flow goes through the steps as defined in <a href="http://openid.net/specs/openid-connect-core-1_0.html#CodeFlowSteps" target="_blank">OpenID Connect Core Authorization Code Flow Steps</a>, depicted in the following  [diagram](https://www.draw.io/?lightbox=1&highlight=0000ff&edit=_blank&layers=1&nav=1&title=Untitled%20Diagram.html#R7Vxbd6o4FP41PtZFCAF87HVOH6bTNZ1Zc%2BaRQlRWkTiAtZ1fP4mEWxIrYhC1Yx9aNnFjvi%2F7mtgRvF18%2FJJ4y%2FmvJMDRyDSCjxG8G5kmoD%2F0F5N85hLb5YJZEgZ8UCV4Cf%2FFXGhw6SoMcNoYmBESZeGyKfRJHGM%2Fa8i8JCHr5rApiZpPXXozLAlefC%2BSpX%2BFQTbPpa7pVPIfOJzNiycDe5LfWXjFYD6TdO4FZF0TwfsRvE0IyfK%2FFh%2B3OGLgFbjk73vYcrf8YAmOszZv4Li%2Fe9GKz41%2FruyzmGxCVnGA2XgwgjfreZjhl6Xns7trSi%2BVzbNFxG9zdTjJ8MfWjwTKidIVgskCZ8knHcLf4HBo%2BNqA%2FHJdAQ0K9OY1kG0u8zi3s1JxNX36B0dAjQY8PTSGhMM6PTisAeFApweHPSActgTHnylOJEjo5LLmvNMsIW%2F4lkQkoZKYxHTkzTSMIkHkReEsppc%2BBYMqhjcMqpB64Wt%2BYxEGAXuMEuiKCoOpJ3HG4wiAerAHoIk9krG3FNCbGqB3JOjDLF1gKrpeLr8PA3A4BtytDNx4%2FtsVjoPvQ4Pl7qQBGD3xMJF4eMHJe%2BgzJp4T8k7zROqSjEcG3JTB8m1YsQdkpbDLr0IlNZFrlopXgAZeOi9jZ40QERwKQPL5kyE3RsXl342YigMpfxdgowWDl8xwMWc1kjWokAKpQpbgyMvC9%2BYTVfDxJzyTkH6WKo7AJlG2GJxTskp8zN9Vz%2BVFRWZTURmgCkX5lCVFGzbLabcjuEXd0J3gtiQOxRgUGIOizbRlTFQkUa%2BRsRa1zdAmmcPGobFlM7WGJB3pMlNkNxVN%2BuO8RQE3NOd1ftGQ%2FDq6%2BHWcY%2FFb0FnjF4w32U6YhV7GciDPz0ISb0CkGNK35lnqySdBpqYKwRJCoiFnQWZfWZCiYWCOr1fZ%2FIkKf8f%2FrHCanQEVmloVSMhHgSVTUWYsuqmAsie0xvcbnGidMGcAmsbTavF6Fg0MXa1FJDAyUTAC%2B2JEbh9JyNcgXzJ3uXkcuhmhOybBSUifynC9I0k2JzMSe9FzJe1lERc2zSErK51GVSUjZulATO76nFw0r2dwtitH%2BJz180%2Fh4LFCfLFwWpJOljjesBc8hFFBNb2qh9Uti2Afor8kENh62BLrYgAmY9SNLzG3U6jSSNl%2BtfElUSbhrMvAAOqTsBZlUo0BMYi%2Fkiwji5w0gdSG8604dbXyyCEzxtBGkyZq%2FbhNUDxm7wbVLkVbOK0UFQPJdJrig3mX83M4vgvTZeQxXB8Dinw4pVTzEurFTzCl9fvkh2LxpMrYnZ7yQyQbJWK1bcXP2qNVbjxjZKiJOQebNVAvJmpanWOlaKQKVcc1UySbqc1Wwm%2BMr7Kjodz53HMFvEbEf%2Bsrxrr9ZEVS7tm192y67ZxxFwYHqVyocZW3a2Xhxlu2ZVToSY%2Bkgga5O4k%2F4kaEvrXQW0GD5J3zr8z2ROp%2BKNb9qlaJguey33jQQRsgQeaM6YSnJFlQMQ1%2Bm0zFi9J8mrWcpR9%2FWLM4w2yaHDDQ4TZXNzB7t4Fp8qwQNjnuvKuHhH1YCNy%2BrMmWY6PLYuMToZnrZxEVDTJl6evK93GaHroojpkmHZoViUR0LUfFbQRJkUZGz9I%2Fin1R2LIvKsaZTu5RPgMkIda939JY5wy5B28RRmyaP3D0jpm5KHISdl0UeyMTGpuXquJrlor1od2t5kjNVNEmxPyxa3NOUqTPuIpitbZUJs2isnSS26r9y%2FWWUPSWXbNJsUSRFGkk1JQIBWD8BzUpVhpu22MdvCzkQF3RfElyk5qyGdGqrM7ZzC5Fx%2B0GOHJfCMBxfvLceIyn5H%2FeG3QhsQLpyruk6Mi8y5kuMGuWni5JnMonW07YXZc9QKAnHot22vn80i5FRyZebh4BSzL4M6ZfWwu4mYIjeMB26U5VR14DF1EUlZHzCIdFnP2KosGPfjqD9lF1HgwZw9pLqIrdydiuvSZOK6PqYDCuXOicocEgxWnNvgzG3e98%2FCWf2kBiV6Zr4Scp0rjAFWUA2rOUv9iNe7FdiYot1PricRWGpOWbkYp8zWDMVCeftyRrF8uHePQZOgq%2FpvqatpZT6L5jTbDtADidIs%2FGVwqCvvJ0fuSlaeh%2F7c9qCQEcNfdggaV3Q2jYI6TS9rv4Hbuum0TlV%2BoO9pX0svr%2FIfnw6r%2BwwPv%2FAA%3D%3D),
 
![enter image description here](https://lh3.googleusercontent.com/vi1iEAv0LtjFbvT30UE62rHDLu-fPFysH5oj1dpa_hVzaTbmKSV2Js_NjTCI7-5tXGVKgd8p4CQ "auth diag")
## 3.1. **Authorization Endpoint** 
The first step is forming an HTTPS request to the Authorization Endpoint with the appropriate URI parameters. Please note the use of HTTPS rather than HTTP in all the steps of this process; HTTP connections are refused.
### 3.1.1. Authentication Request Specifications
As per the OpenID Connect specification <a href="http://openid.net/specs/openid-connect-core-1_0.html#AuthRequest" target="_blank">Authentication Request</a> and <a href="http://openid.net/specs/openid-connect-core-1_0.html#AuthorizationEndpoint" target="_blank">Authorization Endpoint</a>.

You should retrieve the base URI from the [Discovery document](https://merchant.itsme.be/oidc/.well-known/openid-configuration) using the key **authorization_endpoint**. The following discussion assumes the endpoint is `https://merchant.itsme.be/oidc/authorize`.

**itsme®** supports the use of both HTTP `GET` and `POST` methods. If using the HTTP `POST` method, the request parameters must be serialized using [Form Serialization](http://openid.net/specs/openid-connect-core-1_0.html#FormSerialization).

Please check the following table for request parameters,

```http--inline
 GET /authorize?response_type=code
 &scope=openid%20profile%20email%20service%3Aclient.registration
 &client_id=s6BhdRkqt3
 &state=af0ifjsldkj
 &redirect_uri=https%3A%2F%2Fclient.example.org%2Fcb HTTP/1.1
 Host: server.itsme.be
 ```

Parameter | Required | Specification | Parameter Explained
:--------- | :-------| :-----|:--------------|
**client_id** | Required | MUST be the Partner Code you obtained from BMID during [on-boarding process](#Onboarding) and this value will be unique to each partner per environment. |This is your client identifier at the OpenID Provider.
**response_type** | Required | MUST be <code>code</code>.|Used to indicate an authorization code flow.
**scope** | Required | MUST contain at least `openid` or an HTTP ERROR `not_implemented` will be returned. `offline_access` value will yield an error. MUST also includes the target service in the form `service:<SERVICE_CODE>` as provided by BMID during the on-boarding process (see <a name="ServiceCode"></a>[Service Code Concept](#ServiceCode) for further information).| Used to specify the scope of the requested authorization in OAuth, as well as pre-defined sets of claims (see [Scope](#scope)). The scope value `openid` signals a request for OpenID authentication and ID token. . ***Note**: Requested data will only be provided based on your current accesses. These accesses are specified in the on-boarding file provided by BMID.* The value `service` is a BMID extension of the OpenID standard specification.
**redirect_uri** | Required | MUST be the HTTPS endpoint on your server that will receive the Authentication Response from **itsme(r)**. This value MUST match one of the values provided to BMID during on-boarding process. ***Note**: the Partner can define different **redirect_uri** specific to each Service.*|The URI where the user will be redirected to  as for the Authentication Response.
**state** | An appropriate value is RECOMMENDED. No syntactic requirement on this parameter | Should include the value of the anti-forgery unique session token, as well as any other information needed to recover the context when the user returns to your application.|Value set by your end to maintain state between request and callback.
**nonce** | Optional | a random value generated by your app that enables replay protection when present. No synctactic requirement on this parameter. |The purpose is to prevent id token to be displaced by an attacker by sending a valid id token from a different request in order to trick user visiting another website. The nonce parameter value needs to include per-session state and be unguessable to attackers. One method to achieve this for Web Server Clients is to store a cryptographically random value as an HttpOnly session cookie and use a cryptographic hash of the value as the nonce parameter. In that case, the nonce in the returned ID Token is compared to the hash of the session cookie to detect ID Token replay by third parties. A related method applicable to JavaScript Clients is to store the cryptographically random value in HTML5 local storage and use a cryptographic hash of this value.
**login_hint** | Optional | OPTIONAL and supported, though NOT RECOMMENDED. Only phone numbers are supported as `login_hint`. Format is : `<countrycode>+<phonenumber>`. E.g. `login_hint=32+123456789`. `login_hint` with invalid syntax will be ignored. Usage of claim value `phone_number` in an encrypted `request` object is recommended in order to avoid disclosure of phone number of the end user on the user agent (such as mobile app or web browser)|Hint to the Authorization Server about the login identifier the End-User might use to log in.
**display** | Optional | MUST be `page` if provided. Other values will yield an HTTP ERROR `not_implemented`.|An ASCII string value for specifying how the authorization server displays the authentication and consent user interface pages.
**prompt** | Optional | MUST be `consent` if provided.|By giving 'consent' as value, the authorization server prompts the user for consent before returning information to the client. Offline access to data is not supported by BMID on purpose.
**ui_locales** | Optional | Supported values are: {“fr”, “nl”, “en”, “de”}. Any other value will be ignored. |Can be used to specify the language to be used by the Open ID login page.
**max_age** | Optional | Supported but not used |Specifies the allowable elapsed time in seconds since the last time the End-User was actively authenticated by the OP. If the elapsed time is greater than this value, the OP MUST attempt to actively re-authenticate the End-User. BMID does not maintain a session mechanism on purpose, an active authentication is thus always required.
<a name id="acrvalues">**acr_values**</a> | Optional | OPTIONAL and supported, though NOT RECOMMENDED. Two values are supported `tag:itsmetag:sixdots.be,2016-06:acr_basic`: Basic level will let the User to choose either fingerprint usage(if Device is compatible) or PIN, `tag:itsmetag:sixdots.be,2016-06:acr_advanced`: Advanced level will force the User to use PIN. *When multiple values are provided only the most constraining will be used (advanced > basic). If not provided basic level will be used.* |As there is no such idea of an existing session on itsme(r) Core, even if the `acr_values` is requested as a voluntarily claim, the `acr` value returned will always be the more constraining method in the `acr_values` list, or the authentication will fail. Usage of `acr` parameter in the `request` object is recommended over this parameter as it will be signed in the JWT token.| These values are used to specify the level of authentication the RP requires from the OP. BMID imposes more strict security rules on advanced level as for basic.
**claims** | Optional | NOT RECOMMENDED. Usage of claims parameter in the request object is recommended over this parameter as it will be signed in the JWT token, and the data will be encrypted | MAY include claims for end user data.
**request** | Optional | See [Passing Request Parameters as JWTs](#JWTRequest)
**response_mode** | Unsupported | MUST not be used. Any supplied value will be ignored.|
**id\_token\_hint** | Unsupported | Ignored if provided.
**claims_locales** | Unsupported | None are supported.|
**request_uri** | Unsupported | Not supported (yet)|
**registration** | Unsupported | Not supported due to “client dynamic registration”is not supported. The client registration process is done during the [on boarding process](#Onboarding).|

### 3.1.2. Authentication Response Specification
An Authentication Response is an [OAuth 2.0 Authorization Response](https://tools.ietf.org/html/rfc6749#section-4.1.2) message. 

The itsme Back-End provides an Authorization Code to the Service Provider Back-End. In the Response, the Service Provider Back-End knows that the User was successfully authenticated.
 
```http--inline
 HTTP/1.1 302 Found
 Location: https://client.example.org/cb?
 code=SplxlOBeZQQYbYS6WxSbIA&
 state=af0ifjsldkj
 ```
As such, the Authentication Response will return the following parameters:

Parameter | Provided | Description
:--:| :--:|:--
**code** | Always | Authorization code to later provide to the token endpoint. This code has a lifetime of 3 minutes.
**state** |  | The exact value received from the client, if the parameter was present in the Authentication Request.
 
### 3.1.3. Authentication Errors
As explained by OIDC [http://openid.net/specs/openid-connect-core-1_0.html#AuthError](http://openid.net/specs/openid-connect-core-1_0.html#AuthError), if the authentication is NOT successful, the following errors can be triggered by itsme®:

Error | Description
:-- |:--
`interaction_required`  | The Authorization Server requires End-User interaction of some form to proceed.
`invalid_request_object` | The request parameter contains an invalid Request Object.
`request_uri_not_supported` | does not support use of the request_uri parameter.
`registration_not_supported` | does not support use of the registration parameter.

#### 3.1.3.1. Response When User Rejects the Action
`{"error_description":null,"error":"access_denied"}`
#### 3.1.3.2. Response Received Session Expired 


## <a name="tokenEndpoint"></a> 3.2. Token Endpoint
As per specified by OIDC, [http://openid.net/specs/openid-connect-core-1_0.html#TokenRequest](http://openid.net/specs/openid-connect-core-1_0.html#TokenRequest).

Possible token end-point URLs:  
>[https://merchant.itsme.be/oidc/token](https://merchant.itsme.be/oidc/token)  
>[https://e2emerchant.itsme.be/oidc/token](https://e2emerchant.itsme.be/oidc/token)  

### 3.2.1. About Tokens
In this section, we will go through token types and their specifics.

There are three types of tokens in OIDC: [id_token](#idtoken), [access_token](#actoken) and [refresh_token](#rfshtoken).
#### <a name id="idtoken"></a> 3.2.1.1. ID Tokens 
As per the [OIDC Specification](http://openid.net/specs/openid-connect-core-1_0.html#TokenResponse), an `id_token` is a JWT.

- ID tokens carry user’s authentication information encoded in the token itself, it must be a JWT and authorization server will return them.
-  the token can be certainly verified to prove that it hasn’t been tampered with.

There’s a set of [rules](http://openid.net/specs/openid-connect-core-1_0.html#IDTokenValidation) in the specification for validating an `id_token`.

#### <a name id="actoken"></a> 3.2.1.2 Access Tokens

Bearer token concept must be mentioned before access token is explained. Bearer token is a protected token which can access to authorized resources without further identification. The format for OAuth 2.0 Bearer tokens is actually described in a separate spec, [RFC 6750](https://tools.ietf.org/html/rfc6750).

- Access tokens are bearer tokens. They specify set of end user data the RP has access to in the context of the authentication (content of access token thus depends on the static access rights to data as defined during [on boarding process](#Onboarding)).
- They have short lifespan, expire for improving security. The user must authenticate again for the RP to get a new access token limiting the exposure of the fact that it’s a bearer token.

#### <a name id="rfshtoken"></a> 3.2.1.3 Refresh Tokens (_not supported_) 

Refresh tokens are not supported since we do not maintain authenticated sessions on purpose.

### 3.2.2. Token Request Specification
As per the [OIDC specification](http://openid.net/specs/openid-connect-core-1_0.html#TokenRequest), with a private_key_jwt for client authentication.

The Authentication Response includes a `code` parameter, a one-time authorization code that your server can exchange for an ID token. Your server makes this exchange by sending an HTPS `POST`request. The `POST` request is sent to the token endpoint, which you should retrieve from the [Discovery document](https://merchant.itsme.be/oidc/.well-known/openid-configuration) using the **token_endpoint** key. The following discussion assumes the endpoint is `https://merchant.itsme.be/oidc/token`. 

<aside class="success">Which information must be present to contact Token Endpoint?
- Request MUST be a POST, not a GET
- In header the Content-Type : application/x-www-form-urlencoded  MUST be added</aside>

<aside class="success">Do you need an App Client Secret on the OpenID client?
OpenID allows multiple ways for authentication as a Service Provider. BMID only supports `private_key_jwt` as client authentication method with all the SSL requirements exposed via JWKSet, use the corresponding private key to encrypt/sign and decrypt/validate exchanged information. So other authentication methods such as
`client_secret`(Open ID Connect default method but the less secured one are not supported since they are considered less secure. </aside>

In order to communicate with Token Endpoint, TLS MUST be implemented. See  [Section 16.17](http://openid.net/specs/openid-connect-core-1_0.html#TLSRequirements)  for more information on using TLS.

```http--inline
GET /oidc/authorization?response_type=code
&client_id=yourpartnercode
&yourredirecturl
&scope=openid+service%3Ayourservicecode+profile+
&claims={"userinfo":{" tag:sixdots.be,2016-06:claim_nationality": null},
"id_token":{"auth_time": {"essential": true},"acr_values
{"value":["tag:sixdots.be,2016-06:acr_advanced"]
}}}&state=anystate&nonce=anonce&prompt=login&max_age=1
```
The Token Request must include the following parameters in the POST body:

Parameter | Required | Comment
:-- | -- | --
**grant_type** | Required | Must be `authorization_code`. 
**code** | Required | The code value provided in the Authentication Response
**redirect_uri** | Required | The **redirect_uri** used in the Authentication Request. This is the URL to which you want the user to be redirected after the authorization is complete.
**client_assertion** | Required | Must be a valid JWT complying with the `private_key_jwt` client authentication method as defined in [Section 9](http://openid.net/specs/openid-connect-core-1_0.html#ClientAuthentication) of the OpenID specification. This JWT must be signed.
**client\_assertion\_type** | Required | Must be `urn:ietf:params:oauth:client-assertion-type:jwt-bearer` 

#### 3.2.2.1. `client_assertion` 
According to the `private_key_jwt` client authentication method, the **client assertion** JWT must contain the following properties:
Property | Comment
:-- | :--
**iss** | The issuer of the `private_key_jwt` (the client ID). MUST be the Partner Code you obtained from BMID during on-boarding process (this information is in the onboarding file provided by BMID).
**sub** | The subject of the `private_key_jwt` (the client ID). MUST be the Partner Code you obtained from BMID during on-boarding process (this information is in the on boarding file provided by BMID). 
**aud** | Must be the token endpoint URL
**jti** | A unique identifier for the token, which can be used to prevent reuse of the token. These tokens MUST only be used once.
**exp** | Expiration time on or after which the ID Token MUST NOT be accepted for processing.

### 3.2.3. Token Response Specification
The Token Response follow these specifications:

Parameter | Provided | Comment
-- | -- | :--
**[`access_token`](#actoken)** | Always | Will be provided. 
**[`token_type`](http://openid.net/specs/openid-connect-core-1_0.html#TokenResponse)** | Always | Will be `Bearer`
**[`id_token`](#idtoken)** | Always | The id_token corresponding to the Authentication Request (signed and  encrypted). 
**[`at_hash`](http://openid.net/specs/openid-connect-core-1_0.html#CodeIDToken)** | Never | Current version of itsme(r) Core does not produce the `at_hash` value
**[`refresh_token`](#rfshtoken)** | Never | Won't be provided as **itsme(r)** only maintains short-lived session to enforce re-authentication.
### 3.2.5. Token Response Example
`{"user_info":{"sub":"qn2b631umr23bpou8rfzbtu79b5q5phxcml8","aud":"OIDC_TEST1","birthdate":"1974-04-12","gender":"male","name":"Ada Gardner","iss":"tokenEndpointURL","given_name":"Ada","locale":"fr","family_name":"Gardner"},"id_token":{"access_token":"UVfXK3QzTRKyFiw3f1v85Yr4ko4o7uI1oJ8XNZeRcJE","id_token":{"sub":"qn2b631umr23bpou8rfzbtu79b5q5phxcml8","aud":"OIDC_TEST1","acr":"tag:sixdots.be,2016-06:acr_basic","auth_time":1523626355,"iss":"tokenEndpointURL","exp":1523626660,"iat":1523626360,"nonce":"anonce"},"token_type":"Bearer","expire_in":163}}`
### 3.2.4. Token Error Response 
As per <a href="http://openid.net/specs/openid-connect-core-1_0.html#TokenErrorResponse" onclick="return ! window.open(this.href);">OIDC Specification Aggregated Response</a>



# 4. User Data
## 4.1. What is a claim?
The concept of claim is about declaring something you expect as return from the OP. When it comes to end user data, you have to use claims in order to declare the end user data you will need for your business before the authentication. This is a privacy-oriented way of getting data.
Technically, you have to declare the claims in the Authorization Request in the way described in the section [Declaring Claims](#decClaim).
Claims will come as name/value pairs packaged in a JSON object that contain information about a user, as well as meta-information about the OIDC service. The official definition from the spec is a [“piece of information asserted about an Entity.”](http://openid.net/specs/openid-connect-core-1_0.html#Terminology)
### 4.1.1. Claim Types
Three representations of Claim Values are defined officially: 
- Normal Claims
- Aggregated Claims (not supported)
- Distributed Claims (not supported)
We do not support Aggregated and Distributed Claims due to all the data we expose come from our own database.
#### 4.1.1.1. Normal Claims
Claims that are directly asserted by the OpenID Provider.
Normal Claims are represented as members in a JSON object. The Claim Name is the member name and the Claim Value is the member value.

The following is a non-normative response containing Normal Claims:
 ```http--inline
  {
   "name": "Jane Doe",
   "given_name": "Jane",
   "family_name": "Doe",
   "email": "janedoe@example.com",
   "picture": "https://www.itsme.be/uploads/media/57da4dee8c5d2/logo-itsme-badge.svg?production-f26c079"
  } 
  ```
### 4.1.2 Essential Claims vs Voluntary Claims
In current version and in contradiction to the OpenID Connect specification, **itsme(r)** considers all claims requested via scope as **Essential** (see [Individual Claim Request](http://openid.net/specs/openid-connect-core-1_0.html#IndividualClaimsRequests)). **Voluntary** claims are thus supported, but are used as **Essential**.

In practice, it means the User may not opt out the sharing of specific Data; the User must either gives his consent for the sharing of all Data or refuse the request as a whole. However, in a future version **itsme(r)**  will make the difference between **Essential** and **Voluntary** claims. You should thus already request claims according to your business case. 
## <a name id="decClaim"></a> 4.2. Declaring Claims
You can declare Claims in two ways:
- With 'scope' values
- With 'claims' parameter

In each case, the claims need to be declared in the Authorization Request.
### <a name id="scope"></a>4.2.2. Scope

Scopes are space-separated lists of identifiers used to specify what access privileges are being requested. 

Scopes can be used to request that specific sets of information available as Claim Values in User Info Token. 

Using this method, you will always receive Claims from the UserInfo Endpoint.

<!-- (always User Info Endpoint)-->
#### 4.2.1.1 List of Supported Scope Values
<aside class="success">Any claim requested by using the scope value can only be obtained from the User Info endpoint.</aside>

The following scope values are supported and allow access to predefined sets of Identity Data:
|Scope Value | Associated Claims|
|--|:--|
|profile | given_name, family_name, gender, birthdate,  locale|
email|email, email_verified|
phone| phone_number, phone_number_verified| 
address|address, with subfields,<br>street_address (newline separator \n)<br> locality <br> postal_code <br> country
<aside class="success">Is it normal that the scope "eid" is not mentioned in the supported scopes?
The “eid” scope was introduced exclusively for FAS (BOSSA). </aside>

#### 4.2.1.2 Example of an Authorization Request using "scope" values
URL:

```http--inline
GET /oidc/authorization?response_type=code&client_id=OIDC_TEST1&redirect_uri=https%3A%2F%2Fstaging1.labo.sixdots.be%2Fopenidclient%2Fuat_OIDC_TEST1%2Fauthz_cb&scope=openid+service%3AOIDC_TEST1_LOGIN+profile+eid+phone=phone_number+phone_number_verified+email=email_verified+address=postal_code+country+&state=anystate&nonce=anonce&prompt=login&max_age=1 HTTP/1.1
Host: uatmerchant.sixdots.be
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:52.0) Gecko/20100101 Firefox/52.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate, br
Cookie: JSESSIONID=871EA8BD595FE9F89BB9F4346ABD16B094ABB62AEFC2ABED925F8361E1B3031178073D9C12A0DBDF4FB02C28AB72E917763275E9E853CB6A0D178744291D15DB92DECDA43BE66D0E5673A7E48DD23564B6E40BAC61F9012F4137B5A02112CEF5788DD47500AC51B1024AE061E0A93593CF954051E913B7BC1D772B947C8C7FB8; TS01afcf7e=0163058fe5252ff79320ae0af6fe6d839a75ea2aec42d57c6c4f7781ddc75d5c4aeec90a783e72b1e9ea8053bb334a3bc6a2ca0c09cd3409043e8ac82f7001989a812dee31; cookieconsent_status=dismiss; mobileid-phonenumber=null; BIGipServer~DMZ~pool_uat_5000=rd1o00000000000000000000ffff0ac21e03o5000; TS01da5469=0163058fe527f1fa81765220131ba80249a2f3e34342d57c6c4f7781ddc75d5c4aeec90a7804b2672a8fd3a85c4d2f9c7eb480d78ce9f71f0e45414210e79c2179584c20a4
Connection: keep-alive
Upgrade-Insecure-Requests: 1
```
URL Parameters without encoding:
```http inline--   
response_type=code
client_id=yourpartnercode
redirect_uri=https%3A%2F%2Fyourredirecturl
scope=openid+service%3Ayourservicecode+profile+eid+phone=phone_number+phone_number_verified+email=email_verified+address=postal_code+country+
state=anystate
nonce=anonce
prompt=login
max_age=1
```
### 4.2.2 “claims” Parameter
Some specific data cannot be requested by using scope values. They have to be requested in the claims as request parameter of the Authentication Request. Using this [method](https://openid.net/specs/openid-connect-core-1_0.html#ClaimsParameter) of requesting claims, you need to specify the endpoint you want the claims to come from. ( see example for different specifying endpoints [4.2.2.1. Set of Request Parameter Adapted to itsme(r)](#example-endpoint))

**List of Supported Custom "claim" Values:**

Here are these claims:

Data | Claim | Comment 
:-- | :--: | :--: 
Subject | **`sub`** | The subject of the `private_key_jwt` (the client ID). Supports value in request. 
Nationality | **`tag:itsmetag:sixdots.be,2016-06:claim_nationality`** | An error will be raised if request as a value element for the claim 
Place of Birth - city | **`tag:itsmetag:sixdots.be,2016-06:claim_city_of_birth`** |An error will be raised if request as a value element for the claim 
Place of Birth - country | **`tag:itsmetag:sixdots.be,2016-06:claim_country_of_birth`** | An error will be raised if request as a value element for the claim 
E-ID Metadata  | **`tag:itsmetag:sixdots.be,2016-06:claim_eid`** |  [Specifications](#eidMetadata)
Passport Number | **`tag:sixdots.be,2017-05:claim_passport_sn`** | Simple string containing the user’s Passport Serial Number. 
Device | **`tag:sixdots.be,2017-05:claim_device`** | [Specifications](#deviceClaim) and [an example of device claim value usage](#exampleDeviceClaimValue)<br>
Transaction Info| **`tag:sixdots.be,2017-05:claim_transaction_info`** |[Specifications](#transactionInfo) and [an example of usage](#transactionInfoExample) 
E-ID Picture | **`tag:sixdots.be,2017-05:2017-05:claim_photo`**|
NRN | not supported|
<aside class="success"> Taking into account you allowed to receive the NRN from us, we can't and get access to NRN and block the access to the eID group</aside> TODO 
 
#### <b id="example-endpoint"></b> 4.2.2.1. Set of Request Parameter Adapted to itsme(r)
```json--inline
 {
    "userinfo":
     {
      "given_name": {"essential": true},
      "nickname": null,
      "email": {"essential": true},
      "email_verified": {"essential": true},
      "picture": null,
      "http://example.info/claims/groups": null
     },
    "id_token":
     {
      "auth_time": {"essential": true},
      "acr": {"values": ["urn:mace:incommon:iap:silver"] }
     }
 }
 ```
 
 **List of Supported Standard "claim" Values:**

As per specified by OpenID Connect, there is a set of [standard claims](https://openid.net/specs/openid-connect-core-1_0.html#StandardClaims), or user attributes. They are intended to supply the client app with consented user details such as email, name and picture, upon request.  They can be requested to be returned either in the UserInfo Response, per [Section 5.3.2](https://openid.net/specs/openid-connect-core-1_0.html#UserInfoResponse), or in the ID Token, per [Section 2](https://openid.net/specs/openid-connect-core-1_0.html#IDToken).

Here following table lists the supported standard "claim" values, 
|Member  |Type  |Description |
|:--|--|--|
name |string|Subject - Identifier for the End-User at the Issuer.
given_name|string|  Given name(s) or first name(s) of the End-User. Note that in some cultures, people can have multiple given names; all can be present, with the names being separated by space characters.
family_name|string|Surname(s) or last name(s) of the End-User. Note that in some cultures, people can have multiple family names or no family name; all can be present, with the names being separated by space characters.
profile|string|URL of the End-User's profile page. The contents of this Web page SHOULD be about the End-User.
email|string|End-User's preferred e-mail address. Its value MUST conform to the [RFC 5322](https://openid.net/specs/openid-connect-core-1_0.html#RFC5322) [RFC5322] addr-spec syntax. The RP MUST NOT rely upon this value being unique, as discussed in [Section 5.7](https://openid.net/specs/openid-connect-core-1_0.html#ClaimStability).
email_verified|boolean|True if the End-User's e-mail address has been verified; otherwise false. When this Claim Value is true, this means that the OP took affirmative steps to ensure that this e-mail address was controlled by the End-User at the time the verification was performed. The means by which an e-mail address is verified is context-specific, and dependent upon the trust framework or contractual agreements within which the parties are operating.
gender|string|End-User's gender. Values defined by this specification are female and male. Other values MAY be used when neither of the defined values are applicable.
birthdate|string|End-User's birthday, represented as an [ISO 8601:2004](https://openid.net/specs/openid-connect-core-1_0.html#ISO8601-2004) [ISO8601‑2004] YYYY-MM-DD format. The year MAY be 0000, indicating that it is omitted. To represent only the year, YYYY format is allowed. Note that depending on the underlying platform's date related function, providing just year can result in varying month and day, so the implementers need to take this factor into account to correctly process the dates.
locale|string|End-User's locale, represented as a [BCP47](https://openid.net/specs/openid-connect-core-1_0.html#RFC5646) [RFC5646] language tag. This is typically an [ISO 639-1 Alpha-2](https://openid.net/specs/openid-connect-core-1_0.html#ISO639-1) [ISO639‑1] language code in lowercase and an [ISO 3166-1 Alpha-2](https://openid.net/specs/openid-connect-core-1_0.html#ISO3166-1)[ISO3166‑1] country code in uppercase, separated by a dash. For example, en-US or fr-CA. As a compatibility note, some implementations have used an underscore as the separator rather than a dash, for example, en_US; Relying Parties MAY choose to accept this locale syntax as well.
phone_number|string|  End-User's preferred telephone number.  [E.164](https://openid.net/specs/openid-connect-core-1_0.html#E.164)  [E.164] is RECOMMENDED as the format of this Claim.If the phone number contains an extension, it is RECOMMENDED that the extension be represented using the  [RFC 3966](https://openid.net/specs/openid-connect-core-1_0.html#RFC3966)  [RFC3966] extension syntax.
phone_number_verified |boolean (**always true**)|True if the End-User's phone number has been verified; otherwise false. When this Claim Value is true, this means that the OP took affirmative steps to ensure that this phone number was controlled by the End-User at the time the verification was performed. The means by which a phone number is verified is context-specific, and dependent upon the trust framework or contractual agreements within which the parties are operating. When true, the phone_number Claim MUST be in E.164 format and any extensions MUST be represented in RFC 3966 format.
address|JSON object|End-User's preferred postal address. The value of the address member is a JSON [[RFC4627]](https://openid.net/specs/openid-connect-core-1_0.html#RFC4627) structure containing some or all of the members defined in [Section 5.1.1](https://openid.net/specs/openid-connect-core-1_0.html#AddressClaim).
#### 4.2.2.2. Example of a Valid “claims” Object 
Example of JSON device object requested with `tag:sixdots.be,2017-05:claim_device`:
```json--inline
 {  
 	"os": "ANDROID",  
 	"appName": "itsme app", "appRelease": "1.17.13",
 	"deviceLabel": "myDevice",
 	"debugEnabled": false, 
 	"deviceId": "deviceId",
 	"osRelease": "Android 4.4.2", 
 	"manufacturer": "samsung", 
 	"hasSimEnabled": true,
 	"deviceLockLevel": "touchID", 
 	"smsEnabled": true,
 	"rooted": false,
 	"imei": "12345678901234567",
 	"deviceModel": "S8",  
 	"msisdn": "0412123123", 
 	"sdkRelease": "1.17.12"  
 }
 ```
#### <a name id="deviceClaim"></a> 4.2.2.2. Device Claim 
This claim is the information about the end user device. 

Claim value: **`tag:sixdots.be,2017-05:claim_device`**

A JSON object with the following keys: (only keys with cardinality [1…1] will be always available)

- “os” [1…1]: the device operating system (supported values:
- {ANDROID, IOS})   
- “appName” [0…1]: the application name.   
- “appRelease” [0…1]: the application current release.   
- “deviceLabel” [0…1]: the name of the device.   
- “debugEnabled” [0…1]: if debug mode is activated.   
- “deviceId” [1…1]: (regexp =“[a-f0-9]{33}”) the device identifier.   
- “osRelease” [0…1]: Version of the OS running on your Device.  
- “manufacturer” [0…1]: Brand of the device manufacturer (‘Apple’ on iOS, device specific on Android).
- “hasSimEnabled” [0…1]: Whether there is a SIM in the Device. Should
be always true, as long as BMID keeps forbidding installing itsme on
a tablet.   
-  “deviceLockLevel” [0…1]: The type of action to be
performed to unlock the Device. On iOS : TOUCH_ID, PASSCODE or NONE if User protected his Device with TouchID, PIN or nothing.   
- “smsEnabled” [0…1]: Can send SMS. On iOS, means it’s an iPhone.    
-  “rooted” [0…1]: Coming from Gemalto. ‘true’ the device is
jailbreaked/rooted.   
- “imei” [0…1]: (regexp = “[0-9]{15,17}”) the
device IMEI value.   
-  “deviceModel” [0…1]: Model of the Device.  
e.g. SAMSUNG GALAXY A5   
- “msisdn” [0…1]: the user’s phone number.  
- “sdkRelease” [0…1]: Sdk release
##### <a name id="exampleDeviceClaimValue"></a>4.2.2.2.1. Example of Device Claim Value Usage
`{ "os": "ANDROID", "appName": "itsme app", "appRelease": "1.17.13", "deviceLabel": "myDevice", "debugEnabled": false, "deviceId": "deviceId", "osRelease": "Android 4.4.2", "manufacturer": "samsung", "hasSimEnabled": true, "deviceLockLevel": "touchID", "smsEnabled": true, "rooted": false,"imei": "12345678901234567", "deviceModel": "S8", "msisdn": "0412123123", "sdkRelease": "1.17.12" }`
#### <a name id="eidMetadata"></a>4.2.2.3. Eid Metadata Claim 
Claim value: **`tag:itsmetag:sixdots.be,2016-06:claim_eid`**

This claim is Belgian Electronic ID card information encoded in JSON, with the following keys,

`eid`: the electronic ID card serial number. <br>`issuance_locality`: the issuance locality. <br>`validity_from`: eID card validity “from” date. <br>`validity_to`: eID card validity “to” date. <br>`certificate_validity`: the certificate validity. <br>`read_date`: the data extraction date. Each date is encoded using ISO 8601 UTC (timezone) date format. Example of ISO 8601 UTC date: 2017-04-01T19:43:37+0000
#### <a name id="transactionInfo"></a>4.2.2.4.Transaction Info Claim
Claim value: **`tag:sixdots.be,2017-05:claim_transaction_info`**
Information available in the context of the current transaction.

A JSON object with the following keys: (only keys with cardinality \\\[1..1\\\] will be always available)<br> **“securityLevel” \\\[1..1\\\]**: (supported values: <br>{SOFT\\\_ONLY, SIM\\\_ONLY, SIM\\\_AND\\\_SOFT}) Security level used during transaction. <br>**“bindLevel” \\\[1..1\\\]**: (supported values: {SOFT\\\_ONLY, SIM\\\_ONLY, SIM\\\_AND\\\_SOFT}) tells if the user account is bound to a SIM or not, at the time the transaction occurred. <br>**“mcc” \\\[0..1\\\]**: the Mobile Country Code. An Integer (three digits) representing the mobile network country. 
##### <a name id="transactionInfoExample"></a>4.2.2.4.1. Example of The Usage of Transaction Info Claim Value
`{ "securityLevel": "SIM\\\_AND\\\_SOFT", "bindLevel": "SIM\\\_AND\\\_SOFT", "mcc": 206 }`
## 4.3. Getting Data

As per the [OpenID Connect specification](http://openid.net/specs/openid-connect-core-1_0.html#UserInfoRequest),
Depending on how you declared claims in the Authentication Request, you will receive the user data from
- The UserInfo Endpoint
- The Token Endpoint, in the ID Token
<aside class="success">Should there be 2 calls to itsme(r) for this schedule, one for Token Request & one for UserInfo Request?   
Indeed, You need to perform two Back-End to Back-End calls:  
- The Token Request  
- The UserInfo Request  
On top of this, the Authorization Request (AuthN Request in the schedule) consists of an HTTP redirection to the OpenID webpage of BMID. The content of this HTTP request is to be crafted by your system, it is actually a third call from your side to BMID, this one being Front-End to Back-End.</aside>

### 4.3.1. UserInfo Endpoint
As per the [OpenID Connect specification](http://openid.net/specs/openid-connect-core-1_0.html#UserInfoRequest), 

If you declared claims with `scope` values or if you declared claims in the `userinfo` part of the `claims` parameter in the `request` object, you will receive the end user data from the UserInfo Endpoint.

The UserInfo endpoint returns previously consented user profile information to the client app. For that a valid access token is required.

Your server sends the User Info Request using either HTTP  `GET`  or HTTP  `POST` method. The Access Token obtained from an Authentication Request must be sent as a Bearer Token. It is recommended that the request use the HTTP  `GET`method and the Access Token be sent the using the  `Authorization`  header field. The HTTP request is sent to the User Info endpoint, which you should retrieve from the  [Discovery document](https://merchant.itsme.be/oidc/.well-known/openid-configuration)  using the key  **userinfo_endpoint**.

The content type of the response will be `application/JWT`. The response will be signed and encrypted. 

The UserInfo endpoint can be accessed only with a valid **access_token** and for a very limited duration after end user authentication. There must be _less than 3 minutes_ between the creation of the user action to be confirmed by the end user on his mobile device, and the access to the User Info Endpoint.
The Access Token will define the list of Data that will be provided back to the client. In order to request specific claims, you can  [use scopes](https://stackedit.io/app#stClaims)  in the Authentication Request and/or  [use the claims parameter](https://stackedit.io/app#Claims-Request)  of the  request Object.
#### 4.3.1.1. User info Request Specification
As per specified [OIDC UserInfo Request](http://openid.net/specs/openid-connect-core-1_0.html#UserInfoRequest).

The Client sends the UserInfo Request using either HTTP  GET  or HTTP  POST. The Access Token obtained from an OpenID Connect Authentication Request MUST be sent as a Bearer Token, per Section 2 of  [OAuth 2.0 Bearer Token Usage](http://openid.net/specs/openid-connect-core-1_0.html#RFC6750)  [RFC6750].

It is RECOMMENDED that the request use the HTTP  GET  method and the Access Token be sent using the  Authorization  header field.
```http--inline
GET /userinfo HTTP/1.1 
Host: server.example.com 
Authorization: Bearer SlAV32hkKG
``` 
|Parameter  | Comment  |
|--|:--|
| acr | Possible values: <br>`tag:sixdots.be,2016-06:acr_basic`<br>`tag:sixdots.be,2016-06:acr_advanced` |
| amr |Won’t be provided  |
| azp| Won’t be provided |
|auth_time | Will always be provided

#### 4.3.1.2. User info Response Specification
The content type of the response will be `application/jwt`. The response will be signed and encrypted by BMID using the signing and encryption certificate exposed. The itsme Back-End replies with the Identity Data that were requested in the Authorization Request.
<aside class="success">What is the lay-out of the Identity Data that we get back in the Userinfo Response? They are the same as on the eID card </aside>
<aside class="success">What format does the certificate need to be?
It needs to be in ZIP file, X509 format (cer or crt). Pem file is not supported.  </aside>

#### 4.3.1.3. User info Response Example
(Not encrypted nor signed)
 
```http--inline
 HTTP/1.1 200 OK
 Content-Type: application/json
 
 {
    "sub": "248289761001",
    "name": "Jane Doe",
    "email": "janedoe@example.com"
 }
 ```
#### 4.3.1.4. User info Errors
When an error condition occurs, the UserInfo Endpoint returns an Error Response as defined in Section 3 of  [OAuth 2.0 Bearer Token Usage RFC6750](https://tools.ietf.org/html/rfc6750)   (HTTP errors unrelated to RFC 6750 are returned to the User Agent using the appropriate HTTP status code.)

The following is a non-normative example of a UserInfo Error Response:
```http--inline
  HTTP/1.1 401 Unauthorized
  WWW-Authenticate: error="invalid_token",
    error_description="The Access Token expired"
 ```

### 4.3.2. Token Endpoint
As per specified by OIDC, when using the authorization code flow to obtain an Access Token and an ID Token, you will send a token request to token endpoint to have a token response.
If you declared  claims in the `id_Token` part of the `claims` parameter in the `request` object,  you will receive the end user data from the Token Endpoint.
#### 4.3.2.1. Token Endpoint Specs
To get further information about token types, token request/response specifications please proceed with [3.2 Token Endpoint](#tokenEndpoint).
#### 4.3.2.2 Example of Id Token Containing “claims”
*Will be provided soon*
# 5. Advanced topics
## 5.1. <a name="JWTRequest"></a>Passing Request Parameters as JWTs
As per specified by OIDC [here](https://openid.net/specs/openid-connect-core-1_0.html#JWTRequests), Authorization Request parameters to enable Authentication Requests to be signed and optionally encrypted are explained.
 
The Request Object is a JWT token as defined in [RFC 7519](https://tools.ietf.org/html/rfc7519), which contains at least the following properties:
Property | Required | Comment
-- | -- | :--:
**iss** | Required | Specifies the issuing authority. Issuer of the id_tokenIssuer. Must be the `client_id`
**aud** | Required | Audience. MUST be the Token Endpoint URL
 
> Example of claim request before base64url encoding, signing and encryption. In this example, the partner is using the login service.
 
```json--inline
 {
 	// JWT Registered claims (https://tools.ietf.org/html/rfc7519#section-4.1)
 	iss: "s6BhdRkqt3",
 	aud: "https://server.itsme.be",
 	// OIDC parameters, must reflect the values of the HTTP parameters
 	client_id: "s6BhdRkqt3",
 	response_type: "code",
 	redirect_uri: "https://client.example.org/cb",
 	scope: "openid email service:PARTNER_LOGIN",
 	state: "af0ifjsldkj",
 	nonce: "n-0S6_WzA2Mj",
 	claims:
 	{
 		userinfo: {
 		    "tag:itsmetag:sixdots.be,2016-06:claim_nationality": null,
 		},
 		id_token: {
 			auth_time: {"essential": true},
 			acr: {"value": \["tag:itsmetag:sixdots.be,2016-06:acr_advanced"\] 		
 		}
 	}
 }
 ```
 
## 5.2. Requests Signing and Encryption 
Encryption algorithm used: RSA SHA-256
 
Supported algorithms and encryption methods for:
- ID Token
-- Encryption Method (enc): A128CBC-HS256
-- Encryption Algorithm (alg): RSA-OAEP
-- Signing Algorithm (alg): RS256
- User Info
-- Encryption Method (enc): A128CBC-HS256
-- Encryption Algorithm (alg): RSA-OAEP
-- Signing Algorithm (alg):  RS256
- Request Object
-- Encryption Method (enc): A128CBC-HS256
-- Encryption Algorithm (alg): RSA-OAEP
-- Signing Algorithm (alg): RS256
 
Offline access is not supported. 
 
Dynamic client registration is not allowed.
 
itsme(r) exposes its signing and encryption keys on a public endpoint (JWKSet) 
https://merchant.itsme.be/oidc/jwkSet
 
It is expected that you will also expose their signing and encryption keys in such a way. The location of your JWKSet must be configured by an  administrator of BMID during your on-boarding. The exposed endpoint must be HTTPS.
 
# 6. **FAQ**
>**How to use itsme-UAT through Hockey-app on IOS ( TODO only IOS?) properly? (without having"Non-trusted company app developer" message)**

To be able to use itsme on Hockey App, you need to :  
  
Select : “Annuleer”  
Go to your settings  
Select : General  
Select : Profiles & Device Management  
Select : Belgian Mobile ID NV  
Select : Trust
>**Can I have my own itsme app (production) together with the itsme UAT app?**
Currently, you can only have 1 itsme app on your device at one moment in time independently of Production, UAT or E2E.
>**Has itsme app in Prod & UAT different URL-scheme's?**

On iOS:  
- for enrollment, it's always "be.bmid.itsme://" "  
- for app to app actions, it's based on universal links and these are different for each environment depicted in the following table: 

|Environment| URL |
|--|--|
UAT|https://uatmobileapp.sixdots.be/mobile/processAction<br>https://uatmobileapp.sixdots.be/mobile/authorize  
| E2E  |https://e2emobileapp.sixdots.be/mobile/processAction<br> https://e2emobileapp.sixdots.be/mobile/authorize 
| PRD |https://mobileapp.sixdots.be/mobile/processAction  https://mobileapp.sixdots.be/mobile/authorize

>**How can I set the level of security at the level of the App (5 digit code only without fingerprint or facial recognition  eg.)**?
You can configure this option through the parameter “**acr_values**”, documented in   [this](#acrvalues) section. 

> **What is Two Factor Authentication?**

Two Factor Authentication, also known as 2FA, two step verification or TFA (as an acronym), is an extra layer of security that is known as "multi factor authentication" that requires not only a password and username but also something that only that user has on them, i.e. a piece of information only they should know or have immediately to hand - such as a physical token.

The purpose is to use username and password together with a piece of information that only the user knows make it harder for potential intruders to gain access and steal that person's personal data or identity.

2FA in the course of itsme(r):
You must provide at least two from this list :
- knowledge (something you know), Eg. password
- possession (something you have), Eg. mobile phone
- inherence (something you are). Eg. biometrics
 
- With standard login/password on web app: You only have to provide something you know (i.e. the password).
- With itsme, web2app, you obviously have two factors: The itsme PIN and the mobile device.
- With app2app: It is still the same, as the two apps need to be running on the same smartphone. Therefore, the attacker needs the device to initiate any malicious transaction. So there are still two factors : you must possess the mobile AND you must be acknowledged of the PIN.
 
//TODO
Aside of that there's:
With Rogue Apps, an attacker can attempt to intercept Codes, PIN, transaction details and (try to …) replay them: No need to “Possess” the Compromised Device, and attacker will in time obtain “Knowledge” of the PIN, compromising the 2 factors required. In theory this remains possible.
 
So far for theory: We acknowledge this risk, treat it accordingly, and test if it works. itsme’s apps (iOS and Android) are  safeguarded to detect device compromises (and block). On top, even when compromised, make it (extremely) difficult to intercept the PIN, or replay transactions. As the official test by French ANSSI Authorities state it, for Gemalto’s SDK on Android:  “Even if root rights obtained, PIN cannot be obtained to generate OTP / Authentication Response” 
//TODO

>**What is the purpose of the https://uatmerchant.sixdots.be/oidc/register? Seems to be a link to be registered as a SP, but do we make use of it?**

We do not use this as it is part of the OIDC standard. Normally used to register new OIDC partners but the registering process is done by an administrator (“the back end team” for the moment) during the “partner on boarding process”.

>**Is this following auto generated?:**
> https://uatmerchant.sixdots.be/oidc/.well-known/openid-configuration
 
Yes, this page is auto-generated by the back end. But the content is mainly composed of hard coded values, only the environment specific values (like URLs) are loaded from configuration files. It is a summary of the parameters used for BMID implementation of OpenID Connect standard (supported encryption types, list of supported claims and scope values, endpoints, ...).
 
>### <a name id="ServiceCode"></a>[Service Code Concept](#ServiceCode), 	What is it?
To be able to use an itsme service (such as login, confirm, sign, share data) you should be provided a service instance for it. The service code is the identifier of this instance. The same Service Provider may utilise several service instances. 

For example, assuming that one SP would like to use login as an itsme(r) service for business and private channels. In this case, SP could ask BMID to allocate two service instances, one issued for private account login, one for business account login. Consent screen needs to be customised for each instance.
>### I would like to receive a new service code to do my local development...
So we would need a service code for the register operation with the following callback URL: http://localhost:23874/web2app/Identify/IdentificationCallBack  
 
First, you need to  validate that you are able (and **authorized**) to keep the "xxx UAT JWKSet signing and encryption private keys" on your local machine because you will need them to sign the requests and decrypt the JWT tokens.
>**Why App2Back-end communication does not exist but a App2B2B connection does instead?**

We are sending the user information not through an app but to a highly authenticated server (which implies to put in place necessary measure to protect the data confidentiality)  
This condition implies the need to put in place a back end layer on your side.  
 
 <!--stackedit_data:
-eyJoaXN0b3J5IjpbMTExNjg1ODVdfQ==
+eyJoaXN0b3J5IjpbLTE4Nzg1Nzg0NTZdfQ==
 --> 
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTM2ODEyNjQ2OSwtOTA2NTQ0NDkxLC04Mj
k2OTYwNzQsMTQ0MjgxODMwNiwtMTY2MzMyNzQ3OSwtMTk2ODY0
OTA0MCwtMTk3MjQ3NzcwNSwtMjA5Mzk0NjMxNCwtOTA1NDA3OT
Y2LDk4ODMwMjQ0NCw1NDkyMjE0MjYsLTU1MDE2OTU4LC0xMTA4
MzI2NDY5LDI1OTg4ODMxMCwxNjcxMTQzNTY4LDE0NjczOTkzOT
AsLTIwNDM2MDMwNTksMTAxNTMwNTc1MiwtMTM4MTY2ODg1OSwx
MDE3NTU1NDQzXX0=
-->
>**When the end-user is redirected to itsme in the browser, we specify a redirect_uri afterwards. We have to provide BMID the Redirect URIs. Can we give additional parameters? Or does the entire redirect_uri need to match?”**

The entire redirect_uri must match therefore no additional parameter is allowed.

**Questions ONLY related to OpenID Connect (OIDC) aspects,**

**For signature and encryption, does BMID accept either RP self-signed certificates or certificates signed by an internal PKI (in which case we need to provide also our internal CA certificate)?”**

This JWKSet is accessible, using a secured HTTPS URL. (sample HTTPS URL for OpenID Provider https://merchant.itsme.be/oidc/jwkSet)

This HTTPS URL must be communicated to us, during the partner on-boarding.

This HTTPS URL must be protected using a valid certificates chain starting from a Root CA trusted by our back end.

During on-boarding, we can check that we have all the required certificates to establish the trust.

So, on the HTTPS protocol level, the connections must be secured using trusted Root CA (not self-signed). In contrary, the key pairs used for signing and/or encrypting the OIDC JWT tokens can be self-signed.


<!--stackedit_data:
eyJoaXN0b3J5IjpbMTk4MTc1MjU1OCwtMTI2OTc2NjczNiw5Mz
MzOTQzNzUsLTQxNTk4NzE3NywtMzkzOTgzNTc1LDY5NDc1MjM1
OSwtOTgyNDAwMzk2LC0xMTczNzc1NDM1LDk2NzU5MzYxNywtNj
EwOTAzOTk5LDY4NTE2ODAyLDMxNDY0NTg2OSwtMTgyNTM4NTI0
MiwxNjUxODcwMTA2LDIxMTExMzMxODAsLTEyMjY0Mzk0MzksLT
IzNDU1NTI4NCw1MTYyNzAzMCwtNDIxOTc3MiwtODAxMjczMDUy
XX0=
-->