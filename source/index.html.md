---
title: API Reference

language_tabs: # must be one of https://git.io/vQNgJ
  - shell
  - ruby
  - python
  - javascript

toc_footers:
  - <a href='#'>Sign Up for a Developer Key</a>
  - <a href='https://github.com/lord/slate'>Documentation Powered by Slate</a>

includes:
  - errors

search: true
---

# Introduction

itsme(r) Login service is based on code flow of the OpenID Connect 1.0 protocol as defined in http://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth

Except where otherwise specified, the itsme solution follows the OpenId Connect Core specifications.

itsme(r) allows discovery as specified in OpenID Connect Discovery 1.0 https://openid.net/specs/openid-connect-discovery-1_0.html exposed at the following URL

>https://merchant.itsme.be/oidc/.well-known/openid-configuration.

# itsme(r) Login Authentication

As per the OpenID Connect specification http://openid.net/specs/openid-connect-core-1_0.html#CodeFlowSteps

## 1. Authentication Request

As per the OpenID Connect specification http://openid.net/specs/openid-connect-core-1_0.html#AuthRequest and http://openid.net/specs/openid-connect-core-1_0.html#AuthorizationEndpoint

itsme(r) Web endpoint : https://merchant.itsme.be/oidc/authorization
itsme(r) Mobile App endpoint : TODO

The fingerprint of the SSL/TLS certificate is xxxxxxx.

itsme(r) supports the use of the HTTP `GET` and `POST` methods. If using the HTTP `POST` method, the request parameters are serialized using Form Serialization, per http://openid.net/specs/openid-connect-core-1_0.html#FormSerialization.

The User is redirected to the interface of the itsme Authorization endpoint. This will be a webpage if the User is in the Service Provider website, or a webview if the User is in a Service Provider App. The parameters of the redirection URL contain all information itsme needs to execute the Authentication and Authorization process, according to the OpenID specifications :

Parameter | Required | Comment
--------- | ------- | -----
**scope** | REQUIRED | MUST contain at least `openid` or an HTTP ERROR not\_implemented will be returned. `offline\_access` value will yield an error. MUST also include the target service in the form `service:SERVICE_CODE`. Requested data will only be provided based on your current (license? TODO) see XXXXXX (TODO) for list of available data.
**response_type** | REQUIRED | MUST be <code>code</code>. Other values will produce an HTTP ERROR not_implemented.
**client_id** | REQUIRED | MUST be a valid partner code as provided by the BMID during on-boarding process (this information is in the onboarding file). This value will be unique to each partner.
redirect_uri | REQUIRED | This value MUST match one of the values provided by the partner to the itsme administrator when registering the Partner. It must include the https scheme. Note: the Partner can define a different redirect_uri specific to each Service.
**state** | OPTIONAL | An appropriate value is RECOMMENDED
**nonce** | OPTIONAL | An appropriate value is RECOMMENDED
**display** | OPTIONAL | OPTIONAL but not recommended. MUST be page if provided. Other values will yield an HTTP ERROR not_implemented.
**prompt** | OPTIONAL | OPTIONAL but not recommended Only login and consent values are supported. If prompt contains none or select\_account or any other values an HTTP ERROR not\_implemented will be returned.
**max_age** | OPTIONAL | OPTIONAL supported but not used: itsme will always actively re-authenticate the End-User. Value must be an integer.
**ui_locales** | OPTIONAL | OPTIONAL and supported. Can be used to specify the language to be used by the OpenID login page. Supported languagesvalues are: {“fr”, “nl”, “en”, “de”}. Any other value will be ignored.
**login_hint** | OPTIONAL | OPTIONAL and supported, though not recommended. Only phone numbers are supported as login_hint. Format is: “coutrycode+phonenumber”.E.g. “login_hint=32+123456789”. Usage of claim value phone number in an encrypted request object is recommended in order to avoid disclosure of phone number of the enduser on the user agent
**acr_values** | OPTIONAL | OPTIONAL and supported, though not recommended. Possible values are tag:itsmetag:sixdots.be,2016-06:acr_basic, tag:itsmetag:sixdots.be,2016-06:acr_advanced. When multiple values are provided only the most constraining will be used (advanced > basic). If not provided basic level will be used. As there is no such idea of an existing session on itsme Core, even if the acr\_values is requested as a voluntarily claim, the acr value returned will always be the more constraining method in the acr\_values list, or the authentication will fail. Usage of acr parameter in the request object is recommended over this parameter as it will be signed in the JWT token
**claims_locales** | OPTIONAL | OPTIONAL supported but not used
**claims** | OPTIONAL | OPTIONAL and supported, though not recommended. If some notification text has to be displayed on the screen of the itsme app, this text MUST be specified in the “notification_text”parameter. Usage of claims parameter in the request object is recommended over this parameter as it will be signed in the JWT token, and the data will be encrypted
**request** | OPTIONAL | OPTIONAL and recommended. The JWT token MUST be signed using a key known to BMID for the given clientId. The JWT token must (must??? TODO) also be encrypted, as it may contain confidential data (in the approval use case for example)
**response_mode** | OPTIONAL | Should not be used. Any supplied value will be ignored.
**id\_token\_hint** | OPTIONAL | Supported, but not used.
**request_uri** | UNSUPPORTED | N/A
**registration** | UNSUPPORTED | N/A

