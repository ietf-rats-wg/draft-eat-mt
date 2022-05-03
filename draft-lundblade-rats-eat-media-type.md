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

Evidence payloads used in Remote Attestation Procedures may
require an associated media type for their conveyance, for
example when used in RESTful APIs.

This memo defines a media type to be used for EAT.

--- middle

# Introduction


~~~ aasvg
+----------+           +----------+
| Attester |           | Verifier |
+----+-----+           +-----+----+
     |                    |
     |        EAT         |
     |------------------> |
     |         AR         |
     | <----------------- |
     |                    |
~~~
{: #fig-api artwork-align="center"
   title="EAT in REST API"}

## Requirements Language

This document uses the terms and concepts defined in {{-RATS-ARCH}}.

{::boilerplate bcp14-tagged}

# Security Considerations {#seccons}

TODO

# IANA Considerations

[^to-be-removed]

[^to-be-removed]: RFC Editor: please replace RFC XXXX with this RFC number and remove this note.

## Media Type

IANA is requested to add the following Media-Types to the
"Media Types" registry {{!IANA.media-types}}.

<cref>See CoRIM for profile</cref>

| Name                         | Template                                 | Reference              |
| eat+cbor | application/eat+cbor | RFCXXXX, {{media-type}} |
| eat+json | application/eat+json | RFCXXXX, {{media-type}} |
{: #new-media-type align="left" title="New Media Types"}

{:compact}
Type name:
: application

Subtype name:
: eat+cbor

Required parameters:
: none

Optional parameters:
: none

Encoding considerations:
: binary (CBOR data item)

Security considerations:
: {{seccons}} of RFC XXXX

Interoperability considerations:
: none

Published specification:
: {{media-type}} of RFC XXXX

Applications that use this media type:
: Clients and servers in the Internet of Things

Fragment identifier considerations:
: The syntax and semantics of fragment identifiers is as specified for
  "application/cbor".  (At publication of RFC XXXX, there is no
  fragment identification syntax defined for "application/cbor".)

Person & email address to contact for further information:
: CoRE WG mailing list (core@ietf.org),
  or IETF Applications and Real-Time Area (art@ietf.org)

Intended usage:
: COMMON

Restrictions on usage:
: none

Author/Change controller:
: IETF

Provisional registration:
: no

## Content-Format

IANA is requested to register a Content-Format number in the
"CoAP Content-Formats" sub-registry, within
the "Constrained RESTful Environments (CoRE) Parameters"
Registry {{!IANA.core-parameters}}, as follows:

| Content-Type                             | Content Coding |
ID   | Reference |
| application/eat+cbor | -              | TBD1 | RFC XXXX  |
{: align="left" title="New Content-Format"}

TBD1 is to be assigned from the space 256..999.

In the registry as defined by {{Section 12.3 of -COAP}} at the
time of writing, the column "Content-Type" is called "Media
type" and the column "Content Coding" is called "Encoding".

--- back

# Acknowledgments
{:unnumbered}

Someone
