---
title: itsme® OpenID Connect documentation

language_tabs: # must be one of https://git.io/vQNgJ
- HTTP

toc_footers:
- <a href='#'>Sign Up for a Developer Key</a> -->
- <a href='https://github.com/lord/slate'>Documentation Powered by Slate</a>

search: true
---
<a name="Onboarding"></a>
# Selecting your client authentication method

A client authentication method is requied to protect the exhange of entitlement information between us, ensure the requested information get issued to a legitimate service provider and not some other party.

The OpenID Connect Core specifications support multiple authentication methods, but itsme® only supports :
<ul>
  <li><i>"private_key_jwt"</i> : 
  <li><i>"client_secret_post"</i></li>
</ul>

### private_key_jwt method

This method requires that each party exposes its public keys as a simple JWK Set document on a URI accessible to all, and keep its private set for itself. For itsme®, this URI can be retrieved from the [itsme® Discovery document](#OpenIDConfig), using the <i>"jwks_uri"</i> key.</li>

Your private and public keys can be generated using your own tool or via Yeoman. If using Yeoman, you need to install generator-itsme with NPM:

<code style=display:block;white-space:pre-wrap>$ npm install -g yo generator-itsme</code>

After installation, run the generator:

<code style=display:block;white-space:pre-wrap>$ yo itsme</code>

The Yeoman tool will generate two files, the jwks_private.json which MUST be stored securely somewhere in your systems, and the jwks_public.json which need to be exposed as a JWK Set on a URI accessible to all parties.

<aside class="notice">Whatever the tool you are chossing to create your key pairs, don't forget to send your JWK Set URI by email to onboarding@itsme.be and we’ll make sure to complete the configuration for you in no time!
</aside>

### client_secret_post method

This method requires the exchange of a static secret that will be used to authenticate with our Back-End. 

The client_secret value will be provided by itsme® when [registering your project](#Onboarding).


<a name="OpenIDConfig"></a>
# Check itsme® OpenID Provider configuration

To simplify implementations and increase flexibility, <a href="https://openid.net/specs/openid-connect-discovery-1_0.html" target="blank">OpenID Connect allows the use of a Discovery Document</a>, a JSON document containing key-value pairs which provide details about itsme® configuration, such as the URIs of the 

<ul>
  <li>Authorization, Token and userInfo Endpoints</li>
  <li>supported claims</li>
  <li>JWKSet URL</li>
  <li>...</li>
</ul>

The Discovery document for itsme® can be retrieved from: 

Client Authentication method |Environment | URL
:-------- | :-------- | :--------
**private_key_jwt** | **SANDBOX** | <a href="https://idp.e2e.itsme.services/v2/.well-known/openid-configuration" target="blank">https://idp.e2e.itsme.services/v2/.well-known/openid-configuration</a>
**** | **PRODUCTION** | <a href="https://idp.prd.itsme.services/v2/.well-known/openid-configuration" target="blank">https://idp.prd.itsme.services/v2/.well-known/openid-configuration</a>
**client_secret_post** | **SANDBOX** | <a href="https://oidc.e2e.itsme.services/clientsecret-oidc/csapi/v0.1/.well-known/openid-configuration" target="blank">https://oidc.e2e.itsme.services/clientsecret-oidc/csapi/v0.1/.well-known/openid-configuration</a>
**** | **PRODUCTION** | <a href="https://oidc.prd.itsme.services/clientsecret-oidc/csapi/v0.1/.well-known/openid-configuration" target="blank">https://oidc.prd.itsme.services/clientsecret-oidc/csapi/v0.1/.well-known/openid-configuration</a>


# Building your Authorization Request

### Composing your base URL

You will forg a HTTPS GET request that MUST be sent to the itsme® Authorization Endpoint. The itsme® Authorization Endpoint can be retrieved from one of the [itsme® Discovery document URL](#OpenIDConfig), using the key <i>"authorization_endpoint"</i>.

The OpenID Connect Core specification defines a number of parameters to integrate in the HTTPS GET query string :

Parameter | Required | Description
:-------- | :--------| :----- 
**client_id** | Required |This is the client ID you received after sharing your organisation details with us.
**response_type** | Required | This defines the processing flow to be used when forming the response. Because itsme® uses the Authorization Code Flow as described above, this value MUST be <i>"code"</i>.
**scope** | Required | The scope parameter allows the application to express the desired scope of the access request. It MUST contain the value <i>"openid"</i> and <i>"service:TEST_code"</i>, by replacing "TEST_code" with the service code you received when registering your project in the [itsme® B2B portal](#Onboarding).<br>You MAY also specify additional scopes, separated by spaces, to request more information about the User. See the [list](#Data) below for more information.</br><br>An HTTP ERROR <i>"not_implemented"</i> will be returned if the required values are not specified.</br><br>Unrecognised values will be ignored.</br>
**redirect_uri** | Required | This is the URI to which the authentication response will be sent. The Redirection URI MUST use the <i>"https"</i> scheme. The Redirection URI MAY NOT use the <i>"http"</i> or an alternate scheme, such as one that is intended to identify a callback into a native application.<br></br><b>Note</b> : <ul><li>this URI MUST be whitelisted in our systems. So, don't forget to send your it by email to onboarding@itsme.be and we’ll make sure to complete the configuration for you in no time!</li><li>Regardless of the application you are building you should make sure that your redirect URIs support the Universal links and App links mechanism. Functionally, it will allow you to have only one single link that will either open your desktop web application, your mobile app or your mobile site on the User’s device. Universal links and App links are standard web links (http://mydomain.com) that point to both a web page and a piece of content inside an app. When a Universal Link is opened, the app OS checks to see if any installed app is registered for that domain. If so, the app is launched immediately without ever loading the web page. If not, the web URL is loaded into the webbrowser. The specifications for the implementation of Universal links and App links can be found in the Appendix.</li></ul>
**state** | Strongly RECOMMENDED | An opaque value used in the Authentication Request, which will be returned unchanged in the Authorization Code. This parameter SHOULD be used for preventing cross-site request forgery (XRSF). <br>When deciding how to implement this, one suggestion is to use a private key together with some easily verifiable variables, for example, your client ID and a session cookie, to compute a hashed value. This will result in a byte value that will be infeasibility difficult to guess without the private key. After computing such an HMAC, base-64 encode it and pass it to the Authorization  Server as <i>"state"</i> parameter. Another suggestion is to hash the current date and time. This requires your application to save the time of transmission in order to verify it or to allow a sliding period of validity.</br>
**nonce** | Strongly RECOMMENDED | A string value used to associate a session with an ID Token, and to mitigate replay attacks. The value is passed through unmodified from the Authentication Request to the ID Token. Sufficient entropy MUST be present in the <i>"nonce"</i> values used to prevent attackers from guessing values. See <a href="http://openid.net/specs/openid-connect-core-1_0.html#NonceNotes" target="blank">OpenID Connect Core specifications</a> for more information.
**login_hint** | Optional | Can be used to pre-fill the phone number field on the itsme® OpenID web page for the User, if your application knows ahead of time which User is trying to authenticate. If provided, this value MUST be a phone number in the format specified for the <i>"phone_number"</i> claim: <i>"<countrycode>+<phonenumber>"</i>. E.g. <i>"login_hint=32+123456789"</i>.</br><br><i>"login_hint"</i> with invalid syntax will be ignored.</br>
**display** | Optional | ASCII string value that specifies how the Authorization Server displays the authentication and consent User interface pages to the User. MUST be <i>"page"</i> if provided.<br>Other values will yield an HTTP ERROR <i>"not_implemented"</i>.</br>
**prompt** | Optional | Space delimited, case sensitive list of ASCII string values that specifies whether the Authorization Server prompts the User for reauthentication and consent. MUST be <i>"consent"</i> if provided. 
**ui_locales** | Optional | User's preferred languages and scripts for the User interface (e.g.: OpenID web page). Supported values are: <i>"fr"</i>, <i>"nl"</i>, <i>"en"</i> and <i>"de"</i>. Any other value will be ignored.
<a name="acrvalues">**acr_values**</a> | Optional | Space-separated string that specifies the acr values that the Authorization Server is being requested to use for processing this Authentication Request, with the values appearing in order of preference.<br>2 values are supported:<ul><li>Basic level - let the User to choose either fingerprint usage (if device is compatible) or PIN<br><i>"http://itsme.services/v2/claim/acr_basic"</i></br></li><li>Advanced level - force the User to use PIN<br><i>"http://itsme.services/v2/claim/acr_advanced"</i></br></li></ul>When multiple values are provided only the most constraining will be used (advanced > basic). If not provided basic level will be used.</br>
**max_age** | Not supported | Any supplied value will be ignored.<br>As itsme® does not maintain a session mechanism, an active authentication is always required.</br>
**response_mode** | Not supported | Any supplied value will be ignored.
**id\_token\_hint** | Not supported | Any supplied value will be ignored.
**claims_locales** | Not supported | Any supplied value will be ignored.
**registration** | Not supported | Any supplied value will be ignored.
**claims** | Optional | This parameter is used to request specific claims. The value is a JSON object listing the requested claims. <br>See the [list](#Data) below for more information.</br>
**request_uri** | Optional | This parameter enables OpenID Connect parameters to be passed by reference. The <i>"request_uri"</i> value is a URL using the https scheme referencing a resource containing a Request Object value, which is a JWT containing the request parameters. <br>When the <i>"request_uri"</i> parameter is used, the OpenID Connect request parameter values contained in the referenced JWT supersede those passed using the OAuth 2.0 request syntax.</br><br>The following validations should be done when using the <i>"request_uri"</i> parameter:</br><ul><li>The values for the <i>"response_type"</i> and <i>"client_id"</i> parameters MUST be filled in the Authentication Request, since they are REQUIRED in the OpenID Connect Core specifications. The values for these parameters MUST match those in the Request Object, if present.</li><li>Even if a <i>"scope"</i> parameter is present in the Request Object value, a <i>"scope"</i> parameter – containing the <i>"openid"</i> scope value to indicate to the underlying OpenID Connect Core logic that this is an OpenID Connect request – MUST always be passed in the Authentication Request.</li><li>You need to store the Request Object resource remotely at a URL the the Authorization Server can access. This URL is the Request URI, <i>"request_uri"</i>. Usage of 'localhost' is not permitted.<li>The Request URI MUST contain the port 443 as in this example: https://test.istme.be:443/p/test.</li><li>The Request URI value is a URL using the <i>https</i> scheme.</li></ul><br>Don't forget to send share this URI by email to onboarding@itsme.be and we’ll make sure to complete the configuration for you in no time!</br><br>If the <i>"request"</i> parameters is used, this parameter MUST NOT be used in the same request.</br>
**request** | Optional | If the <i>"request_uri"</i> parameters is used, this parameter MUST NOT be used in the same request.</br>










###  Requesting claims about the User and the Authentication event 




