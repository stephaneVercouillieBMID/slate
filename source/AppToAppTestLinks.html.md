---
title: App to App test links

toc_footers:
 - <a href='#'>Sign Up for a Developer Key</a> -->
 - <a href='https://github.com/lord/slate'>Documentation Powered by Slate</a>
---
# Requested data

Please note all the requests below ask for the following claims (all from 'scope' values):
      <ul>
        <li>profile</li>
        <li>eid</li>
        <li>email</li>
        <li>address</li>
      </ul>

Please note also the Confirm links are only valid for a limited amount of time. After a while, the request object on which they are based are wiped in the hosting server. To re-create new ones, please consult <a href="https://confluence.belgianmobileid.be/display/ITSME/How+to+create+A2A+Confirm+links" target="blank">How to create A2A confirm links</a> (hosted on BMID Confluence).

# Links

## UAT

### Login
<a href="https://uatmerchant.itsme.be/oidc/authorization?response_type=code&client_id=OIDC_TEST1&redirect_uri=https%3A%2F%2Fcore-emulators-ssl.default-clu01.mgmt.belgianmobileid.be%2Fopenidclient%2Fuat_OIDC_TEST1%2Fauthz_cb&scope=openid+service%3AOIDC_TEST1_LOGIN+profile+eid+phone+email+address&state=anystate&nonce=anonce&prompt=login&max_age=1" target="blank">UAT - OIDC V1 login</a>
      
<a href="https://idp.uat.itsme.services/v2/authorization?response_type=code&client_id=OIDC_TEST1&redirect_uri=https%3A%2F%2Fcore-emulators-ssl.default-clu01.mgmt.belgianmobileid.be%2Fopenidclient%2Fuat_OIDC_TEST1_I18N%2Fauthz_cb&scope=openid+service%3AOIDC_TEST1_LOGIN+profile+phone+email+address+eid&state=anystate&nonce=anonce&prompt=login&max_age=1" target="blank">UAT - OIDC V2 login</a>

### Confirm (only works temporarily)
<a href="https://uatmerchant.itsme.be/oidc/authorization?response_type=code&client_id=OIDC_TEST1&redirect_uri=https%3A%2F%2Fcore-emulators-ssl.default-clu01.mgmt.belgianmobileid.be%2Fopenidclient%2Fuat_OIDC_TEST1%2Fauthz_cb&scope=openid+service%3AOIDC_TEST1_APPROVAL+profile+phone+email+address+eid&state=anystate&nonce=anonce&prompt=login+consent&max_age=1&claims=%7B%22userinfo%22%3A%7B%22name%22%3A%7B%22essential%22%3Atrue%7D%7D%7D&request_uri=https%3A%2F%2Fcore-emulators-ssl.default-clu01.mgmt.belgianmobileid.be%3A443%2Fopenidclient%2Fjwt%2Fkxm03snn050oxvti418y7j" target="blank">UAT - OIDC V1 confirm</a>

<a href="https://idp.uat.itsme.services/v2/authorization?response_type=code&client_id=OIDC_TEST1&redirect_uri=https%3A%2F%2Fcore-emulators-ssl.default-clu01.mgmt.belgianmobileid.be%2Fopenidclient%2Fuat_OIDC_TEST1_I18N%2Fauthz_cb&scope=openid+service%3AOIDC_TEST1_APPROVAL_I18N+profile+phone+email+address+eid&state=anystate&nonce=anonce&prompt=login+consent&max_age=1&claims=%7B%22userinfo%22%3A%7B%22name%22%3A%7B%22essential%22%3Atrue%7D%7D%7D&request_uri=https%3A%2F%2Fcore-emulators-ssl.default-clu01.mgmt.belgianmobileid.be%3A443%2Fopenidclient%2Fjwt%2Fuiye8b45w08464r6ar31tj" target="blank">UAT - OIDC V2 confirm</a>


    
## E2E

### Login

<a href="https://e2emerchant.itsme.be/oidc/authorization?response_type=code&client_id=OIDC_TEST1&redirect_uri=https%3A%2F%2Fstaging1.labo.sixdots.be%2Fopenidclient%2Fe2e_OIDC_TEST1%2Fauthz_cb&scope=openid+service%3AOIDC_TEST1_LOGIN+profile+phone+email+address+eid&state=anystate&nonce=anonce&prompt=login+consent&max_age=1" target="blank">E2E - OIDC V1 login</a>

<a href="https://idp.e2e.itsme.services/v2/authorization?response_type=code&client_id=OIDC_TEST1&redirect_uri=https%3A%2F%2Fstaging1.labo.sixdots.be%2Fopenidclient%2Fe2e_OIDC_TEST1_I18N%2Fauthz_cb&scope=openid+service%3AOIDC_TEST1_LOGIN+profile+phone+email+address+eid&state=anystate&nonce=anonce&prompt=login+consent&max_age=1" target="blank">E2E - OIDC V2 login</a>
      
