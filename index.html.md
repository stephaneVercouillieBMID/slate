---


---

<hr>
<p>title: itsme® OpenID Connect documentation</p>
<p>language_tabs: # must be one of <a href="https://git.io/vQNgJ">https://git.io/vQNgJ</a></p>
<ul>
<li>json: JSON</li>
<li>http: HTTP</li>
</ul>
<p>toc_footers:</p>
<ul>
<li><a href="#">Sign Up for a Developer Key</a></li>
<li><a href="https://github.com/lord/slate">Documentation Powered by Slate</a></li>
</ul>
<h2 id="search-true">search: true</h2>
<h1 id="introduction-to-itsme®">Introduction to itsme®</h1>
<p>This documentation describes our OAuth 2.0 implementation of <strong>itsme® Login</strong>, which conforms to the <a href="http://openid.net/specs/openid-connect-core-1_0.html">OpenID Connect 1.0</a> specifications, and is OpenID certified (TODO).</p>
<h2 id="login">Login</h2>
<p><strong>itsme® Login</strong> is a service provided by <a href="https://www.belgianmobileid.be">Belgian Mobile ID</a> (BMID) to allow End-Users to login securely to your application.</p>
<p>itsme® Login uses pairwise user identifier, meaning each Partner will have a unique <em>User Code</em> for the same User. Doing so, nobody except BMID can link one given <em>User Code</em> of Partner to a specific User identity.</p>
<h2 id="share-data">Share Data</h2>
<p>If purpose of use is stated during the on-boarding process and consent is provided by the End-User during Authentication, Data can be shared with your application.</p>
<p>Data is currently shared only during Login or Approval. Off-line access to User information is not authorised.</p>
<p>The following sets of Data are available:</p>
<ul>
<li><strong>Verified Identity Data</strong>: identity information retrieved from the National eID Card</li>
<li><strong>Commercial Information</strong>: information provided by the end-user</li>
<li><strong>Security Information</strong>: information retrieved during execution of the service that could impact security level of the transaction.</li>
</ul>
<h3 id="verified-identity-data">Verified Identity Data</h3>
<p>The user identity data provided by <strong>itsme®</strong> are Data coming from the National Registry. These datas are provided to <strong>itsme®</strong> during user enrolment either directly from a card readout or indirectly through an IDentity Registrar (IDR) having a strong identity verification process (e.g. face-to-face KYC with eID readout)  in-place.</p>

<table>
<thead>
<tr>
<th>Data</th>
<th>Definition</th>
</tr>
</thead>
<tbody>
<tr>
<td><strong>Full Name</strong></td>
<td>Full name is a concatenation of firstname, middlenames and lastname.</td>
</tr>
<tr>
<td><strong>Date of birth</strong></td>
<td>Birthdate</td>
</tr>
<tr>
<td><strong>Place of birth</strong></td>
<td>Place of birth. <em><strong>Note</strong>: this information can be localized</em></td>
</tr>
<tr>
<td><strong>Gender</strong></td>
<td>Gender</td>
</tr>
<tr>
<td><strong>Language</strong></td>
<td>Language</td>
</tr>
<tr>
<td><strong>Nationality</strong></td>
<td>Nationality</td>
</tr>
<tr>
<td><strong>Address</strong></td>
<td>Address containing street, house number, postbox, locality, postcode and country</td>
</tr>
<tr>
<td><strong>Passport Number</strong></td>
<td>Passport Number</td>
</tr>
<tr>
<td><strong>NRN</strong></td>
<td>National Registry Number</td>
</tr>
<tr>
<td><strong>E-ID picture</strong></td>
<td>Picture taken from the National eID Card in low-resolution.</td>
</tr>
<tr>
<td><strong>E-ID Metadata</strong></td>
<td>See <a href="#e-id-info">E-ID Metadata Information</a></td>
</tr>
</tbody>
</table><h3 id="a-namee-id-infoae-id-metadata-information"><a></a>E-ID Metadata Information</h3>
<p>Provides some information about the eID card readout related to the identity data provided by <strong>itsme®</strong>.</p>

<table>
<thead>
<tr>
<th>Data</th>
<th>Definition</th>
</tr>
</thead>
<tbody>
<tr>
<td><strong>eID Serial Number</strong></td>
<td>the electronic ID card serial number.</td>
</tr>
<tr>
<td><strong>issuance_locality</strong></td>
<td>the issuance locality.</td>
</tr>
<tr>
<td><strong>Validity from</strong></td>
<td>eID card validity “from” date.</td>
</tr>
<tr>
<td><strong>Validity to</strong></td>
<td>eID card validity “to” date.</td>
</tr>
<tr>
<td><strong>Certificate Validity</strong></td>
<td>the certificate validity.</td>
</tr>
<tr>
<td><strong>Read Date</strong></td>
<td>the data extraction date.</td>
</tr>
</tbody>
</table><h3 id="commercial-data">Commercial Data</h3>

<table>
<thead>
<tr>
<th>Data</th>
<th>Definition</th>
</tr>
</thead>
<tbody>
<tr>
<td><strong>Phone Number</strong></td>
<td>Verified phone number associated to the <strong>itsme®</strong> user account.</td>
</tr>
<tr>
<td><strong>E-Mail Address</strong></td>
<td>E-Mail address. NOT Verified! TODO</td>
</tr>
</tbody>
</table><h3 id="security-data">Security Data</h3>

