[OpenPass Developer Documentation](README.md) > Glossary of Terms

# OpenPass Glossary of Terms

<p>Provides definitions for some terms used in the OpenPass documentation.</p>

<table>
<thead>
<tr align= "center">
<th>A-I</th>
<th>J</th>
<th>O-R</th>
<th>S-Z</th>
</tr>
</thead>
<tbody>
<tr align= "left">
<td style="vertical-align:top">
<ul>
<li><a href="#access-token">access token</a></li>
<li><a href="#advertising-token">advertising token</a></li>
<li><a href="#authorization-code">authorization code</a></li>
<li><a href="#authorization-server">authorization server</a></li>
<li><a href="#basic-authorization-header">Basic Authorization header</a></li>
<li><a href="#bearer-token">bearer token</a></li>
<li><a href="#claim">claim</a></li>
<li><a href="#csrf">CSRF</a></li>
<li><a href="#id-token">ID token</a></li>
</ul>
</td>
<td style="vertical-align:top">
<ul>
<li><a href="#json-web-key">JSON Web Key</a></li>
<li><a href="#json-web-key-set">JSON Web Key Set</a></li>
<li><a href="#json-web-signature">JSON Web Signature</a></li>
<li><a href="#json-web-token">JSON Web Token</a></li>
<li><a href="#jwk">JWK</a></li>
<li><a href="#jwks">JWKS</a></li>
<li><a href="#jws">JWS</a></li>
<li><a href="#jwt">JWT</a></li>
</ul>
</td>
<td style="vertical-align:top">
<ul>
<li><a href="#oauth-20">OAuth 2.0</a></li>
<li><a href="#oidc">OIDC</a></li>
<li><a href="#openid-connect">OpenID Connect</a></li>
<li><a href="#openid-connect-provider">OpenID Connect Provider</a></li>
<li><a href="#pgp">PGP</a></li>
<li><a href="#pkce">PKCE</a></li>
<li><a href="#redirect-uri">redirect URI</a></li>
</ul><br><br>
</td>
<td style="vertical-align:top">
<ul>
<li><a href="#sso">SSO</a></li>
<li><a href="#state-parameter">state (parameter)</a></li>
<li><a href="#uid2">UID2</a></li>
<li><a href="#unified-id-20">Unified ID 2.0</a></li>
<li><a href="#utc">UTC</a></li>
<li><a href="#well-known">well-known</a></li>
</ul><br><br><br>
</td>
</tr>
</tbody>
</table>

<dl>
<dt id="access-token">access token</dt>
<dd>A value returned by the <a href="#authorization-server">authorization server</a>, after authenticating the user, as part of the <a href="#openid-connect">OpenID Connect</a> protocol.</dd>

<dd>OpenPass does not use the access token.</dd>

<dt id="advertising-token">advertising token</dt>
<dd>The advertising token is the <a href="#unified-id-20">Unified ID 2.0</a> token returned by The Trade Desk.</dd>

<dt id="authorization-code">authorization code</dt>
<dd>With the <a href="#oauth-20">OAuth 2.0</a> Authorization Code grant type, the resource owner (the consumer, app user, or site user) is redirected to the authorization server and gives authorization for the client (the site or app) to access the resource. The authorization server then redirects the consumer back to the client with an authorization code. The client presents this authorization code, along with its own authentication credentials, back to the authorization server, requesting an access token. The client then uses the access token to call the service on behalf of the resource owner.</dd>

<dd>In the context of OpenPass, the authorization server is the OpenPass server, the client is the publisher, the resource owner is the consumer, and the protected resource is the publisher's content.</dd>

<dt id="authorization-server">authorization server</dt>
<dd>In <a href="#oauth-20">OAuth 2.0</a>, the authorization server is the server that authenticates the resource owner (consumer), gets the consumer's authorization, and then issues a token to the client. The token allows the consumer to access the protected resource by means of the client.</dd>

<dd>In the context of OpenPass, the authorization server is the OpenPass server, the client is the publisher, the resource owner is the consumer, the token is the <a href="#id-token">ID token</a>, and the protected resource is the publisher's content.</dd>

<dt id="basic-authorization-header">Basic Authorization header</dt>
<dd>In <a href="#openid-connect">OpenID Connect</a>, requests to the token endpoint should include the HTTP Basic Authorization header, which allows the OpenPass server to authenticate the identity of the client. The value of this header is constructed from the Client ID and Client Secret values, with a colon separator, Base64-encoded.</dd>

