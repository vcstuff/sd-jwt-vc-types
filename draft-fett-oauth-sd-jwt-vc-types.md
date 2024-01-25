%%%
title = "SD-JWT VC Type Metadata"
ipr = "trust200902"
area = "Security"
workgroup = "Web Authorization Protocol"
keyword = ["security", "oauth2", "sd-jwt"]

[seriesInfo]
name = "Internet-Draft"
value = "draft-fett-oauth-sd-jwt-vc-types-latest"
stream = "IETF"
status = "standard"

[[author]]
initials="D."
surname="Fett"
fullname="Daniel Fett"
organization="Authlete Inc. "
    [author.address]
    email = "mail@danielfett.de"

%%%

.# Abstract

This specification describes metadata for SD-JWT based Verifiable Credentials.

{mainmatter}

# Introduction

The SD-JWT VC specification [@!I-D.oauth-sd-jwt-vc] defines the JWT claim `vct`
(for verifiable credential type). The `vct` value serves as an identifier for
the type of the SD-JWT VC. 

A type is associated with rules defining which claims may or must appear in the
Unsecured Payload of the SD-JWT VC and whether they may, must, or must not be
selectively disclosable.

This specification defines metadata that can be associated with a type as well
as a method for retrieving the metadata and processing rules. This metadata is
intended to be used, among other things, for the following purposes:

 * Developers of Issuers and Verifiers can use the metadata to understand the
   semantics of the type and the associated rules. While in some cases, Issuers
   are the parties that define types (credential formats), this is not always
   the case. For example, a type can be defined by a standardization body or a
   community.
 * Verifiers can use the metadata to determine whether a credential is valid
   according to the rules of the type. For example, a Verifier can check whether
   a credential contains all required claims and whether the claims are
   selectively disclosable.
 * Wallets can use the metadata to display the credential in a way that is
   consistent with the Issuer's intent.

Applications using this specification are called "consuming applications" in the
following. This typically includes Issuers, Verifiers, and Wallets.

# Example

All examples in this section are non-normative.

