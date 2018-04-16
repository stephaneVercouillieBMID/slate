 
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
 
 This documentation describes our OAuth 2.0 implementation of **itsme® Login**, which conforms to the [OpenID Connect 1.0](http://openid.net/specs/openid-connect-core-1_0.html) specifications.
 
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
 
 <aside class="success">
 Once your Sandbox is created, you will receive:
  <ul> 
    <li>Your Partner Code, which corresponds to the OpenID <b>client_id</b> </li>
    <li>Your Service Codes, which are the identifiers of your instances of itsme(r) services. Please consult <a name="(#ServiceCode)">Service Code Consept</a> for more information about the notion of Service Code. </li>
  </ul>
 </aside>

 
 ## 2.2. Integration of itsme® services
 Once your Sandbox has been created, you can start working on the integration itself. All the information needed to do so is the object of this document.
 ### 2.2.1. itsme® OpenID Configuration
 The OpenID Connect protocol requires the use of multiple endpoints for authenticating users, and for requesting resources including tokens, user information and public keys.
 
 To simplify implementations and increase flexibility, OpenID Connect allows the use of a "Discovery document", a JSON document found at a well-known location containing key-value pairs which provide details about the OpenID Connect provider's configuration including, 
- URIs of the authorization, 
- token, 
- userinfo, 
- supported claims 
-  [JWKSet](#jwks) to interact with it. 
 
 <aside class="success">The Discovery document for itsme® service may be retrieved from: <a href="https://merchant.itsme.be/oidc/.well-known/openid-configuration">https://merchant.itsme.be/oidc/.well-known/openid-configuration</a></aside>
 
 
 Field  names and meanings in this document are defined in [OpenID Connect Discovery 1.0](https://openid.net/specs/openid-connect-discovery-1_0.html)
 
 # 3. Authenticating the User
 **itsme® Login** is based on the [Authorization Code Flow](http://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth) of OpenID Connect 1.0. The Authorization Code Flow goes through the steps as defined in [OpenID Connect Core Authorization Code Flow Steps](http://openid.net/specs/openid-connect-core-1_0.html#CodeFlowSteps), depicted in the following  [diagram](https://www.draw.io/?lightbox=1&highlight=0000ff&edit=_blank&layers=1&nav=1&title=Untitled%20Diagram.html#R7Vxbd6M2EP41flwOQgjwY67dPDTd07Sn20cCss0JRi7IufTXVzLiJuEYY2Fsp96HDQMM1vdpNPMNSibwZvn%2bS%2bqvFr%2bSEMcTywzfJ/B2YlmA/WP/cctHbnE8YZinUSguqgxP0b9YGE1hXUchzhoXUkJiGq2axoAkCQ5ow%2banKXlrXjYjcfOpK3%2bOFcNT4Meq9a8opIvc6lluZf%2bOo/mieDJwpvmZpV9cLEaSLfyQvNVM8G4Cb1JCaP7T8v0Gxxy8Apf8vvstZ8svluKEdrlB4P7qx2sxNvG96Ecx2JSskxDz68EEXr8tIoqfVn7Az74xepltQZexOC3c4ZTi961fCZQDZTMEkyWm6Qe7RNzgCmjE3IDi8K0CGhToLWogO8LmC27npeNq%2bOwHgUA7GvD00BgTDvv04LBHhAOdHhzOiHA4Chx/ZjhVIGGDo81xZzQlL/iGxCRlloQk7MrrWRTHksmPo3nCDgMGBnMMrzlUEVuFr8SJZRSG/DGtQFdUmNw9SajIIwDqwR6AJvZIxd5ugd7SAL2rQB/RbImZ6Wq1%2bjoMwPEY8LYycO0HL99wEn4dGmxvJw3AHIiHqcLDE05fo4Az8SMlr6xOZEuS%2bcCBm3FYvgwrzoisFHH5WapkIXLFS/EK0NDPFmXurBEig8MASD9%2bcuQMVBz%2b3cipOFTqdwk2Jhj8dI6LMbcjWYMKtSBV2FIc%2bzR6bT6xDT7xhB8kYt%2blyiOwSZQjJ%2beMrNMAi7vqtbzsyGo6KhNU4SgfsuJow2Y57G4Ed9AN/QnuSuJYjEGJMSjHTFfGZEcK9RoZ66Btxg7JHDYBjaOGqT0m6UhXmCKn6Wg6HOcdBNzYnNf5RWPy6%2bri13WPxW9BZ41fYGyqnYhGPuU1kB/QiCQbEBmG7Na8Sj35IsjSpBBsKSWaahVkDVUFtTQMLONqTRePzPg7/meNM3oGVGhqVSCpHgW2SkVZseimAqoroW3cbXBiOmHBAbTMx/Xy%2bSwaGLpai0hiZNrCCByKEbV9pCBfg3zFl8vN49D1BN1yC04j9lSO6y1J6YLMSeLHPyrrIJO4iGkBWal0GqpKRczWgZja9Tm5bF6v4BxPzfA56%2bdfwsFjpfhi4nQknaxwsmEvvI/igmp2VE%2brWybBPkR/SiBw9LAl62IApgbqx5dc27W40kjZftr4kihTcNYVYAANSVgHmVRjQE7iz4RSssxJk0htLL4Vp55WHgVkpgEdNG2iNsyyCYrH7N2g2uVoC6eVo%2bJCMptl%2bGDe1focGrdRtop9jutDyJCPZoxqIaGeghQzWr9OfSiLp7aK3R2oPkRqUCKubSt%2b3nymcpM5J6OdmHOIWRMNEqKW3TtXykHa4uq4YYrUMHX4TPiN81V2NFrffO45A55jErwMlWO9Yaoipfbs23u2vG6LcR8GR1EuLLjK0zVZuFktuzIq9aQniqBB3k7ij/giQt9cGEzQIPXN%2bWdheyK6H8q6v61V0sJz2W88aKMNUCBzDTbgGUmXzMyS36ZS8eMsH2atZhlmPaxFnGk1Qw6Y6PCYqweYszvANK2sEDY57v1WD0nvYSHwhoomR82NHs%2bNj4RVrh9FVjTJjJev6yDAWXbopDhmmXRoVSQT0VeOyq8RFEcaGT3L9VHui8KOfVE5z/RaHtU9QApi/fstjXnOkbv3l1HMh/kdx6%2bYh0tLTcKPC7E3saC5%2bbQpvqZUrF/aP2qO1EyVY0KuH/s25xRH%2boKrEKu1qTJtispykdym9i93tYTyatm3mpQliuJII6GWQigAxh8spLg03PaOdXRZKID6xuolZZnUVM3IUWX3rmZ2OTpuN8BV%2b0IAGvnOc/MhmZH/eW/QhWQF0pd3xdGReVcrXWDVIj1bkSRTd7ac8HJd9gCBnnwsx2nv/Uu7HB2ZeLV5BGwl4M%2bYfm0t4GYJjuABr0t3ujryHLgIUVRmziNsFnH3E0Wjb/10R%2b2j6twYYsDaR1LF3tRwap%2bp2ymoegSMpwqdMwwY1LJbc6iA8fbbH3/JuzaQ3JXpK/wURxoneIsMQHtK%2bYt9cS%2b3K1HxCrU%2bebyWQNLym5Et9ZrJmal2Pm8p1i6WD3nrM3Rb1rW2X9PuwQc7rP56RB5Y1d/ggHf/AQ==),
 
![enter image description here](https://lh3.googleusercontent.com/vi1iEAv0LtjFbvT30UE62rHDLu-fPFysH5oj1dpa_hVzaTbmKSV2Js_NjTCI7-5tXGVKgd8p4CQ "auth diag")

## 3.1. **Authorization Endpoint** 
The first step is forming an HTTPS request to the Authorization Endpoint with the appropriate URI parameters. Please note the use of HTTPS rather than HTTP in all the steps of this process; HTTP connections are refused.
### 3.1.1. Authentication Request Specifications
As per the OpenID Connect specification [Authentication Request](http://openid.net/specs/openid-connect-core-1_0.html#AuthRequest) and [Authorization Endpoint](http://openid.net/specs/openid-connect-core-1_0.html#AuthorizationEndpoint).

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
 --------- | ------- | -----|-----
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
 **acr_values** | Optional | OPTIONAL and supported, though NOT RECOMMENDED. Possible values are `tag:itsmetag:sixdots.be,2016-06:acr_basic`, `tag:itsmetag:sixdots.be,2016-06:acr_advanced`. When multiple values are provided only the most constraining will be used (advanced > basic). If not provided basic level will be used. As there is no such idea of an existing session on itsme Core, even if the `acr_values` is requested as a voluntarily claim, the `acr` value returned will always be the more constraining method in the `acr_values` list, or the authentication will fail. Usage of `acr` parameter in the `request` object is recommended over this parameter as it will be signed in the JWT token.| These values are used to specify the level of authentication the RP requires from the OP. BMID imposes more strict security rules on advanced level as for basic.
**claims** | Optional | NOT RECOMMENDED. Usage of claims parameter in the request object is recommended over this parameter as it will be signed in the JWT token, and the data will be encrypted | MAY include claims for end user data
 **request** | Optional | See [Passing Request Parameters as JWTs](#JWTRequest)
 **response_mode** | Unsupported | MUST not be used. Any supplied value will be ignored.|
 **id\_token\_hint** | Unsupported | Ignored if provided.
 **claims_locales** | Unsupported | None are supported.|
 **request_uri** | Unsupported | Not supported (yet)|
 **registration** | Unsupported | Not supported due to “client dynamic registration”is not supported. The client registration process is done during the [on boarding process](#Onboarding).|

### 3.1.2. Authentication Response Specification
An Authentication Response is an [OAuth 2.0 Authorization Response](https://tools.ietf.org/html/rfc6749#section-4.1.2) message. 

The itsme Back-End provides an Authorization Code to the Service Provider Back-End. In the Response, the Service Provider Back-End knows that the User was successfully authenticated.

As such, the Authentication Response will return the following parameters:

 Parameter | Provided | Description
 -- | -- | --
 **code** | Always | Authorization code to later provide to the token endpoint. This code has a lifetime of 3 minutes.
 **state** |  | The exact value received from the client, if the parameter was present in the Authentication Request.
### 3.1.3. Authentication Response Example
 
 ```http--inline
 HTTP/1.1 302 Found
 Location: https://client.example.org/cb?
 code=SplxlOBeZQQYbYS6WxSbIA&
 state=af0ifjsldkj
 ```
### 3.1.4. Authentication Errors
As explained by OIDC [http://openid.net/specs/openid-connect-core-1_0.html#AuthError](http://openid.net/specs/openid-connect-core-1_0.html#AuthError), if the authentication is NOT successful, the following errors can be triggered by itsme®:

 Error | Description
 -- | --
 `interaction_required`  | The Authorization Server requires End-User interaction of some form to proceed.
 `invalid_request_object` | The request parameter contains an invalid Request Object.
 `request_uri_not_supported` | does not support use of the request_uri parameter.
 `registration_not_supported` | does not support use of the registration parameter.
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

The Authentication Response includes a `code` parameter, a one-time authorization code that your server can exchange for an ID token. Your server makes this exchange by sending an HTPS `POST`request. The `POST` request is sent to the token endpoint, which you should retrieve from the [Discovery document](https://merchant.itsme.be/oidc/.well-known/openid-configuration) using the **token_endpoint** key. The following discussion assumes the endpoint is `https://merchant.itsme.be/oidc/token`. Please note that BMID only supports `private_key_jwt` as client authentication method. The `client_secret` authentication methods are not supported since they are considered less secure.

In order to communicate with Token Endpoint, TLS MUST be implemented. See  [Section 16.17](http://openid.net/specs/openid-connect-core-1_0.html#TLSRequirements)  for more information on using TLS.

The Token Request must include the following parameters in the POST body:

Parameter | Required | Comment
 -- | -- | --
 **grant_type** | Required | Must be `authorization_code`. 
 **code** | Required | The code value provided in the Authentication Response
 **redirect_uri** | Required | The **redirect_uri** used in the Authentication Request. This is the URL to which you want the user to be redirected after the authorization is complete.
  **client_assertion** | Required | Must be a valid JWT complying with the `private_key_jwt` client authentication method as defined in [Section 9](http://openid.net/specs/openid-connect-core-1_0.html#ClientAuthentication) of the OpenID specification. This JWT must be signed.
 **client\_assertion\_type** | Required | Must be `urn:ietf:params:oauth:client-assertion-type:jwt-bearer` 

#### 3.2.2.1. `client_assertion` 
According to the `private_key_jwt` client authentication method, the **client assertion** JWT must contain the following properties:

 Property | Comment
 -- | --
 **iss** | The issuer of the `private_key_jwt` (the client ID). MUST be the Partner Code you obtained from BMID during on-boarding process (this information is in the onboarding file provided by BMID).
 **sub** | The subject of the `private_key_jwt` (the client ID). MUST be the Partner Code you obtained from BMID during on-boarding process (this information is in the on boarding file provided by BMID). 
 **aud** | Must be the token endpoint URL
 **jti** | A unique identifier for the token, which can be used to prevent reuse of the token. These tokens MUST only be used once.
 **exp** | Expiration time on or after which the ID Token MUST NOT be accepted for processing.

### 3.2.3. Token Request Example

```http--inline
GET /oidc/authorization?response_type=code
&client_id=yourpartnercode
&yourredirecturl
&scope=openid+service%3Ayourservicecode+profile+
&claims={"userinfo":{" tag:sixdots.be,2016-06:claim_nationality": null},
"id_token":{"auth_time": {"essential": true},"acr_values":{"value":["tag:sixdots.be,2016-06:acr_advanced"]
}}}&state=anystate&nonce=anonce&prompt=login&max_age=1
```
### 3.2.4. Token Response Specification
The Token Response follow these specifications:

 Parameter | Provided | Comment
 -- | -- | --
 **[`access_token`](#actoken)** | Always | Will be provided. 
 **[`token_type`](http://openid.net/specs/openid-connect-core-1_0.html#TokenResponse)** | Always | Will be `Bearer`
 **[`id_token`](#idtoken)** | Always | The id_token corresponding to the Authentication Request (signed and  encrypted). 
 **[`at_hash`](http://openid.net/specs/openid-connect-core-1_0.html#CodeIDToken)** | Never | Current version of itsme(r) Core does not produce the `at_hash` value
 **[`refresh_token`](#rfshtoken)** | Never | Won't be provided as **itsme(r)** only maintains short-lived session to enforce re-authentication.


### 3.2.5. Token Response Example
`{"user_info":{"sub":"qn2b631umr23bpou8rfzbtu79b5q5phxcml8","aud":"OIDC_TEST1","birthdate":"1974-04-12","gender":"male","name":"Ada Gardner","iss":"tokenEndpointURL","given_name":"Ada","locale":"fr","family_name":"Gardner"},"id_token":{"access_token":"UVfXK3QzTRKyFiw3f1v85Yr4ko4o7uI1oJ8XNZeRcJE","id_token":{"sub":"qn2b631umr23bpou8rfzbtu79b5q5phxcml8","aud":"OIDC_TEST1","acr":"tag:sixdots.be,2016-06:acr_basic","auth_time":1523626355,"iss":"tokenEndpointURL","exp":1523626660,"iat":1523626360,"nonce":"anonce"},"token_type":"Bearer","expire_in":163}}`

### 3.2.6. Token Error Response 

As per [OIDC Specification Aggregated Response](http://openid.net/specs/openid-connect-core-1_0.html#TokenErrorResponse).
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
**Any claim requested by using the scope value can only be obtained from the User Info endpoint.</aside>**

The following scope values are supported and allow access to predefined sets of Identity Data:

| Scope Value | Associated Claims|
|--|--|
| profile | given_name, family_name, gender, birthdate,  locale|
email|email, email_verified|
phone| phone_number, phone_number_verified| 
 address|address, with subfields,<br>street_address (newline separator \n)<br> locality <br> postal_code <br> country

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
 -- | -- | -- 
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
|--|--|--|
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
   {ANDROID, IOS})   
  -  “appName” [0…1]: the application name.   
  -   “appRelease” [0…1]: the application current release.   
  -   “deviceLabel” [0…1]: the name of the device.   
  - “debugEnabled” [0…1]: if debug mode is activated.   
  -  “deviceId” [1…1]: (regexp =“[a-f0-9]{33}”) the device identifier.   
  -  “osRelease” [0…1]: Version of the OS running on your Device.  
  -   “manufacturer” [0…1]: Brand of the device manufacturer (‘Apple’ on iOS, device specific on Android).
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

As per the [OpenID Connect specification](http://openid.net/specs/openid-connect-core-1_0.html#UserInfoRequest(http://openid.net/specs/openid-connect-core-1_0.html#UserInfoRequest)

Depending on how you declared claims in the Authentication Request, you will receive the user data from
- The UserInfo Endpoint
- The Token Endpoint, in the ID Token


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

| Parameter  | Comment  |
|--|--|
| acr | Possible values: <br>`tag:sixdots.be,2016-06:acr_basic`<br>`tag:sixdots.be,2016-06:acr_advanced` |
| amr |Won’t be provided  |
| azp| Won’t be provided |
|auth_time | Will always be provided
#### 4.3.1.2. User info Request Example

```http--inline
GET /userinfo HTTP/1.1 
Host: server.example.com 
Authorization: Bearer SlAV32hkKG
``` 
#### 4.3.1.3. User info Response Specification

The content type of the response will be `application/jwt`. The response will be signed and encrypted by BMID using the signing and encryption certificate exposed. The itsme Back-End replies with the Identity Data that were requested in the Authorization Request.
 
#### 4.3.1.4. User info Response Example

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
#### 4.3.1.5. User info Errors
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
 -- | -- | --
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
 ># 6. **FAQ**
 >## <a name="jwks"></a> JWKSet
 >
 >### What is a JWKSet?
 We require you to expose a set of public key (a signature key & one encryption key) to setup the connectivity.
 
 For example our E2E JWKSet is here https://e2emerchant.itsme.be/oidc/jwkSet. We need the Service Provider to expose some similar content – on a very public https endpoint (nothing is confidential in there).
  
 The signature key is used to verify the Service provider when they come fetch data on a client.
 
 The encryption key is used to encrypt data so that nobody can read the data which we are transfering apart from the service provider component who has the right to access these data (so that you can encode/reencode the https in intern in a less secured component; it stays encrypted).
  
 If the question is ONLY related to OpenID Connect (OIDC) aspects,
 >
 > _“For signature and encryption, does BMID accept either RP self-signed certificates or certificates signed by an internal PKI (in which case we need to provide also our internal CA certificate)?”_
 > 
  >_The RP (the partner) provides his public key used for encryption and the other one used for signature of OIDC JWT (= JSON Web Tokens), using a JSON file called “JWKSet”. (sample JWKSet for Itsme OpenID Provider)_
 >
 - This JWKSet is accessible, using a secured HTTPS URL.
 (sample HTTPS URL for OpenID Provider https://merchant.itsme.be/oidc/jwkSet)
  
 - This HTTPS URL must be communicated to us, during the partner on-boarding.<br>
 - This HTTPS URL must be protected using a valid certificates chain starting from a Root CA trusted by our backend.
  >
 During on-boarding, we can check that we have all the required certificates to establish the trust.<br>
 So, on the HTTPS protocol level, the connections must be secured using trusted Root CA (not self-signed).
 In contrary, the key pairs used for signing and/or encrypting the OIDC JWT tokens can be self-signed.
 
 >### How to create JWKSet?
 
 The following JSON Web Key Generator can be used to create JWKSet
  ```
 https://mkjwk.org/ 
  ```
 Another resource you could use to generate your JWKSet is: 
  ```
 https://connect2id.com/products/nimbus-jose-jwt/examples/jwk-generation
  ```
 Another option (using python) is available at:
  ```
 https://stackoverflow.com/questions/42504079/how-do-you-extract-n-and-e-from-a-rsa-public-key-in-python
  ``` 
 
 
 >### How to Transfer JWKSets?
 The following link can be used to convert encoded public and private keys to JWKSet format:
  ```
 https://www.npmjs.com/package/rsa-pem-to-jwk
  ```
  >
 >### What are the JWKSet requirements?
 In Opend ID Connect 1.0 the important is the jwKset which is exposed on a public URL and the linked chain of certificates which is included in our trust store.
 >
 This is 1 per partner. So in UAT 1, in Prod, for your 4 clients, it would be 1 JWKSet if it's under Norbloc name or 4 JWKSet if it's under each client name.
 >
 When you send request to us,
 
   - You sign the request with your 'sign' private key and to encrypt it you use our 'encryption' public key.
   - Once we answer your request, we send an encrypted JWT token (based on your 'encryption' public key) which needs to be decrypted with your 'encryption' private key. 
   - Once decrypted, you need to validate our signature with our 'public' sign key found in our JWKSet.
 
 We are here working in HTTPS Client Authentication.For more details the best is to refer to the Open ID Connect documentation(http://openid.net/connect/) - and more specifically follow links relating JWT encryption & signing.
 
 For the call back URI (not URL) you will need in UAT 4 URI, one per service. So in Prod, for 4 clients using each the 4 services, you would have at least 16 Service codes.
 
 >### What are the JWKSet encryption requirements?
 In the JWKSet, encryption should be done with **RSA256**,  the signature in **SHA256** (in short the encryption algorithm is RS256) and the key size is 2048 bits.
 
 As a comparison here's the public keys for itsme UAT JWKSet.<br/>
 >**JWKSET itsme(r) UAT**
  
 
 ```json--inline
 {
 "keys": [
 {
 "kty": "RSA",
 "e": "AQAB",
 "use": "sig",
 "kid": "s1",
 "n": "46FaPodZLqflpnRlFpxwDWT7WNweWAtJ_QYJML2XjB71AlgW20D97VekezlxIudYXbp3aNSkIBcaBQGhzTNHQWuBPwTfemeH9KC2iOTEm3Bu2CsAtaeLzAJT2BjSC2Q4ZUAP2pUq1UQh1XoqjzViXzZIRV35eesKZD301bPsJ6E4z4VKkuzcCeG4jIicNWmbWjkdbdhDe39Ja-BhZrhRmfeoVUe_h2SuplVip4MycLggaO89LomHWF7GktNsmHkt1xOtBgWeCNYj7exXSegoR-29HMS4lzFielxOlr3K0oS8ImMHx-6aRbe3635vfn6Jy6Q9_uAlbWTUsdtFhnpofw"
 },
 {
 "kty": "RSA",
 "e": "AQAB",
 "use": "enc",
 "kid": "e1",
 "n": "rvwzdu-sVCABIgd4plRfoLifTZw6K7JKHuA8HeSgJvUYDUk5MPA7xNKQ5xIXyxisKUzMqIeGaWuaHoFTg4uy-mBFOpm-vG5yGr5GdMCtsA1Ub80x_MmdbkUG3MFsGqz7hE4VpQw0docrrsoS2D9plAMpGA3-_a2u6T1fxomBY5_sxFvyW3erkvjFPG3waU3b34txVIEvdZ6KAjAWR57Y6C749T6ky0eL_uBvPwcq9xmQ6O327MKU8sakEBXb57KDqe4Vm8CbezwIUAC23ia5sO1grqlJGDZW_dbosTcggCxgHW7m6j9RdkGR_mnmECukitwlNvXeCLQPFSunlUh52Q"
 }
 ]
 }
 ```  
 You can find the configuration parameters in in document « 33020 Technical Specification Service Provider OpenID Documentation » or via the following back-end URL:
 
  https://uatmerchant.sixdots.be/oidc/.well-known/openid-configuration<br> 
  >### What should I do once I changed my JWKSet?
  Please notify Support and create a new certificate.
 
 >### What should I do once I changed my JWKSet but not the URL, does it mean no new certificates?<br>
  You should contact support and mention the change to allow a refresh of the cache on back-end side, this should be solved in March where the refresh time will be 30 min.
  
 A workaround to force a refresh, let the SP send a request with an unknown **"KID"** (key id).<br>
 Because not found in the current cache, this one will be refreshed. But the client has to correct his JWKSet first, if not yet done. 
 
  >### <a name id="ServiceCode"></a>[Service Code Concept](#ServiceCode), 	What is it?
  To be able to use an itsme service (such as login, confirm, sign, share data) you should be provided a service instance for it. The service code is the identifier of this instance. The same Service Provider may utilise several service instances. 

For example, assuming that one SP would like to use login as an itsme(r) service for business and private channels. In this case, SP could ask BMID to allocate two service instances, one issued for private account login, one for business account login. Consent screen needs to be customised for each instance.

 >
 >### I would like to receive a new service code to do my local development...
   
 So we would need a service code for the register operation with the following callback URL: http://localhost:23874/web2app/Identify/IdentificationCallBack  
 
 First, you need to  validate that you are able (and **authorized**) to keep the "xxx UAT JWKset signing and encryption private keys" on your local machine because you will need them to sign the requests and decrypt the JWT tokens.
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
<!--stackedit_data:
eyJoaXN0b3J5IjpbMTU1OTY5OTA3OCwxNjMyODU1MTksLTYyNz
QzMTMyOCwtMTQzNzk3NjY4MCwyNTIxMDM1ODYsMTg3MDExMDQ2
LDUwNjQ2OTc4LDIxMjM2OTEwMDksLTExNTYwNzMyNzEsNTMyMT
I2OTgsOTA2OTk1OTQ5LDIxNDIzMzkyNzddfQ==
-->