<table>
<thead>
<tr>
<th>Data</th>
<th>Definition</th>
</tr>
</thead>
<tbody>
<tr>
<td><strong>Device</strong></td>
<td>Information about the end user device. See <a href="#device-information">Device Information</a></td>
</tr>
</tbody>
</table><h4 id="a-namedevice-informationadevice-information"><a></a>Device Information</h4>

<table>
<thead>
<tr>
<th>Data</th>
<th>Definition</th>
</tr>
</thead>
<tbody>
<tr>
<td><strong>OS</strong></td>
<td>the device operating system (supported values: {<code>ANDROID</code>, <code>IOS</code>})</td>
</tr>
<tr>
<td><strong>Device Identifier</strong></td>
<td>Device identifier.</td>
</tr>
<tr>
<td><strong>Application Name</strong></td>
<td>Application name.</td>
</tr>
<tr>
<td><strong>Application Release</strong></td>
<td>Application current release.</td>
</tr>
<tr>
<td><strong>Device Label</strong></td>
<td>Name of the device.</td>
</tr>
<tr>
<td><strong>Debug Enabled</strong></td>
<td>True if debug mode is activated.</td>
</tr>
<tr>
<td><strong>OS Release</strong></td>
<td>Version of the OS running on your Device.</td>
</tr>
<tr>
<td><strong>Manufacturer</strong></td>
<td>Brand of the device manufacturer (‘Apple’ on iOS, device specific on Android).</td>
</tr>
<tr>
<td><strong>SIM Enabled</strong></td>
<td>True if there is a SIM in the Device. Should be always true, as long as BMID keeps forbidding installing <strong>itsme®</strong> on a tablet.</td>
</tr>
<tr>
<td><strong>Lock Level</strong></td>
<td>The type of action to be performed to unlock the Device. On iOS : TOUCH_ID, PASSCODE or NONE if User protected his Device with TouchID, PIN or nothing.</td>
</tr>
<tr>
<td><strong>SMS Enabled</strong></td>
<td>True if can send SMS. On iOS, this means it’s an iPhone.</td>
</tr>
<tr>
<td><strong>Rooted</strong></td>
<td>True if the device is jailbreaked/rooted.</td>
</tr>
<tr>
<td><strong>IMEI</strong></td>
<td>Device IMEI value.</td>
</tr>
<tr>
<td><strong>Model</strong></td>
<td>Model of the Device. e.g. SAMSUNG GALAXY A5</td>
</tr>
<tr>
<td><strong>MSISDN</strong></td>
<td>User’s phone number.</td>
</tr>
<tr>
<td><strong>SDK Release</strong></td>
<td>SDK release</td>
</tr>
</tbody>
</table><h2 id="on-boarding-process">On-boarding Process</h2>
<p>Before your application can use <strong>itsme®</strong> for user login, you must set up a project to obtain OIDC credentials, set redirect URIs for your services, and customise the branding information that your users see on the <strong>itsme®</strong> user-consent screen.</p>
<h3 id="customize-the-user-consent-screen">Customize the user consent screen</h3>
<p>SP provides</p>
<ul>
<li>Partner name, description and localized labels</li>
<li>Services’ name, description and localized labels</li>
<li>Data access with justification (to comply with GDPR)</li>
</ul>
<h3 id="obtain-oauth-2.0-credentials">Obtain OAuth 2.0 credentials</h3>
<p>BMID provides</p>
<ul>
<li>Partner Code, used as <strong>client_id</strong></li>
<li>Service Code for each Service.</li>
</ul>
<h3 id="set-a-redirect-uri-and-certificates">Set a redirect URI and Certificates</h3>
<p>SP provides</p>
<ul>
<li>the redirect URIs for each Service to use within the OpenID Connect protocol to send back the response of the Authentication Request.</li>
<li>the JWKSet HTTPS endpoint exposing the signing and encryption public certificates of the SP.</li>
<li>the SSL/TLS certificate used on the JWKSet HTTPS endpoint of the SP.</li>
</ul>
<h3 id="interact-with-itsme®">Interact with itsme®</h3>
<p>The OpenID Connect protocol requires the use of multiple endpoints for authenticating users, and for requesting resources including tokens, user information, and public keys.</p>
<p>To simplify implementations and increase flexibility, OpenID Connect allows the use of a “Discovery document”, a JSON document found at a well-known location containing key-value pairs which provide details about the OpenID Connect provider’s configuration, including the URIs of the authorization, token, userinfo, and public-keys endpoints to interact with it.</p>
<aside class="success">The Discovery document for **itsme® ** service may be retrieved from: <a href="https://merchant.itsme.be/oidc/.well-known/openid-configuration">https://merchant.itsme.be/oidc/.well-known/openid-configuration</a></aside>
<p>Field  names and meanings in this document are defined in <a href="https://openid.net/specs/openid-connect-discovery-1_0.html">OpenID Connect Discovery 1.0</a></p>
<h1 id="authenticating-the-user">Authenticating the User</h1>
<p><strong>itsme® Login</strong> is based on the <a href="http://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth">Authorization Code Flow</a> of OpenID Connect 1.0.</p>
<p>The Authorization Code Flow goes through the following steps as defined in  <a href="http://openid.net/specs/openid-connect-core-1_0.html#CodeFlowSteps">http://openid.net/specs/openid-connect-core-1_0.html#CodeFlowSteps</a></p>
<h2 id="authentication-request">1. Authentication Request</h2>
<p>As per the OpenID Connect specification <a href="http://openid.net/specs/openid-connect-core-1_0.html#AuthRequest">http://openid.net/specs/openid-connect-core-1_0.html#AuthRequest</a> and <a href="http://openid.net/specs/openid-connect-core-1_0.html#AuthorizationEndpoint">http://openid.net/specs/openid-connect-core-1_0.html#AuthorizationEndpoint</a></p>
<blockquote>
<p>Example Request</p>
</blockquote>
<pre class=" language-http"><code class="prism --inline language-http">POST /token HTTP/1.1
<span class="token header-name keyword">Host:</span> server.example.com
<span class="token header-name keyword">Content-Type:</span> application/x-www-form-urlencoded

