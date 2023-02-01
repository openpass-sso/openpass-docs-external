[OpenPass Developer Documentation](../README.md) > [Getting Started with OpenPass](../getting-started.md) > [Reference Information](info-summary.md) > OpenPass Use of the OIDC (OAuth 2.0) standard

# OpenPass Use of the OIDC (OAuth 2.0) Standard

OpenPass implements the [OpenID Connect (OIDC)](/glossary.md#openid-connect) specification to allow users to authenticate themselves in an industry-standard and secure way.

> OpenID Connect Core 1.0 specification: https://openid.net/specs/openid-connect-core-1_0.html

## OpenPass Use of OIDC Discovery

The OpenPass implementation of OIDC includes discovery, a sub-set of the OIDC core specification.

As detailed in the OpenID Connect Discovery 1.0 specification, you can access information about the OpenPass OIDC configuration at the discovery endpoint (well-known configuration endpoint): https://auth.myopenpass.com/.well-known/openid-configuration. Use the values returned by this endpoint to understand which parts of the OpenID Connect standard OpenPass uses, and the values to use in your implementation.

> OpenID Connect Discovery 1.0 specification: https://openid.net/specs/openid-connect-discovery-1_0.html