### Request Examples
>Example of Authorization request
```http
GET /authorize?response_type=code
&scope=openid%20profile%20email%20service%3Aclient.registration
&client_id=s6BhdRkqt3
&state=af0ifjsldkj
&redirect_uri=https%3A%2F%2Fclient.example.org%2Fcb
&request=ekdlzldjkf...request..object..as..JWT..token... HTTP/1.1
Host: server.itsme.be
```
> Example of claim request before base64url encoding, signing and encryption
```json
{
	iss: "s6BhdRkqt3",
	aud: "https://server.itsme.be",
	response_type: "code",
	client_id: "s6BhdRkqt3",
	redirect_uri: "https://client.example.org/cb",
	scope: "openid email service:client.registration",
	state: "af0ifjsldkj",
	nonce: "n-0S6_WzA2Mj",
	claims:
	{
		userinfo: {
		"tag:itsmetag:sixdots.be,2016-06:claim_nationality": null,
		"tag:itsme.be,2016-06:claim_notification_text": {
			value: "Login to the FOO service"}
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

As per the OpenID Connect specification http://openid.net/specs/openid-connect-core-1_0.html#TokenErrorResponse

TODO if specifics

## 3. UserInfo Request

As per the OpenID Connect specification http://openid.net/specs/openid-connect-core-1_0.html#UserInfoRequest

The content type of the response will be `application/jwt`. The response will be signed and encrypted by BMID using the signing and encryption certificate exposed.

The UserInfo endpoint can be accessed only with a valid access_token, and for a very limited duration after end user authentication: there must be less than 3 minutes between the creation of the user action to be confirmed by the end user on his mobile device, and the access to the UserInfo endpoint (this reflects a constraint set on the SOAP version of the interface).

### Claims

itsme(r) won't deliver any aggregated nor distributed claims in the current version.

In current version and in contradiction to the OpenID Connect specification, itsme(r) considers all claims as **Essential**, even if they are requested as a **Voluntary** claim. However, as in a future version, itsme(r)  will make the difference between Essential and Voluntary Claims, Relying Parties should already request claims with appropriate level regarding their business case.

Following values for scope allow access to a predefined sets of Identity Data:

Scope | Identity Data | Claim
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

Identity Data | Claim | Comment
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

## Signing Requests

## Device Information

<!\[endif\]-->

Information about the end user device.

A JSON object with the following keys:

(only keys with cardinality \[1..1\] will be always available) 

Key | Cardinality | Description
-- | -- | -- 
**os** | 1 | the device operating system (supported values: {`ANDROID`, `IOS`})
**appName** | 0..1 | the application name.
**appRelease**” \[0..1\]: the application current release.
**deviceLabel” \[0..1\]: the name of the device.
**debugEnabled” \[0..1\]: if debug mode is activated.
**deviceId” \[1..1\]: (regexp = "\[a-f0-9\]{33}") the device identifier.
**osRelease” \[0..1\]: Version of the OS running on your Device.
**manufacturer” \[0..1\]: Brand of the device manufacturer (‘Apple’ on iOS, device specific on Android). 
**hasSimEnabled” \[0..1\]: Whether there is a SIM in the Device. Should be always true, as long as BMID keeps forbidding installing itsme on a tablet.

<!\[if !supportLists\]>· <!\[endif\]>“deviceLockLevel” \[0..1\]: The type of action to be performed to unlock the Device. On iOS : TOUCH_ID, PASSCODE or NONE if User protected his Device with TouchID, PIN or nothing.

<!\[if !supportLists\]>· <!\[endif\]>“smsEnabled” \[0..1\]: Can send SMS. On iOS, means it’s an iPhone. 

<!\[if !supportLists\]>· <!\[endif\]>“rooted” \[0..1\]: Coming from Gemalto. ‘true’ the device is jailbreaked/rooted.

<!\[if !supportLists\]>· <!\[endif\]>“imei” \[0..1\]: (regexp = "\[0-9\]{15,17}") the device IMEI value.

<!\[if !supportLists\]>· <!\[endif\]>“deviceModel” \[0..1\]: Model of the Device. e.g. SAMSUNG GALAXY A5

<!\[if !supportLists\]>· <!\[endif\]>“msisdn” \[0..1\]: the user’s phone number. 

<!\[if !supportLists\]>· <!\[endif\]>“sdkRelease” \[0..1\]: Sdk release 

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
> To authorize, use this code:

```ruby
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
```

```python
import kittn

