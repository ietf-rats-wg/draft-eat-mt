---
v: 3

title: EAT Media Types
abbrev: EAT Media Types
docname: draft-ietf-rats-eat-media-type-latest
submissionType: IETF
category: std

ipr: trust200902
area: "Security"
workgroup: "Remote ATtestation ProcedureS"
keyword: EAT, media type

stand_alone: yes
pi: [toc, sortrefs, symrefs]

author:
 - ins: L. Lundblade
   name: Laurence Lundblade
   organization: Security Theory LLC
   email: lgl@securitytheory.com
 - ins: H. Birkholz
   name: Henk Birkholz
   org: Fraunhofer Institute for Secure Information Technology
   abbrev: Fraunhofer SIT
   email: henk.birkholz@ietf.contact
   street: Rheinstrasse 75
   code: '64295'
   city: Darmstadt
   country: Germany
 - ins: T. Fossati
   name: Thomas Fossati
   organization: Linaro
   email: thomas.fossati@linaro.org

normative:
  EAT: I-D.ietf-rats-eat
  JWT: RFC7519
  CWT: RFC8392
  UCCS: I-D.ietf-rats-uccs
  MediaTypes: RFC6838
  URI: RFC3986
  HTTP: RFC9110
  JSON: RFC8259

informative:
  RATS-Arch: RFC9334
  BUILD-W-HTTP: BCP56
  REST-IoT: I-D.irtf-t2trg-rest-iot
  TAG: RFC4151

entity:
  SELF: "RFCthis"

--- abstract

Payloads used in Remote Attestation Procedures may require an associated media
type for their conveyance, for example when used in RESTful APIs.

This memo defines media types to be used for Entity Attestation Tokens (EAT).

--- middle

# Introduction

Payloads used in Remote Attestation Procedures {{RATS-Arch}} may require an
associated media type for their conveyance, for example when used in RESTful
APIs ({{fig-api-sd}}).

~~~ aasvg
.---------------.         .----------.             .----------.
| Relying Party |         | Attester |             | Verifier |
'-+-------------'         '----+-----'             '--------+-'
  |                            | POST /verify               |
  |                            | EAT(Evidence)              |
  |                            +--------------------------->|
  |                            |                     200 OK |
  |                            |   EAT(Attestation Results) |
  |                            |<---------------------------+
  |                 POST /auth |                            |
  |   EAT(Attestation Results) |                            |
  |<---------------------------+                            |
  | 201 Created                |                            |
  +--------------------------->|                            |
  |                            |                            |
  |                            |                            |
~~~
{: #fig-api-sd artwork-align="center"
   title="Conveying RATS conceptual messages in REST APIs using EAT"}

This memo defines media types to be used for Entity Attestation Token (EAT)
{{EAT}} payloads independently of the RATS Conceptual Message in which they
manifest themselves.  The objective is to give protocol, API and application
designers a number of readily available and reusable media types for
integrating EAT-based messages in their flows, for example when using HTTP
{{BUILD-W-HTTP}} or CoAP {{REST-IoT}}.

## Requirements Language

This document uses the terms and concepts defined in {{RATS-Arch}}.

# EAT Types

{{fig-eat-types}} illustrates the six EAT wire formats and how they relate to
each other.  {{EAT}} defines four of them (CWT, JWT and Detached EAT Bundle in
its JSON and CBOR flavours), whilst {{UCCS}} defines UCCS and UJCS.

~~~ aasvg
{::include misc/EAT-pieces.txt}
~~~
{: #fig-eat-types artwork-align="center"
   title="EAT Types"}

# A Media Type Parameter for EAT Profiles

EAT is an open and flexible format.  To improve interoperability, {{Section 6
of EAT}} defines the concept of EAT profiles.  Profiles are used to constrain
the parameters that producers and consumers of a specific EAT profile need to
understand in order to interoperate.  For example: the number and type of
claims, which serialisation format, the supported signature schemes, etc.  EATs
carry an in-band profile identifier using the `eat_profile` claim (see
{{Section 4.3.2 of EAT}}).  The value of the `eat_profile` claim is either an
OID or a URI.

