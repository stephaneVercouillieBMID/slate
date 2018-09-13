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

```http--inline
POST /oidc/authorization
response_type=code
client_id=MY_PARTNER_CODE
scope=openid service:MY_APPROVAL_SERVICE_CODE
redirect_uri=https:\/\/service-provider.be\/my_call_back_url nonce=A_VALID_NONCE state=A_VALID_STATE request={
"response_type":"code",
"client_id":"MY_PARTNER_CODE",
"redirect_uri":" https:\/\/service-provider.be\/my_call_back_url",
"aud":"https:\/\/merchant.itsme.be\/oidc",
"scope":"openid service: MY_APPROVAL_SERVICE_CODE",
"acr_values":"tag:sixdots.be,2016-06:acr_advanced",
"iss":"MY_PARTNER_CODE",
"nonce":"A_VALID_NONCE",
"state":"A_VALID_STATE",
"claims":{
"userinfo":{
"sub":{
"value":"THE_END_USER_ALREADY_KNOWN_USER_CODE"
},
"tag:sixdots.be,2016-08:claim_approval_template_name":{
"value":"adv_payment",
"essential":true
},
"tag:sixdots.be,2016-08:claim_approval_amount_key":{
"value":"100",
"essential":true
},
"tag:sixdots.be,2016-08:claim_approval_currency_key":{
"value":"EUR",
"essential":true
},
"tag:sixdots.be,2016-08:claim_approval_iban_key":{
"value":"BE00793774892029",
"essential":true
}
}
}
}
```

<ul>
  <li>The Authentication Request MUST be communicated using HTTPS POST protocol and the <code>application/x-www-form-urlencoded</code> media type. HTTPS GET calls will be refused by the Authorization Server because the Authorization Request MIGHT contain sensitive data.   </li>
  <li>You CAN also specify the type of info you want to have visualized in the itsme® app. This will be done by using the key <code>tag:sixdots.be,2016-08:claim_approval_template_name</code> in the </code>claim</code> parameter when forming the Authentication Request. More information on the available templates can be found in the Appendixes.</li>    
  <li>Using the <code>request</code> or <code>request_uri</code> parameter is mandatory when forming the Authentication Request. This parameter MUST be signed with your private key and/or encrypted with the itsme® public key.</li>
  <li>The <code>prompt</code> parameter can be <code>login</code> and/or <login>consent</code>.</li>
  <li>The Token Endpoint MUST ALWAYS be called to validate that the Authorization Code is valid and corresponds effectively to the Confirm transaction initiated by your application.</li>
  <li>The OpenID Connect Core Specification specifies the validation you MUST perform on the ID Token. This includes but is not limited to the the following:
    <ul>
      <li>The issuer identifier of itsme® must exactly match the value of the <code>iss</code> claim</li>
      <li>The <code>aud</code> claim must contain the <code>client_id</code> value provided during the registration of your application in the itsme® B2B portal.</li>
      <li>The transaction is effectively signed by itsme®.</li>
      <li>The current time MUST be before the time represented by the <code>exp</code> claim.</li>
      <li>It is correctly unencrypted using your private key.</li>
      <li>The <code>nonce</code> value is similar to the one provided in the Authentication Request (if specified).</li>
      <li>If the User is already logged in your application, the <code>sub</code> value MUST match the one provided during the log in of the User.</li>
    </ul>
   </li>
 </ul>
  
For example, the Confirm Authentication Request using the `sub` identifier might look like the sample aside. The value `THE_END_USER_ALREADY_KNOWN_USER_CODE` MUST be replaced with a valid `sub` (e.g.: an identifier for the User, unique among all itsme® accounts and never reused).

<aside class="notice">In the example aside, the <code>request</code> parameter is represented as a regular JSON formatted object for clarity only. Indeed, it MUST be correctly encoded, signed and then encrypted as explained in the official OpenID Connect Core specification.</aside>


# 3. Appendixes

## 3.1 Templates

There are currently two templates that can be used when forming a Confirm Authentication Request. Depending on the template used, the Authentication Request MUST include the following parameters in the body:

Template | Parameters |  Description
:-------- | :-------- | :--------
**adv_payment** | Amount | A string holding an integer value inside. This MUST be set to <code>tag:sixdots.be,2016-08:claim_approval_amount_key</code>.
<label></label> | Currency | A string holding a valid currency code (e.g. “EUR”). This MUST be set to <code>tag:sixdots.be,2016-08:claim_approval_currency_key</code>.
<label></label> | IBAN | A string holding a valid IBAN account number. This MUST be set to <code>tag:sixdots.be,2016-08:claim_approval_iban_key</code>.
 **free_text** | Text | A string holding any text to be displayed in the itsme® app. This MUST be set to <code>tag:sixdots.be,2016-08:claim_approval_text_key</code>.