grant_type=authorization\_code&amp;
code=SplxlOBeZQQYbYS6WxSbIA&amp;
redirect_uri=https%3A%2F%2Fclient.example.org%2Fcb
client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer&amp;
client_assertion=PHNhbWxwOl ... ZT
</code></pre>
<p>The first step is forming an HTTPS request with the appropriate URI parameters. Note the use of HTTPS rather than HTTP in all the steps of this process; HTTP connections are refused. You should retrieve the base URI from the <a href="https://merchant.itsme.be/oidc/.well-known/openid-configuration">Discovery document</a> using the key <strong>authorization_endpoint</strong>. The following discussion assumes the base URI is <code>https://merchant.itsme.be/oidc/authorization</code>.</p>
<p><strong>itsme®</strong> supports the use of the HTTP <code>GET</code> and <code>POST</code> methods. If using the HTTP <code>POST</code> method, the request parameters must be serialized using <a href="http://openid.net/specs/openid-connect-core-1_0.html#FormSerialization">Form Serialization</a>.</p>
<p>For a basic request, specify the following parameters:</p>

<table>
<thead>
<tr>
<th>Parameter</th>
<th>Required</th>
<th>Comment</th>
</tr>
</thead>
<tbody>
<tr>
<td><strong>client_id</strong></td>
<td>Required</td>
<td>MUST be the Partner Code you obtained from BMID during on-boarding process (this information is in the onboarding file TODO).</td>
</tr>
<tr>
<td><strong>response_type</strong></td>
<td>Required</td>
<td>MUST be <code>code</code>.</td>
</tr>
<tr>
<td><strong>scope</strong></td>
<td>Required</td>
<td>MUST contain at least <code>openid</code> or an HTTP ERROR <code>not_implemented</code> will be returned. <code>offline_access</code> value will yield an error. MUST also includes the target service in the form <code>service:&lt;SERVICE_CODE&gt;</code> as provided by BMID during the on-boarding process. <em><strong>Note</strong>: Requested data will only be provided based on your current accesses</em>. See XXXXXX (TODO) for list of available data.</td>
</tr>
<tr>
<td><strong>redirect_uri</strong></td>
<td>Required</td>
<td>should be the HTTPS endpoint on your server that will receive the response from <strong>itsme®</strong>. This value MUST match one of the values provided to BMID during on-boarding process. <em><strong>Note</strong>: the Partner can define different <strong>redirect_uri</strong> specific to each Service.</em></td>
</tr>
<tr>
<td><strong>state</strong></td>
<td>Optional, but strongly recommended</td>
<td>should include the value of the anti-forgery unique session token, as well as any other information needed to recover the context when the user returns to your application.</td>
</tr>
<tr>
<td><strong>nonce</strong></td>
<td>Optional</td>
<td>a random value generated by your app that enables replay protection when present.</td>
</tr>
<tr>
<td><strong>login_hint</strong></td>
<td>Optional</td>
<td>OPTIONAL and supported, though not recommended. Only phone numbers are supported as <code>login_hint</code>. Format is: <code>\&lt;coutrycode\&gt;+\&lt;phonenumber\&gt;</code>. E.g. <code>login_hint=32+123456789</code>. Usage of claim value <code>phone_number</code> in an encrypted request object is recommended in order to avoid disclosure of phone number of the enduser on the user agent (TODO what does it means?)</td>
</tr>
<tr>
<td><strong>display</strong></td>
<td>Optional</td>
<td>MUST be <code>page</code> if provided. Other values will yield an HTTP ERROR <code>not_implemented</code>.</td>
</tr>
<tr>
<td><strong>prompt</strong></td>
<td>Optional</td>
<td>MUST be <code>consent</code> if provided.</td>
</tr>
<tr>
<td><strong>ui_locales</strong></td>
<td>Optional</td>
<td>Can be used to specify the language to be used by the OpenID login page. Supported languages are: <code>fr</code>, <code>nl</code>, <code>en</code> and <code>de</code>. Any other value will be ignored.</td>
</tr>
<tr>
<td><strong>max_age</strong></td>
<td>Optional</td>
<td>Supported but not used: <strong>itsme®</strong> will always actively re-authenticate the End-User.</td>
</tr>
<tr>
<td><strong>acr_values</strong></td>
<td>Optional</td>
<td>OPTIONAL and supported, though not recommended. Possible values are tag:itsmetag:sixdots.be,2016-06:acr_basic, tag:itsmetag:sixdots.be,2016-06:acr_advanced. When multiple values are provided only the most constraining will be used (advanced &gt; basic). If not provided basic level will be used. As there is no such idea of an existing session on itsme Core, even if the acr_values is requested as a voluntarily claim, the acr value returned will always be the more constraining method in the acr_values list, or the authentication will fail. Usage of acr parameter in the request object is recommended over this parameter as it will be signed in the JWT token</td>
</tr>
<tr>
<td><strong>claims</strong></td>
<td>Optional</td>
<td>Not recommended. Usage of claims parameter in the request object is recommended over this parameter as it will be signed in the JWT token, and the data will be encrypted</td>
</tr>
<tr>
<td><strong>request</strong></td>
<td>Optional</td>
<td>See <a href="#JWTRequest">Passing Request Parameters as JWTs</a></td>
</tr>
<tr>
<td><strong>response_mode</strong></td>
<td>Unsupported</td>
<td>Ignored if provided.</td>
</tr>
<tr>
<td><strong>id_token_hint</strong></td>
<td>Unsupported</td>
<td>Ignored if provided.</td>
</tr>
<tr>
<td><strong>claims_locales</strong></td>
<td>Unsupported</td>
<td>None are supported.</td>
</tr>
<tr>
<td><strong>request_uri</strong></td>
<td>Unsupported</td>
<td>N/A TODO</td>
</tr>
<tr>
<td><strong>registration</strong></td>
<td>Unsupported</td>
<td>N/A TODO</td>
</tr>
</tbody>
</table><h3 id="request-examples">Request Examples</h3>
<blockquote>
<p>Example of a minimal Authorization request</p>
</blockquote>
<pre class=" language-http"><code class="prism --inline language-http">GET /authorize?response_type=code
&amp;scope=openid%20profile%20email%20service%3Aclient.registration
&amp;client_id=s6BhdRkqt3
&amp;state=af0ifjsldkj
&amp;redirect_uri=https%3A%2F%2Fclient.example.org%2Fcb HTTP/1.1
<span class="token header-name keyword">Host:</span> server.itsme.be
</code></pre>
<h3 id="authentication-response">Authentication Response</h3>
<p>An Authentication Response is an <a href="https://tools.ietf.org/html/rfc6749#section-4.1.2">OAuth 2.0 Authorization Response</a> message. As such, the Authentication Response will return the following parameters:</p>

