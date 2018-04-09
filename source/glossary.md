


Written with [StackEdit](https://stackedit.io/).
># S
 ### <a name="glossaryServiceCodes"></a> Service Code Concept 
 
To be able to use an itsme service (such as login, confirm, sign, share data) you should be provided a service instance for it. The service code is the identifier of this instance. The same Service Provider may utilise several service instances. 

For example, assuming that one SP would like to use login as an itsme(r) service for business and private channels. In this case, SP could ask BMID to allocate two service instances, one issued for private account login, one for business account login. Consent screen needs to be customised for each instance.

### Sandbox
A sandbox is an isolated computing environment in which a program or file can be executed without affecting the application in which it runs.


># J
### **JSON Web Token (JWT)**

JSON Web Token (JWT) is an open standard ([RFC 7519](https://tools.ietf.org/html/rfc7519)) that defines a compact and self-contained way for securely transmitting information between parties as a JSON object. This information can be verified and trusted because it is digitally signed. JWTs can be signed using a secret (with the **HMAC** algorithm) or a public/private key pair using **RSA**.

**Smaller size, fast transmission, compact**: JWTs can be sent through a URL, POST parameter, or inside an HTTP header.

**Self-contained**: The payload contains all the required information about the user, avoiding the need to query the database more than once.

### **JWKSet**
The JWKset is a set of public keys exposed by the expeditor of a JWT token. The JWK set is used by the receiver of a JWT token to decrypt and verify the signature of this JWT token.


<!--stackedit_data:
eyJoaXN0b3J5IjpbLTQyNjg1ODc0OV19
-->