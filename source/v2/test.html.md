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
# Prerequisite

Before you start integrating itsme®, you MUST create an organisation on the following url: <a href="https://docs.google.com/forms/d/e/1FAIpQLSdyfhKiiehNg4DhFzhQeHaj9EG2VeFoyPNVaI-TSwnG5WlFfw/viewform" target="blank">https://docs.google.com/forms/d/e/1FAIpQLSdyfhKiiehNg4DhFzhQeHaj9EG2VeFoyPNVaI-TSwnG5WlFfw/viewform</a>.

Once there, you will need to fill out a basic form with the following questions:

<ul>
  <li>Contact details such as your email, name, phone number.</li>
  <li>Organisation details as shown on the company register for your jurisdiction.</li>
  <li>Information about the project you want to set-up (the services you want to use, the client authentication method you want to set-up, the user attributes you want to request, ...)</li>
  <li>itsme® terms and conditions. If you require a copy of this please contact onboarding@itsme.be.</li>
</ul>

Our onboarding team will review your project and get in touch within 3 days with your credentials:
<ul>
  <li>a <i>"client_id"</i></li>
  <li>a <i>"service_code"</i></li>
  <li>information about your <a href="https://belgianmobileid.github.io/slate/v2/test2#selecting-your-client-authentication-method" target="blank">client authentication method</a></li>
  <li>the list of user attributes you can request</li>
</ul>

# Integration sequence

itsme® integration is based on the <a href="http://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth" target="blank">Authorization Code Flow</a> of OpenID Connect 1.0. The Authorization Code Flow goes through the steps as defined in <a href="http://openid.net/specs/openid-connect-core-1_0.html#CodeFlowSteps" target="blank">OpenID Connect Core Authorization Code Flow Steps</a>, depicted in the following diagram:
  
 ![Sequence diagram describing the OpenID flow](OpenID_Login_SeqDiag.png)

The integration steps, with linked code-level documentation are:

<ol>
  <li>The User indicates on your end he wishes to authenticate with itsme®. <a href="https://brand.belgianmobileid.be/d/CX5YsAKEmVI7/documentation#/ux/buttons-1518207548" target="blank">You will need to create a button to allow your users to authenticate with itsme®</a></li>.
  <li><a href="https://belgianmobileid.github.io/slate/v2/test2#building-your-authorization-request" target="blank">Your web desktop, mobile web or mobile application sends a request to itsme® to authenticate the User</a> (aka. the Authorization Request). This request will redirect the User to the itsme® Front-End. itsme® then authenticates the User by asking him
    <ul type>
      <li>to enter his phone number on the itsme® OpenID web page</li>
      <li>authorize the release of some information’s to your application</li>
      <li>to provide his credentials (itsme® code or fingerprint or FaceID)</li>
    </ul>
  <br>It is also in this Authorization Request that you will be able to <a href="https://belgianmobileid.github.io/slate/v2/test2#requesting-claims-about-the-user-and-the-authentication-event" target="blank">request claims about the User and the Authentication event</a></br>
  <br>If you are building a mobile web or mobile application, the User don’t need to enter his mobile phone number on the itsme® OpenID web page, he will be automatically redirected to the itsme app via the Universal links or App links mechanism.</br></li>
  <li>Once the User has authorized the request and has been authenticated, itsme® will return an Authorization Code to the Service Provider Front-End, redirecting the user to your mobile or web application.</li>
  <li>The Service Provider Back-End calls the itsme® Token Endpoint and exchanges the Authorization Code for an ID Token identifying the User and an Access Token.</li>
  <li>The Service Provider Back-End MAY request the additional User information from the itsme® userInfo Endpoint by presenting the Access Token obtained in the previous step.</li>
  <li>At this stage you are able to confirm the success of the operation and display a success message.</li>
</ol>

If a user doesn't have the itsme® app, they'll be redirected to a mobile website with more information and download links.