<table>
<thead>
<tr>
<th>Parameter</th>
<th>Provided</th>
<th>Description</th>
</tr>
</thead>
<tbody>
<tr>
<td><strong>code</strong></td>
<td>Always</td>
<td>Authorization code to later provide to the token endpoint. This code has a lifetime of 3 minutes.</td>
</tr>
<tr>
<td><strong>state</strong></td>
<td></td>
<td>The exact value received from the client, if the parameter was present in the Authentication Request.</td>
</tr>
</tbody>
</table><blockquote>
<p>Example of Authentication Response</p>
</blockquote>
<pre class=" language-http"><code class="prism --inline language-http"><span class="token response-status">HTTP/1.1 <span class="token property">302 Found</span></span>
<span class="token header-name keyword">Location:</span> https://client.example.org/cb?
  code=SplxlOBeZQQYbYS6WxSbIA
  &amp;state=af0ifjsldkj
</code></pre>
<h3 id="errors">Errors</h3>
<p>As per <a href="http://openid.net/specs/openid-connect-core-1_0.html#AuthError">http://openid.net/specs/openid-connect-core-1_0.html#AuthError</a></p>
<p>If the authentication is NOT successful, the following errors can be triggered by itsme®</p>

<table>
<thead>
<tr>
<th>Error</th>
<th>Description</th>
</tr>
</thead>
<tbody>
<tr>
<td><code>interaction_required</code></td>
<td>The Authorization Server requires End-User interaction of some form to proceed.</td>
</tr>
<tr>
<td><code>invalid_request_object</code></td>
<td>The request parameter contains an invalid Request Object.</td>
</tr>
<tr>
<td><code>request_uri_not_supported</code></td>
<td>does not support use of the request_uri parameter.</td>
</tr>
<tr>
<td><code>registration_not_supported</code></td>
<td>does not support use of the registration parameter.</td>
</tr>
</tbody>
</table><h3 id="app-to-app--todo">App to App ??? TODO</h3>
<p><strong>itsme®</strong> Mobile App endpoint : TODO</p>
<h2 id="token-request">2. Token Request</h2>
<p>As per the OpenID Connect specification <a href="http://openid.net/specs/openid-connect-core-1_0.html#TokenRequest">http://openid.net/specs/openid-connect-core-1_0.html#TokenRequest</a></p>
<p>The Authentication Response includes a <code>code</code> parameter, a one-time authorization code that your server can exchange for an ID token. Your server makes this exchange by sending an HTTPS <code>POST</code> request. The <code>POST</code> request is sent to the token endpoint, which you should retrieve from the <a href="https://merchant.itsme.be/oidc/.well-known/openid-configuration">Discovery document</a> using the key <strong>token_endpoint</strong>. The following discussion assumes the endpoint is <code>https://merchant.itsme.be/oidc/token</code>. The request must include the following parameters in the <code>POST</code> body:</p>

<table>
<thead>
<tr>
<th>Parameter</th>
<th>Required</th>
<th>Comment</th>
</tr>
</thead>
<tbody>
<tr>
<td><strong>grant_type</strong></td>
<td>Required</td>
<td>Must be <code>authorization_code</code></td>
</tr>
<tr>
<td><strong>code</strong></td>
<td>Required</td>
<td>The <strong>code</strong> value provided in the Authentication Response</td>
</tr>
<tr>
<td><strong>redirect_uri</strong></td>
<td>Required</td>
<td>The <strong>redirect_uri</strong> used in the Authentication Request</td>
</tr>
<tr>
<td><strong>client_assertion</strong></td>
<td>Required</td>
<td>Must be a valid JWT complying with the <code>private_key_jwt</code> client authentication method as defined in <a href="http://openid.net/specs/openid-connect-core-1_0.html#ClientAuthentication">Section 9</a> of the OpenID specification. This JWT must be signed.</td>
</tr>
<tr>
<td><strong>client_assertion_type</strong></td>
<td>Required</td>
<td>Must be <code>urn:ietf:params:oauth:client-assertion-type:jwt-bearer</code></td>
</tr>
</tbody>
</table><p>Fllowing the <code>private_key_jwt</code> client authentication method, the <strong>client assertion</strong> JWT must contain the following properties:</p>

