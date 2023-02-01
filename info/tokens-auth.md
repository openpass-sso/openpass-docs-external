[OpenPass Developer Documentation](../README.md) > [Getting Started with OpenPass](../getting-started.md) > [Reference Information](info-summary.md) > Authentication Tokens

# Authentication Tokens

This page includes the following information about OpenPass authentication tokens:

* [ID Token Details](#id-token-details)
   * [ID Token Header and Payload Sections](#id-token-header-and-payload-sections)
   * [JWT Header](#jwt-header)
   * [JWT Payload](#jwt-payload)
   * [JWT Signature](#jwt-signature)
   * [ID Token Signature Section](#id-token-signature-section)

## ID Token Details

The ID token is part of the response to the [POST /v1/api/token](/api/v1/api-reference.md#post-v1apitoken) API call. It is a [JSON Web Token (JWT)](/glossary.md#json-web-token) that includes all the [claims](/glossary.md#claim) associated with the user, including the user's email address.  You can decode the ID token to get the information stored in it.

The following example is a snippet from a valid response to a call to the [POST /v1/api/token](/api/v1/api-reference.md#post-v1apitoken) endpoint, with the ID token. The ellipsis indicates that additional properties in the response example are not shown.

```json
{
  "id_token":"eyJraWQiOiJUc1F0cG5ZZmNmWm41ZVBLRWFnaDNjU1lGcWxnTG91eEVPbU5YTVFSUWVVIiwidHlwIjoiSldUIiwiYWxnIjoiUlMyNTYifQ.eyJzdWIiOiI5ZTJkNzRlMi0wODQxLTQ4YzAtYmIzYy0xOTNjMzg2MjQ3ZTAiLCJhdWQiOiIxMjM0NTY3ODkwIiwiaXNzIjoiaHR0cHM6Ly9teW9wZW5wYXNzLmNvbSIsImV4cCI6MTY3MzAzMDM3MywiaWF0IjoxNjcwNDM4MzczLCJlbWFpbCI6InRlc3RAZXhhbXBsZS5jb20ifQ.d_OkLveN8KhRcz3DioQsFTPnTg0rb1FUBCr-pMOW037vScCXcwk2e6WwxA1DFkppxPLvW1It-ZmDEeQ-P7YVcrfAQqzsOhf8gXJTQNAfQOnNmRjVn8V98S-OsWvHIRDierjjnJwypNqU80fllvX95UMDaQkK_IpYxx2b6fBxj48rLEl9MCdlMO1Igg088gT62SfeV_gqSbEnbuL5GbOQ-zKVKN_F0V06Y0ndUeCiWNATkmOFomMmzlCL_99VrGIPwzGJ1QGg3mRmkZizvH38lvHJ8fwVPOQqodLvP50ixBwjqMthP0mqMy97g2rKo0KGBIaH4ngnGOtSGNUhJARLBg",
...
}
```

If you're using an SDK, the SDK manages the token information for you. If you're not using an SDK, you can write code to decode the token and use the values, as well as to verify the signature.

For example, you could decode the token at a site such as [https://jwt.io/](https://jwt.io/).

### ID Token Header and Payload Sections

At [https://jwt.io/](https://jwt.io/), the token in this sample response gives the following results:

![Sample ID token, decoded at https://jwt.io/](images/jwt_io_01_decoded.jpg)

The three dot-separated sections of the JWT token are as follows:

1. [Header](#jwt-header)
2. [Payload](#jwt-payload)
3. [Signature](#jwt-signature)

### JWT Header

The JWT header includes metadata about the token, in the form of the header parameters shown in the following table.

For details, see [JOSE Header](https://datatracker.ietf.org/doc/html/rfc7519#section-5) in the JWT specification and [Registered Header Parameter Names](https://datatracker.ietf.org/doc/html/rfc7515#section-4.1) in the JSON Web Signature specification.

| Header Parameter | Meaning | Sample Value |
| :--- | :--- | :--- |
| `kid`  | A unique ID indicating the key that was used for the signature. For details, see ["kid" (Key ID) Header Parameter](https://datatracker.ietf.org/doc/html/rfc7515#section-4.1.4) in the JSON Web Signature specification.<br>You can use the `kid` value to find the key at the [GET /.well-known/jwks](/api/v1/api-reference.md#get-well-knownjwks) endpoint and then use the key to decode the JWT token signature. | `VSgwvbXeulFW0LiSo_dLjJrCkvAVN06euw7Ie0FC8p0` | 
| `typ`  | Indicates the type of token. For OpenPass it is always `JWT`. | `JWT` | 
| `alg`  | Indicates the encryption algorithm used for signing the token. For OpenPass it is always `RS256`. For details, see ["alg" (Algorithm) Header Parameter](https://datatracker.ietf.org/doc/html/rfc7515#section-4.1.1) in the JSON Web Signature specification. | `RS256` | 


### JWT Payload

The second part of the token, the JWT payload, includes the values and [claims](/glossary.md#claim) shown in the following table.

| Payload Claim | Meaning | Sample Value |
| :--- | :--- |:--- |
| `aud` | The __audience__ value, indicating the entity that will consume the token. The `aud` value is the Client ID for the OpenPass account. | `29352915982374239857` | 
| `email` | The user's email address | `test@example.com` | 
| `exp` | The __expiration__ value, indicating the expiration timestamp for the token. | `1663862025` | 
| `iat` | The __issued at__ value, indicating the timestamp for when the token was issued. Must be a number representing a [UTC](/glossary.md#utc) time value. | `1661270025` | 
| `iss` | the __issuer__ value, indicating the entity that issued the token. | `https://auth.myopenpass.com` | 
| `sub` | The __subject__ value, indicating a UUID (universally unique ID) for the user.  | `34716067-c9cd-47d7-a6a7-d646fe9e2ecf` | 

### JWT Signature

The last part of the token is the signature. The token is signed with the OpenPass private key, per the [JSON Web Token (JWT) specification](https://datatracker.ietf.org/doc/html/rfc7519). The publisher must verify the signature, using the OpenPass public key. Signature verification confirms that the token is genuine&#8212;it proves that the token was signed by the OpenPass server, using the OpenPass private key.

To identify the correct OpenPass public key, follow these steps:

1. Find the key at the [GET /.well-known/jwks](/api/v1/api-reference.md#get-well-knownjwks) endpoint, using the `kid` value in the JWT header to identify the correct key.
2. Decode the signature by applying the public key. Use all values in the applicable key from the JWKS endpoint. For an example, see [ID Token Details - Signature Section](#id-token-details-signature-section).

### ID Token Signature Section

The following example shows all the values in the JWS key used for the example in this section. The `kid` value is the second value in the key.

```json
{
  "kty": "RSA",
  "kid": "TsQtpnYfcfZn5ePKEagh3cSYFqlgLouxEOmNXMQRQeU",
  "n": "x7PX7QkS0DuOELNq6UrI-oAhNSibSDIP27twYAo9jnW3EI1Cv76Dgiv4jJMfNjAv4NUgJ2NidZeBhq2YXwZx9W4vdFfK2NwMWwZ5EWx_MRTH972fndQaTUh5Gw1beA_dRV4NMik_MPE3QTuwXcqgdnIb4efv955OP_KbMOI63RIhhgh4GddF-22Uy0UsQcfDPsWUpGS9mafUTG3HAiwaTg7A9HWrAAN2QJmXeIgKR2zd3KB7S1pB63F7exh1qMqEmtwV1D5u1ee_zacJGs1P7X1eggkQIC4vdoaq_j0rnvDWtcvbeCwHj2l6ixJmsBc6BwPjZWoxNas7lwbhDlbnDQ",
  "e": "AQAB"
}
```

For more information on the values in a key, see [Parameters for RSA Keys](https://www.rfc-editor.org/rfc/rfc7518#section-6.3) in the JSON Web Algorithms (JWA) specification.

At [https://jwt.io/](https://jwt.io/), when you provide the key, the token in this sample response gives the following results:

![Sample ID token, decoded at https://jwt.io/, with signature verified](images/jwt_io_02_signature_verif.jpg)

The signature is verified, which confirms that the token was sent by the specified issuer, the OpenPass authentication server.