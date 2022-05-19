---
v: 3

title: EAT Media Types
abbrev: EAT Media Types
docname: draft-lundblade-rats-eat-media-type-latest
category: std

ipr: trust200902
area: Security
workgroup: RATS
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
   email: henk.birkholz@sit.fraunhofer.de
   street: Rheinstrasse 75
   code: '64295'
   city: Darmstadt
   country: Germany
 - ins: T. Fossati
   name: Thomas Fossati
   organization: arm
   email: thomas.fossati@arm.com

normative:
  EAT: I-D.ietf-rats-eat
  UCCS: I-D.ietf-rats-uccs
  CoAP: RFC7252

informative:
  RATS-Arch: I-D.ietf-rats-architecture

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
.----.                    .----------.                .----------.
| RP |                    | Attester |                | Verifier |
'-+--'                    '----+-----'                '-----+----'
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
manifest themselves.

## Requirements Language

This document uses the terms and concepts defined in {{RATS-Arch}}.

{::boilerplate bcp14-tagged}

# EAT Types

{{fig-eat-types}} illustrates the six EAT wire formats and how they relate to
each other.  {{EAT}} defines four of them (CWT, JWT and DEB in its JSON and
CBOR flavours), whilst {{UCCS}} defines the remaining two: UCCS and UJCS.

~~~ aasvg
{::include misc/EAT-pieces.txt}
~~~
{: #fig-eat-types artwork-align="center"
   title="EAT Types"}

# A Media Type Parameter for EAT Profiles

EAT is an open and flexible format.  To improve interoperability, {{Section 7
of EAT}} defines the concept of EAT profiles.  Profiles are used to constrain
the parameters that producers and consumers of a specific EAT profile need to
understand in order to interoperate.  For example: the number and type of
claims, which serialisation format, the supported signature schemes, etc.  EATs
carry an in-band profile identifier using the `eat_profile` claim (see
{{Section 3.20 of EAT}}).  The value of the `eat_profile` claim is either an
OID or a URI.