<table>
<thead>
<tr>
<th>Property</th>
<th>Comment</th>
</tr>
</thead>
<tbody>
<tr>
<td><strong>iss</strong></td>
<td>MUST be the Partner Code you obtained from BMID during on-boarding process (this information is in the onboarding file TODO).</td>
</tr>
<tr>
<td><strong>sub</strong></td>
<td>MUST be the Partner Code you obtained from BMID during on-boarding process (this information is in the onboarding file TODO).</td>
</tr>
<tr>
<td><strong>aud</strong></td>
<td>Must be the token endpoint URL, e.g. <code>https://merchant.itsme.be/oidc/token</code></td>
</tr>
<tr>
<td><strong>jti</strong></td>
<td>A unique identifier for the token, which can be used to prevent reuse of the token. These tokens MUST only be used once.</td>
</tr>
<tr>
<td><strong>exp</strong></td>
<td>Expiration time on or after which the ID Token MUST NOT be accepted for processing.</td>
</tr>
</tbody>
</table><h3 id="successful-token-response">Successful Token Response</h3>

<table>
<thead>
<tr>
<th>Parameter</th>
<th>Provided</th>
<th>Comment</th>
</tr>
</thead>
<tbody>
<tr>
<td><strong>access_token</strong></td>
<td>Always</td>
<td>A token that can be sent to the UserInfo endpoint to retrieve additional information about the user.</td>
</tr>
<tr>
<td><strong>token_type</strong></td>
<td>Always</td>
<td>Will be <code>Bearer</code></td>
</tr>
<tr>
<td><strong>id_token</strong></td>
<td>Always</td>
<td>The JWT <code>id_token</code> corresponding to the Authentication Request (signed and encrypted TODO). See <a href="#id-token">ID Token TODO</a>.</td>
</tr>
<tr>
<td><strong>at_hash</strong></td>
<td></td>
<td>first version of itsme® does not produce the at_hash value ??? TODO which version</td>
</tr>
<tr>
<td><strong>refresh_token</strong></td>
<td>Never</td>
<td>Won’t be provided as <strong>itsme®</strong> only maintains short-lived session to enforce re-authentication.</td>
</tr>
</tbody>
</table><h3 id="errors-1">Errors</h3>
<p>As per the OpenID Connect specification <a href="http://openid.net/specs/openid-connect-core-1_0.html#TokenErrorResponse">http://openid.net/specs/openid-connect-core-1_0.html#TokenErrorResponse</a></p>
<p>TODO if specifics</p>
<h1 id="requesting-user-data">Requesting User Data</h1>
<h2 id="userinfo-request">3. UserInfo Request</h2>
<p>As per the OpenID Connect specification <a href="http://openid.net/specs/openid-connect-core-1_0.html#UserInfoRequest">http://openid.net/specs/openid-connect-core-1_0.html#UserInfoRequest</a></p>
<p>The UserInfo endpoint can be accessed only with a valid <strong>access_token</strong> received from the Token endpoint during User Authentication, and for a very limited duration after end user authentication; there must be less than 3 minutes between the creation of the user action to be confirmed by the end user on his mobile device, and the access to the UserInfo endpoint.</p>
<p>Your server sends the UserInfo Request using either HTTP <code>GET</code> or HTTP <code>POST</code>. The Access Token obtained from an Authentication Request must be sent as a Bearer Token. It is recommended that the request use the HTTP <code>GET</code> method and the Access Token be sent the using the <code>Authorization</code> header field. The HTTP request is sent to the UserInfo endpoint, which you should retrieve from the <a href="https://merchant.itsme.be/oidc/.well-known/openid-configuration">Discovery document</a> using the key <strong>userinfo_endpoint</strong>. The following discussion assumes the endpoint is <code>https://merchant.itsme.be/oidc/userinfo</code>.</p>
<p>The Access Token will define the list of Data that will be provided back to the client (TODO). In order to request specific claims, you can <a href="#">use scopes</a> in the Authentication Request and/or <a href="#">use the claims parameter</a> of the <strong>request</strong> Object.</p>
<blockquote>
<p>Example UserInfo Request</p>
</blockquote>
<pre class=" language-http"><code class="prism --inline language-http">GET /userinfo HTTP/1.1
<span class="token header-name keyword">Host:</span> server.example.com
<span class="token header-name keyword">Authorization:</span> Bearer SlAV32hkKG
</code></pre>
<h3 id="successful-userinfo-response">Successful UserInfo Response</h3>
<p>The content type of the response will be <code>application/jwt</code>. The response will be signed and encrypted by BMID using the signing and encryption certificate exposed.</p>
<blockquote>
<p>Example Successful UserInfo Response (Not encrypted nor signed)</p>
</blockquote>
<pre class=" language-http"><code class="prism --inline language-http"><span class="token response-status">HTTP/1.1 <span class="token property">200 OK</span></span>
<span class="token header-name keyword">Content-Type:</span> application/json<span class="token application/json">

