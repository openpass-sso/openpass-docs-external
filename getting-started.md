[OpenPass Developer Documentation](README.md) > Getting Started with OpenPass

# Getting Started with OpenPass
On this page:

 * [Setting Up Your Account](#setting-up-your-account)
 * [Choosing How to Integrate With OpenPass](#choosing-how-to-integrate-with-openpass)
 * [More Information](#more-information)

## Setting Up Your Account

> NOTE: OpenPass is currently in the testing stage. Account creation and legal paperwork are managed by the team at The Trade Desk.

To get an account set up, contact openpass@thetradedesk.com. Include the following information:

| Information Type | Description | Examples |
| - | - | - |
| Client Name | Your application or publisher name. | Boost Fitness |
| Client Logo | Your application or publisher logo. Here are some guidelines for logo files:<br>- Make sure the logo has a transparent or white background. <br>- Ideally, use an SVG file so that it scales appropriately on any viewing device.<br>- If SVG is not available, you can use PNG format: recommended maximum width 320 pixels, maximum height 100 pixels. | logo.svg |
| Friendly Website URL | The human-friendly base URL for your website. | boostfitness.com |
| Redirect URLs | One or more valid URLs that OpenPass redirects to when a user has completed the sign-in process.<br>The redirect URL sent during the authentication process must exactly match one of the redirect URLs provided for the account. See [Redirect URLs](info/redirect-vs-popup.md#importance-of-the-redirect-uri). <br> Include local development URLs. | https://boostfitness.com/op_callback <br> http://localhost:8000/op_callback |
| Origins for CORS Requests to OpenPass API | A complete list of the origins of your site that call the OpenPass API is required for enabling CORS requests. | https://boostfitness.com |
| Integration Method Your Site Uses | Specify one of the following:<br>- Direct API<br>- OpenPass JavaScript SDK | OpenPass JavaScript SDK |

When your account is set up we'll send you:
* A unique Client ID. This value is your unique identifier with OpenPass, and is required for setting up your SDK client or when calling the OpenPass API directly.
* A Client Secret value. This is the equivalent of an account password. Be sure to store it securely and share it securely when needed.

## Choosing How to Integrate With OpenPass

You can integrate your website or app with OpenPass in a number of different ways to suit each use case. Integration options include the following:

* Browser-based websites or web applications 
    * [OpenPass JavaScript SDK](sdks/openpass-js-sdk/guide.md) - client-side integration for web applications.
    * [OpenPass API](api/v1/guide.md) - use the API directly from the client side or server side.
* Native apps (mobile, tablet)
    * [OpenPass API](api/v1/guide.md) - use the API directly from your app.
* TV apps
    * For information about the roadmap for Connected TV SDKs, ask your contact at The Trade Desk.

> NOTE: Other languages and frameworks are planned for future support. For details, ask your contact at The Trade Desk.

## More information

The following additional information is available to help you use OpenPass:

* [OpenPass Benefits for Publishers and Consumers](info/benefits.md)
* [Choosing the Right Flow: Redirect or Popup](info/redirect-vs-popup.md)
* [Authentication Tokens](info/tokens-auth.md)
* [OpenPass Security Considerations](info/security.md)
* [OpenPass Use of the OIDC (OAuth 2.0) Standard](info/oidc.md)
* [Reference Implementations](info/reference-implementations.md)
* [OpenPass License Information](info/license.md)