### Confirm (only works temporarily)

<a href="https://e2emerchant.itsme.be/oidc/authorization?response_type=code&client_id=OIDC_TEST1&redirect_uri=https%3A%2F%2Fstaging1.labo.sixdots.be%2Fopenidclient%2Fe2e_OIDC_TEST1%2Fauthz_cb&scope=openid+service%3AOIDC_TEST1_APPROVAL+profile+phone+email+address+eid&state=anystate&nonce=anonce&prompt=login+consent&max_age=1&claims=%7B%22userinfo%22%3A%7B%22name%22%3A%7B%22essential%22%3Atrue%7D%7D%7D&request_uri=https%3A%2F%2Fstaging1.labo.sixdots.be%3A443%2Fopenidclient%2Fjwt%2F08on16xk47x1zdv7rimhv2" target="blank">E2E - OIDC V1 confirm</a>

<a href="https://idp.e2e.itsme.services/v2/authorization?response_type=code&client_id=OIDC_TEST1&redirect_uri=https%3A%2F%2Fstaging1.labo.sixdots.be%2Fopenidclient%2Fe2e_OIDC_TEST1_I18N%2Fauthz_cb&scope=openid+service%3AOIDC_TEST1_APPROVAL_I18N+profile+phone+email+address+eid&state=anystate&nonce=anonce&prompt=login+consent&max_age=1&claims=%7B%22userinfo%22%3A%7B%22name%22%3A%7B%22essential%22%3Atrue%7D%7D%7D&request_uri=https%3A%2F%2Fstaging1.labo.sixdots.be%3A443%2Fopenidclient%2Fjwt%2Ff6lblz9zszd3ckfe3v9ijb" target="blank">E2E - OIDC V2 confirm</a>

## PRD

### Login

<a href="https://merchant.itsme.be/oidc/authorization?response_type=code&client_id=OIDC_TEST1&redirect_uri=https%3A%2F%2Fstaging1.labo.sixdots.be%2Fopenidclient%2Fprod_OIDC_TEST1%2Fauthz_cb&scope=openid+service%3AOIDC_TEST1_LOGIN+profile+eid+phone+email+address&state=anystate&nonce=anonce&prompt=login&max_age=1" target="blank">PRD - OIDC V1 login</a>
      
<a href="https://idp.prd.itsme.services/v2/authorization?response_type=code&client_id=OIDC_TEST1&redirect_uri=https%3A%2F%2Fstaging1.labo.sixdots.be%2Fopenidclient%2Fprod_OIDC_TEST1_I18N%2Fauthz_cb&scope=openid+service%3AOIDC_TEST1_LOGIN_I18N+profile+eid+phone+email+address&state=anystate&nonce=anonce&prompt=login&max_age=1" target="blank">PRD - OIDC V2 login</a>

### Confirm (to come)

<a href="https://merchant.itsme.be/oidc/authorization?response_type=code&client_id=OIDC_TEST1&redirect_uri=https%3A%2F%2Fstaging1.labo.sixdots.be%2Fopenidclient%2Fprod_OIDC_TEST1%2Fauthz_cb&scope=openid+service%3AOIDC_TEST1_APPROVAL+profile+phone+email+address+eid&state=anystate&nonce=anonce&prompt=login+consent&max_age=1&claims=%7B%22userinfo%22%3A%7B%22name%22%3A%7B%22essential%22%3Atrue%7D%7D%7D&request_uri=https%3A%2F%2Fstaging1.labo.sixdots.be%3A443%2Fopenidclient%2Fjwt%2Fomk524xoe4dqdpmqg536xt" target="blank">PRD - OIDC V1 Confirm</a>

<a href="https://idp.prd.itsme.services/v2/authorization?response_type=code&client_id=OIDC_TEST1&redirect_uri=https%3A%2F%2Fstaging1.labo.sixdots.be%2Fopenidclient%2Fprod_OIDC_TEST1_I18N%2Fauthz_cb&scope=openid+service%3AOIDC_TEST1_APPROVAL_I18N+profile+phone+email+address+eid&state=anystate&nonce=anonce&prompt=login+consent&max_age=1&claims=%7B%22userinfo%22%3A%7B%22name%22%3A%7B%22essential%22%3Atrue%7D%7D%7D&request_uri=https%3A%2F%2Fstaging1.labo.sixdots.be%3A443%2Fopenidclient%2Fjwt%2Fwcml708uvasab2x4hzhdyp" target="blank">PRD - OIDC V2 Confirm</a>
