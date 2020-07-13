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

Some itsme® endpoints require a client authentication in order to protect entitlement information between interested parties. 

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



# Building your Authorization Request

### Composing your base URL





###  Requesting claims about the User and the Authentication event 