api = kittn.authorize('meowmeowmeow')
```

```shell
# With shell, you can just pass the correct header with each request
curl "api_endpoint_here"
  -H "Authorization: meowmeowmeow"
```

```javascript
const kittn = require('kittn');

let api = kittn.authorize('meowmeowmeow');
```

> Make sure to replace `meowmeowmeow` with your API key.

Kittn uses API keys to allow access to the API. You can register a new Kittn API key at our [developer portal](http://example.com/developers).

Kittn expects for the API key to be included in all API requests to the server in a header that looks like the following:

`Authorization: meowmeowmeow`

<aside class="notice">
You must replace <code>meowmeowmeow</code> with your personal API key.
</aside>

# Kittens

## Get All Kittens

```ruby
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
api.kittens.get
```

```python
import kittn

api = kittn.authorize('meowmeowmeow')
api.kittens.get()
```

```shell
curl "http://example.com/api/kittens"
  -H "Authorization: meowmeowmeow"
```

```javascript
const kittn = require('kittn');

let api = kittn.authorize('meowmeowmeow');
let kittens = api.kittens.get();
```

> The above command returns JSON structured like this:

```json
[
  {
    "id": 1,
    "name": "Fluffums",
    "breed": "calico",
    "fluffiness": 6,
    "cuteness": 7
  },
  {
    "id": 2,
    "name": "Max",
    "breed": "unknown",
    "fluffiness": 5,
    "cuteness": 10
  }
]
```

This endpoint retrieves all kittens.

### HTTP Request

`GET http://example.com/api/kittens`

### Query Parameters

Parameter | Default | Description
--------- | ------- | -----------
include_cats | false | If set to true, the result will also include cats.
available | true | If set to false, the result will include kittens that have already been adopted.

<aside class="success">
Remember — a happy kitten is an authenticated kitten!
</aside>

## Get a Specific Kitten

```ruby
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
api.kittens.get(2)
```

```python
import kittn

api = kittn.authorize('meowmeowmeow')
api.kittens.get(2)
```

```shell
curl "http://example.com/api/kittens/2"
  -H "Authorization: meowmeowmeow"
```

```javascript
const kittn = require('kittn');

let api = kittn.authorize('meowmeowmeow');
let max = api.kittens.get(2);
```

> The above command returns JSON structured like this:

```json
{
  "id": 2,
  "name": "Max",
  "breed": "unknown",
  "fluffiness": 5,
  "cuteness": 10
}
```

This endpoint retrieves a specific kitten.

<aside class="warning">Inside HTML code blocks like this one, you can't use Markdown, so use <code>&lt;code&gt;</code> blocks to denote code.</aside>

### HTTP Request

`GET http://example.com/kittens/<ID>`

### URL Parameters

Parameter | Description
--------- | -----------
ID | The ID of the kitten to retrieve

## Delete a Specific Kitten

```ruby
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
api.kittens.delete(2)
```

```python
import kittn

api = kittn.authorize('meowmeowmeow')
api.kittens.delete(2)
```

```shell
curl "http://example.com/api/kittens/2"
  -X DELETE
  -H "Authorization: meowmeowmeow"
```

```javascript
const kittn = require('kittn');

let api = kittn.authorize('meowmeowmeow');
let max = api.kittens.delete(2);
```

> The above command returns JSON structured like this:

```json
{
  "id": 2,
  "deleted" : ":("
}
```

This endpoint deletes a specific kitten.

### HTTP Request

`DELETE http://example.com/kittens/<ID>`

### URL Parameters

Parameter | Description
--------- | -----------
ID | The ID of the kitten to delete

<!--stackedit_data:
eyJoaXN0b3J5IjpbNjI4NDIwMDg2XX0=
-->