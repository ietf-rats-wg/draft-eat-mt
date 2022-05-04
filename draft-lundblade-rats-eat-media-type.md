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
 - ins: A. Fuchs
   name: Andreas Fuchs
   org: Fraunhofer Institute for Secure Information Technology
   abbrev: Fraunhofer SIT
   email: andreas.fuchs@sit.fraunhofer.de
   street: Rheinstrasse 75
   code: '64295'
   city: Darmstadt
   country: Germany
 - ins: T. Fossati
   name: Thomas Fossati
   organization: Arm Limited
   email: thomas.fossati@arm.com

normative:
  I-D.ietf-rats-eat: EAT
  RFC7252: COAP

informative:
  I-D.ietf-rats-architecture: RATS-ARCH

--- abstract

Payloads used in Remote Attestation Procedures may require an associated media
type for their conveyance, for example when used in RESTful APIs.

This memo defines a media type to be used for Entity Attestation Tokens (EAT).

--- middle

# Introduction

Payloads used in Remote Attestation Procedures {{-RATS-ARCH}} may require an
associated media type for their conveyance, for example when used in RESTful
APIs.

~~~ aasvg
.----.      .----------.          .----------.
| RP |      | Attester |          | Verifier |
'-+--'      '----+-----'          '-----+----'
  |              |                      |
  |              |     EAT(Evidence)    |
  |              +--------------------->|
  |              |     EAT(AR)          |
  |              |<---------------------+
  |              |                      |
  |              |                      |
  |    EAT(AR)   |                      |
  |<-------------+                      |
  |              |                      |
~~~
{: #fig-api artwork-align="center"
   title="Conveying RATS conceptual messages in REST APIs using EAT"}

This memo defines a media type to be used for Entity Attestation Token (EAT)
{{-EAT}} payloads.

## Requirements Language

This document uses the terms and concepts defined in {{-RATS-ARCH}}.

{::boilerplate bcp14-tagged}

# A Media Type Parameter for EAT Profiles

EAT is an open and flexible format.  To improve interoperability, {{Section 7
of -EAT}} defines the concept of EAT profiles.  Profiles are used to constrain
the parameters that producers and consumers of a specific EAT profile need to
understand in order to interoperate.  For example: the number and type of
claims, which serialisation format, the supported signature schemes, etc.  EATs
carry an in-band profile identifier using the Profile claim (see {{Section 3.20
of -EAT}}).  The value of the Profile claim is either an OID or a URI.

The media type(s) defined in this document include an optional parameter that
can be used to mirror the profile claim of the transported EAT.  This can be
used as a routing hint for API front-ends to allow dispatching payloads
directly to the profile-specific processor.  Note that the profile claim can be
deeply nested (it is typically an element of a map carried in the CBOR-encoded
payload of a COSE Sign1 object), which makes extracting it from the EAT a
cumbersome operation.

Exposing the profile in a media type parameter provides a finer-grained and
consistent type system to the extensible class of EAT profiles.  The
expectation being that a certain EAT profile automatically obtains a media type
derived from the base `application/eat+cbor` and/or `application/eat+json` by
populating the `profile` parameter with the right OID or URL.

# Examples

The example in {{fig-ex1}} illustrates the usage of EAT media types in evidence
and attestation results.  In both cases the profile is carried as an explicit
parameter.

~~~
>> Request:

  POST /challenge-response/v1/session/1234567890
  Host: verifier.example
  Accept: application/vnd.example.challenge-response-session+json
  Content-Type: application/eat+cbor; profile=tag:evidence.example,2022

  ... CBOR-encoded EAT w/ profile=tag:evidence.example,2022 ...

<< Response:

  HTTP/1.1 200 OK
  Content-Type: application/vnd.example.challenge-response-session+json

  {
    "nonce": "MTIzNDU2Nzg5MDEyMzQ1Njc4OTAxMjM0NTY3ODkwMTI=",
    "expiry": "2030-10-12T07:20:50.52Z",
    "accepting": [
      "application/eat+json; profile=https://attestation.org/1.0.0",
      "application/eat+cbor; profile=tag:evidence.example,2022",
      "application/eat+cbor; profile=tag:evidence.example,2020"
    ],
    "state": "complete",
    "evidence": {
      "type": "application/eat+cbor; profile=tag:evidence.example,2022",
      "value": "0oRDoQEmo...7HJp91mLE="
    },
    "attestation-results": {
      "type": "application/eat+cbor; profile=tag:ar4si.example,2021",
      "value": "0oRDoQEmo...1ormzZLxk="
    }
  }
~~~
{: #fig-ex1 artwork-align="center"
   title="Challenge-Response Verification API"}

# Security Considerations {#seccons}

TODO

# IANA Considerations

[^to-be-removed]

[^to-be-removed]: RFC Editor: please replace RFCthis with this RFC number and remove this note.

## Media Types {#media-type}

IANA is requested to add the following media types to the
"Media Types" registry {{!IANA.media-types}}.

| Name | Template | Reference |
| eat+cbor | application/eat+cbor | RFCthis, {{media-type}} |
| eat+json | application/eat+json | RFCthis, {{media-type}} |
{: #new-media-type align="left" title="New Media Types"}

## application/eat+cbor Registration

{:compact}
Type name:
: application

Subtype name:
: eat+cbor

Required parameters:
: n/a

Optional parameters:
: "profile" (EAT profile in string format.  OIDs MUST use the dotted-decimal
notation.)

Encoding considerations:
: binary

Security considerations:
: {{seccons}} of RFCthis

Interoperability considerations:
: n/a

Published specification:
: {{media-type}} of RFCthis

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

## application/eat+json Registration

{:compact}
Type name:
: application

Subtype name:
: eat+json

Required parameters:
: n/a

Optional parameters:
: "profile" (EAT profile in string format.  OIDs MUST use the dotted-decimal
notation.)

Encoding considerations:
: 8bit

Security considerations:
: {{seccons}} of RFCthis

Interoperability considerations:
: n/a

Published specification:
: {{media-type}} of RFCthis

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
| application/eat+cbor | - | TBD1 | RFCthis |
| application/eat+json | - | TBD2 | RFCthis |
{: align="left" title="New Content-Formats"}

TBD1 and TBD2 are to be assigned from the space 256..999.

In the registry as defined by {{Section 12.3 of -COAP}} at the
time of writing, the column "Content-Type" is called "Media
type" and the column "Content Coding" is called "Encoding".

--- back

# Acknowledgments
{:unnumbered}

TODO