In an SD-JWT VC, `vct` can be used to express a type. If the type is a URI,
metadata can be retrieved as described in (#RetrievingMetadata).

```json
{
  "vct": "https://betelgeuse.example.com/identity_credential",
  "vct#integrity": "sha256-WRL5ca_xGgX3c1VLmXfh-9cLlJNXN-TsMk-PmKjZ5t0",
  ...
}
```

Here, the type is `https://betelgeuse.example.com/identity_credential`. The
metadata is retrieved from the URL
`https://betelgeuse.example.com/.well-known/vct/identity_credential`.

The following is an example for a metadata document:

```json
{
	"name": "Betelgeuse Education Credential - Preliminary Version",
	"description": "This is our development version of the education credential. Don't panic.",
	"extends": "https://galaxy.example.com/galactic-education-credential-0.9",
	"display": [
		{
			"en-US": {
				"name": "University Credential",
                "rendering": {
                    "simple": {
                        "logo": {
                            "uri": "https://exampleuniversity.com/public/logo.png",
                            "uri#integrity": "sha256-LmXfh-9cLlJNXN-TsMk-PmKjZ5t0WRL5ca_xGgX3c1V",
                            "alt_text": "a square logo of a university"
                        },
                        "background_color": "#12107c",
                        "text_color": "#FFFFFF",
                    },
                    "svg_templates": [
                        {
                            "uri": "https://exampleuniversity.com/public/credential-english.svg",
                            "uri#integrity": "sha256-8cLlJNXN-TsMk-PmKjZ5t0WRL5ca_xGgX3c1VLmXfh-9c",
                            "properties": {
                                "orientation": "landscape",
                                "color_scheme": "light",
                                "contrast": "high",
                            }
                        }
                    ]
                }
			}
		}
	],
	"claims":{
        "/name": {
            "display": {
                "de-DE": {
                    "label": "Vor- und Nachname",
                    "description": "Der Name des Studenten"
                },
                "en-US": {
                    "label": "Name",
                    "description": "The name of the student"
                }
            },
            "verification": "verified",
            "sd": "allowed"
        },
        "/degree": {
            "display": {
                "de-DE": {
                    "label": "Abschluss",
                    "description": "Der Abschluss des Studenten"
                },
                "en-US": {
                    "label": "Degree",
                    "description": "Degree earned by the student"
                }
            },
            "verification": "authoritative",
            "sd": "allowed"
        },
        "/address": {
            "display": {
                "de-DE": {
                    "label": "Adresse",
                    "description": "Adresse zum Zeitpunkt des Abschlusses"
                },
                "en-US": {
                    "label": "Address",
                    "description": "Address at the time of graduation"
                }
            },
            "verification": "self-attested",
            "sd": "always"
        },
        "/address/street_address": {
            "display": {
                "de-DE": {
                    "label": "Straße"
                },
                "en-US": {
                    "label": "Street Address"
                }
            },
            "verification": "self-attested",
            "sd": "always"
        }
    },
    
    "schema_url": "https://exampleuniversity.com/public/credential-schema-0.9",
    "schema_url#integrity": "sha256-o984vn819a48ui1llkwPmKjZ5t0WRL5ca_xGgX3c1VLmXfh"
}
```

# Retrieving Metadata {#RetrievingMetadata}

In an SD-JWT VC, a URI in the `vct` claim can be used to express a type. If the
type is a URL, metadata can be retrieved from the URL
`https://<authority>/.well-known/vct/<type>`, i.e., by inserting
`/.well-known/vct` after the authority part of the URL.

The metadata is retrieved using the HTTP GET method. The response MUST be a JSON
object as defined in (#MetadataFormat).

If the claim `vct#integrity` is present in the SD-JWT VC, its value
`vct#integrity` MUST be an "integrity metadata" string as defined in Section (#Integrity).

# Document Integrity {#Integrity}

Both the `vct` claim in the SD-JWT VC and various URIs/URLs in the metadata
document can be accompanied by a respective claim suffixed with `#integrity`.
Its value MUST be an "integrity metadata" string as defined in Section 3 of
[@!W3C.SRI]. A consuming application of the respective documents MUST verify the
integrity of the retrieved document as defined in Section 3.3.5 of [@!W3C.SRI].

# Metadata Format {#MetadataFormat}

The metadata document MUST be a JSON object. The following properties are
defined:

- `name`: A human-readable name for the type, intended for developers reading
  the JSON document. This property is OPTIONAL.
- `description`: A human-readable description for the type, intended for
  developers reading the JSON document. This property is OPTIONAL.
- `extends`: A URI of another type that this type extends, as described in
  (#ExtendingMetadata). This property is OPTIONAL. 
- `display`: An object containing display information for the type, as described
  in (#DisplayMetadata). This property is OPTIONAL.
- `claims`: An object containing claim information for the type, as described in
  (#ClaimMetadata). This property is OPTIONAL.
- `schema`: An embedded JSON Schema document describing the structure of the
  credential as described in (#Schema). This property is OPTIONAL. MUST NOT be
  used if `schema_url` is present.
- `schema_url`: A URL pointing to a JSON Schema document describing the
  structure of the credential as described in (#Schema). This property is
  OPTIONAL. MUST NOT be used if `schema` is present.
- `schema_url#integrity`: An "integrity metadata" string as described in
  (#Integrity). This property is OPTIONAL.

# Extending Metadata {#ExtendingMetadata}

A type can extend another type. The extended type is identified by the URI in
the `extends` property. The consuming application MUST retrieve and process
metadata for the extended type before processing the metadata for the extending
type.

The extended type MAY itself extend another type. This can be used to create a
chain or hierarchy of types. The security considerations described in
(#CircularExtends) apply in order to avoid problems with circular dependencies.

The following processing rules apply for extending types (recursively for all extended types):

 * The `display` property of the extending type MUST be used for display
   purposes. If there is no such property, the `display` property of the
   extended type MAY be used.
 * For any particular claim, the respective property in `claims` of the
   extending type MUST be used for validation purposes if it exists. If there is
   no such property, the respective property of the extended type MUST be used.
 * The `schema` or `schema_url` property of the extending type MUST be evaluated
   for validation purposes if it exists. Afterwards, or if there is no such
   property, the `schema` property of the extended type MUST evaluated. This
   means that all schemas in the chain of extended types MUST be evaluated for a
   particular credential.


# Display Metadata {#DisplayMetadata}

The `display` property is an object containing display information for the type.
The object MUST contain a property for each language that is supported by the
type. The property name MUST be a language tag as defined in Section 2 of
[@!RFC5646]. The property value MUST be an object containing the following
properties:

- `name`: A human-readable name for the type, intended for end users. This
  property is OPTIONAL.
- `description`: A human-readable description for the type, intended for end
  users. This property is OPTIONAL.
- `rendering`: An object containing rendering information for the type, as
  described in (#RenderingMetadata). This property is OPTIONAL.

## Rendering Metadata {#RenderingMetadata}

The `rendering` property is an object containing rendering information for the
type. The object MUST contain a property for each rendering method that is
supported by the type. The property name MUST be a rendering method identifier
and the property value MUST be an object containing the properties defined for
the rendering method.

### Rendering Method "simple" {#RenderingMethodSimple}

The `simple` rendering method is intended for use in applications that do not
support SVG rendering. The object MUST contain the following properties:

- `logo`: An object containing information about the logo to be displayed for
  the type, as described in (#LogoMetadata). This property is OPTIONAL.
- `background_color`: A CSS color value for the background of the credential.
  This property is OPTIONAL.
- `text_color`: A CSS color value for the text of the credential. This property
  is OPTIONAL.

#### Logo Metadata {#LogoMetadata}

The `logo` property is an object containing information about the logo to be
displayed for the type. The object contains the following properties:

- `uri`: A URI pointing to the logo image. This property is REQUIRED.
- `uri#integrity`: An "integrity metadata" string as described in
  (#Integrity). This property is OPTIONAL.
- `alt_text`: A string containing alternative text for the logo image. This
  property is OPTIONAL.

### Rendering Method "svg_template" {#RenderingMethodSvg}

The `svg_template` rendering method is intended for use in applications that
support SVG rendering. The object MUST contain an array of objects containing
information about the SVG templates available for the type. Each object contains
the following properties:

- `uri`: A URI pointing to the SVG template. This property is REQUIRED.
- `uri#integrity`: An "integrity metadata" string as described in
  (#Integrity). This property is OPTIONAL.
- `properties`: An object containing properties for the SVG template, as
  described in (#SvgTemplateProperties). This property is REQUIRED if more than
  one SVG template is present, otherwise it is OPTIONAL.

#### SVG Template Properties {#SvgTemplateProperties}

The `properties` property is an object containing properties for the SVG
template. Consuming applications MUST use these properties to find the best SVG
template available for display to the user based on the display properties
(landscape/portrait) and user preferences (color scheme, contrast). The object
MUST contain at least one of the following properties:

- `orientation`: The orientation for which the SVG template is optimized, with
  valid values being `portrait` and `landscape`. This property is OPTIONAL.
- `color_scheme`: The color scheme for which the SVG template is optimized, with
  valid values being `light` and `dark`. This property is OPTIONAL.
- `contrast`: The contrast for which the SVG template is optimized, with valid
  values being `normal` and `high`. This property is OPTIONAL.

# Claim Metadata {#ClaimMetadata}

The `claims` property is an object containing information about particular claims
for displaying and validating the claims. 

The object MAY contain a property for each claim that is supported by the type. 
The property name MUST be a JSON Pointer [@!RFC6901] pointing to the claim in the
credential. The property value MUST be an object containing the following
properties:

- `display`: An object containing display information for the claim, as
  described in (#ClaimDisplayMetadata). This property is OPTIONAL.
- `verification`: A string indicating how the claim is verified, as described in
  (#ClaimVerificationMetadata). This property is OPTIONAL.
- `sd`: A string indicating whether the claim is selectively disclosable, as
  described in (#ClaimSelectiveDisclosureMetadata). This property is OPTIONAL.

Note: The JSON Pointer MUST point to the respective claim as if all selectively
disclosable claims were disclosed to the Verifier. That means that a consuming
application which does not have access to all disclosures may not be able to
validate the claim.

## Claim Display Metadata {#ClaimDisplayMetadata}

The `display` property is an object containing display information for the
claim. The object MUST contain a property for each language that is supported by
the type. The property name MUST be a language tag as defined in Section 2 of
[@!RFC5646]. The consuming application MUST use the language tag it considers most 
appropriate for the user.

The property value MUST be an object containing the following properties:

- `label`: A human-readable label for the claim, intended for end users. This
  property is OPTIONAL.
- `description`: A human-readable description for the claim, intended for end
  users. This property is OPTIONAL.

## Claim Verification Metadata {#ClaimVerificationMetadata}

The `verification` property is a string indicating how the claim is verified.
The following values are defined:

- `self-attested`: The claim's value was self-attested by the End-User towards
  the Issuer. The Issuer did not verify the claim. For example, in a diploma, 
  the residential address of the student may be self-attested.
- `verified`: The claim's value was verified by the Issuer. The Issuer may have
  used a third party to verify the claim. For example, in a diploma, the birth
  date of the student may have been verified by the university using the
  student's passport.
- `authoritative`: The Issuer claims to be the authority to make a statement
  about the claim's value. For example, in a diploma, the degree earned by the
  student may be authoritative if the Issuer is the university that issued the
  degree.

## Claim Selective Disclosure Metadata {#ClaimSelectiveDisclosureMetadata}

The `sd` property is a string indicating whether the claim is selectively
disclosable. The following values are defined:

- `always`: The Issuer MUST make the claim selectively disclosable.
- `allowed`: The Issuer MAY make the claim selectively disclosable.
- `never`: The Issuer MUST NOT make the claim selectively disclosable.

# Schema {#Schema}

If a `schema` or `schema_url` property is present, a consuming application that
intends to validate the credential (in particular, Verifiers) MUST validate the
credential against the provided JSON Schema document.

# Security Considerations

## Circular "extends" Dependencies (#CircularExtends)

A type MUST NOT extend another type that extends (either directly or with steps
in-between) the first type. This would result in a circular dependency that
could lead to infinite recursion when retrieving and processing the metadata.

Consuming applications MUST detect such circular dependencies and reject the
credential.

# Privacy Considerations

## Undescribed Claims

A Wallet MUST always ensure that all claims that are disclosed to a Verifier are
also displayed to the user as appropriate for the use case. If there is no
information about the claim in the `claims` section of the metadata, the Wallet
MUST either fall back to show the user the 'raw' claim value or MUST NOT
disclose the claim to the Verifier.

{backmatter}



<reference anchor="W3C.SRI" target="https://www.w3.org/TR/SRI/">
<front>
  <author initials="D." surname="Akhawe" fullname="Devdatta Akhawe"/>
  <author initials="F." surname="Braun" fullname="Frederik Braun"/>
  <author initials="F." surname="Marier" fullname="François Marier"/>
  <author initials="J." surname="Weinberger" fullname="Joel Weinberger"/>
  <title>Subresource Integrity</title>
  <date year="2016" month="06" day="23"/>
</front>
</reference>

# Acknowledgements {#Acknowledgements}

The SVG rendering method is based on work of the W3C CCG: https://w3c-ccg.github.io/vc-render-method/

The authors would like to thank the following people for their contributions to
this document and the discussions around it: 
Giuseppe De Marco,
Paul Bastian,
Oliver Terbu,
Torsten Lodderstedt


# Document History

Individual draft

-00

* Initial version
