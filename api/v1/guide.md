[OpenPass Developer Documentation](../../README.md) > [Getting Started](../../getting-started.md) > OpenPass API

# OpenPass API

This page includes the following information to help you get up and running with the OpenPass API:

* [API Version](#api-version)
* [OpenAPI Spec](#openapi-spec)
* [Environment](#environment)
* [Important OpenPass OpenID Connect URLs](#important-openpass-openid-connect-urls)
* [Using the OpenPass API: Step-By-Step Guide](#using-the-openpass-api-step-by-step-guide)
    * [Prepare Security Parameters](#prepare-security-parameters)
    * [Generate and Navigate to the Authorize URL](#generate-and-navigate-to-the-authorize-url)
    * [Exchange Authorization Code for Tokens](#exchange-authorization-code-for-tokens)

Additional information:

* For detail about the OpenPass API, see [OpenPass API Reference](api-reference.md).
* For information about getting started, applicable to API and SDK users, see [Getting Started with OpenPass](../../getting-started.md).
* For high-level OpenPass information, see [OpenPass Developer Documentation](../../README.md).

## API Version

The current version of the OpenPass API is v1.

## OpenAPI Spec

You can download the OpenAPI specification for the OpenPass API using the following endpoint: [https://auth.myopenpass.com/.well-known/open-api-v1](https://auth.myopenpass.com/.well-known/open-api-v1).

## Environment 

The OpenPass API uses the following base URLs:

| Environment | Base URL | Example |
| :- | :- | :- |
| Production | `https://auth.myopenpass.com` | `https://auth.myopenpass.com/v1/token` |
| Staging | `https://auth.stg.myopenpass.com` | `https://auth.stg.myopenpass.com/v1/token` |

## Important OpenPass OpenID Connect URLs

The following table lists the OpenPass OIDC URLs.

| Endpoint or Domain | URL | Additional Info |
| :- | :- | :- |
| OpenPass authentication endpoint | `https://auth.myopenpass.com/v1/api/authorize` | [GET /v1/api/authorize](api-reference.md#get-v1apiauthorize) |
| OpenPass token endpoint | `http://auth.myopenpass.com/v1/api/token` | [POST /v1/api/token](api-reference.md#post-v1apitoken) |

# Using the OpenPass API: Step-By-Step Guide

> NOTE: This section details only the [Redirect Flow](/info/redirect-vs-popup.md#redirectflow).

The following are the high-level steps for implementing OpenPass using the API:

1. [Prepare Security Parameters](#prepare-security-parameters)
2. [Generate and Navigate to the Authorize URL](#generate-and-navigate-to-the-authorize-url)
3. [Exchange Authorization Code for Tokens](#exchange-authorization-code-for-tokens)

### Prepare Security Parameters

As part of the user authentication process, OpenPass requires the [PKCE](/info/security.md#openpass-use-of-pkce) security parameters shown in the following table.

| PKCE Parameter | Description | Sample Value |
| :--- | :--- | :--- |
| `code_challenge` | A Base64 URL-encoded SHA256 hash of the `code_verifier` value (or, if the client is unable to support this, just the `code_verifier` value, unmodified). For details, see [GET /v1/api/authorize](api-reference.md#get-v1apiauthorize). | 1c1d219f-1610-4174-8d24-d75c5cce875a |
| `code_challenge_method` |  either `S256` (if the `code_challenge` was created by SHA256 hashing of the `code_verifier`) value, or `plain` (if using the `code_verifier` value unmodified). For details, see [GET /v1/api/authorize](api-reference.md#get-v1apiauthorize). | `S256` |
| `code_verifier` | A high-entropy cryptographic random string with a minimum length of 43 characters. For details, see [POST /v1/api/token](api-reference.md#post-v1apitoken). | 762ac555-cabc-452b-972d-4aaaba9cfb2f |

The `code_challenge` is sent in the [GET /v1/api/authorize](api-reference.md#get-v1apiauthorize) request, and OpenPass stores this value. Later on, the `code_verifier` is sent to the [POST /v1/api/token](api-reference.md#post-v1apitoken) endpoint along with the authorization code in the `code` property. OpenPass validates the `code_verifier` against the `code_challenge` value that it previously stored.

This sequence makes sure that the original [GET /v1/api/authorize](api-reference.md#get-v1apiauthorize) request was sent by the same actor as the [POST /v1/api/token](api-reference.md#post-v1apitoken) request, and prevents CSRF and authorization code injection attacks.

You can generate the PKCE parameters by using the following pseudo-code as a guideline:

```pseudo
    code_verifier = randomString(length=43)
    code_challenge = base64UrlEncode(sha256(toAscii(code_verifier)))
    code_challenge_method = "S256"
```

### Generate and Navigate to the Authorize URL

To initiate the authentication flow, if you're using the redirect flow, the client must direct the user to the Authorize URL on a browser by using a full-page redirect. A common approach is to generate the Authorize URL and link an OpenPass-branded **Sign In** button to it.

You must generate and store the PKCE security parameters and `state` parameter, because they are required later when exchanging the authorization code for the OpenPass tokens. There are multiple ways of doing this. Two examples are:

* On the client side: Store them in local storage.
* On the server side: Store them on your user's session storage.

Authorize base URL: `https://auth.myopenpass.com/v1/api/authorize`

The following table summarizes the query string parameters. For details, see [GET v1/api/authorize](api-reference.md#get-v1apiauthorize).

| Parameter | Additional Information |
| :--- | :--- |
| `client_id` | The unique ID provided to you by The Trade Desk for your client. If you don't have a `client_id` value, ask your contact at The Trade Desk. |
| `redirect_uri` | The URI that OpenPass redirects back to after user sign-in (or an error during the sign-in). This URI must exactly match one that you've set up in your client configuration (with no fragments). See [Setting Up Your Account](../../getting-started.md#setting-up-your-account) in the Getting Started guide. |
| `response_type` | A parameter required by OpenID Connect. For OpenPass, the value must be `code`. |
| `scope` | A parameter required by OpenID Connect. For OpenPass, the value must be `oidc`. |
| `state` | Another security parameter to prevent forgery attacks. Often, the [state](../../glossary.md#state-parameter) parameter is a random string with sufficient length and entropy.
| `code_challenge` | See [Prepare Security Parameters](#prepare-security-parameters). |
| `code_challenge_method` | See [Prepare Security Parameters](#prepare-security-parameters). |

#### Example Code

The following example is pseudo-code to demonstrate one implementation approach for generating and navigating to the URL. The example includes the Java code and the corresponding HTML coding.

The following Java pseudo-code implements this example on the back end.

```java
String generateAuthorizeUrl(String userSessionId) {

    var clientId = "123abc";
    var redirectUri = urlEncode("https://yoursite.com/handle_op_callback");

    var state = generateRandomString(50);
    var codeVerifier = generateRandomString(50);
    var codeChallenge = base64UrlEncode(sha256Hash(codeVerifier);
    var codeChallengeMethod = "S256";
    
    this.securityParameterStorage.store(userSessionId, codeVerifier, state);

    var authorizeUrlTemplate = "https://auth.myopenpass.com/v1/api/authorize?response_type=code&scope=openid&client_id=%s&redirect_uri=%s&state=%s&code_challenge_method=%s&code_challenge=%s";

    return String.format(authorizeUrlTemplate, clientId, redirectUrl, state, codeChallengeMethod, codeChallenge);
}
```

The following HTML implements the example in the user interface, providing the **Sign In** button to the user and triggering the underlying code when the user clicks **Sign In to OpenPass**.

```html
<a href="{{authorizeUrl}}">Sign In to OpenPass</a>
```

#### Example Call to the Authorize Endpoint


The following example shows a call to the Authorize endpoint, with all required parameters and values.

```
https://auth.myopenpass.com/v1/api/authorize?response_type=code&client_id=29352915982374239857&scope=openid&state=97166977ea60581eee4a701a4287f656&code_challenge_method=S256&code_challenge=f_I4cgCYHdurO9gHjtAtkOb3fzybXNX-_B2VW2M8iNs&redirect_uri=https%3A%2F%2Fsite.opw1.net%2Fredirect-callback
```

### Exchange Authorization Code for Tokens

In the redirect flow, after the user has navigated to the authorize URL, several things can happen:

* The user signs in successfully. See [Successful Sign-In](#successful-sign-in).
* Sign-in fails because an error occurs during the sign-in process (either transient, such as network connectivity, or due to user error). See [Unsuccessful Sign-In](#unsuccessful-sign-in).
* Sign-in fails because the user cancels the sign-in (closes the page or navigates backwards). See [Unsuccessful Sign-In](#unsuccessful-sign-in).

#### Successful Sign-In

For successful sign-in, the following is the sequence of steps. When you have a successful result, follow the additional steps to verify the ID token.

1. OpenPass redirects the page to the `redirect_uri` that was set on the Authorize URL. The request includes two parameters on the query string:
    * `code`: Required for the next step to complete the authentication flow and receive authentication tokens.
    * `state`: A security parameter to prevent forgery attacks. See [state parameter](../../glossary.md#state-parameter).
2. The client must handle the redirect (GET) request to the `redirect_uri` and complete the authentication process:
    1. Verify that the `state` value you passed to the Authorize endpoint matches the one sent in this redirect request.
    2. (Conditional) If the `state` value does not match, abort the authentication process and display an error to the user or ask the user to sign in again.
3. The client exchanges the authorization `code` value for the authentication tokens. When the tokens are verified, the user is authenticated. To do this the client sends a POST HTTP request to [https://auth.myopenpass.com/v1/api/token](api-reference.md#post-v1apitoken), with a [Basic Authorization header](../../glossary.md#basic-authorization-header) (server-side integrations only), and the following parameters (content-type: `application/x-www-form-urlencoded`):
    * `client_id` (only if Basic Authorization header is not included): Your client ID from OpenPass. Must be the same `client_id` that you passed to the Authorize URL. NOTE: The Basic Scheme Authorization header is required only when supplying a client secret (server-side integration). For client-side integrations, where this header is not included, the `client_id` parameter is required instead. These options are mutually exclusive.
    * `redirect_uri`: The same `redirect_uri` that you passed to the Authorize URL.
    * `code`: The authorization `code` that you retrieved from the redirect query string.
    * `grant_type`: Must be `authorization_code`.
    * `code_verifier`: The `code_verifier` that was generated when constructing the `code_challenge` for the Authorize URL.

> NOTE: This request can be issued from the client/browser or on the server side.

The result you get is either a success (200) or an error (400-500), with a JSON body:

* Success
    * `id_token`: The identity token JWT.
    * `access_token`: The access token JWT.
    * `token_type`: The type of token (for OpenPass, always `Bearer`).
* Error
    * `error`: The error code.
    * `error_description`: A description of the error, along with any assistance to help resolve it.
    * `error_uri`: The URI of the error.

The ID token is a JWT that contains information about the authenticated user including the user's email address. The access token is also a JWT, but is used to access protected resources.

To verify the ID token JWT, follow these steps (or use a library such as [Verify a JWT](https://github.com/auth0/java-jwt#verify-a-jwt) from Auth0):

1. Get the OpenPass [JWKS](../../glossary.md#json-web-key-set) from [https://auth.myopenpass.com/.well-known/jwks](api-reference.md#get-well-knownjwks).
2. Get the Key ID from the ID token JWT headers.
3. Get the correct JWK from the JWKS using the Key ID. This is the public signing key for the JWT.
4. Verify the JWT signature using the JWK.
5. Verify that the algorithm used to generate the JWT is the same algorithm listed in the JWK.
6. Verify that the JWT token issuer is `https://auth.myopenpass.com`.

#### Example Code

The following example is pseudo-code to demonstrate one implementation approach for managing the sign-in process.

```java
void handleOpenPassAuthorizeRedirect(HttpRequest request, String userSessionId) {

    var clientId = "123abc";
    var redirectUri = urlEncode("https://yoursite.com/handle_op_callback");

    var state = getParameter(request.query, "state");
    var authCode = getParameter(request.query, "code");

    var originalSecurityParameters = this.securityParameterStorage.get(userSessionId);

    // Make sure that the state originally generated and passed in the Authorize URL
    // matches the state on the redirect request.
    verifyStateMatch(originalSecurityParameters.state, state);

    // The code_verifier is passed to the token endpoint, and is then matched against the code_challenge
    // that was passed in to the Authorize URL.
    var postBodyParameters = new HashMap<String, String>();
    parameters["clientId"] = clientId;
    parameters["redirect_uri"] = redirectUrl;
    parameters["code"] = authCode;
    parameters["grant_type"] = "authorization_code";
    parameters["code_verifier"] = securityParameters.codeVerifier;

    var response = http.post("https://auth.myopenpass.com/v1/api/token", postBodyParameters);
    var idTokenString = response.json()["id_token"];

    // When the JWT is verified, we can say that the user is authenticated.
    DecodedJWT idToken = verifyIdToken(idTokenString);

    System.out.println("User email: " + idToken.sub);
}

void verifyIdToken(String idToken){

    // decode JWT to check for valid structure.
    DecodedJWT decodedJwt = JWT.decode(jwt);

    // Connect to the OpenPass JWKS provider endpoint to retrieve the JSON web key related to the JWT key id.
    JwkProvider jwkProvider = new UrlJwkProvider("https://auth.myopenpass.com/.well-known/jwks");
    Jwk jwk = jwkProvider.get(decodedJwt.getKeyId());

    // Get the public key from the JWK endpoint and create verification algorithm.
    PublicKey publicKey = jwk.getPublicKey();
    Algorithm algorithm = Algorithm.RSA256((RSAKey) publicKey);

    // verify that the JWT has the expected algorithm and is signed and issued by OpenPass.
    JWTVerifier verifier = JWT.require(algorithm).withIssuer("https://auth.myopenpass.com").build();

    verifier.verify(decodedJwt);

    return decodedJwt;
}
```

#### Unsuccessful Sign-In

If the sign-in was unsuccessful, you must handle the error redirect. OpenPass redirects to the redirect URI you provided in the `redirect_uri` parameter on the Authorize URL, and includes error details on the query string of that request.

#### Example Unsuccessful Response

```
http://example.com/handle_op_redirect?error=invalid_client&error_description=Client+Does+Not+Exist
```