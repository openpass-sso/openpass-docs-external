[OpenPass Developer Documentation](../../README.md) > [SDKs](../sdks.md) > OpenPass JavaScript SDK API Reference

# OpenPass JavaScript SDK API Reference

For background information on the OpenPass service, see [OpenPass Developer Documentation](../../README.md). For general information about implementing OpenPass, see [Getting Started with OpenPass](../../../getting-started.md).

## Reference
This SDK uses the following JavaScript methods:

    
* [OpenPassClient](#OpenPassClient)
    * [new OpenPassClient(options)](#new_OpenPassClient_new)
    * [.signInWithRedirect(options)](#OpenPassClient+signInWithRedirect) ⇒ <code>Promise.&lt;void&gt;</code>
    * [.signInWithPopup()](#OpenPassClient+signInWithPopup) ⇒ <code>Promise.&lt;SignInResponse&gt;</code>
    * [.signInWithPopupWithFallbackToRedirect(options)](#OpenPassClient+signInWithPopupWithFallbackToRedirect) ⇒ <code>Promise.&lt;SignInResponse&gt;</code>
    * [.isAuthenticationRedirect()](#OpenPassClient+isAuthenticationRedirect) ⇒ <code>boolean</code>
    * [.handleAuthenticationRedirect()](#OpenPassClient+handleAuthenticationRedirect) ⇒ <code>Promise.&lt;SignInResponse&gt;</code>

<a name="new_OpenPassClient_new"></a>

### new OpenPassClient(options)
<p>Creates a new instance of the OpenPassClient.</p>


| Param | Type | Default | Description |
| --- | --- | --- | --- |
| options | <code>OpenPassOptions</code> |  | <p>The options to initialize the client.</p> |
| options.clientId | <code>string</code> |  | <p>Your OpenPass client identifier.</p> |
| [options.baseUrl] | <code>string</code> | <code>&quot;OpenPass Api Production Url&quot;</code> | <p>Optionally set the base URL of the OpenPass API.</p> |

<a name="OpenPassClient+signInWithRedirect"></a>

### openPassClient.signInWithRedirect(options) ⇒ <code>Promise.&lt;void&gt;</code>
<p>Initiates a full-page redirect OpenPass sign-in. This method first redirects the user to the OpenPass authorization server and then, when authorization is complete, redirects back to the specified redirect URI.</p>

**Kind**: instance method of [<code>OpenPassClient</code>](#OpenPassClient)  

| Param | Type | Description |
| --- | --- | --- |
| options | <code>SignInOptions</code> | <p>Sign-in options.</p> |
| options.redirectUrl | <code>string</code> | <p>A valid redirect URI that processes a callback from the OpenPass authorization server. The URI must exactly match a redirect URI you have configured with OpenPass.</p> |
| [options.clientState] | <code>object</code> | <p>An optional object containing any client-side state value that is stored, and is then returned back when authentication is complete. You can use this to store the original URL, to redirect back to after a successful authentication.</p> |

<a name="OpenPassClient+signInWithPopup"></a>

### openPassClient.signInWithPopup() ⇒ <code>Promise.&lt;SignInResponse&gt;</code>
<p>Initiates an OpenPass sign-in in a popup window.</p>
<p>The following example shows the popup flow:</p>
<pre class="prettyprint source"><code>try {
  const authResponse = await openpassClient.signInWithPopup();
  console.log(authResponse.idToken);
  console.log(authResponse.clientState);
} catch (e) {
  console.log(e);
}
</code></pre>
<h3>Sample Response</h3>
<p>The <code>signInWithPopup()</code> returns a response with the following properties:</p>
<pre class="prettyprint source lang-js"><code>  idToken // The decoded ID token, which contains the user's identity details (including email).
  rawIdToken // The raw ID token string (JWT), which contains the users identity details (including email).
  accessIdToken // The raw access token string (JWT), which can be used to do Bearer authorization against APIs.
  clientState // The `state` value, which is passed to the `signInWithPopup()` (for popup with fallback to redirect) and `signInWithRedirect()` flows.
</code></pre>

**Kind**: instance method of [<code>OpenPassClient</code>](#OpenPassClient)  
**Returns**: <code>Promise.&lt;SignInResponse&gt;</code> - <p>The sign-in response</p>  
<a name="OpenPassClient+signInWithPopupWithFallbackToRedirect"></a>

### openPassClient.signInWithPopupWithFallbackToRedirect(options) ⇒ <code>Promise.&lt;SignInResponse&gt;</code>
<p>Initiates an OpenPass sign-in in a popup window, with fallback to a full-page redirect sign-in if it's not possible to create a popup window.</p>
<p>A fallback to a full-page redirect happens in these scenarios:</p>
<ul>
<li>If the device width or height is too small.</li>
<li>If a popup cannot be opened (for example, if blocked).</li>
</ul>
<pre class="prettyprint source"><code>try {
  const authResponse = await openpassClient.signInWithPopupWithFallbackToRedirect({
    redirectUrl: &quot;[Your_Redirect_URL]&quot;,
    clientState: &quot;[Your_Client_State]&quot;
  });
  console.log(authResponse.idToken);
  console.log(authResponse.clientState);
} catch (e) {
  console.log(e);
}
</code></pre>
<p>It is also necessary to manage the authentication redirect if the full-page redirect sign-in is triggered. To do this, use the <code>handleRedirect()</code> method.</p>
<h3>Sample Response</h3>
<p>The <code>signInWithPopupWithFallbackToRedirect()</code> and <code>handleRedirect()</code> methods both return a response with the following properties:</p>
<pre class="prettyprint source lang-js"><code>  idToken // The decoded ID token, which contains the user's identity details (including email).
  rawIdToken // The raw ID token string (JWT), which contains the users identity details (including email).
  accessIdToken // The raw access token string (JWT), which can be used to do Bearer authorization against APIs.
  clientState // The `state` value, which is passed to the `signInWithPopup()` (for popup with fallback to redirect) and `signInWithRedirect()` flows.
</code></pre>

**Kind**: instance method of [<code>OpenPassClient</code>](#OpenPassClient)  

| Param | Type | Description |
| --- | --- | --- |
| options | <code>SignInOptions</code> | <p>Sign-in options</p> |
| options.redirectUrl | <code>string</code> | <p>A valid redirect URI that processes a callback from the OpenPass authorization server. The URL must exactly match a redirect URI you have configured with OpenPass.</p> |
| [options.clientState] | <code>Object</code> | <p>An optional object containing any client-side state value that is stored, and is then returned back when authentication is complete. You can use this to store the original URL, to redirect back to after a successful authentication.</p> |

<a name="OpenPassClient+isAuthenticationRedirect"></a>

### openPassClient.isAuthenticationRedirect() ⇒ <code>boolean</code>
<p>Returns <code>true</code> if the current page was redirected to by the OpenPass authentication process after a successful or unsuccessful user sign-in.</p>

**Kind**: instance method of [<code>OpenPassClient</code>](#OpenPassClient)  
<a name="OpenPassClient+handleAuthenticationRedirect"></a>

### openPassClient.handleAuthenticationRedirect() ⇒ <code>Promise.&lt;SignInResponse&gt;</code>
<p>Call this method to complete the OpenPass authentication flow in response to receiving a callback to a redirect URI from OpenPass.</p>

**Kind**: instance method of [<code>OpenPassClient</code>](#OpenPassClient)  
**Example**  
```js
if (openpassClient.isAuthenticationRedirect()) {
  try {
    // Attempt to complete the sign-in process.
    const authResponse = await openpassClient.handleAuthenticationRedirect();
    console.log(authResponse.idToken);
    console.log(authResponse.clientState);
  } catch (e) {
    console.log(e);
  }
}
```