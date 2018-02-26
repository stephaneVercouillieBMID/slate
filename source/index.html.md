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

# itsme(r) Login

**itsme(r) Login** is a service provided by [Belgian Mobile ID](https://www.belgianmobileid.be) to allow user to login securely and to share verified identity data with your application.




To simplify implementations and increase flexibility, OpenID Connect allows the use of a "Discovery document", a JSON document found at a well-known location containing key-value pairs which provide details about the OpenID Connect provider's configuration, including the URIs of the authorization, token, userinfo, and public-keys endpoints. The Discovery document for **itsme(r)** OpenID Connect service may be retrieved from:

[https://merchant.itsme.be/oidc/.well-known/openid-configuration](https://merchant.itsme.be/oidc/.well-known/openid-configuration).

Format and content of this document is defined in [OpenID Connect Discovery 1.0](https://openid.net/specs/openid-connect-discovery-1_0.html)
# Prerequisites 
## Obtain OAuth 2.0 credentials
## Set a redirect URI
## Customize the user consent screen
Before your application can use **itsme(r)** for user login, you must set up a project to obtain OIDC credentials, set a redirect URI, and customise the branding information that your users see on the **itsme(r)** user-consent screen.

In order to integrate with itsme(r), the client (relying party) must first go through the itsme(r) on-boarding process in order to create the corresponding partner and services to be consumed. 

The following information provided by BMID will be useful during the integration

Information | Description
-- | --
Partner Code | Identifier of the client, sometime referred as `client_id`
Service Code | Identifier of the service to be executed. One client can have several services, each service being one of the following type: Login or APPROVAL.
Service Redirect URL | Each Service is associated to a Redirect URL that will be used during the Authentication flow to send back information to the client.


# Authenticating the user

itsme(r) Login service is based on the Authorization Code Flow of OpenID Connect 1.0 as defined in [http://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth](http://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth)

The Authorization Code Flow goes through the following steps as defined in  [http://openid.net/specs/openid-connect-core-1_0.html#CodeFlowSteps](http://openid.net/specs/openid-connect-core-1_0.html#CodeFlowSteps)

## 1. Authenticating the user
As per the OpenID Connect specification [http://openid.net/specs/openid-connect-core-1_0.html#AuthRequest](http://openid.net/specs/openid-connect-core-1_0.html#AuthRequest) and [http://openid.net/specs/openid-connect-core-1_0.html#AuthorizationEndpoint](http://openid.net/specs/openid-connect-core-1_0.html#AuthorizationEndpoint)

The first step is forming an HTTPS request with the appropriate URI parameters. Note the use of HTTPS rather than HTTP in all the steps of this process; HTTP connections are refused. You should retrieve the base URI from the [Discovery document](https://merchant.itsme.be/oidc/.well-known/openid-configuration) using the key `authorization_endpoint`. The following discussion assumes the base URI is `https://merchant.itsme.be/oidc/authorization`.

**itsme(r)** supports the use of the HTTP `GET` and `POST` methods. If using the HTTP `POST` method, the request parameters must be serialized using Form Serialization, per [http://openid.net/specs/openid-connect-core-1_0.html#FormSerialization](http://openid.net/specs/openid-connect-core-1_0.html#FormSerialization).

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
**claims_locales** | Optional | OPTIONAL supported but not used
**claims** | Optional | OPTIONAL and supported, though not recommended. If some notification text has to be displayed on the screen of the itsme app, this text MUST be specified in the “notification_text”parameter. Usage of claims parameter in the request object is recommended over this parameter as it will be signed in the JWT token, and the data will be encrypted
**request** | Optional | OPTIONAL and recommended. The JWT token MUST be signed using a key known to BMID for the given clientId. The JWT token must (must??? TODO) also be encrypted, as it may contain confidential data (in the approval use case for example)
**response_mode** | Optional | Should not be used. Any supplied value will be ignored.
**id\_token\_hint** | Optional | Supported, but not used.
**request_uri** | Unsupported | N/A TODO 
**registration** | Unsupported | N/A TODO

### Request Examples

>Example of a minimal Authorization request

```http
GET /authorize?response_type=code
&scope=openid%20profile%20email%20service%3Aclient.registration
&client_id=s6BhdRkqt3
&state=af0ifjsldkj
&redirect_uri=https%3A%2F%2Fclient.example.org%2Fcb
HTTP/1.1
Host: server.itsme.be
```

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

### Errors
If the authentication is NOT successful, the following errors can be triggered

Error | Description
-- | --
interaction_required  | TODO
invalid\_request\_object | TODO
request\_uri\_not_supported | TODO
registration\_not\_supported | TODO

**itsme(r)** Mobile App endpoint : TODO

## 2. Token Request

As per the OpenID Connect specification http://openid.net/specs/openid-connect-core-1_0.html#TokenRequest

As per the specification, with a `private\_key\_jwt` for client authentication.

Parameter | Required | Comment
-- | -- | --
**grant_type** | REQUIRED | Must be `authorization_code`
**code** | REQUIRED | The `code` value provided in Authorization response
**redirect_uri** | REQUIRED | The `redirect_uri` used in the authorization request
**client_assertion** | REQUIRED | Must be provided: private\_key\_jwt
**client\_assertion\_type** | REQUIRED | Must be `urn:ietf:params:oauth:client-assertion-type:jwt-bearer`

A valid private\_key\_jwt token must be provided by the client.

The private\_key\_jwt must be signed and the JWT token content must have the following properties:

Parameter | Comment
-- | --
**sub** | The subject of the private\_key\_jwt (the client ID)
**aud** | The token endpoint url
**iss** | The issuer of the private\_key\_jwt (the client ID)
**exp** | Expiration time on or after which the ID Token MUST NOT be accepted for processing.
**jti** | A unique identifier for the token, which can be used to prevent reuse of the token. These tokens MUST only be used once.

#### Successful Token Response

Parameter | Comment
-- | --
**access_token** | Will be provided
**token_type** | Bearer
**refresh_token** | Won't be provided
**id_token** | the id_token correspoding to the authentication request (signed and encrypted)
**at_hash** | OPTIONAL: first version of itsme Core does not produce the at_hash value

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
**profile** | Full Name | `name` or `given_name` TODO ??? or `family_name` TODO ???
- | Gender | `gender`
- | Date of Birth | `birthdate`
- | Language | `locale`
**address** | Address | `address`, with following subfields: `street_address` (newline separator \\n), `locality`, `postal_code`, `country`
**email** | E-Mail Address | `email` or `email_verified` TODO verified???
**phone** | Phone Number | `phone_number` or `phone\_number\_verified`


***NOTE**: Any claim requested by using the scope value can only be obtained from the UserInfo endpoint.*

We have favored the request of Identity Data in the scope values. However, some Identity Data have to be requested in the “claims” parameter of the Authentication Request. Here are these claims:

Data | Claim | Comment
-- | -- | --
User code | **sub** | TODO
Nationality | **tag:itsmetag:sixdots.be,2016-06:claim_nationality** | TODO
Place of Birth | **tag:itsmetag:sixdots.be,2016-06:claim\_city\_of_birth** and **tag:itsmetag:sixdots.be,2016-06:claim\_country\_of_birth** | TODO
E-ID Info  | **tag:itsmetag:sixdots.be,2016-06:claim_eid** | TODO
Passport Number | **tag:sixdots.be,2017-05:claim_passport_sn** | TODO
Device | **tag:sixdots.be,2017-05:claim_device** | see XXXX (TODO URL)
??? TODO | **tag:sixdots.be,2017-05:claim\_transaction\_info** | TODO
E-ID Picture | TODO | TODO
NRN | TODO | TODO

The following standard claims (check http://openid.net/specs/openid-connect-core-1_0.html#StandardClaims) won’t be provided: TODO UNSUPPORTED????

 - middle_name 
 - nickname 
 - preferred_username 
 - picture (supposed to be an url) 
 - website 
 - zoneinfo 
 - updated\_at

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

## National ID Information

## Commercial Information

## Security Information

Information about the end user device.

A JSON object with the following keys (only keys with cardinality \[1..1\] will be always available):

Key | Cardinality | Description
-- | -- | -- 
**os** | 1..1 | the device operating system (supported values: {`ANDROID`, `IOS`})
**deviceId** | 1..1 | (regexp = "\[a-f0-9\]{33}") the device identifier.
**appName** | 0..1 | the application name.
**appRelease** | 0..1 | the application current release.
**deviceLabel** | 0..1 | the name of the device.
**debugEnabled** | 0..1 | if debug mode is activated.
**osRelease** | 0..1 | Version of the OS running on your Device.
**manufacturer** | 0..1 | Brand of the device manufacturer (‘Apple’ on iOS, device specific on Android). 
**hasSimEnabled** | 0..1 | Whether there is a SIM in the Device. Should be always true, as long as BMID keeps forbidding installing itsme on a tablet.
**deviceLockLevel** | 0..1 | The type of action to be performed to unlock the Device. On iOS : TOUCH_ID, PASSCODE or NONE if User protected his Device with TouchID, PIN or nothing.
**smsEnabled** | 0..1 | Can send SMS. On iOS, means it’s an iPhone. 
**rooted** | 0..1 | Coming from Gemalto. ‘true’ the device is jailbreaked/rooted.
**imei** | 0..1 | (regexp = "\[0-9\]{15,17}") the device IMEI value.
**deviceModel** | 0..1 | Model of the Device. e.g. SAMSUNG GALAXY A5
**msisdn** | 0..1 | the user’s phone number. 
**sdkRelease** | 0..1 | Sdk release 

> Example
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

<!--stackedit_data:
eyJoaXN0b3J5IjpbMTI3ODk0ODAzMV19
-->