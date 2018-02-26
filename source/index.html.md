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

# itsme(r) Login Authentication

http://openid.net/specs/openid-connect-core-1_0.html#CodeFlowSteps

## 1. Authentication Request

http://openid.net/specs/openid-connect-core-1_0.html#AuthRequest
http://openid.net/specs/openid-connect-core-1_0.html#AuthorizationEndpoint

itsme(r) endpoint : https://merchant.itsme.be/oidc/authorization

The fingerprint of the SSL/TLS certificate is xxxxxxx.

The User is redirected to the interface of the itsme Authorization endpoint. This will be a webpage if the User is in the Service Provider website, or a webview if the User is in a Service Provider App. The parameters of the redirection URL contain all information itsme needs to execute the Authentication and Authorization process, according to the OpenID specifications :

Parameter | Comment
--------- | -------
**scope** | MUST contain at least `openid` or an HTTP ERROR not\_implemented will be returned. `offline\_access` value will yield an error. Other recognized values are: `profile`, `email`, `phone`, `address`. MUST also include the target service in the form `service:SERVICE_CODE`
**response_type** | MUST be <code>code</code>. Other values will produce an HTTP ERROR not_implemented.
**client_id** | MUST be a valid partner code as provided by the BMID during on-boarding process (this information is in the onboarding file). This value will be unique to each partner.
redirect_uri | This value MUST match one of the values provided by the partner to the itsme administrator when registering the Partner. It must include the https scheme. Note: the Partner can define a different redirect_uri specific to each Service.
**state** | An appropriate value is RECOMMENDED
**response_mode** | Should not be used. Any supplied value will be ignored.
**nonce** | An appropriate value is RECOMMENDED
**display** | OPTIONAL but not recommended. MUST be page if provided. Other values will yield an HTTP ERROR not_implemented.
**prompt** | OPTIONAL but not recommended Only login and consent values are supported. If prompt contains none or select\_account or any other values an HTTP ERROR not\_implemented will be returned.
**max_age** | OPTIONAL supported but not used: itsme will always actively re-authenticate the End-User. Value must be an integer.
**ui_locales** | <OPTIONAL and supported. Can be used to specify the language to be used by the OpenID login page.

: sSupported languagesvalues are: {“fr”, “nl”, “en”, “de”}. Any other value will be ignored.


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
eyJoaXN0b3J5IjpbMTA3NTA3NzkxMF19
-->