<dd>For client-side implementations where it is not possible to send the Client Secret value securely, a different approach is available. Rather than the Basic Authentication header, send the Client ID value to the <a href="api/v1/api-reference.md#post-v1apitoken">POST /v1/api/token</a> endpoint in the <code>client_id</code> query parameter. If the Basic Authentication header is not present, the <code>client_id</code> parameter is required.</dd>

<dd>For details, see <a href="https://openid.net/specs/openid-connect-basic-1_0.html#TokenRequest">Client Sends Code</a> in the OpenID Connect specification. For an example, see <a href="https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Authorization#basic_authentication">Authorization</a> in the Examples section (Mozilla documentation).</dd>

<dt id="bearer-token">bearer token</dt>
<dd>In <a href="#oauth-20">OAuth 2.0</a>, a bearer token is a security token issued by the <a href="#authorization-server">authorization server</a> after authenticating the user. Any entity in possession of the bearer token can use it to access the protected resource.</dd>

<dd>In the context of OpenPass, the token returned by the OpenPass authorization server, after authentication of the user, is a bearer token in <a href="#json-web-token">JWT</a> format. OpenPass includes an extra layer of security, <a href="#pkce">Proof Key for Code Exchange (PKCE)</a>, to help prevent malicious interception of the bearer token.</dd>

<dd>For details, see <a href="https://datatracker.ietf.org/doc/html/rfc6750">The OAuth 2.0 Authorization Framework: Bearer Token Usage</a>.</dd>

<dt id="claim">claim</dt>
<dd>In <a href="#openid-connect">OpenID Connect</a> a claim is a piece of information about the user, which is returned after user authentication.</dd>

<dd>The OpenPass authentication server returns claims in the ID token, in the response to a call to the <a href="api/v1/api-reference.md#post-v1apitoken">POST /v1/api/token</a> endpoint. See <a href="info/tokens-auth.md#id-token-details">ID Token Details</a>.</dd>

<dt id="csrf">CSRF</dt>
<dd>Acronym for cross-site request forgery, a type of attack in which a malicious user exploits the fact that a user has authenticated with a site and therefore the site's cookie is in the user's cache. Malicious code from one browser tab can leverage the authentication that was granted in another tab, to execute actions unknown to the authorized user.</dd>

<dt id="id-token">ID token</dt>
<dd>A value returned by the <a href="#authorization-server">authorization server</a> after authentication of the user.</dd>

<dd>The OpenPass authorization server authenticates the user and then issues an authentication token (ID token), which also contains UID2 data and the user's email, and sends it back to the publisher. This completes a successful sign-in, and gives the user access to protected content on the current site and all sites that support OpenPass.</dd>

<dd>The type of ID token that OpenPass uses is a <a href="#json-web-token">JWT</a> <a href="#bearer-token">bearer token</a>.</dd>

<dt id="json-web-key">JSON Web Key</dt>
<dd>A JSON Web Key (JWK) is a JSON data structure that represents a cryptographic key (for example, an RSA key). See also: <a href="#json-web-key-set">JSON Web Key Set</a>. The properties within the JSON object represent properties of the key, including its value.</dd>

<dd>The OpenPass authentication server publishes its JSON web key or keys at the JWKS endpoint: see <a href="api/v1/api-reference.md#get-well-knownjwks">GET /.well-known/jwks</a>.</dd>

<dd>For information about the values in a JWK, see <a href="https://www.rfc-editor.org/rfc/rfc7518#section-6.3">Parameters for RSA Keys</a> in the JSON Web Algorithms (JWA) specification.</dd>

<dt id="json-web-key-set">JSON Web Key Set</dt>
<dd>A JSON Web Key Set (JWKS) is a JSON data structure that represents a set of one or more <a href="#json-web-key">JSON web keys</a>.</dd>

<dd>The OAuth Provider publishes a JSON Web Key Set document, which includes one or more JSON web keys, used for signing the signature algorithm.</dd>

<dd>The <a href="http://openid.net/specs/openid-connect-core-1_0.html#RotateSigKeys">OpenID Connect specification</a> explains how the JWKS works:</dd>

<dd>"Rotation of signing keys can be accomplished with the following approach. The signer publishes its keys in a JWK Set at its <code>jwks_uri</code> location and includes the <code>kid</code> of the signing key in the JOSE Header of each message to indicate to the verifier which key is to be used to validate the signature. Keys can be rolled over by periodically adding new keys to the JWK Set at the <code>jwks_uri</code> location. The signer can begin using a new key at its discretion and signals the change to the verifier using the <code>kid</code> value. The verifier knows to go back to the <code>jwks_uri</code> location to re-retrieve the keys when it sees an unfamiliar <code>kid</code> value. The JWK Set document at the <code>jwks_uri</code> SHOULD retain recently decommissioned signing keys for a reasonable period of time to facilitate a smooth transition."</dd>

