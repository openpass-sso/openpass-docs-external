[OpenPass Developer Documentation](../README.md) > [Getting Started with OpenPass](../getting-started.md) > [Reference Information](info-summary.md) > OpenPass Security Considerations

# OpenPass Security Considerations

This page includes the following information about security measures that OpenPass applies:

* [OpenPass Use of PKCE](#openpass-use-of-pkce)
* [Sending Client Credentials](#sending-client-credentials)

## OpenPass Use of PKCE

OpenPass uses an additional security measure, [Proof Key for Code Exchange (PKCE)](/glossary.md#pkce), to prevent malicious interception of information in the authentication process.

With PKCE, the authorization request message includes an additional value, and the token request message includes another value that has a relationship to the first value. By comparing these two values, the authorization server can verify that the token request message is genuine. A malicious actor intercepting the response to the first message cannot provide the second value, and therefore cannot impersonate the publisher and falsely receive the ID token.

Per the PKCE standard, the value can be sent in plain text or can also be hashed and encoded.

If you're using an OpenPass SDK, the SDK applies the most secure option of hashing and encoding.

## Sending Client Credentials

There are two options for sending the client credentials that identify the publisher to OpenPass. This section provides the following information about those options:

* [Options for Sending Client Credentials: Overview](#options-for-sending-client-credentials-overview)
* [Basic Authentication Header](#basic-authentication-header)
* [Constructing the Basic Authentication Header](#constructing-the-basic-authentication-header)
* [Basic Authentication Header: Example](#basic-authentication-header-example)
* [Client ID](#client-id)
* [Summary](#summary)

### Options for Sending Client Credentials: Overview

To complete the process of signing in a consumer with OpenPass, the publisher first requests an authorization code from the authorization server (API endpoint [GET /v1/api/authorize](../api/v1/api-reference.md#get-v1apiauthorize)) and then sends the authorization code to the token endpoint to get the OpenID Connect ID token and access token (API endpoint [POST /v1/api/token](../api/v1/api-reference.md#post-v1apitoken)).

At each step of this process, it's important that communications from both parties, OpenPass and the publisher, can be trusted. One important way that the publisher establishes trust is by sending the client credentials to OpenPass.

The credentials, established as part of [account setup](../getting-started.md#setting-up-your-account), are:
* Client ID
* Client Secret

There are two options for sending client credentials:
* [Basic Authentication Header](#basic-authentication-header): In this header, send Client ID and also Client Secret. This is the preferred option if it can be sent securely. Because the Basic Authentication header includes the client secret value, it must be sent securely or not at all.
* [Client ID](#client-id): In the `client_id` parameter, send the Client ID only. Do not send the Client Secret. Use this option if you cannot send the information securely.

### Basic Authentication Header

Per the [OpenID Connect](/glossary.md#openid-connect) specification, the request to the token endpoint should, if possible, include the HTTP Basic Authentication header, which allows the OpenPass server to authenticate the identity of the client. 

The Basic Authentication header gives the OpenPass authorization server the information needed to complete these key security steps:

* Identify the sender (the publisher).
* Verify that the message is authentic and valid.

The purpose of the message exchange is to authenticate the consumer (end-user), but at each step trust must also be in place for both OpenPass and the publisher. For this reason, as long as the header can be sent securely, the client credentials are sent as the Base 64-encoded value of the Basic Authentication header. For details, see <a href="https://openid.net/specs/openid-connect-basic-1_0.html#TokenRequest">Client Sends Code</a> in the OpenID Connect specification. For an example, see <a href="https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Authorization#basic_authentication">Authorization</a> in the Examples section (Mozilla documentation).

### Constructing the Basic Authentication Header

The value of the Basic Authentication header is constructed from the publisher's credentials, as follows:

1. Create a string of the Client ID, then a colon, then the client secret value:
      ```
      {CLIENT_ID}:{CLIENT_SECRET}
      ```
2. Base64-encode the string.  

### Basic Authentication Header: Example

For illustration purposes, let's say the client credentials are as follows:
- Client ID: 123456789
- Client secret value: TheTradeDeskPassword

If you go to [www.base64encode.org](https://www.base64encode.org), you can see that the Base64-encoded representation of these two values, with a colon separator, is the following:

Before encoding:

```
123456789:TheTradeDeskPassword
```

After Base64 encoding:

```
MTIzNDU2Nzg5OlRoZVRyYWRlRGVza1Bhc3N3b3Jk
```

In this example, the following is the format for the Basic Authentication header that is sent with the request to the token endpoint:

```
Authorization: Basic MTIzNDU2Nzg5OlRoZVRyYWRlRGVza1Bhc3N3b3Jk
```

To verify the value of the Basic Authentication header, you can decode it at [www.base64decode.org](https://www.base64decode.org). You can see that the decoded value is made up of the Client ID value, a colon separator, and the client secret value:

```
123456789:TheTradeDeskPassword
```

### Client ID

 For client-side implementations where it is not possible to send the client secret value securely&#8212;for example, from a mobile app that could be decompiled&#8212;there is a different approach for identifying the client. Rather than sending the Basic Authentication header, send the  <code>client_id</code> query parameter to the [POST /v1/api/token](../api/v1/api-reference.md#post-v1apitoken) endpoint. The value of this parameter is the Client ID assigned to the publisher's OpenPass account.
 
 If the Basic Authentication header is not present, the `client_id` query parameter is required. These two options, Client ID in a query parameter or Client ID + client secret value sent in the Basic Authentication header, are mutually exclusive; send one or the other, but not both.

The scenario where the client sends only the `client_id` value, rather than Client ID and client secret, is of course less secure than providing both values. In this scenario, the security mechanisms already in place are particularly important:
* The additional security provided by the [PKCE](#openpass-use-of-pkce) header.
* The client's redirect URI, which must exactly match a redirect URI pre-configured in the client's account.

### Summary

If it is secure to do so, include a Basic Authentication header in the message to the token endpoint. The value of this header is the Client ID and client secret, colon-separated and then Base64-encoded.

If it is not secure to send the Basic Authentication header, such as from an app, send the Client ID in the <code>client_id</code> query parameter and do not send the Basic Authentication header.