<span class="token punctuation">{</span>
   <span class="token string">"sub"</span><span class="token punctuation">:</span> <span class="token string">"248289761001"</span><span class="token punctuation">,</span>
   <span class="token string">"name"</span><span class="token punctuation">:</span> <span class="token string">"Jane Doe"</span><span class="token punctuation">,</span>
   <span class="token string">"email"</span><span class="token punctuation">:</span> <span class="token string">"janedoe@example.com"</span>
<span class="token punctuation">}</span>
</span></code></pre>
<h2 id="requesting-claims-using-scope-values">Requesting Claims using Scope Values</h2>
<aside class="notice">Note: itsme(r) won't deliver any <a href="http://openid.net/specs/openid-connect-core-1_0.html#AggregatedDistributedClaims">aggregated nor distributed claims</a> in the current version.</aside>
<p>As per OpenID Connect specification, scopes can be used to request that specific sets of information be made available as Claim Values in UserInfo Token.</p>
<p>In current version and in contradiction to the OpenID Connect specification, <strong>itsme®</strong> considers all claims requested via scope as <strong>Essential</strong>. It means the User may not opt out the sharing of specific Data; the User must either gives his consent for the sharing of all Data or refuse the request as a whole.</p>
<p>The following values for <strong>scope</strong> allow access to predefined sets of Data:</p>

<table>
<thead>
<tr>
<th>Scope</th>
<th>Data</th>
<th>Claim</th>
</tr>
</thead>
<tbody>
<tr>
<td><strong><code>profile</code></strong></td>
<td>Full Name</td>
<td><code>name</code> or <code>given_name</code> TODO ??? or <code>family_name</code> TODO ???</td>
</tr>
<tr>
<td></td>
<td>Gender</td>
<td><code>gender</code></td>
</tr>
<tr>
<td></td>
<td>Date of Birth</td>
<td><code>birthdate</code></td>
</tr>
<tr>
<td></td>
<td>Language</td>
<td><code>locale</code></td>
</tr>
<tr>
<td><strong><code>address</code></strong></td>
<td>Address</td>
<td><code>address</code>, with following subfields: <code>street_address</code> (newline separator \n), <code>locality</code>, <code>postal_code</code>, <code>country</code></td>
</tr>
<tr>
<td><strong><code>email</code></strong></td>
<td>E-Mail Address</td>
<td><code>email</code> or <code>email_verified</code> TODO verified???</td>
</tr>
<tr>
<td><strong><code>phone</code></strong></td>
<td>Phone Number</td>
<td><code>phone_number</code> or <code>phone_number_verified</code></td>
</tr>
</tbody>
</table><aside class="notice">NOTE: Any claim requested by using the scope value can only be obtained from the UserInfo endpoint.</aside>
<h2 id="requesting-claims-using-the-claims-request-parameter">Requesting Claims using the “claims” Request Parameter</h2>
<p>We have favoured the request of Data in the <strong>scope</strong> values. However, some specific Data have to be requested in the <strong>claims</strong> parameter of the Authentication Request. Here are these claims:</p>