<dd>For details, see <a href="https://www.rfc-editor.org/rfc/rfc7517.html#section-5">JWK Set Format</a> in the JSON Web Key (JWK) specification.</dd>

<dt id="json-web-signature">JSON Web Signature</dt>
<dd>The JSON Web Signature (JWS) standard provides detailed information for securing JSON content with a digital signature.</dd>

<dd>For details, see <a href="https://datatracker.ietf.org/doc/html/rfc7515">JSON Web Signature (JWS)</a>.</dd>

<dt id="json-web-token">JSON Web Token</dt>
<dd>A JSON Web Token (JWT) is a type of <a href="#bearer-token">bearer token</a>), meaning that it contains all the information needed to allow the bearer to get access to a protected resource. With a JWT, the information is structured as a JSON object in a predefined format. It is URL-safe and digitally signed.</dd>

<dd>The JWT token has three URL-encoded and dot-separated parts:
<ol>
<li><strong>Header</strong>&#8212;Includes metadata about the token, including key ID, token type, and encryption algorithm.</li>
<li><strong>Payload</strong>&#8212;The token itself.</li>
<li><strong>Signature</strong>&#8212;The digital signature, which you can decode with the OpenPass public key. Public keys are available at the OpenPass JWKS endpoint.</li>
</ol>
</dd>

<dd>For details, see <a href="info/tokens-auth.md#id-token-details">ID Token Details</a> and the specification: <a href="https://datatracker.ietf.org/doc/html/rfc7519">JSON Web Token (JWT)</a>.</dd>

<dt id="jwk">JWK</dt>
<dd>JWK is an acronym for <a href="#json-web-key">JSON Web Key</a>.</dd>

<dt id="jwks">JWKS</dt>
<dd>JWKS is an acronym for <a href="#json-web-key-set">JSON Web Key Set</a>.</dd>

<dt id="jws">JWS</dt>
<dd>JWS is an acronym for <a href="#json-web-signature">JSON web signature</a>.</dd>

<dt id="jwt">JWT</dt>
<dd>JWT is an acronym for <a href="#json-web-token">JSON web token</a>. 

<dt id="oauth-20">OAuth 2.0</dt>
<dd>OAuth is a popular security protocol for authorization. It allows you to share private resources stored on one site with another site without having to share credentials. OAuth 2.0 is the current version.</dd>

<dd>OpenPass uses OAuth to manage the user login, with <a href="#openid-connect">OpenID Connect</a> for the user authentication.</dd>

<dd>For details, see <a href="https://datatracker.ietf.org/doc/html/rfc6749">The OAuth 2.0 Authorization Framework</a>.</dd>

<dt id="oidc">OIDC</dt>
<dd>OIDC is an acronym for <a href="#openid-connect">OpenID Connect</a>.</dd>

<dt id="openid-connect">OpenID Connect</dt>
<dd>OpenID Connect (OIDC) is an identity layer on top of the OAuth 2.0 protocol that allows the client to verify the identity of an end-user based on authentication by an authorization server.</dd>

<dd>In the context of OpenPass, the authorization server is the OpenPass server, the client is the publisher, and the end-user is the consumer.</dd>

<dd>For details, see <a href="https://openid.net/specs/openid-connect-basic-1_0.html">OpenID Connect Basic Client Implementer's Guide 1.0 - draft 40</a>.</dd>

<dt id="openid-connect-provider">OpenID Connect Provider</dt>
<dd>In the <a href="#openid-connect">OpenID Connect</a> standard, the entity that provides authentication via OpenID Connect is called the OpenID Connect Provider. The entity receiving authentication services is called the Relying Party.</dd>
<dd>In the context of OpenPass, OpenPass is the OpenID Connect Provider, and the publisher is the Relying Party.</dd>

<dt id="pgp">PGP</dt>
<dd>PGP is an acronym for Pretty Good Privacy, a secure encryption program used for signing, encrypting, and decrypting. PGP encryption includes several steps.</dd>

<dd>In OpenPass, the <a href="api/v1/api-reference.md#get-well-knownpgp-key">GET /.well-known/pgp-key</a> endpoint gets the PGP public key used to validate the signature of the <code>security.txt</code> file.</dd>

<dt id="pkce">PKCE</dt>
<dd>PKCE is an acronym for Proof Key for Code Exchange, which is a security extension to <a href="#oauth-20">OAuth 2.0</a>, intended for public clients on mobile devices. PKCE is designed to help prevent malicious interception of information in the authentication process.</dd>

