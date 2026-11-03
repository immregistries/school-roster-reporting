# Status and Scope

> **Status:** Draft 0.1.0, a working draft for community discussion. Nothing in
> this document is a published standard, an HL7 ballot, an implementation
> guide, or a final decision. Its working decisions are starting points that
> the community has not yet reviewed, and any of them may change.

## In brief

Schools know which school a student attends, the student's grade, and the
identifier an education agency assigned to the student. Immunization
information systems (IIS) that support school immunization work need the same
roster information to associate a patient with a school. Approaches seen in
practice, such as manual entry in an IIS user interface, separate flat-file
uploads, and school and grade written into free-text notes fields of HL7
messages, are slow, leave gaps between updates, or are not reliably
machine-readable. National HL7 v2 immunization guidance does not say how a
school-side system reports roster information.

This document sets out a candidate approach for reporting roster information
from school-side systems to an IIS with HL7 v2.5.1: a small, explicit extension
that adds roster information to the VXU defined by the national immunization
messaging guide. It also frames the questions the approach has not yet answered, including how
enrollment and grade periods and the school year are represented.

## What this topic covers

- The use cases and update semantics for reporting a student's school
  association, grade level, and education-assigned student identifier to an
  IIS.
- A transport-independent information model for that roster information.
- A candidate HL7 v2.5.1 message profile, with synthetic examples.
- Acknowledgment behavior and conformance expectations at the interface
  between the sender and the IIS.

## What this topic does not cover

This document defines how roster information is communicated. It does not
define:

- school data exchange more broadly, including returning immunization
  histories or compliance status to schools, or querying an IIS for school or
  grade (see [School Data Exchange](#related-work));
- how an IIS matches patients or stores, applies, or keeps a history of roster
  information;
- how a school-side system produces source extracts or converts them to HL7;
- who may send or receive roster information, or how exchanged information is
  used, disclosed, or protected;
- the meaning or representation of vaccine exemptions, which belong to the
  [Vaccine Exemptions and Deferrals](#related-work) topic; or
- transport and authentication. These are defined nationally, and roster
  messages use the transport and authentication an IIS already uses for VXU
  submissions.

This document is not an HL7 implementation guide or conformance profile.

## Related work

| Work | Relationship to this work |
|---|---|
| [HL7 Version 2.5.1 Implementation Guide for Immunization Messaging, Release 1.5, and addendum](https://www.cdc.gov/iis/technical-guidance/hl7.html) | Base guide. Defines VXU and the `Z22` profile the candidate roster profile extends. |
| [AIRA Guidance for HL7 Acknowledgement Messages to Support Interoperability](https://repository.immregistries.org/resource/guidance-for-hl7-acknowledgement-messages-to-support-interoperability/) | Current acknowledgment guidance applied in [Acknowledgments](#acknowledgments). |
| [AIRA Acknowledgment (ACK) Messaging Updated Specifications and Guidance](https://repository.immregistries.org/resource/tab-resources/) | Updated acknowledgment direction the candidate profile must remain compatible with. |
| [School Data Exchange, InteropHub topic 37](https://informatics.immregistries.org/hub/es/topic/37) | Broader exchange between IIS and schools. This topic is one part of it. |
| [Vaccine Exemptions and Deferrals Topic Reference](https://immregistries.github.io/vaccine-exemptions-and-deferrals/latest/) | Defines exemption information. Its relationship to roster messages is open ([OQ-11](#open-questions)). |
| [HL7 v2.5.1 School Roster Reporting, InteropHub topic 77](https://informatics.immregistries.org/hub/es/topic/77) | Community coordination, meetings, and discussion for this topic. |