The media type(s) defined in this document include an optional `profile`
parameter that can be used to mirror the `eat_profile` claim of the transported
EAT.  Exposing the EAT profile at the API layer allows API routers to dispatch
payloads directly to the profile-specific processor without having to snoop
into the request bodies.  This design also provides a finer-grained and
scalable type system that matches the inherent extensibility of EAT.  The
expectation being that a certain EAT profile automatically obtains a media type
derived from the base (e.g., `application/eat-cwt)` by populating the `profile`
parameter with the corresponding OID or URL.

# Examples

The example in {{fig-rest-req}} illustrates the usage of EAT media types for
transporting attestation evidence.

~~~ http-message
POST /challenge-response/v1/session/1234567890 HTTP/1.1
Host: verifier.example
Accept: application/eat-cwt; profile=tag:ar4si.example,2021
Content-Type: application/eat-cwt; profile=tag:evidence.example,2022

[ CBOR-encoded EAT w/ profile=tag:evidence.example,2022 ]
~~~
{: #fig-rest-req title="Example REST Verification API (request)"}

The example in {{fig-rest-rsp}} illustrates the usage of EAT media types for
transporting attestation results.

~~~ http-message
HTTP/1.1 200 OK
Content-Type: application/eat-cwt; profile=tag:ar4si.example,2021

[ CBOR-encoded EAT w/ profile=tag:ar4si.example,2021 ]
~~~
{: #fig-rest-rsp title="Example REST Verification API (response)"}

In both cases the profile is carried as an explicit parameter.

# Security Considerations {#seccons}

The security consideration of {{EAT}} and {{UCCS}} apply in full.

# IANA Considerations

[^to-be-removed]

[^to-be-removed]: RFC Editor: please replace {{&SELF}} with this RFC number and remove this note.

## Media Types {#media-type}

IANA is requested to add the following media types to the
"Media Types" registry {{!IANA.media-types}}.

| Name | Template | Reference |
| EAT CWT | application/eat-cwt | {{&SELF}}, {{media-type-eat-cwt}} |
| EAT JWT | application/eat-jwt | {{&SELF}}, {{media-type-eat-jwt}} |
| EAT CBOR DEB | application/eat-deb+cbor | {{&SELF}}, {{media-type-deb-cbor}} |
| EAT JSON DEB | application/eat-deb+json | {{&SELF}}, {{media-type-deb-json}} |
| EAT UCCS | application/eat-ucs+cbor | {{&SELF}}, {{media-type-ucs-cbor}} |
| EAT UJCS | application/eat-ucs+json | {{&SELF}}, {{media-type-ucs-json}} |
{: #new-media-type align="left" title="New Media Types"}

## application/eat-cwt Registration {#media-type-eat-cwt}

{:compact}
Type name:
: application

Subtype name:
: eat-cwt

Required parameters:
: n/a

Optional parameters:
: "profile" (EAT profile in string format.  OIDs MUST use the dotted-decimal
  notation.  The parameter value is case-insensitive.)

Encoding considerations:
: binary

Security considerations:
: {{seccons}} of {{&SELF}}

Interoperability considerations:
: n/a

Published specification:
: {{media-type}} of {{&SELF}}

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
: <cref>maybe</cref>

## application/eat-jwt Registration {#media-type-eat-jwt}

{:compact}
Type name:
: application

Subtype name:
: eat-jwt

Required parameters:
: n/a

Optional parameters:
: "profile" (EAT profile in string format.  OIDs MUST use the dotted-decimal
  notation.  The parameter value is case-insensitive.)

Encoding considerations:
: 8bit

Security considerations:
: {{seccons}} of {{&SELF}}

Interoperability considerations:
: n/a

Published specification:
: {{media-type}} of {{&SELF}}

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
: <cref>maybe</cref>

## application/eat-deb+cbor Registration {#media-type-deb-cbor}

{:compact}
Type name:
: application

Subtype name:
: eat-deb+cbor

Required parameters:
: n/a

Optional parameters:
: "profile" (EAT profile in string format.  OIDs MUST use the dotted-decimal
  notation.  The parameter value is case-insensitive.)

Encoding considerations:
: binary

Security considerations:
: {{seccons}} of {{&SELF}}

Interoperability considerations:
: n/a

Published specification:
: {{media-type}} of {{&SELF}}

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
: <cref>maybe</cref>

## application/eat-deb+json Registration {#media-type-deb-json}

{:compact}
Type name:
: application

Subtype name:
: eat-deb+json

Required parameters:
: n/a

Optional parameters:
: "profile" (EAT profile in string format.  OIDs MUST use the dotted-decimal
  notation.  The parameter value is case-insensitive.)

Encoding considerations:
: Same as {{!RFC7159}}

Security considerations:
: {{seccons}} of {{&SELF}}

Interoperability considerations:
: n/a

Published specification:
: {{media-type}} of {{&SELF}}

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
: <cref>maybe</cref>

## application/eat-ucs+cbor Registration {#media-type-ucs-cbor}

{:compact}
Type name:
: application

Subtype name:
: eat-ucs+cbor

Required parameters:
: n/a

Optional parameters:
: "profile" (EAT profile in string format.  OIDs MUST use the dotted-decimal
  notation.  The parameter value is case-insensitive.)

Encoding considerations:
: binary

Security considerations:
: {{seccons}} of {{&SELF}}

Interoperability considerations:
: n/a

Published specification:
: {{media-type}} of {{&SELF}}

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
: <cref>maybe</cref>

## application/eat-ucs+json Registration {#media-type-ucs-json}

{:compact}
Type name:
: application

Subtype name:
: eat-ucs+json

Required parameters:
: n/a

Optional parameters:
: "profile" (EAT profile in string format.  OIDs MUST use the dotted-decimal
  notation.  The parameter value is case-insensitive.)

Encoding considerations:
: Same as {{!RFC7159}}

Security considerations:
: {{seccons}} of {{&SELF}}

Interoperability considerations:
: n/a

Published specification:
: {{media-type}} of {{&SELF}}

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
: <cref>maybe</cref>

## Content-Format

{:aside}
> (**Issue**: need a way to pass the profile information when using content
> formats.  A new CoAP option?)

IANA is requested to register a Content-Format number in the
"CoAP Content-Formats" sub-registry, within
the "Constrained RESTful Environments (CoRE) Parameters"
Registry {{!IANA.core-parameters}}, as follows:

| Content-Type | Content Coding | ID | Reference |
| application/eat-cwt | - | TBD1 | {{&SELF}} |
| application/eat-jwt | - | TBD2 | {{&SELF}} |
| application/eat-deb+cbor | - | TBD3 | {{&SELF}} |
| application/eat-deb+json | - | TBD4 | {{&SELF}} |
| application/eat-ucs+cbor | - | TBD5 | {{&SELF}} |
| application/eat-ucs+json | - | TBD6 | {{&SELF}} |
{: align="left" title="New Content-Formats"}

TBD1..6 are to be assigned from the space 256..999.

In the registry as defined by {{Section 12.3 of CoAP}} at the time of writing,
the column "Content-Type" is called "Media type" and the column "Content
Coding" is called "Encoding".  [^remove]

[^remove]: RFC editor: please remove this paragraph.

--- back

# Acknowledgments
{:unnumbered}

TODO
