[OpenPass Developer Documentation](../../README.md) > [OpenPass SDKs](../sdks.md) > OpenPass JavaScript SDK Guide

# OpenPass JavaScript SDK Guide

The OpenPass JavaScript SDK makes it easy to integrate OpenPass directly on your website.

Install the SDK, following the steps in this document, and then configure your site to reference it.

* For overview information about OpenPass, see [OpenPass Developer Documentation](../../../README.md) > [SDKs](../sdks.md) > OpenPass JavaScript SDK API Reference
](/README.md).
* For a general guide for getting started, including account creation, see [Getting Started with OpenPass](/getting-started.md).
* For SDK details, see [OpenPass JavaScript SDK API Reference](api-reference.md).
* For information about integrating your OpenPass implementation with UID2, ask your contact at The Trade Desk.

### Supported JavaScript Versions

The minimum supported JavaScript version the OpenPass JavaScript SDK supports is ES6.

# Using the OpenPass JavaScript SDK: Step-By-Step Guide

To get up and running with OpenPass using the JavaScript SDK, follow these steps:

1. [Install the SDK](#install-the-sdk).
2. [Initialize the OpenPass Client](#initialize-the-openpass-client).
3. Add [Sign In](#sign-in). Follow the instructions for the sign-in flow you are using:
   * [Redirect flow](#redirect-flow)
   * [Popup flow](#popup-flow)
   * [Popup flow, with fallback to redirect](#popup-flow-with-fallback-to-redirect)

For best practices and to see the code in action, review [Reference Implementations](/info/reference-implementations.md).

## Install the SDK

### CDN

To install on a CDN, run the following command.

```html
<script src="https://cdn.myopenpass.com/openpass-js-sdk/v1/openpass-js-sdk.min.js"></script>
```

### NPM

To install using NPM, run the following command.

> NOTE: NPM download is not currently available. For more information, ask your contact at The Trade Desk.

```sh
# In your project, run:
npm install openpass-js-sdk
```

In your code, reference the library. Here are some examples of how you can do that:

```js
// ES6 / TypeScript
import {OpenPassClient} from "openpass-js-sdk";
```

## Initialize the OpenPass Client

You must create an OpenPass client in your web application to manage the following tasks:
1. Generate the link for your OpenPass **Sign In** button.
2. Handle the result of a user sign-in.

Ideally, the OpenPass client is created once in your application and referenced in the appropriate places. For examples and best practices, see [Reference Implementations](/info/reference-implementations.md).

```js
  openpassClient = new openpass.OpenPassClient({ clientId: "{{clientId}}" });
```

For detailed information on client initialization, see [JavaScript SDK API Reference](api-reference.md).

## Sign-In

The OpenPass login can be standalone, or you can add it to a list if you already support other SSO providers, such as Google or Facebook.

The sign-in process varies based on the flow you are using. Choose the correct flow for your site or application:

| Type | Description |
| - | - |
| [Redirect flow](#redirect-flow) | A full-page redirect to the OpenPass sign-in form, and then back to the redirect URI that you provided, where you can finish the authentication process. |
| [Popup flow](#popup-flow) | The OpenPass sign-in form is launched in a new popup browser window on top of your site. When sign-in is complete, the window closes and a message is sent back to the origin page to finish the authentication process. |
| [Popup flow, with fallback to redirect](#popup-flow-with-fallback-to-redirect) | Executes the popup flow unless the popup has been blocked or the screen size is too small for the window to be displayed properly. In these cases, OpenPass executes the redirect flow. |

 For details, see [Choosing the Right Flow: Redirect or Popup](/info/redirect-vs-popup.md).


### Redirect Flow

The redirect flow uses the OpenPass client method [signInWithRedirect()](api-reference.md#signinwithredirect) to initiate the authentication flow, and [isRedirect()](api-reference.md#isredirect) and [handleAuthenticationRedirect()](api-reference.md#handleauthenticationredirect) to handle the OpenPass authorization code redirect to finalize the authentication flow.

> IMPORTANT: Make sure that the redirect URI you provide to the [signInWithRedirect()](api-reference.md#signinwithredirect) method exactly matches one of the redirect URIs specified in your OpenPass account.

To implement the redirect flow, follow these steps:

1. [Add a click handler to the **Sign In** Button (Redirect Flow)](#add-a-click-handler-to-the-sign-in-button-redirect-flow)
2. [Handle the redirect from the OpenPass Sign-In Form (Redirect Flow)](#handle-the-redirect-from-the-openpass-sign-in-form-redirect-flow)

#### Add a Click Handler to the **Sign In** Button (Redirect Flow)

```js
// Example: on route http://boostfitness.com/articles/example.

signInButton.addEventListener("click", () => openpassClient.signInWithRedirect({

    // The URI on your site that OpenPass redirects back to, which then finalizes the authentication process (see next code block).
    redirectUri: "http://boostfitness.com/handle_op_auth";

    // We can remember the page URI where the user initiated the sign-in, and redirect to that when the authentication process is complete.
    clientState: {
        originalUri = "http://boostfitness.com/articles/example";
    }
}));
```

#### Handle the Redirect from the OpenPass Sign-In Form (Redirect Flow)

```js
// Example: on route http://boostfitness.com/handle_op_auth.

// This JavaScript code should run when the window loads. For example:
// a) on success: redirect to the originating page that triggered the sign-in, or
// b) on error: redirect to an error page.

// Check if this page was loaded by means of an OpenPass redirect.
if(openpassClient.isAuthenticationRedirect()) {
  try {

    // The following reads the authorization code from the OpenPass redirect request,
    // and exchanges it for an ID token and an access token.
    const signInResponse = await openpassClient.handleAuthenticationRedirect();

    // access the user's email from the response
    console.log(signInResponse.idToken.email);

    // and then redirect the user back to the original content.
    window.location = signInResponse.clientState.originalUrl;

  } catch (e) {

    // Decide what to do if the sign-in fails (for example, network issue):
    window.location = "/login";
  }
}
```

### Popup Flow

The Popup flow uses the OpenPass client method [signInWithPopup()](api-reference.md#signinwithpopup) to initiate the authentication flow and handle the result.

OpenPass uses the browser's Post Message API to allow the popup window to communicate with the originating window. The SDK handles this using the following code.

```js
// Example: on route http://boostfitness.com/articles/example.

signInButton.addEventListener("click", () => openpassClient
    .signInWithPopup()
    .then((signInResponse)=> {

        // access the user's email from the response
        console.log(signInResponse.idToken.email);

        // and then redirect the user back to the original content.
        window.location = signInResponse.clientState.originalUrl;

    }).catch((e) => {

        // Decide what to do if the sign-in fails (for example, network issue):
        window.location = "/login";
    })
);
```


### Popup Flow with Fallback to Redirect

The popup flow with fallback to redirect uses different methods depending on the scenario:
* If there is no fallback to redirect: It uses the OpenPass client method [signInWithPopup()](api-reference.md#signinwithpopup) to initiate the authentication flow and to handle the result.
* If fallback to redirect is triggered: It uses [signInWithPopupWithFallbackToRedirect()](api-reference.md#signinwithpopupwithfallbacktoredirect) to initiate the authentication flow and [handleAuthenticationRedirect()](api-reference.md#handleauthenticationRedirect) to handle the OpenPass authorization code redirect.

To implement the popup flow with fallback to redirect, follow these steps:

1. [Add a Click Handler to the **Sign In** Button (Popup with Fallback)](#add-a-click-handler-to-the-sign-in-button-popup-with-fallback)
2. [Handle the Redirect from the OpenPass Sign-In Form (Popup with Fallback)](#handle-the-redirect-from-the-openpass-sign-in-form-popup-with-fallback)

#### Add a Click Handler to the **Sign In** Button (Popup with Fallback)
The code below shows an example of adding a click handler to the **Sign In** button. This is just one approach.

```js
// Example: on route http://boostfitness.com/articles/example.

signInButton.addEventListener("click", () => openpassClient
    .signInWithPopupWithFallbackToRedirect(
        redirectUrl,
        clientState: {
            originalUrl: window.location.origin + "/article-sign-in-popup-with-fallback"
        }
    )
    .then((signInResponse)=> {

        // access the user's email from the response
        console.log(signInResponse.idToken.email);

        // and then redirect the user back to the original content.
        window.location = signInResponse.clientState.originalUrl;

    }).catch((e) => {

        // Decide what to do if the sign-in fails (for example, network issue):
        window.location = "/login";
    })
);
```

#### Handle the Redirect from the OpenPass Sign-In Form (Popup with Fallback)

The code below shows an example of managing the redirect. This is just one approach.

```js
// Example: on route http://boostfitness.com/handle_op_auth.

// This JavaScript code should run when the window loads. For example:
// a) on success: redirect to the originating page that triggered the sign-in, or
// b) on error: redirect to an error page.

// Check if this page was loaded with an OpenPass redirect.
if(openpassClient.isAuthenticationRedirect()) {
  try {

    // The following reads the authorization code from the OpenPass redirect request,
    // and exchanges it for an ID token and an access token.
    const signInResponse = await openpassClient.handleAuthenticationRedirect();

    // access the user's email from the response
    console.log(signInResponse.idToken.email);

    // and then redirect the user back to the original content.
    window.location = signInResponse.clientState.originalUrl;

  } catch (e) {

    // Decide what to do if the sign-in fails (for example, network issue):
    window.location = "/login";
  }
}
```