<table>
<thead>
<tr>
<th>Data</th>
<th>Claim</th>
<th>Comment</th>
</tr>
</thead>
<tbody>
<tr>
<td>???</td>
<td><strong><code>sub</code></strong></td>
<td>TODO</td>
</tr>
<tr>
<td>Nationality</td>
<td><strong><code>tag:itsmetag:sixdots.be,2016-06:claim_nationality</code></strong></td>
<td>TODO</td>
</tr>
<tr>
<td>Place of Birth</td>
<td><strong><code>tag:itsmetag:sixdots.be,2016-06:claim_city_of_birth</code></strong> and <strong><code>tag:itsmetag:sixdots.be,2016-06:claim_country_of_birth</code></strong></td>
<td>TODO</td>
</tr>
<tr>
<td>E-ID Info</td>
<td><strong><code>tag:itsmetag:sixdots.be,2016-06:claim_eid</code></strong></td>
<td>TODO</td>
</tr>
<tr>
<td>Passport Number</td>
<td><strong><code>tag:sixdots.be,2017-05:claim_passport_sn</code></strong></td>
<td>TODO</td>
</tr>
<tr>
<td>Device</td>
<td><strong><code>tag:sixdots.be,2017-05:claim_device</code></strong></td>
<td>see XXXX (TODO URL)</td>
</tr>
<tr>
<td>??? TODO</td>
<td><strong><code>tag:sixdots.be,2017-05:claim_transaction_info</code></strong></td>
<td>TODO</td>
</tr>
<tr>
<td>E-ID Picture</td>
<td>TODO</td>
<td>TODO</td>
</tr>
<tr>
<td>NRN</td>
<td>TODO</td>
<td>TODO</td>
</tr>
</tbody>
</table><p>In current version and in contradiction to the OpenID Connect specification, <strong>itsme®</strong> considers all claims as <strong>Essential</strong>, even if they are requested as a <strong>Voluntary</strong> claim (see <a href="http://openid.net/specs/openid-connect-core-1_0.html#IndividualClaimsRequests">Individual Claim Request</a>). However, as in a future version <strong>itsme®</strong>  will make the difference between <strong>Essential</strong> and <strong>Voluntary</strong> claims, you should already request claims with appropriate level regarding your business case. TODO =&gt; it means we should not request by scope???</p>
<blockquote>
<p>Example of Claims request parameter TODO adapt to itsme® data</p>
</blockquote>
<pre class=" language-json"><code class="prism --inline language-json"><span class="token punctuation">{</span>
   <span class="token string">"userinfo"</span><span class="token punctuation">:</span>
    <span class="token punctuation">{</span>
     <span class="token string">"given_name"</span><span class="token punctuation">:</span> <span class="token punctuation">{</span><span class="token string">"essential"</span><span class="token punctuation">:</span> <span class="token boolean">true</span><span class="token punctuation">}</span><span class="token punctuation">,</span>
     <span class="token string">"nickname"</span><span class="token punctuation">:</span> <span class="token keyword">null</span><span class="token punctuation">,</span>
     <span class="token string">"email"</span><span class="token punctuation">:</span> <span class="token punctuation">{</span><span class="token string">"essential"</span><span class="token punctuation">:</span> <span class="token boolean">true</span><span class="token punctuation">}</span><span class="token punctuation">,</span>
     <span class="token string">"email_verified"</span><span class="token punctuation">:</span> <span class="token punctuation">{</span><span class="token string">"essential"</span><span class="token punctuation">:</span> <span class="token boolean">true</span><span class="token punctuation">}</span><span class="token punctuation">,</span>
     <span class="token string">"picture"</span><span class="token punctuation">:</span> <span class="token keyword">null</span><span class="token punctuation">,</span>
     <span class="token string">"http://example.info/claims/groups"</span><span class="token punctuation">:</span> <span class="token keyword">null</span>
    <span class="token punctuation">}</span><span class="token punctuation">,</span>
   <span class="token string">"id_token"</span><span class="token punctuation">:</span>
    <span class="token punctuation">{</span>
     <span class="token string">"auth_time"</span><span class="token punctuation">:</span> <span class="token punctuation">{</span><span class="token string">"essential"</span><span class="token punctuation">:</span> <span class="token boolean">true</span><span class="token punctuation">}</span><span class="token punctuation">,</span>
     <span class="token string">"acr"</span><span class="token punctuation">:</span> <span class="token punctuation">{</span><span class="token string">"values"</span><span class="token punctuation">:</span> <span class="token punctuation">[</span><span class="token string">"urn:mace:incommon:iap:silver"</span><span class="token punctuation">]</span> <span class="token punctuation">}</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span>
</code></pre>
<blockquote>
<p>Example of JSON device object requested with tag:sixdots.be,2017-05:claim_device</p>
</blockquote>
<pre class=" language-json"><code class="prism --inline language-json"><span class="token punctuation">{</span>  
	<span class="token string">"os"</span><span class="token punctuation">:</span> <span class="token string">"ANDROID"</span><span class="token punctuation">,</span>  
	<span class="token string">"appName"</span><span class="token punctuation">:</span> <span class="token string">"itsme app"</span><span class="token punctuation">,</span> <span class="token string">"appRelease"</span><span class="token punctuation">:</span> <span class="token string">"1.17.13"</span><span class="token punctuation">,</span>
	<span class="token string">"deviceLabel"</span><span class="token punctuation">:</span> <span class="token string">"myDevice"</span><span class="token punctuation">,</span>
	<span class="token string">"debugEnabled"</span><span class="token punctuation">:</span> <span class="token boolean">false</span><span class="token punctuation">,</span> 
	<span class="token string">"deviceId"</span><span class="token punctuation">:</span> <span class="token string">"deviceId"</span><span class="token punctuation">,</span>
	<span class="token string">"osRelease"</span><span class="token punctuation">:</span> <span class="token string">"Android 4.4.2"</span><span class="token punctuation">,</span> 
	<span class="token string">"manufacturer"</span><span class="token punctuation">:</span> <span class="token string">"samsung"</span><span class="token punctuation">,</span> 
	<span class="token string">"hasSimEnabled"</span><span class="token punctuation">:</span> <span class="token boolean">true</span><span class="token punctuation">,</span>
	<span class="token string">"deviceLockLevel"</span><span class="token punctuation">:</span> <span class="token string">"touchID"</span><span class="token punctuation">,</span> 
	<span class="token string">"smsEnabled"</span><span class="token punctuation">:</span> <span class="token boolean">true</span><span class="token punctuation">,</span>
	<span class="token string">"rooted"</span><span class="token punctuation">:</span> <span class="token boolean">false</span><span class="token punctuation">,</span>
	<span class="token string">"imei"</span><span class="token punctuation">:</span> <span class="token string">"12345678901234567"</span><span class="token punctuation">,</span>
	<span class="token string">"deviceModel"</span><span class="token punctuation">:</span> <span class="token string">"S8"</span><span class="token punctuation">,</span>  
	<span class="token string">"msisdn"</span><span class="token punctuation">:</span> <span class="token string">"0412123123"</span><span class="token punctuation">,</span> 
	<span class="token string">"sdkRelease"</span><span class="token punctuation">:</span> <span class="token string">"1.17.12"</span>  
<span class="token punctuation">}</span>
</code></pre>
<h2 id="unsupported-standard-claims">Unsupported Standard Claims</h2>
<p>The following standard claims (check <a href="http://openid.net/specs/openid-connect-core-1_0.html#StandardClaims">http://openid.net/specs/openid-connect-core-1_0.html#StandardClaims</a>) won’t be provided if requested: TODO UNSUPPORTED???</p>
<ul>
<li><code>middle_name</code></li>
<li><code>nickname</code></li>
<li><code>preferred_username</code></li>
<li><code>picture</code></li>
<li><code>website</code></li>
<li><code>zoneinfo</code></li>
<li><code>updated_at</code></li>
</ul>
<h1 id="advanced-topics">Advanced topics</h1>
<h2 id="a-namejwtrequestapassing-request-parameters-as-jwts"><a></a>Passing Request Parameters as JWTs</h2>
<p>OpenID Connect defines the following Authentication Request parameters to enable Authentication Requests to be signed and optionally encrypted:</p>

<table>
<thead>
<tr>
<th>Parameter</th>
<th>Required</th>
<th>Comment</th>
</tr>
</thead>
<tbody>
<tr>
<td><strong>request</strong></td>
<td>Optional</td>
<td>This parameter enables OpenID Connect requests to be passed in a single, self-contained parameter and to be optionally signed and/or encrypted. The parameter value is a Request Object value, as specified in <a href="http://openid.net/specs/openid-connect-core-1_0.html#RequestObject">Section 6.1</a>. It represents the request as a JWT whose Claims are the request parameters.</td>
</tr>
<tr>
<td><strong>request_uri</strong></td>
<td>Unsupported</td>
<td>Refer to <a href="http://openid.net/specs/openid-connect-core-1_0.html#JWTRequests">JWTRequest</a></td>
</tr>
</tbody>
</table><p>The Request Object is a JWT token as defined in <a href="https://tools.ietf.org/html/rfc7519">RFC 7519</a>, which contains the following properties:</p>

<table>
<thead>
<tr>
<th>Property</th>
<th>Required</th>
<th>Comment</th>
</tr>
</thead>
<tbody>
<tr>
<td><strong>iss</strong></td>
<td>Required</td>
<td>Issuer. Must be the <code>client_id</code></td>
</tr>
<tr>
<td><strong>aud</strong></td>
<td>Required</td>
<td>Audience. Must be <code>https://server.itsme.be</code></td>
</tr>
</tbody>
</table><blockquote>
<p>Example of claim request before base64url encoding, signing and encryption</p>
</blockquote>
<pre class=" language-json"><code class="prism --inline language-json"><span class="token punctuation">{</span>
	<span class="token comment">// JWT Registered claims (https://tools.ietf.org/html/rfc7519#section-4.1)</span>
	iss<span class="token punctuation">:</span> <span class="token string">"s6BhdRkqt3"</span><span class="token punctuation">,</span>
	aud<span class="token punctuation">:</span> <span class="token string">"https://server.itsme.be"</span><span class="token punctuation">,</span>
	<span class="token comment">// OIDC parameters, must reflect the values of the HTTP parameters</span>
	client_id<span class="token punctuation">:</span> <span class="token string">"s6BhdRkqt3"</span><span class="token punctuation">,</span>
	response_type<span class="token punctuation">:</span> <span class="token string">"code"</span><span class="token punctuation">,</span>
	redirect_uri<span class="token punctuation">:</span> <span class="token string">"https://client.example.org/cb"</span><span class="token punctuation">,</span>
	scope<span class="token punctuation">:</span> <span class="token string">"openid email service:client.registration"</span><span class="token punctuation">,</span>
	state<span class="token punctuation">:</span> <span class="token string">"af0ifjsldkj"</span><span class="token punctuation">,</span>
	nonce<span class="token punctuation">:</span> <span class="token string">"n-0S6_WzA2Mj"</span><span class="token punctuation">,</span>
	claims<span class="token punctuation">:</span>
	<span class="token punctuation">{</span>
		userinfo<span class="token punctuation">:</span> <span class="token punctuation">{</span>
		    <span class="token string">"tag:itsmetag:sixdots.be,2016-06:claim_nationality"</span><span class="token punctuation">:</span> <span class="token keyword">null</span><span class="token punctuation">,</span>
		<span class="token punctuation">}</span><span class="token punctuation">,</span>
		id_token<span class="token punctuation">:</span> <span class="token punctuation">{</span>
			auth_time<span class="token punctuation">:</span> <span class="token punctuation">{</span><span class="token string">"essential"</span><span class="token punctuation">:</span> <span class="token boolean">true</span><span class="token punctuation">}</span><span class="token punctuation">,</span>
			acr<span class="token punctuation">:</span> <span class="token punctuation">{</span><span class="token string">"value"</span><span class="token punctuation">:</span> \<span class="token punctuation">[</span><span class="token string">"tag:itsmetag:sixdots.be,2016-06:acr_securedadvanced"</span>\<span class="token punctuation">]</span> 		
		<span class="token punctuation">}</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span>
</code></pre>
<h2 id="requests-signing-and-encryption">Requests Signing and Encryption</h2>
<p>Encryption algorithm used: RSA SHA-256 TODO not aligned with</p>
<p>Supported algorithms and encryption methods for:</p>
<ul>
<li>ID Token<br>
– Encryption Method (enc): A128CBC-HS256<br>
– Encryption Algorithm (alg): RSA-OAEP<br>
– Signing Algorithm (alg): RS256</li>
<li>User Info<br>
– Encryption Method (enc): A128CBC-HS256<br>
– Encryption Algorithm (alg): RSA-OAEP<br>
– Signing Algorithm (alg):  RS256</li>
<li>Request Object”<br>
– Encryption Method (enc): A128CBC-HS256<br>
– Encryption Algorithm (alg): RSA-OAEP<br>
– Signing Algorithm (alg): RS256</li>
</ul>
<p>Offline access is not supported. TODO</p>
<p>Dynamic client registration is not allowed. TODO</p>
<p>itsme® exposes its signing and encryption keys on a public endpoint (JWKSet)</p>
<pre><code>https://merchant.itsme.be/oidc/jwkSet
</code></pre>
<p>It is expected that the RP will also expose their signing and encryption keys in such a way. The location of the RP JWKSet must be configured by an itsme administrator during onboarding of RP. The exposed endpoint must be HTTPS</p>

