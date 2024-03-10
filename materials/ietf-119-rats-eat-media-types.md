slidenumbers: true
autoscale: true

# EAT Media Types

## [draft-ietf-rats-eat-media-type-05](https://datatracker.ietf.org/doc/draft-ietf-rats-eat-media-type/05)

### RATS WG, IETF 119, Brisbane

---

# Quick recap

---

# New Media Types

| Name | Template |
| ---- | -------- |
| EAT CWT | `application/eat+cwt` |
| EAT JWT | `application/eat+jwt` |
| Detached EAT Bundle CBOR | `application/eat-bun+cbor` |
| Detached EAT Bundle JSON | `application/eat-bun+json` |
| EAT UCCS | `application/eat-ucs+cbor` |
| EAT UJCS | `application/eat-ucs+json` |

---

# New Structured Syntax Suffix

`+cwt`, to indicate that the media type is encoded as a CWT.

(Parallel to `+jwt` defined in [§7.2 of RFC8417](https://www.rfc-editor.org/rfc/rfc8417.html#section-7.2))

---

# WGLC recap

---

* No on-list feedback (somewhat expected given the purely bureaucratic nature of the document)

* Giri's [[Issue#17](https://github.com/ietf-rats-wg/draft-eat-mt/issues/17)] and [[PR#18]](https://github.com/ietf-rats-wg/draft-eat-mt/pull/18)
  * _"[d]iscussion of when it might be appropriate *not* to use the media types [...]"_
  * Please chime in

---

# Next Steps

* Find document shepherd (Ionuț Mihalcea volunteered)

* Request early allocations

^ Unblock PSA and other documents that depend on EAT (e.g., EAR)

* Send to AD/IESG

^ It makes a lot of sense to have this document published alongside EAT

---

# Early Allocation Questionnaire

:white_check_mark: The code points must be from a space designated as "RFC Required", "IETF Review", or "Standards Action". Additionally, requests for early assignment of code points from a "Specification Required" registry are allowed if the specification will be published as an RFC.

^ yes, as we are requesting from the standard tree

:white_check_mark: The format, semantics, processing, and other rules related to handling the protocol entities defined by the code points (henceforth called "specifications") must be adequately described in an Internet-Draft.

^ yes, draft-ietf-rats-eat-media-type is that Internet-Draft

:white_check_mark: The specifications of these code points must be stable; i.e., if there is a change, implementations based on the earlier and later specifications must be seamlessly interoperable.

^ yes, we have no reason to think they are not stable

:question: The Working Group chairs and Area Directors (ADs) judge that there is sufficient interest in the community for early (pre-RFC) implementation and deployment, or that failure to make an early allocation might lead to contention for the code point in the field.
