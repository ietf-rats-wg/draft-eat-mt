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

This memo defines a media type to be used for EAT.

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

This memo defines a media type to be used for EAT {{-EAT}} payloads.

## Requirements Language

This document uses the terms and concepts defined in {{-RATS-ARCH}}.

{::boilerplate bcp14-tagged}

# Examples

TODO

# Security Considerations {#seccons}

TODO

# IANA Considerations

[^to-be-removed]

[^to-be-removed]: RFC Editor: please replace RFC XXXX with this RFC number and remove this note.

## Media Types {#media-type}

IANA is requested to add the following media types to the
"Media Types" registry {{!IANA.media-types}}.

| Name | Template | Reference |
| eat+cbor | application/eat+cbor | RFCXXXX, {{media-type}} |
{: #new-media-type align="left" title="New Media Types"}

## application/eat+cbor Registration

{:compact}
Type name
: application

Subtype name
: eat+cbor

Required parameters
: n/a

Optional parameters
: "profile" (EAT profile in string format.  OIDs MUST use the dotted-decimal
notation.)

Encoding considerations:
: binary (CBOR data item)

Security considerations:
: {{seccons}} of RFC XXXX

Interoperability considerations:
: none

Published specification:
: {{media-type}} of RFC XXXX

Applications that use this media type
: Attesters, Verifiers, Endorsers and Reference-Value providers, Relying
Parties that need to transfer EAT payloads over HTTP(S), CoAP(S), and other
transports.

Fragment identifier considerations:
: The syntax and semantics of fragment identifiers is as specified for
  "application/cbor".  (At publication of RFC XXXX, there is no
  fragment identification syntax defined for "application/cbor".)

Person & email address to contact for further information:
: RATS WG mailing list (rats@ietf.org), or IETF Security Area (saag@ietf.org)

Intended usage:
: COMMON

Restrictions on usage:
: none

Author/Change controller:
: IETF

Provisional registration:
: no

## Content-Format

{:aside}
> (**Issue**: need a way to pass the profile information when using content
> formats.  A new CoAP option?)

IANA is requested to register a Content-Format number in the
"CoAP Content-Formats" sub-registry, within
the "Constrained RESTful Environments (CoRE) Parameters"
Registry {{!IANA.core-parameters}}, as follows:

| Content-Type | Content Coding | ID | Reference |
| application/eat+cbor | - | TBD1 | RFC XXXX |
{: align="left" title="New Content-Formats"}

TBD1 is to be assigned from the space 256..999.

In the registry as defined by {{Section 12.3 of -COAP}} at the
time of writing, the column "Content-Type" is called "Media
type" and the column "Content Coding" is called "Encoding".

--- back

# Acknowledgments
{:unnumbered}

TODO
