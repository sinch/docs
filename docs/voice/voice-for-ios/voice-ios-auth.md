---
title: Authentication & Authorization
excerpt: Secure your application and authorize Sinch client (user) registrations.
next:
  pages:
    - voice-ios-miscellaneous
---

A user identity must be provided when initiating a Sinch client. The first time the application instance and the Sinch client are running on behalf of a particular user, they are required to register against the Sinch service. This is mostly handled transparently by the Sinch SDK, but it works slightly differently depending on which authentication scheme you choose to use.

The step of registering a user identity against the Sinch service requires the application instance to be authenticated and authorized to perform the user registration. Once the application instance has successfully registered the user identity, it will also have obtained the necessary credentials to perform further authorized requests for that specific user, for example, calling.

Two different authentication schemes are available: authentication by client access to application secret and authentication supported by application server.

## Authentication by Client Access to Application Secret

This application authentication scheme is based on giving the application direct access to the _Application Secret_, which enables the Sinch Client SDK in the application to self-sign an authorized request to perform user registration. Choosing this authentication scheme corresponds to initiating the Sinch client by using the factory method that takes both an _Application Key_ and an _Application Secret_.

Using this authentication scheme is the quickest way to get started as the client application instances can directly perform authorized requests against the Sinch service, but it **should not** be used in production deployments.

> ❗️
> It is strongly discouraged to have the application secret in plain text in the source code in the production version of the application.

## Authentication Supported by Application Server

This application authentication scheme is based on the client application instance not having direct access to the _Application Secret_. Instead, when the Sinch client needs to perform an authorized request to register a user identity against the Sinch service, it needs to be provided with an authentication signature and a registration sequence to perform the registration. This should be provided by the application’s backend service, for example, by using a HTTP request over a secure TLS connection.

This scheme has the benefit of the application secret never being directly accessible by the client applications and provides a better level of security as well as flexibility.

> 📘
> The need for the Sinch client to request an authentication signature and registration sequence is only required once per user and device–not on every application launch.


![authentication_via_application_server.png](images\0711e55-authentication_via_application_server.png)

### Generating the Signature

The *Application Server* is responsible for generating a valid signature for each registration request that it accepts as a valid user registration. The *sequence* is a [cryptographic nonce](http://en.wikipedia.org/wiki/Cryptographic_nonce), and must be a monotonically increasing value. The signature is then generated as as follows (pseudogrammar):
```objectivec
string userId;
string applicationKey; // E.g. "196087a1-e815-4bc4-8984-60d8d8a43f1d"
string applicationSecret; // E.g. "oYdgGRXoxEuJhGDY2KQ/HQ=="
uint64 sequence = previous_sequence + 1; // E.g. previous_sequence = 0

string stringToSign = userId + applicationKey + sequence + applicationSecret;

// Use a Base64-encoder that don't introduce line-breaks,
// or trim the output signature afterwards.
string signature = Base64.encode(SHA1.digest(stringToSign));
```


For example, in Java:
```java
// Generating the Signature - Java
// import java.security.MessageDigest;
// import org.apache.commons.codec.binary.Base64;

String userId;
String applicationKey; // E.g. "196087a1-e815-4bc4-8984-60d8d8a43f1d";
String applicationSecret; // E.g. "oYdgGRXoxEuJhGDY2KQ/HQ==";
long sequence; // fetch and increment last used sequence

String toSign = userId + applicationKey + sequence + applicationSecret;

MessageDigest messageDigest = MessageDigest.getInstance("SHA-1");
byte[] hash = messageDigest.digest(toSign.getBytes("UTF-8"));

String signature = Base64.encodeBase64String(hash).trim();
```


### Starting the Client and Providing Authorization Credentials for User Registration

```objectivec
// Instantiate a client object using the client factory method.
id<SINClient> client = [Sinch clientWithApplicationKey:@"<application key>"
                                       environmentHost:@"clientapi.sinch.com"
                                                userId:@"<user id>"];

client.delegate = ...;

[client start];

// This will on the first run for this user, call
// -[SINClientDelegate client:requiresRegistrationCredentials:],
// which implementations could look something like this:

- (void)client:(id<SINClient>)client
requiresRegistrationCredentials:(id<SINClientRegistration>) registrationCallback {

  // Perform API request to server which keeps the Application Secret
  [myAPIService getAuthorizedSignatureForUser:[client userId]
  onSuccess:^(NSString* signature, long long sequence){

    // Forward the signature and sequence back into Sinch SDK
    [registrationCallback registerWithSignature:signature sequence:sequence];
  }
  onFailure:^(NSError* error) {

    // Forward potential network request error to Sinch SDK,
    // e.g. failure due to no internet connection.
    [registrationCallback registerDidFail:error];
  }];
}
```