The media types defined in this document include an optional `eat_profile`
parameter that can be used to mirror the `eat_profile` claim of the transported
EAT.  Exposing the EAT profile at the API layer allows API routers to dispatch
payloads directly to the profile-specific processor without having to snoop
into the request bodies.  This design also provides a finer-grained and
scalable type system that matches the inherent extensibility of EAT.  The
expectation being that a certain EAT profile automatically obtains a media type
derived from the base (e.g., `application/eat+cwt)` by populating the
`eat_profile` parameter with the corresponding OID or URL.

When the parameterised version of the EAT media type is used in HTTP (for
example, with the "Content-Type" and "Accept" headers), and the value is an
absolute URI ({{Section 4.3 of URI}}), the `parameter-value` ({{Appendix A of
HTTP}}) uses the `quoted-string` encoding, e.g.:

> `application/eat+jwt; eat_profile="tag:evidence.example,2022"`

Instead, when the EAT profile is an OID, the `token` encoding (i.e., without
quotes) can be used, e.g.:

> `application/eat+cwt; eat_profile=2.999.1`.

# Examples

The example in {{fig-rest-req}} illustrates the usage of EAT media types for
transporting attestation evidence as well as negotiating the acceptable format
of the attestation result.

~~~ http-message
# NOTE: '\' line wrapping per RFC 8792

POST /challenge-response/v1/session/1234567890 HTTP/1.1
Host: verifier.example
Accept: application/eat+cwt; eat_profile="tag:ar4si.example,2021"
Content-Type: application/eat+cwt; \
              eat_profile="tag:evidence.example,2022"

[ CBOR-encoded EAT w/ eat_profile="tag:evidence.example,2022" ]
~~~
{: #fig-rest-req title="Example REST Verification API (request)"}

The example in {{fig-rest-rsp}} illustrates the usage of EAT media types for
transporting attestation results.

~~~ http-message
# NOTE: '\' line wrapping per RFC 8792

HTTP/1.1 200 OK
Content-Type: application/eat+cwt; \
              eat_profile="tag:ar4si.example,2021"

[ CBOR-encoded EAT w/ eat_profile="tag:ar4si.example,2021" ]
~~~
{: #fig-rest-rsp title="Example REST Verification API (response)"}

In both cases, a tag URI {{TAG}} identifying the profile is carried as an
explicit parameter.

# Security Considerations {#seccons}

Media types only provide clues to the processing application. The application
must verify that the received data matches the expected format, regardless of
the advertised media type, and stop further processing on failure. Failing to
do so could expose the user to security risks, such as privilege escalation
and cross-protocol attacks.

The security consideration of {{EAT}} and {{UCCS}} apply in full.

In particular, when using `application/eat-ucs+json` and `application/eat-ucs+cbor` the reader should review {{Section 3 of UCCS}}, which contains a detailed discussion about the characteristics of a "Secure Channel" for conveyance of such messages.

# IANA Considerations

[^to-be-removed]

[^to-be-removed]: RFC Editor: please replace {{&SELF}} with this RFC number and remove this note.

## `+cwt` Structured Syntax Suffix

IANA is requested to register the `+cwt` structured syntax suffix in the
"Structured Syntax Suffixes" registry {{!IANA.media-type-structured-suffix}} in
the manner described in {{MediaTypes}}, which can be used to indicate that the
media type is encoded as a CWT.

### Registry Contents

{:compact}
Name:
: CBOR Web Token (CWT)

+suffix:
: +cwt

References:
: {{CWT}}

Encoding Considerations:
: binary

Interoperability Considerations:
: N/A

Fragment Identifier Considerations:
: The syntax and semantics of fragment identifiers specified for +cwt SHOULD be
  as specified for `application/cwt`.  (At publication of this document, there
  is no fragment identification syntax defined for `application/cwt`.)

Security Considerations:
: See {{Section 8 of CWT}}

Contact:
: RATS WG mailing list (rats@ietf.org), or IETF Security Area (saag@ietf.org)

Author/Change Controller:
: Remote ATtestation ProcedureS (RATS) Working Group.
  The IETF has change control over this registration.

## Media Types {#media-type}

IANA is requested to add the following media types to the
"Media Types" registry {{!IANA.media-types}}.

| Name | Template | Reference |
| EAT CWT | application/eat+cwt | {{&SELF}}, {{media-type-eat-cwt}} |
| EAT JWT | application/eat+jwt | {{&SELF}}, {{media-type-eat-jwt}} |
| Detached EAT Bundle CBOR | application/eat-bun+cbor | {{&SELF}}, {{media-type-deb-cbor}} |
| Detached EAT Bundle JSON | application/eat-bun+json | {{&SELF}}, {{media-type-deb-json}} |
| EAT UCCS | application/eat-ucs+cbor | {{&SELF}}, {{media-type-ucs-cbor}} |
| EAT UJCS | application/eat-ucs+json | {{&SELF}}, {{media-type-ucs-json}} |
{: #new-media-type align="left" title="New Media Types"}

## application/eat+cwt Registration {#media-type-eat-cwt}

{:compact}
Type name:
: application

Subtype name:
: eat+cwt

Required parameters:
: n/a

Optional parameters:
: "eat_profile" (EAT profile in string format.  OIDs must use the
  dotted-decimal notation.  The parameter value is case-insensitive.)

Encoding considerations:
: binary

Security considerations:
: {{Section 9 of EAT}}

Interoperability considerations:
: n/a

Published specification:
: {{&SELF}}

Applications that use this media type:
: Attesters, Verifiers, Endorsers and Reference-Value providers, Relying
  Parties that need to transfer EAT payloads over HTTP(S), CoAP(S), and other
  transports.

Fragment identifier considerations:
: n/a

Person & email address to contact for further information:
: RATS WG mailing list (rats@ietf.org)

Intended usage:
: COMMON

Restrictions on usage:
: none

Author/Change controller:
: IETF

Provisional registration:
: no

## application/eat+jwt Registration {#media-type-eat-jwt}

{:compact}
Type name:
: application

Subtype name:
: eat+jwt

Required parameters:
: n/a

Optional parameters:
: "eat_profile" (EAT profile in string format.  OIDs must use the
  dotted-decimal notation.  The parameter value is case-insensitive.)

Encoding considerations:
: 8bit

Security considerations:
: {{Section 9 of EAT}} and {{!BCP225}}

Interoperability considerations:
: n/a

Published specification:
: {{&SELF}}

Applications that use this media type
: Attesters, Verifiers, Endorsers and Reference-Value providers, Relying
  Parties that need to transfer EAT payloads over HTTP(S), CoAP(S), and other
  transports.

Fragment identifier considerations:
: n/a

Person & email address to contact for further information:
: RATS WG mailing list (rats@ietf.org)

Intended usage:
: COMMON

Restrictions on usage:
: none

Author/Change controller:
: IETF

Provisional registration:
: no

## application/eat-bun+cbor Registration {#media-type-deb-cbor}

{:compact}
Type name:
: application

Subtype name:
: eat-bun+cbor

Required parameters:
: n/a

Optional parameters:
: "eat_profile" (EAT profile in string format.  OIDs must use the
  dotted-decimal notation.  The parameter value is case-insensitive.)

Encoding considerations:
: binary

Security considerations:
: {{Section 9 of EAT}}

Interoperability considerations:
: n/a

Published specification:
: {{&SELF}}

Applications that use this media type:
: Attesters, Verifiers, Endorsers and Reference-Value providers, Relying
  Parties that need to transfer EAT payloads over HTTP(S), CoAP(S), and other
  transports.

Fragment identifier considerations:
: n/a

Person & email address to contact for further information:
: RATS WG mailing list (rats@ietf.org)

Intended usage:
: COMMON

Restrictions on usage:
: none

Author/Change controller:
: IETF

Provisional registration:
: no

## application/eat-bun+json Registration {#media-type-deb-json}

{:compact}
Type name:
: application

Subtype name:
: eat-bun+json

Required parameters:
: n/a

Optional parameters:
: "eat_profile" (EAT profile in string format.  OIDs must use the
  dotted-decimal notation.  The parameter value is case-insensitive.)

Encoding considerations:
: Same as {{JSON}}

Security considerations:
: {{Section 9 of EAT}}

Interoperability considerations:
: n/a

Published specification:
: {{&SELF}}

Applications that use this media type
: Attesters, Verifiers, Endorsers and Reference-Value providers, Relying
  Parties that need to transfer EAT payloads over HTTP(S), CoAP(S), and other
  transports.

Fragment identifier considerations:
: n/a

Person & email address to contact for further information:
: RATS WG mailing list (rats@ietf.org)

Intended usage:
: COMMON

Restrictions on usage:
: none

Author/Change controller:
: IETF

Provisional registration:
: no

## application/eat-ucs+cbor Registration {#media-type-ucs-cbor}

{:compact}
Type name:
: application

Subtype name:
: eat-ucs+cbor

Required parameters:
: n/a

Optional parameters:
: "eat_profile" (EAT profile in string format.  OIDs must use the
  dotted-decimal notation.  The parameter value is case-insensitive.)

Encoding considerations:
: binary

Security considerations:
: {{Sections 3 and 7 of UCCS}}

Interoperability considerations:
: n/a

Published specification:
: {{&SELF}}

Applications that use this media type:
: Attesters, Verifiers, Endorsers and Reference-Value providers, Relying
  Parties that need to transfer EAT payloads over HTTP(S), CoAP(S), and other
  transports.

Fragment identifier considerations:
: n/a

Person & email address to contact for further information:
: RATS WG mailing list (rats@ietf.org)

Intended usage:
: COMMON

Restrictions on usage:
: none

Author/Change controller:
: IETF

Provisional registration:
: no

## application/eat-ucs+json Registration {#media-type-ucs-json}

{:compact}
Type name:
: application

Subtype name:
: eat-ucs+json

Required parameters:
: n/a

Optional parameters:
: "eat_profile" (EAT profile in string format.  OIDs must use the
  dotted-decimal notation.  The parameter value is case-insensitive.)

Encoding considerations:
: Same as {{JSON}}

Security considerations:
: {{Sections 3 and 7 of UCCS}}

Interoperability considerations:
: n/a

Published specification:
: {{&SELF}}

Applications that use this media type
: Attesters, Verifiers, Endorsers and Reference-Value providers, Relying
  Parties that need to transfer EAT payloads over HTTP(S), CoAP(S), and other
  transports.

Fragment identifier considerations:
: n/a

Person & email address to contact for further information:
: RATS WG mailing list (rats@ietf.org)

Intended usage:
: COMMON

Restrictions on usage:
: none

Author/Change controller:
: IETF

Provisional registration:
: no

## CoAP Content-Format Registrations

IANA is requested to register the following Content-Format numbers in the "CoAP
Content-Formats" sub-registry, within the "Constrained RESTful Environments
(CoRE) Parameters" Registry {{!IANA.core-parameters}}:

| Content-Type | Content Coding | ID | Reference |
| application/eat+cwt | - | TBD1 | {{&SELF}} |
| application/eat+jwt | - | TBD2 | {{&SELF}} |
| application/eat-bun+cbor | - | TBD3 | {{&SELF}} |
| application/eat-bun+json | - | TBD4 | {{&SELF}} |
| application/eat-ucs+cbor | - | TBD5 | {{&SELF}} |
| application/eat-ucs+json | - | TBD6 | {{&SELF}} |
{: align="left" title="New Content-Formats"}

TBD1..6 are to be assigned from the space 256..9999.

# Changelog

[^remove-sec]

[^remove-sec]: RFC editor: please remove this section

## -04 {#cl-04}

* Early IANA review

## -03 {#cl-03}

* Update references

## -02 {#cl-02}

* Update references
* Register +cwt SSS
  ([Issue#14](https://github.com/ietf-rats-wg/draft-eat-mt/issues/14))
* Move from eat-jwt to eat+jwt
  ([Issue#14](https://github.com/ietf-rats-wg/draft-eat-mt/issues/14))
* Move from eat-cwt to eat+cwt
  ([Issue#14](https://github.com/ietf-rats-wg/draft-eat-mt/issues/14))

## -01 {#cl-01}

* Rename `profile` to `eat_profile` for consistency with EAT
  ([Issue#4](https://github.com/ietf-rats-wg/draft-eat-mt/issues/4))

* The DEB acronym is gone: shorthand is now "bun" from bundle
  ([Issue#8](https://github.com/ietf-rats-wg/draft-eat-mt/issues/8))

* Incorporate editorial suggestions from Carl and Dave
  ([Issue#7](https://github.com/ietf-rats-wg/draft-eat-mt/issues/7),
  [Issue#9](https://github.com/ietf-rats-wg/draft-eat-mt/issues/9))

--- back

# Acknowledgments
{:unnumbered}

Thank you
Carl Wallace,
Carsten Bormann,
Dave Thaler,
Deb Cooley,
Éric Vyncke,
Francesca Palombini,
Jouni Korhonen,
Kathleen Moriarty,
Michael Richardson,
Murray Kucherawy,
Orie Steele,
Paul Howard,
Roman Danyliw
and
Tim Hollebeek
for your comments and suggestions.


