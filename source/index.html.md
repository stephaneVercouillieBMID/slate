---
title: API Reference

language_tabs: # must be one of https://git.io/vQNgJ
  - json

toc_footers:
  - <a href='#'>Sign Up for a Developer Key</a>
  - <a href='https://github.com/lord/slate'>Documentation Powered by Slate</a>

search: true
---

# Introduction

This documentation describes our OAuth 2.0 implementation of **itsme(r) Login**, which conforms to the [OpenID Connect 1.0](http://openid.net/specs/openid-connect-core-1_0.html) specifications, and is OpenID certified (TODO). 

# itsme(r) Login and Shared Data

**itsme(r) Login** is a service provided by [Belgian Mobile ID](https://www.belgianmobileid.be) to allow users to login securely and to share data with your application.

itsme(r) Login uses pairwise user identifier, meaning each partner will have a different User Code for the same user. Doing, nobody except BMID can link one given user code to a specific user identity.  The user identity is therefore defined the shared data.

If data usage is justified and consent is provided by the end-user during authentication, following data can be shared with your application

- **Verified Identity Data**: identity information retrieved from the National eID Card 
- **Commercial Information**: information provided by the end-user 
- **Security Information**: information retrieved during execution of the service that could impact security level of the transaction. 

## Verified Identity Data

The user identity data provided by **itsme(r)** are data coming from the National Registry. These datas are provided to **itsme(r)** during user enrolment either directly from a card readout or indirectly through an IDentity Registrar (IDR) having a strong identity verification process (e.g. face-to-face KYC with eID readout)  in-place.  

Data | Definition
-- | --
**Full Name** | Full name is a concatenation of firstname, middlenames and lastname.
**Date of birth** | Birthdate 
**Place of birth** | Place of birth. ***Note**: this information can be localized* 
**Gender** | Gender
**Language** | Language
**Nationality** | Nationality
**Address** | Address containing street, house number, postbox, locality, postcode and country
**Passport Number** | Passport Number
**NRN** | National Registry Number
**E-ID picture** | Picture taken from the National eID Card in low-resolution.
**E-ID Metadata** | See [E-ID Metadata Information](#e-id-info)

### <a name="e-id-info"></a>E-ID Metadata Information
Provides some information about the eID card readout related to the identity data provided by **itsme(r)**.

Data | Definition
-- | -- 
eID Serial Number | the electronic ID card serial number.
issuance_locality | the issuance locality.
Validity from | eID card validity “from” date.
Validity to | eID card validity “to” date.
Certificate Validity | the certificate validity.
Read Date | the data extraction date.

## Commercial Information

Data | Definition
-- | --
Phone Number | Verified phone number associated to the **itsme(r)** user account.  
E-Mail Address | E-Mail address. NOT Verified!

## Security Information

Data | Definition
-- | --
Device | Information about the end user device. See [Device Information](#device-information)

### <a name="device-information"></a>Device Information 

A JSON object with the following keys (only keys with cardinality \[1..1\] will be always available):

Data | Provided | Definition
-- | -- | -- 
**OS** | Always | the device operating system (supported values: {`ANDROID`, `IOS`})
**Device Identifier** | Always | (regexp = "\[a-f0-9\]{33}") the device identifier.
**Application Name** |  | the application name.
**Application Release** |  | the application current release.
**Device Label** |  | the name of the device.
**Debug Enabled** |  | if debug mode is activated.
**OS Release** |  | Version of the OS running on your Device.
**Manufacturer** |  | Brand of the device manufacturer (‘Apple’ on iOS, device specific on Android). 
**SIM Enabled** |  | Whether there is a SIM in the Device. Should be always true, as long as BMID keeps forbidding installing itsme on a tablet.
**Lock Level** |  | The type of action to be performed to unlock the Device. On iOS : TOUCH_ID, PASSCODE or NONE if User protected his Device with TouchID, PIN or nothing.
**SMS Enabled** |  | Can send SMS. On iOS, means it’s an iPhone. 
**Rooted** |  | True if the device is jailbreaked/rooted.
**IMEI** |  | (regexp = "\[0-9\]{15,17}") the device IMEI value.
**Model** |  | Model of the Device. e.g. SAMSUNG GALAXY A5
**MSISDN** |  | the user’s phone number. 
**SDK Release** |  | Sdk release 

> Example device object in JSON
```json
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

# Prerequisites 

Before your application can use **itsme(r)** for user login, you must set up a project to obtain OIDC credentials, set a redirect URI for your services, and customise the branding information that your users see on the **itsme(r)** user-consent screen.

## Customize the user consent screen
SP provides 

- Partner name, description and localized labels
- Services' name, description and localized labels
- Data access with justification (to comply with GDPR)

## Obtain OAuth 2.0 credentials
BMID provides 
- Partner Code
- Service Code for each Service.

## Set a redirect URI and Certificates
SP provides 
- the redirect URIs for each Service to use within the OpenID Connect protocol to send back the response of the Authentication Request. 
- the JWKSet HTTPS endpoint exposing the signing and encryption public certificates of the SP.
- the SSL/TLS certificate used on the JWKSet HTTPS endpoint of the SP.

# Service Discovery
To simplify implementations and increase flexibility, OpenID Connect allows the use of a "Discovery document", a JSON document found at a well-known location containing key-value pairs which provide details about the OpenID Connect provider's configuration, including the URIs of the authorization, token, userinfo, and public-keys endpoints. The Discovery document for **itsme(r) Login** service may be retrieved from:

[https://merchant.itsme.be/oidc/.well-known/openid-configuration](https://merchant.itsme.be/oidc/.well-known/openid-configuration).

Format and content of this document is defined in [OpenID Connect Discovery 1.0](https://openid.net/specs/openid-connect-discovery-1_0.html)

# Authenticating the user

**itsme(r) Login** is based on the [Authorization Code Flow](http://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth)
 of OpenID Connect 1.0.
 
The Authorization Code Flow goes through the following steps as defined in  [http://openid.net/specs/openid-connect-core-1_0.html#CodeFlowSteps](http://openid.net/specs/openid-connect-core-1_0.html#CodeFlowSteps)

## 1. Authentication Request
As per the OpenID Connect specification [http://openid.net/specs/openid-connect-core-1_0.html#AuthRequest](http://openid.net/specs/openid-connect-core-1_0.html#AuthRequest) and [http://openid.net/specs/openid-connect-core-1_0.html#AuthorizationEndpoint](http://openid.net/specs/openid-connect-core-1_0.html#AuthorizationEndpoint)

The first step is forming an HTTPS request with the appropriate URI parameters. Note the use of HTTPS rather than HTTP in all the steps of this process; HTTP connections are refused. You should retrieve the base URI from the [Discovery document](https://merchant.itsme.be/oidc/.well-known/openid-configuration) using the key `authorization_endpoint`. The following discussion assumes the base URI is `https://merchant.itsme.be/oidc/authorization`.

**itsme(r)** supports the use of the HTTP `GET` and `POST` methods. If using the HTTP `POST` method, the request parameters must be serialized using [Form Serialization](http://openid.net/specs/openid-connect-core-1_0.html#FormSerialization).

For a basic request, specify the following parameters:

Parameter | Required | Comment
--------- | ------- | -----
**client_id** | Required | MUST be the Partner Code you obtained from BMID during on-boarding process (this information is in the onboarding file TODO). 
**response_type** | Required | MUST be <code>code</code>.
**scope** | Required | MUST contain at least `openid` or an HTTP ERROR `not_implemented` will be returned. `offline_access` value will yield an error. MUST also includes the target service in the form `service:<SERVICE_CODE>` as provided by BMID during the on-boarding process. ***Note**: Requested data will only be provided based on your current accesses*. See XXXXXX (TODO) for list of available data.
**redirect_uri** | Required | should be the HTTPS endpoint on your server that will receive the response from **itsme(r)**. This value MUST match one of the values provided to BMID during on-boarding process. ***Note**: the Partner can define different `redirect_uri` specific to each Service.*
**state** | Optional, but strongly recommended | should include the value of the anti-forgery unique session token, as well as any other information needed to recover the context when the user returns to your application.
**nonce** | Optional | a random value generated by your app that enables replay protection when present.
**login_hint** | Optional | OPTIONAL and supported, though not recommended. Only phone numbers are supported as `login_hint`. Format is: `\<coutrycode\>+\<phonenumber\>`. E.g. `login_hint=32+123456789`. Usage of claim value `phone_number` in an encrypted request object is recommended in order to avoid disclosure of phone number of the enduser on the user agent (TODO what does it means?)
**display** | Optional | MUST be `page` if provided. Other values will yield an HTTP ERROR `not_implemented`.
**prompt** | Optional | MUST be `consent` if provided.
**ui_locales** | Optional | Can be used to specify the language to be used by the OpenID login page. Supported languages are: `fr`, `nl`, `en` and `de`. Any other value will be ignored.
**max_age** | Optional | Supported but not used: **itsme(r)** will always actively re-authenticate the End-User. 
**acr_values** | Optional | OPTIONAL and supported, though not recommended. Possible values are tag:itsmetag:sixdots.be,2016-06:acr_basic, tag:itsmetag:sixdots.be,2016-06:acr_advanced. When multiple values are provided only the most constraining will be used (advanced > basic). If not provided basic level will be used. As there is no such idea of an existing session on itsme Core, even if the acr\_values is requested as a voluntarily claim, the acr value returned will always be the more constraining method in the acr\_values list, or the authentication will fail. Usage of acr parameter in the request object is recommended over this parameter as it will be signed in the JWT token
**claims** | Optional | Not recommended. Usage of claims parameter in the request object is recommended over this parameter as it will be signed in the JWT token, and the data will be encrypted
**request** | Optional | See [Passing Request Parameters as JWTs](#JWTRequest)
**response_mode** | Unsupported | Ignored if provided.
**id\_token\_hint** | Unsupported | Ignored if provided.
**claims_locales** | Unsupported | None are supported.
**request_uri** | Unsupported | N/A TODO 
**registration** | Unsupported | N/A TODO

### Request Examples

>Example of a minimal Authorization request

```http
GET /authorize?response_type=code
&scope=openid%20profile%20email%20service%3Aclient.registration
&client_id=s6BhdRkqt3
&state=af0ifjsldkj
&redirect_uri=https%3A%2F%2Fclient.example.org%2Fcb HTTP/1.1
Host: server.itsme.be
```

### Authentication Response
An Authentication Response is an [OAuth 2.0 Authorization Response](https://tools.ietf.org/html/rfc6749#section-4.1.2) message. As such, the Authentication Response will return the following parameters:

Parameter | Provided | Description
-- | -- | --
**code** | Always | Authorization code to later provide to the token endpoint. This code has a lifetime of 3 minutes.
`state` |  | The exact value received from the client, if the parameter was present in the Authentication Request.

> Example of Authentication Response

```http
HTTP/1.1 302 Found
  Location: https://client.example.org/cb?
    code=SplxlOBeZQQYbYS6WxSbIA
    &state=af0ifjsldkj
```

### Errors
If the authentication is NOT successful, the following errors can be triggered

Error | Description
-- | --
interaction_required  | TODO
invalid\_request\_object | TODO
request\_uri\_not_supported | TODO
registration\_not\_supported | TODO

### App to App ??? TODO
**itsme(r)** Mobile App endpoint : TODO

## 2. Token Request

As per the OpenID Connect specification http://openid.net/specs/openid-connect-core-1_0.html#TokenRequest

The Authentication Response includes a `code` parameter, a one-time authorization code that your server can exchange for an ID token. Your server makes this exchange by sending an HTTPS `POST` request. The `POST` request is sent to the token endpoint, which you should retrieve from the [Discovery document](https://merchant.itsme.be/oidc/.well-known/openid-configuration) using the key `token_endpoint`. The following discussion assumes the endpoint is `https://merchant.itsme.be/oidc/token`. The request must include the following parameters in the `POST` body:

Parameter | Required | Comment
-- | -- | --
**grant_type** | Required | Must be `authorization_code`
**code** | Required | The **code** value provided in the Authentication Response
**redirect_uri** | Required | The **redirect_uri** used in the Authentication Request
**client_assertion** | Required | Must be a valid JWT complying with the **private_key_jwt** client authentication method as defined in [Section 9](http://openid.net/specs/openid-connect-core-1_0.html#ClientAuthentication) of the OpenID specification. This JWT must be signed.
**client\_assertion\_type** | Required | Must be `urn:ietf:params:oauth:client-assertion-type:jwt-bearer`

Fllowing the **private_key_jwt** client authentication method, the JWT must contain the following properties:

Property | Comment
-- | --
**iss** | MUST be the Partner Code you obtained from BMID during on-boarding process (this information is in the onboarding file TODO). 
**sub** | MUST be the Partner Code you obtained from BMID during on-boarding process (this information is in the onboarding file TODO). 
**aud** | Must be the token endpoint URL, e.g. `https://merchant.itsme.be/oidc/token`
**jti** | A unique identifier for the token, which can be used to prevent reuse of the token. These tokens MUST only be used once.
**exp** | Expiration time on or after which the ID Token MUST NOT be accepted for processing.

### Example Request

```http
POST /token HTTP/1.1
  Host: server.example.com
  Content-Type: application/x-www-form-urlencoded

  grant_type=authorization\_code&
    code=SplxlOBeZQQYbYS6WxSbIA&
    client_id=s6BhdRkqt3&
    client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer&
    client_assertion=PHNhbWxwOl ... ZT
```

### Successful Token Response

Parameter | Provided | Comment
-- | -- | --
**access_token** | Always | Will be provided
**token_type** | Always | Will be `Bearer`
**id_token** | Always | The JWT `id_token` corresponding to the Authentication Request (signed and encrypted)
**at_hash** | | first version of itsme(r) does not produce the at_hash value ??? TODO which version
**refresh_token** | Never | Won't be provided

### Errors

As per the OpenID Connect specification [http://openid.net/specs/openid-connect-core-1_0.html#TokenErrorResponse](http://openid.net/specs/openid-connect-core-1_0.html#TokenErrorResponse)

TODO if specifics

## 3. UserInfo Request

As per the OpenID Connect specification [http://openid.net/specs/openid-connect-core-1_0.html#UserInfoRequest](http://openid.net/specs/openid-connect-core-1_0.html#UserInfoRequest)

The content type of the response will be `application/jwt`. The response will be signed and encrypted by BMID using the signing and encryption certificate exposed.

The UserInfo endpoint can be accessed only with a valid access_token, and for a very limited duration after end user authentication: there must be less than 3 minutes between the creation of the user action to be confirmed by the end user on his mobile device, and the access to the UserInfo endpoint (this reflects a constraint set on the SOAP version of the interface).

### Claims

itsme(r) won't deliver any aggregated nor distributed claims in the current version.

In current version and in contradiction to the OpenID Connect specification, itsme(r) considers all claims as **Essential**, even if they are requested as a **Voluntary** claim. However, as in a future version, itsme(r)  will make the difference between Essential and Voluntary Claims, Relying Parties should already request claims with appropriate level regarding their business case.

Following values for scope allow access to a predefined sets of Identity Data:

Scope | Data | Claim
-- | -- | --
**`profile`** | Full Name | `name` or `given_name` TODO ??? or `family_name` TODO ???
- | Gender | `gender`
- | Date of Birth | `birthdate`
- | Language | `locale`
**`address`** | Address | `address`, with following subfields: `street_address` (newline separator \\n), `locality`, `postal_code`, `country`
**`email`** | E-Mail Address | `email` or `email_verified` TODO verified???
**`phone`** | Phone Number | `phone_number` or `phone_number_verified`

***NOTE**: Any claim requested by using the scope value can only be obtained from the UserInfo endpoint.*

We have favored the request of Identity Data in the scope values. However, some Identity Data have to be requested in the “claims” parameter of the Authentication Request. Here are these claims:

Data | Claim | Comment
-- | -- | --
??? | **`sub`** | TODO
Nationality | **`tag:itsmetag:sixdots.be,2016-06:claim_nationality`** | TODO
Place of Birth | **`tag:itsmetag:sixdots.be,2016-06:claim_city_of_birth`** and **`tag:itsmetag:sixdots.be,2016-06:claim_country_of_birth`** | TODO
E-ID Info  | **`tag:itsmetag:sixdots.be,2016-06:claim_eid`** | TODO
Passport Number | **`tag:sixdots.be,2017-05:claim_passport_sn`** | TODO
Device | **`tag:sixdots.be,2017-05:claim_device`** | see XXXX (TODO URL)
??? TODO | **`tag:sixdots.be,2017-05:claim_transaction_info`** | TODO
E-ID Picture | TODO | TODO
NRN | TODO | TODO

The following standard claims (check http://openid.net/specs/openid-connect-core-1_0.html#StandardClaims) won’t be provided: TODO UNSUPPORTED????

 - `middle_name` 
 - `nickname` 
 - `preferred_username` 
 - `picture` (supposed to be an url) 
 - `website` 
 - `zoneinfo` 
 - `updated_at`

## Signing and Encryption Requests

Encryption algorithm used: RSA SHA-256 TODO not aligned with

Supported algorithms and encryption methods for:

- ID Token
-- Encryption Method (enc): A128CBC-HS256
-- Encryption Algorithm (alg): RSA-OAEP
-- Signing Algorithm (alg): RS256
- User Info
-- Encryption Method (enc): A128CBC-HS256
-- Encryption Algorithm (alg): RSA-OAEP
-- Signing Algorithm (alg):  RS256
- Request Object”
-- Encryption Method (enc): A128CBC-HS256
-- Encryption Algorithm (alg): RSA-OAEP
-- Signing Algorithm (alg): RS256

itsme(r) will use pairwise Subject Identifier. TODO

Offline access is not supported. TODO

Dynamic client registration is not allowed. TODO 

itsme(r) exposes its signing and encryption keys on a public endpoint (JWKSet) 
> https://merchant.itsme.be/oidc/jwkSet

It is expected that the RP will also expose their signing and encryption keys in such a way. The location of the RP JWKSet must be configured by an itsme administrator during onboarding of RP. The exposed endpoint must be HTTPS 

# Advanced topics

## <a name="JWTRequest"></a>Passing Request Parameters as JWTs

OpenID Connect defines the following Authorization Request parameters to enable Authentication Requests to be signed and optionally encrypted:

Parameter | Required | Comment
-- | -- | --
request | Optional | This parameter enables OpenID Connect requests to be passed in a single, self-contained parameter and to be optionally signed and/or encrypted. The parameter value is a Request Object value, as specified in [Section 6.1](http://openid.net/specs/openid-connect-core-1_0.html#RequestObject). It represents the request as a JWT whose Claims are the request parameters.
request_uri | Unsupported | Refer to [JWTRequest](http://openid.net/specs/openid-connect-core-1_0.html#JWTRequests)

The Request Object is a JWT Token as defined in [RFC 7519](https://tools.ietf.org/html/rfc7519), which contains the following properties:

Property | Required | Comment
-- | -- | --
**iss** | Required | Issuer. Must be the `client_id`
**aud** | Required | Audience. Must be `https://server.itsme.be`

> Example of claim request before base64url encoding, signing and encryption

```json
{
	// JWT Registered claims (https://tools.ietf.org/html/rfc7519#section-4.1)
	iss: "s6BhdRkqt3",
	aud: "https://server.itsme.be",
	// OIDC parameters, must reflect the values of the HTTP parameters
	client_id: "s6BhdRkqt3",
	response_type: "code",
	redirect_uri: "https://client.example.org/cb",
	scope: "openid email service:client.registration",
	state: "af0ifjsldkj",
	nonce: "n-0S6_WzA2Mj",
	claims:
	{
		userinfo: {
		"tag:itsmetag:sixdots.be,2016-06:claim_nationality": null,
		},
		id_token: {
			auth_time: {"essential": true},
			acr: {"value": \["tag:itsmetag:sixdots.be,2016-06:acr_securedadvanced"\] 		
		}
	}
}
```
<!--stackedit_data:
eyJoaXN0b3J5IjpbMTQzNTM2NTAxNCw2NjcyNjY5MTNdfQ==
-->