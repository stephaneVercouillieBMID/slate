 
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
 
 This documentation describes our OAuth 2.0 implementation of **itsme® Login** and **itsme(r) Confirm**, which conforms to the [OpenID Connect 1.0](http://openid.net/specs/openid-connect-core-1_0.html) specifications.
  ## 1.2. The itsme® services in a few words
 
itsme® offers 4 strong enablers for every process digitalization project. Further information is provided in the links about itsme(r) services.

### 1. [**itsme®Login**](https://brand.belgianmobileid.be/d/CX5YsAKEmVI7/documentation#/documentation/general-information/login-with-itsme)
### 2. [**itsme®Confirm**](https://brand.belgianmobileid.be/d/CX5YsAKEmVI7/documentation#/documentation/general-information/confirm-with-itsme)
 ### 3. [**itsme®Shared Data**](https://brand.belgianmobileid.be/d/CX5YsAKEmVI7/documentation#/documentation/general-information/shared-data)
  ### 4. Sign (*Coming Soon*)
 # 2. Integration Preparation: On boarding Process
 
 Before your application can use **itsme®** for user login, you must set up a project to obtain OIDC credentials, set redirect URIs for your services, and customise the branding information that your users see on the **itsme®** user consent screen.
 
 ## 2.1. Customize the user consent screen
Consent is used to allow an end user to grant a client access to resources and consent screen describes the information that the user is releasing and the terms that apply.

In order to customize your consent screen, feel free to follow the steps via this [link](https://brand.belgianmobileid.be/d/CX5YsAKEmVI7/let-s-get-started#/step-by-step-guide/4-customize-your-sandbox).

Service Provider (you) will provide,
 
 - Partner name, description and localized labels
 - Services' name, description and localized labels
 - Data access with justification (to comply with GDPR)
 
 ## 2.2. Obtain OAuth 2.0 credentials
 
OAuth 2.0 means one protocol for authentication and authorization (obtaining access tokens).

 BMID provides,
 
 - Partner Code, used as **client_id**
 - [Service Code](#ServiceCode) for each

To be able to use an itsme service (such as login, confirm, sign, share data) you should be provided a service instance for it. The service code is the identifier of this instance. The same Service Provider may utilise several service instances. 

For example, assuming that one SP would like to use login as an itsme(r) service for business and private channels. In this case, SP could ask BMID to allocate two service instances, one issued for private account login, one for business account login. Consent screen needs to be customised for each instance.
 ## 2.3. Setting a Redirect URI and Certificates
After a user successfully is authorized, the authorization server will redirect the user back to the application with either an **authorization code** or **access token** in the URL.

 - the JWKSet HTTPS endpoint exposing the signing and encryption public certificates of the SP.
 - the SSL/TLS certificate used on the JWKSet HTTPS endpoint of the SP. 
 - Whenever TLS is used, a TLS server certificate check MUST be performed, per [RFC 6125](https://openid.net/specs/openid-connect-core-1_0.html#RFC6125).
 
 Service Providers provides,
- the redirect URIs for each Service to use within the OpenID Connect protocol to send back the response of the Authentication Request. 


 ## 2.4. itsme® OpenID Configuration
 The OpenID Connect protocol requires the use of multiple endpoints for authenticating users, and for requesting resources including tokens, user information and public keys.
 
 To simplify implementations and increase flexibility, OpenID Connect allows the use of a "Discovery document", a JSON document found at a well-known location containing key-value pairs which provide details about the OpenID Connect provider's configuration including, 
- URIs of the authorization, 
- token, 
- userinfo, 
- supported claims 
-  [JWKSet](#jwks) to interact with it. 
 
 <aside class="success">The Discovery document for itsme® service may be retrieved from: <a href="https://merchant.itsme.be/oidc/.well-known/openid-configuration">https://merchant.itsme.be/oidc/.well-known/openid-configuration</a></aside>
 
 
 Field  names and meanings in this document are defined in [OpenID Connect Discovery 1.0](https://openid.net/specs/openid-connect-discovery-1_0.html)
 
 # 3. Authenticating User
 **itsme® Login** is based on the [Authorization Code Flow](http://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth) of OpenID Connect 1.0. The Authorization Code Flow goes through the steps as defined in [OpenID Connect Core Authorization Code Flow Steps](http://openid.net/specs/openid-connect-core-1_0.html#CodeFlowSteps), depicted in the following diagram,
 
 TODO fix dashed lines
![enter image description here](https://lh3.googleusercontent.com/J-_-tdG9FnHm5wCknxUd2Khjtp8CHb1pfIwxdGLm74RvThkZinxXfN1sXCg68wLwo5UjmFK92WA)

## 3.1. **Authorization Endpoint** 
### 3.1.1. Authentication Request Specifications
As per the OpenID Connect specification [Authentication Request](http://openid.net/specs/openid-connect-core-1_0.html#AuthRequest) and [Authorization Endpoint](http://openid.net/specs/openid-connect-core-1_0.html#AuthorizationEndpoint).

The first step is forming an HTTPS request with the appropriate URI parameters. Please note the use of HTTPS rather than HTTP in all the steps of this process; HTTP connections are refused.

You should retrieve the base URI from the [Discovery document](https://merchant.itsme.be/oidc/.well-known/openid-configuration) using the key **authorization_endpoint**. The following discussion assumes the endpoint is `https://merchant.itsme.be/oidc/authorize`.

**itsme®** supports the use of both HTTP `GET` and `POST` methods. If using the HTTP `POST` method, the request parameters must be serialized using [Form Serialization](http://openid.net/specs/openid-connect-core-1_0.html#FormSerialization).

Please check the following table for request parameters,

Parameter | Required | Comment| Parameter Explained
 --------- | ------- | -----|-----
 **client_id** | Required | MUST be the Partner Code you obtained from BMID during on-boarding process and this value will be unique to each partner per environment. This information is in the on-boarding file provided by BMID.|This is your client identifier at the OpenID Provider.
 **response_type** | Required | MUST be <code>code</code>.|Used to indicate an authorization code flow.
 **scope** | Required | MUST contain at least `openid` or an HTTP ERROR `not_implemented` will be returned. `offline_access` value will yield an error. MUST also includes the target service in the form `service:<SERVICE_CODE>` as provided by BMID during the on-boarding process see (<a name="ServiceCode"></a>[Service Code Concept](#ServiceCode) for further information). ***Note**: Requested data will only be provided based on your current accesses*. These accesses are specified in the on-boarding file provided by BMID.|Used to specify the scope of the requested authorization in OAuth. The scope value `openid`signals a request for OpenID authentication and ID token.
 **redirect_uri** | Required | should be the HTTPS endpoint on your server that will receive the response from **itsme(r)**. This value MUST match one of the values provided to BMID during on-boarding process. ***Note**: the Partner can define different **redirect_uri** specific to each Service.*|Your callback URI for the authentication response.
 **state** | an appropriate value is RECOMMENDED | should include the value of the anti-forgery unique session token, as well as any other information needed to recover the context when the user returns to your application.|Value set by your end to maintain state between request and callback.
 **nonce** | Optional | a random value generated by your app that enables replay protection when present.|The purpose is to prevent id token to be displaced by an attacker by sending a valid id token from a different request in order to trick user visiting another website. The nonce parameter value needs to include per-session state and be unguessable to attackers. One method to achieve this for Web Server Clients is to store a cryptographically random value as an HttpOnly session cookie and use a cryptographic hash of the value as the nonce parameter. In that case, the nonce in the returned ID Token is compared to the hash of the session cookie to detect ID Token replay by third parties. A related method applicable to JavaScript Clients is to store the cryptographically random value in HTML5 local storage and use a cryptographic hash of this value.
 **login_hint** | Optional | OPTIONAL and supported, though NOT RECOMMENDED. Only phone numbers are supported as `login_hint`. Format is : `<countrycode>+<phonenumber>`. E.g. `login_hint=32+123456789`. Usage of claim value `phone_number` in an encrypted request object is recommended in order to avoid disclosure of phone number of the end user on the user agent (such as mobile app or web browser)|Hint to the Authorization Server about the login identifier the End-User might use to log in. If the client receives a value for this string-valued parameter, it MUST include it in the Authentication Request as the login_hint parameter value
 **display** | Optional | MUST be `page` if provided. Other values will yield an HTTP ERROR `not_implemented`.|An ASCII string value for specifying how the authorization server displays the authentication and consent user interface pages.
 **prompt** | Optional | MUST be `consent` if provided.|A space-delimited list of string values that specifies whether the authorization server prompts the user for re authentication and consent. By giving 'consent' as value, the authorization server prompts the user for consent before returning information to the client.
 **ui_locales** | Optional | Can be used to specify the language to be used by the Open ID login page. Supported values are: {“fr”, “nl”, “en”, “de”}. Any other value will be ignored. |Open ID Providers MUST support requests for preferred languages and scripts for the user interface and for Claims via the ui_locales. For instance, the value "fr-CA fr en" represents a preference for French as spoken in Canada, then French (without a region designation), followed by English (without a region designation).
 **max_age** | Optional | Supported but not used: **itsme(r)** will always actively re-authenticate the End-User. |Specifies the allowable elapsed time in seconds since the last time the End-User was actively authenticated by the OP. If the elapsed time is greater than this value, the OP MUST attempt to actively re-authenticate the End-User.
 **acr_values** | Optional | OPTIONAL and supported, though NOT RECOMMENDED. Possible values are `tag:itsmetag:sixdots.be,2016-06:acr_basic`, `tag:itsmetag:sixdots.be,2016-06:acr_advanced`. When multiple values are provided only the most constraining will be used (advanced > basic). If not provided basic level will be used. As there is no such idea of an existing session on itsme Core, even if the `acr_values` is requested as a voluntarily claim, the `acr` value returned will always be the more constraining method in the `acr_values` list, or the authentication will fail. Usage of `acr` parameter in the request object is recommended over this parameter as it will be signed in the JWT token.| ACR values MUST comply with the eIDAS Specification and have the following values:  http://eidas.europa.eu/LoA/high,  http://eidas.europa.eu/LoA/substantial
**claims** | Optional | NOT RECOMMENDED. Usage of claims parameter in the request object is recommended over this parameter as it will be signed in the JWT token, and the data will be encrypted
 **request** | Optional | See [Passing Request Parameters as JWTs](#JWTRequest)
 **response_mode** | Unsupported | MUST not be used. Any supplied value will be ignored.|Informs the Authorization Server of the mechanism to be used for returning parameters from the Authorization Endpoint. 
 **id\_token\_hint** | Unsupported | Ignored if provided.
 **claims_locales** | Unsupported | None are supported.|End-User's preferred languages and scripts for Claims being returned
 **request_uri** | Unsupported | Not supported (yet)|The request_uri value is a URL using the https scheme referencing a resource containing a Request Object value, which is a JWT containing the request parameters. This parameter enables Open ID Connect requests to be passed by reference, rather than by value.
 **registration** | Unsupported | Not supported due to “client dynamic registration”is not supported. The client registration process is done during the partner onboarding.|This parameter is used by the Client to provide information about itself to a Self-Issued OP that would normally be provided to an OP during Dynamic Client RegistrationApproval**, which conforms to the [OpenID Connect 1.0](http://openid.net/specs/openid-connect-core-1_0.html) specifications.

 ### 3.1.2. Authentication Request Examples
 
  ```http--inline
 GET /authorize?response_type=code
 &scope=openid%20profile%20email%20service%3Aclient.registration
 &client_id=s6BhdRkqt3
 &state=af0ifjsldkj
 &redirect_uri=https%3A%2F%2Fclient.example.org%2Fcb HTTP/1.1
 Host: server.itsme.be
 ```

### 3.1.3. Authentication Response Specification
An Authentication Response is an [OAuth 2.0 Authorization Response](https://tools.ietf.org/html/rfc6749#section-4.1.2) message. 

The itsme Back-End provides an Authorization Code to the Service Provider Back-End. In the Response, the Service Provider Back-End knows that the User was successfully authenticated.

As such, the Authentication Response will return the following parameters:
 Parameter | Provided | Description
 -- | -- | --
 **code** | Always | Authorization code to later provide to the token endpoint. This code has a lifetime of 3 minutes.
 **state** |  | The exact value received from the client, if the parameter was present in the Authentication Request.
### 3.1.4. Authentication Response Example
 
 ```http--inline
 HTTP/1.1 302 Found
 Location: https://client.example.org/cb?
 code=SplxlOBeZQQYbYS6WxSbIA&
 state=af0ifjsldkj
 ```
### 3.1.5. Authentication Errors
As per explained by OIDC [http://openid.net/specs/openid-connect-core-1_0.html#AuthError](http://openid.net/specs/openid-connect-core-1_0.html#AuthError), if the authentication is NOT successful, the following errors can be triggered by itsme®:

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
>[https://uatmerchant.sixdots.be/oidc/token](https://uatmerchant.sixdots.be/oidc/token)

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

- Access tokens are used as bearer tokens. 
- They have short lifespan, expire for improving security. The user must authenticate again to get a new access token limiting the exposure of the fact that it’s a bearer token.

#### <a name id="rfshtoken"></a> 3.2.1.3 Refresh Tokens (_not supported_) 

As access tokens have short lifespan, there is a need for refresh tokens in order to take forward long-lived sessions. We do not support refresh tokens due to sessions are not maintained.

### 3.2.2. Token Request Specification
As per the [OIDC specification](http://openid.net/specs/openid-connect-core-1_0.html#TokenRequest), with a private_key_jwt for client authentication.

The Token Request must include the following parameters in the POST body:

Parameter | Required | Comment
 -- | -- | --
 **grant_type** | Required | Must be `authorization_code`. 
 **code** | Required | The code value provided in the Authentication Response
 **redirect_uri** | Required | The **redirect_uri** used in the Authentication Request. This is the URL to which you want the user to be redirected after the authorization is complete.
  **client_assertion** | Required | Must be a valid JWT complying with the `private_key_jwt` client authentication method as defined in [Section 9](http://openid.net/specs/openid-connect-core-1_0.html#ClientAuthentication) of the OpenID specification. This JWT must be signed.
 **client\_assertion\_type** | Required | Must be `urn:ietf:params:oauth:client-assertion-type:jwt-bearer` 

#### 3.2.2.1. `private_key_jwt` 
According to the `private_key_jwt` client authentication method, the **client assertion** JWT must contain the following properties:

 Property | Comment
 -- | --
 **iss** | The issuer of the `private_key_jwt` (the client ID). MUST be the Partner Code you obtained from BMID during on-boarding process (this information is in the onboarding file provided by BMID).
 **sub** | The subject of the `private_key_jwt` (the client ID). MUST be the Partner Code you obtained from BMID during on-boarding process (this information is in the on boarding file provided by BMID). 
 **aud** | Must be the token endpoint URL
 **jti** | A unique identifier for the token, which can be used to prevent reuse of the token. These tokens MUST only be used once.
 **exp** | Expiration time on or after which the ID Token MUST NOT be accepted for processing.

### 3.2.3. Token Request Example

https://uatmerchant.sixdots.be/oidc/authorization?response_type=code&client_id=yourpartnercode&yourredirecturl&scope=openid+service%3Ayourservicecode+profile+&claims={"userinfo":{" tag:sixdots.be,2016-06:claim_nationality": null},"id_token":{"auth_time": {"essential": true},"acr_values":{"value":["tag:sixdots.be,2016-06:acr_advanced"]}}}&state=anystate&nonce=anonce&prompt=login&max_age=1

### 3.2.4. Token Response Specification
The Authentication Response includes a `code` parameter, a one-time authorization code that your server can exchange for an ID token. Your server makes this exchange by sending an HTPS `POST`request. The `POST` request is sent to the token endpoint, which you should retrieve from the [Discovery document](https://merchant.itsme.be/oidc/.well-known/openid-configuration) using the **token_endpoint** key. The following discussion assumes the endpoint is `https://merchant.itsme.be/oidc/token`. Please note that BMID only supports `private_key_jwt` as client authentication method. The `client_secret` authentication methods are not supported since they are considered less secure.
Client must validate following parameters,

 Parameter | Provided | Comment
 -- | -- | --
 **[`access_token`](#actoken)** | Always | Will be provided. 
 **[`token_type`](http://openid.net/specs/openid-connect-core-1_0.html#TokenResponse)** | Always | Will be `Bearer`
 **[`id_token`](#idtoken)** | Always | The id_token corresponding to the Authentication Request (signed and  encrypted). 
 **[`at_hash`](http://openid.net/specs/openid-connect-core-1_0.html#CodeIDToken)** | Never | Current version of itsme(r) Core does not produce the `at_hash` value
 **[`refresh_token`](#rfshtoken)** | Never | Won't be provided as **itsme(r)** only maintains short-lived session to enforce re-authentication.


### 3.2.5. Token Response Example
{"user_info":{"sub":"qn2b631umr23bpou8rfzbtu79b5q5phxcml8","aud":"OIDC_TEST1","birthdate":"1974-04-12","gender":"male","name":"Ada Gardner","iss":"tokenEndpointURL","given_name":"Ada","locale":"fr","family_name":"Gardner"},"id_token":{"access_token":"UVfXK3QzTRKyFiw3f1v85Yr4ko4o7uI1oJ8XNZeRcJE","id_token":{"sub":"qn2b631umr23bpou8rfzbtu79b5q5phxcml8","aud":"OIDC_TEST1","acr":"tag:sixdots.be,2016-06:acr_basic","auth_time":1523626355,"iss":"tokenEndpointURL","exp":1523626660,"iat":1523626360,"nonce":"anonce"},"token_type":"Bearer","expire_in":163}}

### 3.2.6. Token Error Response 

As per [OIDC specification aggregated Response](http://openid.net/specs/openid-connect-core-1_0.html#TokenErrorResponse).
# 4. User Data
## 4.1. What is a claim?
The concept of claim is about declaring the end user data you will need for your business before the authentication. This is a privacy-oriented way of getting data.
Technically, you have to declare the claims in the Authorization Request in the way described in the section [Declaring Claims](#decClaim).
Claims will come as name/value pairs packaged in a JSON object that contain information about a user, as well as meta-information about the OIDC service. The official definition from the spec is a [“piece of information asserted about an Entity.”](http://openid.net/specs/openid-connect-core-1_0.html#Terminology)


### 4.1.1. Claim Types
Three representations of Claim Values are defined officially: 
- Normal Claims
- Aggregated Claims (not supported)
- Distributed Claims (not supported)
 
We do not support Aggregated and Distributed Claims due to all the data we expose come from our database.
 
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

In current version and in contradiction to the OpenID Connect specification, **itsme(r)** considers all claims requested via scope as **Essential** (see [Individual Claim Request](http://openid.net/specs/openid-connect-core-1_0.html#IndividualClaimsRequests)).

 It means the User may not opt out the sharing of specific Data; the User must either gives his consent for the sharing of all Data or refuse the request as a whole. However, as in a future version **itsme(r)**  will make the difference between **Essential** and **Voluntary** claims, you should already request claims with appropriate level regarding your business case. 

## <a name id="decClaim"></a> 4.2. Declaring Claims

You can declare Claims in two ways:

 - With 'scope' values
 - With 'claims' parameter

In each case, the claims need to be declared in the Authorization Request.

### 4.2.2. Scope

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
URL Parameters: TODO exp

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
E-ID Info  | **`tag:itsmetag:sixdots.be,2016-06:claim_eid`** | Belgian Electronic ID card information encoded in JSON, with the following keys<br>`eid`: the electronic ID card serial number. <br>`issuance_locality`: the issuance locality. <br>`validity_from`: eID card validity “from” date. <br>`validity_to`: eID card validity “to” date. <br>`certificate_validity`: the certificate validity. <br>`read_date`: the data extraction date. <br>Each date is encoded using ISO 8601 UTC (timezone) date format. Example of ISO 8601 UTC date: 2017-04-01T19:43:37+0000 
Passport Number | **`tag:sixdots.be,2017-05:claim_passport_sn`** | Simple string containing the user’s Passport Serial Number. 
Device | **`tag:sixdots.be,2017-05:claim_device`** |Information about the end user device. A JSON object with the following keys: (only keys with cardinality [1..1] will be always available)<br>• “os” [1..1]: the device operating system (supported values: {ANDROID, IOS})<br>• “appName” [0..1]: the application name.<br>• “appRelease” [0..1]: the application current release.<br> • “deviceLabel” [0..1]: the name of the device. <br>• “debugEnabled” [0..1]: if debug mode is activated. <br>• “deviceId” [1..1]: (regexp = "[a-f0-9]{33}") the device identifier.<br> • “osRelease” [0..1]: Version of the OS running on your Device. <br>• “manufacturer” [0..1]: Brand of the device manufacturer (‘Apple’ on iOS, device specific on Android).<br> • “hasSimEnabled” [0..1]: Whether there is a SIM in the Device. Should be always true, as long as BMID keeps forbidding installing itsme on a tablet.<br> • “deviceLockLevel” [0..1]: The type of action to be performed to unlock the Device. On iOS : TOUCH_ID, PASSCODE or NONE if User protected his Device with TouchID, PIN or nothing.<br> • “smsEnabled” [0..1]: Can send SMS. On iOS, means it’s an iPhone.<br> • “rooted” [0..1]: Coming from Gemalto. ‘true’ the device is jailbreaked/rooted.<br> • “imei” [0..1]: (regexp = "[0-9]{15,17}") the device IMEI value. <br>• “deviceModel” [0..1]: Model of the Device. <br> e.g. SAMSUNG GALAXY A5 <br>• “msisdn” [0..1]: the user’s phone number. <br>• “sdkRelease” [0..1]: Sdk release <br>For example:<br>`{ "os": "ANDROID", "appName": "itsme app", "appRelease": "1.17.13", "deviceLabel": "myDevice", "debugEnabled": false, "deviceId": "deviceId", "osRelease": "Android 4.4.2", "manufacturer": "samsung", "hasSimEnabled": true, "deviceLockLevel": "touchID", "smsEnabled": true, "rooted": false,"imei": "12345678901234567", "deviceModel": "S8", "msisdn": "0412123123", "sdkRelease": "1.17.12" }`<br>
Transaction Info| **`tag:sixdots.be,2017-05:claim_transaction_info`** |Information available in the context of the current transaction.<br> A JSON object with the following keys:<br> (only keys with cardinality \\\[1..1\\\] will be always available)<br> **“securityLevel” \\\[1..1\\\]**: (supported values: <br>{SOFT\\\_ONLY, SIM\\\_ONLY, SIM\\\_AND\\\_SOFT}) Security level used during transaction. <br>**“bindLevel” \\\[1..1\\\]**: (supported values: {SOFT\\\_ONLY, SIM\\\_ONLY, SIM\\\_AND\\\_SOFT}) tells if the user account is bound to a SIM or not, at the time the transaction occurred. <br>**“mcc” \\\[0..1\\\]**: the Mobile Country Code. An Integer (three digits) representing the mobile network country. <br>For example:<br>`{ "securityLevel": "SIM\\\_AND\\\_SOFT", "bindLevel": "SIM\\\_AND\\\_SOFT", "mcc": 206 }`
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
 
## 4.3. Getting Data

As per the [OpenID Connect specification](http://openid.net/specs/openid-connect-core-1_0.html#UserInfoRequest(http://openid.net/specs/openid-connect-core-1_0.html#UserInfoRequest)

Your server sends the User Info Request using either HTTP  `GET`  or HTTP  `POST`. The Access Token obtained from an Authentication Request must be sent as a Bearer Token. It is recommended that the request use the HTTP  `GET`method and the Access Token be sent the using the  `Authorization`  header field. The HTTP request is sent to the User Info endpoint, which you should retrieve from the  [Discovery document](https://merchant.itsme.be/oidc/.well-known/openid-configuration)  using the key  **userinfo_endpoint**.

The Access Token will define the list of Data that will be provided back to the client. In order to request specific claims, you can  [use scopes](https://stackedit.io/app#stClaims)  in the Authentication Request and/or  [use the claims parameter](https://stackedit.io/app#Claims-Request)  of the  request Object.

### 4.3.1. User Info Endpoint
As per the [OpenID Connect specification](http://openid.net/specs/openid-connect-core-1_0.html#UserInfoRequest), 

The UserInfo endpoint returns previously consented user profile information to the client app. For that a valid access token is required.

The content type of the response will be application/JWT. The response will be signed and encrypted. 

The UserInfo endpoint can be accessed only with a valid **access_token** and for a very limited duration after end user authentication. There must be _less than 3 minutes_ between the creation of the user action to be confirmed by the end user on his mobile device, and the access to the User Info Endpoint (this reflects a constraint set on the SOAP version of the interface).

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
As per specified by OIDC, when using the authorization code flow to obtain an Access Token, an ID Token, you will send a token request to token endpoint to have a token response.

In order to communicate with Token Endpoint, TLS MUST be implemented. See  [Section 16.17](http://openid.net/specs/openid-connect-core-1_0.html#TLSRequirements)  for more information on using TLS.

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
 **aud** | Required | Audience. Possible token end-point URLs:<br>https://merchant.itsme.be/oidc/token<br>https://e2emerchant.itsme.be/oidc/token
 
 > Example of claim request before base64url encoding, signing and encryption. In this example, the partners is using the login service. The end user email and nationality will be returned by the User Info endpoint.
 
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
 
 
 > ## **CERTIFICATE**
 > 
 >### **What is the use of our Certificate?**
 >
 Certificate is used to retrieve your JWKSet. We don't need client certificate, just root and intermediate CA and final public certificate.<br>
 For testing, you could use a self signed certificate like that you could define validity period. 
 
 >### **What format does the certificate need to be?**
 It needs to be a ZIP file,  X509 format (cer or crt). _(Not a pem file)_
 
 > ### **Can I use a self signed certificate for my integration?**
  In Sandbox - **Yes** 
 In Production - **No**, for production we need the certificate & its intermediate chain.
 
 >### **Should I notify BMID if the certificate is changed?**
 Yes, BMID should be notified immediately.
 
 >### **Can a same service point to separate SSL Certificates?**
 Currently, the certificate is used to protect the JWKSet and is not directly linked to the SSL certificate, so for one Partner code you have maximum 1 JWKSet.
 
 >
 >## AUTHENTICATION
 >### Do we need a App Client Secret on the OpenID client?
 
 OpenID allows multiple ways for authentication as a Service ProviderWe only support the private\_key\_jwt, with all the SSL requirements exposed via JWKSet, use the corresponding private key to encrypt/sign and decrypt/validate exchanged information.  
 So other authentication ways such as client_secret (Open ID Connect default method but the less secured one)
 
 >### What does two factor authentication mean?
 Two factor means:  
   
 You must provide at least two from this list :  
 \- **knowledge** (something you know), Eg. password  
 \- **possession** (something you have), Eg. mobile phone  
 \- **inherence** (something you are). Eg. biometrics  
   
 With standard login/password on web app : you only have to provide something you know (i.e. the password).  
   
 With itsme, web2app, you obviously have two factors : the itsme PIN and the mobile device.  
-With app2app, it is still the same, as the two apps needs to be running on the same smartphone. Therefore, the attacker needs the device to initiate any malicious transaction. So there are still two factors : you must possess the mobile AND know the PIN.  
+With app2app, it is still the same, as the two apps need to be running on the same smartphone. Therefore, the attacker needs the device to initiate any malicious transaction. So there are still two factors : you must possess the mobile AND know the PIN.  
   
 When you say (or your customer says) “Since you no longer need a second device to do the confirmation.” It is a miscomprehension of two factors. You don’t need to possess two things.  
   
 Aside of that there's:  
 With Rogue Apps, an attacker can attempt to intercept Codes, PIN, transaction details and (try to …) replay them: No need to “Possess” the Compromised Device, and attacker will in time obtain “Knowledge” of the PIN, compromising the 2 factors required. In theory this remains possible.  
   
 So far for theory: We acknowledge this risk, treat it accordingly, and test if it works. itsme’s apps (iOS and Android) are safeguarded to detect device compromises (and block). On top, even when compromised, make it (extremely) difficult to intercept the PIN, or replay transactions. As the official test by French ANSSI Authorities state it, for Gemalto’s SDK on Android: “Even if root rights obtained, PIN cannot be obtained to generate OTP / Authentication Response”.
 
 >## CALL
 >### How does the call work?
 
 For each Partner 1 JWKSet per environment (Sandbox & PROD) exposed on a public (https protected) URL.  
 
 The full certificate chain of the server hosting the JwkSet file must be communicated so we can add it in our own trust store.  
 
 When you send a request to us,  
 \- Sign request with your 'signing' private key  
 \-  Encrypt it based on our 'encryption' public key.  
   
 We will decrypt your request with our 'encryption' private key and use your 'sign' public key for the signed part.  
   
 Once we answer your request, we send an encrypted JWT token based on your 'encryption' public key  
 \- Decrypt request with your 'encryption' private key  
 \-  Validate our signature with our ‘signing’ public key found in our JWKSet
 
 >### Should there be 2 calls to itsme for this schedule?(One for Token request & one for User Info request)
 You need to perform two Back-End to Back-End calls indeed:
 
 \- The Token Request  
 \- The User Info Request  
 
 On top of this, the Authorization Request (AuthN Request in the schedule) consists of an HTTP redirection to the OpenID webpage of BMID. The content of this HTTP request is to be crafted by your system, it is actually a third call from your side to BMID, this one being Front-End to Back-End.
 
 >### How to set the level of security at the level of the App? (5 digit code only without fingerprint or facial recognition eg.)
 >
 You can configure this option through the parameter “acr_values”, documented in the section 2.1.2.1 of the document _“33020 Technical Specification Service Provider OpenID”_.  
 
 Two values are supported:  
 ` tag:sixdots.be,2016-06:acr_basic` : Basic level will let the User the choice to use either fingerprint (if Device is compatible) or his PIN.<br>
 ` tag:sixdots.be,2016-06:acr_advanced`: Advanced level will force the User to use her/his PIN.
 
 >### Is there any .NET sample code available?
 Please check the following link,
 
 https://docs.microsoft.com/en-us/aspnet/core/migration/1x-to-2x/identity-2x
 
 >### Is `GET` request supported?
 No, only `POST` request is supported.
 
 >### Why App-to-Backend communication exists but a App-to-B2B connection does not?
 We are sending the user information not through an app but through a highly authenticated server (which implies to put in place necessary measure to protect the data confidentiality)  
   
 This condition implies the need to put in place a back-end layer on your side.  
   
 We require you to expose a set of public key (a signature key & one encryption key) otherwise we cannot setup the connectivity with them.  
 
 For example our E2E JWKSet is here https://e2emerchant.itsme.be/oidc/jwkSet. We need the Service Provider to expose some similar content – on a very public https endpoint (nothing is confidential in there).
 
 
 > ### Which information must be present to contact the token endpoint?
 
 \-  This is a `POST` request and not a `GET`;
 
 \- In header the Content-Type : _application/x-www-form-urlencoded_ must be added.
 
 >### Is token encryption mandatory?
 We require you to expose a set of public key (a signature key & one encruption key) otherwise we cannot setup the connectivity with them.  
 
 For example our E2E JWKSet is here https://e2emerchant.itsme.be/oidc/jwkSet. We need the Service Provider to expose some similar content – on a very public https endpoint (nothing is confidential in there)  
   
 The signature key is used to verify the Service provider when they come fetch data on a client.  
 The encryption key is used to encrypt data so that nobody can read the data which we are transfering apart from the service provider component who has the right to access these data (so that you can encode/reencode the https in intern in a less secured component; it stays crypted).  
   
 _Document 33020 Technical Specifications Service Provider Open ID_ should help you go further. Otherwise standard Open ID documentation (http://openid.net/connect/) should also help you.
 
 >## Test Application
 >### After installing Itsme-UAT through Hockey-app on iPhone7, i get this message:"Non-trusted company app developer". What should i do?
 To be able to use itsme on Hockey App, you need to :  
   
 Select : “Annuleer”  
 Go to your settings  
 Select : General  
 Select : Profiles & Device Management  
 Select : Belgian Mobile ID NV  
 Select : Trust
 
 >### Can I have my own itsme app (production) together with the itsme UAT app?
 No, you can only have 1 itsme(r) app on your device one at a time, regardless of the which environment you have E.g. Production, UAT or E2E.
 
 
 >### Is this page auto generated: https://uatmerchant.sixdots.be/oidc/.well-known/openid-configuration?
 Yes, this page is auto-generated by the back end. But the content is mainly composed of hard coded values, only the environment specific values (like URLs) are loaded from config files. It is a summary of the parameters used for BMID implementation of OpenID Connect standard (supported encryption types, list of supported claims and scope values, endpoints).
 >### What is the purpose of the https://uatmerchant.sixdots.be/oidc/register? Seems to be a link to be registered as a SP, but do we make use of it?
 We do not use this. It is part of the OIDC standard. Normally used to register new OIDC partner. The registering process is done by an administrator (“the back end team” for the moment) during the “partner on boarding process”.
 >### Is it possible to register multiple valid callback URIs/jwkset URIs for one environment, or will they have to change this every time they switch URIs?
 While it is possible to register several redirect urls, it is not possible to register several jwkset urls.  
 The redirect url is specified in each Authorization request, the User will be redirected to that url after the (failed or successful) authentication in the itsme(r) App. You need to specify it in the onboarding file because it needs to be white-listed by our F5. We can white-list several URLs. The JWKSet URLs, however, is used by our BE for the decryption and signature verification of the JWTokens present in the OpenID Connect flow. Our BE must know what URL to contact, and thus we can register only one JWKSet URL.
 >### When the end-user is redirected to itsme(r) in the browser, then we specify a redirect\_uri. We have to provide BMID the redirect URI’s. Can we give additional parameters? Or does the entire redirect\_uri needs to match?”
 The entire redirect_uri must match therefore no additional parameter is allowed.
   
   
 >### Has itsme app in Prod & UAT different URL-scheme's?  eg itsme://...  itsmeUAT://...
 On iOS:  
 \- for enrollment, it's always "be.bmid.itsme://" "  
 \- for App to App actions, it's based on universal links and these are different for each environment:<br>
 UAT  
 https://uatmobileapp.sixdots.be/mobile/processAction  
 https://uatmobileapp.sixdots.be/mobile/authorize  
 E2E  
 https://e2emobileapp.sixdots.be/mobile/processAction  
 https://e2emobileapp.sixdots.be/mobile/authorize  
 PRD  
 https://mobileapp.sixdots.be/mobile/processAction  
 https://mobileapp.sixdots.be/mobile/authorize
 
 >## SERVICE
 >### Can we re-use the same service for another environment?
 No, we will create new services for the OIDC.
 
 >### Can we re-use the same service, but seperate SSL certificate?”
 Currently, the certificate is used to protect the jwkset and is not directly linked to the SSL certificate, so for one Partner code you have maximum 1 JWKSET. If my understanding is correct they want to use a service using the Belfius Partner Code.
 
 >### Company A and B have a joint project : login with ItsMe for the website “C”.  
 This is a joint project because C reuses the front-application layer of B, ans also backend systems of B.  
 B is with regard to ItsMe an “identity provider”, whereas C is a “service provider”.  
   
 >### Does SixDots agree that B connectivity platform is used by C to call the “get token” and the “user info” services ?  
   
 This platform is already processing calls from SixDots to B and from B to SixDots (enrollment of B customers via their mobile device).  
 
 This implies that C would reuse the current ssl communication channel between B’ s connectivity platform and sixDots.  
   
 Note: B uses SOAP. C would use Open Id Connect.
 >We require that you present a dedicated SSL certificate so that we can distinguish the different calls in the logs & we could revoke access per service.
 
 >## Should I notify BMID if the IP changes?
 No, this is not required.
 
 >## What is the lay-out of the Identity Data that we get back in the User Info Response
 They are the same as on the eID card
 
 >## Do we need to call the User Info endpoint to get the National Register number or can it be part of the ID Token?
 
 User Info should be used to retrieve data and so NRN if it was available, however, today we cannot send the NRN to private organisations.
 
 >## I would like the access to the NRN data but not other eID data, is it possible?
 Taking into account you allowed to receive the NRN from us, we can't and get access to NRN and block the access to the eID group else it would prevent you to receive the NRN.
 
 >## Error: Token endpoint - unauthorized_client. What should I do?
 The client_assertion you have to provide is expected to contain a body section AND a HEADER one.  
   
 The header must contain the following information:  
   ```json--inline
 {  
 "alg": "<THE\_ALGO\_YOU_USED>",  
 "typ": "JWT",  
 "kid": "<THE\_KEY\_ID\_YOU\_USED\_TO\_SIGN>"  
 }  
  ``` 
 
 Please make sure that,
 
  - *You provide this information in the encrypted assertion*
 - *You signed also the assertion before encrypting it*
 - *You  mention the correct "kid", the "alg" and the constant value "JWT"*
 
 >## I make a call to the token endpoint, the server returns a 500. What should I do?
 
 I make a call to the token endpoint, (https://uatmerchant.sixdots.be/oidc/token) with the following params:  
 ```json--inline
 grant\_type=authorization\_code&code=80fmc3ydci8vuid18oh61l8hq33dwszhni0n&redirect\_uri=https%3A%2F%2Fixxx-nodejs-itsme-poc.herokuapp.com%2Fapi%2Fitsme%2Flogin&client\_assertion=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJqdGkiOiI3MzAxZDg2Zi0yNjEzLTQ2MDAtOTA4ZS0wYmE2MWZkODQ3YjQiLCJpYXQiOjE1MTYxODUwNzEsImV4cCI6MTUxNjE4ODY3MSwiYXVkIjoiaHR0cHM6Ly91YXRtZXJjaGFudC5zaXhkb3RzLmJlL29pZGMvdG9rZW4iLCJpc3MiOiJJQ0FQUFMiLCJzdWIiOiJJQ0FQUFMifQ.L68iMbrrt0ETNBTJtBTCD29d0vKAyxPQ\_6lnBibtlB0&client\_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer  
 ``` 
 >The JWT token, encrypted with our private key, contains:  
   
  ```json--inline
 
 decoded {  
   
 "jti": "7301d86f-2613-4600-908e-0ba61fd847b4",  
   
 "iat": 1516185071,  
   
 "exp": 1516188671,  
   
 "aud": "https://uatmerchant.sixdots.be/oidc/token",  
   
 "iss": "xxx",  
   
 "sub": "xxx"  
   
 }  
  ```
 >The server returns a 500. No extra information.
 
 ### Solution:
 The "/token" end point must be called using:  
   
 (1)  "application/x-www-form-urlencoded" content type.  
   
 The following type should not be used. "*/*, Accept: application/json,text/plain,*/*,."  
   
 (2) The returned media type will be "application/json". _Please make sure to accept it in the request._ 
   
 (3) `POST` is the only http method allowed to call this entry point. Do not use `GET`.  
   
 (4) Please make sure that your private key is not used to encrypt the JWT token.
   
 You need to use your private key to firstly, sign the token, then use the itsme(r) encryption public key to encrypt the  token. The itsme(r) backend will then use his own private encryption key to decode your token and your public  signing key to validate your signature.
 
 >## We would like to integrate itsme(r) but..
 We would like to integrate itsme(r) but we only have 1 development machine & so no unique IP for the back-end in test as it isn't unique & runs in local on each machine.
   
 We need you to provide us a machine name (URL with host-name) so that once the redirect received, it points to your machine. On your local machine, this URL with host-name must point to your local machine (/ ETC / HOSTS) In other words: This will be the only URI we will accept in OpenID queries. So, if it is to be able to use the solution from different dev machines, the best thing is to create a unique redirect_uri like `https: // my-dev-sp / mycallbackuri` and that each developer maps `my-dev-sp` on the 127.0.0.1 via its hosts file.
 
 For the rest, if each developer has the private key of the SP on his post, she/he can impersonate the SP during calls to "/ token" and "/ userinfo". There is no check on the source IP, only the signature JWT account.

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
eyJoaXN0b3J5IjpbNzQwMTU1MzYsLTgyOTY5NjA3NCwxNDQyOD
E4MzA2LC0xNjYzMzI3NDc5LC0xOTY4NjQ5MDQwLC0xOTcyNDc3
NzA1LC0yMDkzOTQ2MzE0LC05MDU0MDc5NjYsOTg4MzAyNDQ0LD
U0OTIyMTQyNiwtNTUwMTY5NTgsLTExMDgzMjY0NjksMjU5ODg4
MzEwLDE2NzExNDM1NjgsMTQ2NzM5OTM5MCwtMjA0MzYwMzA1OS
wxMDE1MzA1NzUyLC0xMzgxNjY4ODU5LDEwMTc1NTU0NDMsLTEx
NjAyNTQ2NjRdfQ==
-->