<dd>With PKCE, the authorization request message includes an additional value, and the token request message includes another value that has a relationship to the first value. By comparing these two values, the authorization server can verify that the token request message is genuine. A malicious actor intercepting the response to the first message cannot impersonate the publisher and falsely receive the ID token. Per the PKCE standard, the value can be sent in plain text or can also be hashed and encoded.</dd>

<dd>The OpenPass JavaScript SDK uses PKCE, and also hashes and encodes the value.</dd>

<dd>For details, see <a href="https://datatracker.ietf.org/doc/html/rfc7636">Proof Key for Code Exchange by OAuth Public Clients</a>.</dd>

<dt id="redirect-url">redirect URI</dt>
<dd>As part of the OpenID Connect authentication process used with OpenPass, the publisher sends a value, <code>redirect_uri</code>, to the authentication server. The Redirect URI must be a valid URL with the scheme <code>https</code>. As part of setting up an account with OpenPass, the publisher also sets up one or more redirect URI values. The value sent in the <code>redirect_uri</code> parameter in the API call must exactly match one of the values set up in the account. If there is no match, authentication fails.</dd>

<dd>>IMPORTANT: The redirect URI is case sensitive.</dd>

<dt id="sso">SSO</dt>
<dd>Acronym for Single Sign-On. SSO allows a user to log in with the same credentials (usually, but not always, ID and password) to one of several software systems, such as apps or websites, that are independent but related. SSO allows the user to log in once to multiple applications or sites, which means that users do not have to remember multiple sets of credentials and also means that multiple sites/apps do not have to maintain their own authentication systems. Often, the authentication is managed by a trusted third party.</dd>

<dd>OpenPass offers a single sign-on service. The OpenPass server manages the authentication and the user provides only an email address.</dd>

<dt id="state-parameter">state parameter</dt>
<dd>A security parameter to prevent forgery attacks, defined in the <a href="#oauth-20">OAuth 2.0</a> specification and used in <a href="#openid-connect">OpenID Connect</a>. Often, this parameter is a random string with sufficient length and entropy.</dd>

<dd>For details, see <a href="https://datatracker.ietf.org/doc/html/rfc6749#section-4.1.1">Authorization Request</a> in the OAuth 2.0 specification.</dd>

<dt id="uid2">UID2</dt>
<dd>UID2 is an acronym for <a href="#unified-id-20">Unified ID 2.0</a>.</dd>

<dt id="unified-id-20">Unified ID 2.0</dt>
<dd>Unified ID 2.0 (also known as UID2 and UID 2.0) is a deterministic identifier based on PII (for example, email or phone number) with user transparency and privacy controls. The UID2 identifier enables logged-in experiences from publisher websites, mobile apps, and CTV apps to monetize through programmatic workflows.</dd>

<dd>For details, see <a href="https://github.com/UnifiedID2/uid2docs/blob/main/README.md">the UID2 documentation</a>.</dd>

<dt id="utc">UTC</dt>
<dd>UTC is an abbreviation for Coordinated Universal Time, also called Zulu time, which is the primary time standard in general use. UTC essentially equates to Greenwich Mean Time (GMT), but is more scientifically precise. Time values returned in the <a href="#json-web-token">JWT</a> ID token are represented in UTC.</dd>

<dt id="well-known">well-known</dt>
<dd>In the <a href="#openid-connect">OpenID Connect</a> standard, the <a href="#openid-connect-provider">OpenID Connect Provider</a> publishes certain pieces of information at specific "well-known" endpoints. These endpoints include <code>well-known</code> as part of the URL. Each endpoint provides a different type of information that should be shared publicly for anyone using the specific OpenID provider.</dd>
<dd>For example, the well-known configuration endpoint, also called the discovery endpoint or discovery URL, is a specific endpoint, published by the OpenID Connect provider, that returns a list of key values for different aspects of the OpenID Connect standard that the provider supports, such as the grant types, response types, and signature algorithms supported.</dd>
<dd>For OpenPass, the well-known configuration endpoint is <a href="https://auth.myopenpass.com/.well-known/openid-configuration">https://auth.myopenpass.com/.well-known/openid-configuration</a>. For details, see <a href="api/v1/api-reference.md#get-well-knownopenid-configuration">GET /.well-known/openid-configuration</a> in the API reference documentation.</dd>
<dd>OpenPass includes several well-known endpoints. Another example is the endpoint where the <a href="#json-web-key-set">JSON Web Key Set</a> is published. For a full list, see <a href="well-known-endpoints">Well-Known Endpoints</a>.</dd>
</dl>