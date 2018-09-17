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
itsme® is a trusted identity provider allowing partners to use verified identities for authentication and authorization on web desktop, mobile web and mobile applications. 

The objective of this document is to provide all the information needed to integrate the **Sign** service. 

At this moment only the Hash Signing variant is available and documented. In this variant, a remote Signature Creation Application (SCA) will provide the WYSIWYS experience to the User, provide the hash of the data to be signed to the itsme® service and use the returned digital signature value to format the signature in one of the AdES formats.

<a name="Onboarding"></a>
# 2. Prerequisites
 
Before you can integrate your application with itsme® service, you MUST set up a project in the <a href="https://brand.belgianmobileid.be/d/CX5YsAKEmVI7" target="blank">itsme® B2B portal</a> to obtain all the needed information.


# 3. Integrating Sign services
The itsme® Sign flow goes through the steps shown in the sequence diagram below. 

 ![Sequence diagram describing the QES Hash Signing flow](QES_Hash_Signing_SeqDiag.png)

<ol>
  <li>The User indicates on your end he wishes to sign a document with itsme®</li>
  <li>Your web desktop, mobile web or mobile SCA application sends a request to itsme® Integration Layer Back-End in order to create the User’s Identification session and obtain the User’s signing certificate to be include in the data to be signed.</li>
  <li>itsme® returns the session id and the redirect URL specific to the User to your SCA Back-End.</li>
  <li>Your SCA Front-End redirect the User to the Integration Layer Front-End of itsme®, meaning that the Iser will be authenticated in the meanwhile. If the User has no signature certificate yet, the certificate creation process will be initiated automatically.</li>
  <li>Finally, when the User is authenticated and has a signature certificate, he is redirected to the your SCA Front-End. The redirection to your SCA Front-End should be (almost) transparent to the User (aka possible displaying of a spinner) as the laps of time between step 5 and step 11 of this diagram should be extremely short.</li>
  <li>Your SCA Back-End contacts the itsme® Integration Layer Back-End to get the signature certificate of the User.</li>
</ol>

<aside class="alert">Currently we only allow the signature of one single hash. Multiple hash signing is not yet available.</aside>

<ol>
  <li value="7">The itsme® Integration Layer Back-End returns your SCA Back-End the signer information as well as the signature certificate of the User.</li>
  <li>Your SCA Back-End constructs the data to be signed and the hash of the signature will be computed by yourself. The value of this hash MUST be base64url encoded.</li> 
  <li>Your SCA Back-End will provide the hash to the itsme® Integration Layer Back-End to request the digital signature value.</li>
  <li>A session id and redirect URL are returned by itsme® to your SCA Back-End.</li>
  <li>Your SCA frontend will then redirect the User to the signature webpage of itsme®, where some explanations about how to process the signature are displayed. Before being able to confirm the signature with the itsme® App, the User will also need to perform a security check, known as poka yoke, is also shown to the User. This consists of three icons that are displayed in the signature webpage, of which one is highlighted in tangerine. The User must make sure that the sequence of three icons displayed his App is the same as the one displayed in the signature webpage. To continue the confirmation of the action, the User must select in his App the icon that is highlighted in tangerine on the signature webpage.</li>    
  <li>The session of the User at itsme® side ends as the process is finished and the User is redirected to your SCA Front-End.</li>
  <li>Your SCA Back-End will contact the itsme® Integration Layer Back-End to check the signature status.</li>
  <li>itsme® then returns the signature completion status and the digital signature value.</li>
  <li>At this stage, your SCA is able to confirm the success of the operation and display a success message.</li>
</ol>

## 3.1. Checking itsme® Sign configuration

To simplify implementations and increase flexibility, all needed key-value pairs about itsme® configuration can be retrieved from a JSON document, such as 

<ul>
  <li>the policies</li>
  <li>commitment types</li>
  <li>supported languages</li>
  <li>...</li>
</ul>

The JSON document for itsme® Sign service may be retrieved from: 

Environment | URL
:-------- | :--------
**SANDBOX** | 
**PRODUCTION** | 
 

   
