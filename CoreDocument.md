<!--
GENERATED FILE. DO NOT EDIT DIRECTLY.

The authoritative source is in content/. Regenerate this reading copy with:
    python process/tools/build_core_markdown.py --assembly SchoolRosterReporting.qmd
-->

# HL7 v2.5.1 School Roster Reporting

*A Topic Reference for community discussion*

> This is a generated reading copy for GitHub. For review and editing, use the
> [canonical section files](content/README.md).

---

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

---

# Use Cases and Exchange Semantics

## Actors

| Actor | Role |
|---|---|
| School-side system | Holds roster information for one or more schools and produces, or supplies the data for, the HL7 message. It may be a school health system, a student information system, or an intermediary that converts their extracts. |
| Submitting organization | The organization accountable for the submission, such as a school district or other local education agency, or a single school. One submission may cover students from several schools. |
| School | The individual school a student is associated with. It is distinct from the submitting organization ([Organizations](#submitting-organization-and-school)). |
| IIS | Receives the message, returns an acknowledgment, and decides internally how to match the patient and apply the roster information. |

A school-side system describes the person as a **student**; the IIS and the
HL7 message describe the same person as a **patient**. The message carries the
student's roster information as information about the patient.

## Exchange boundary

1. A school-side system determines that a student's roster information should
   be reported.
2. It constructs a roster message ([Message Profile](#message-profile))
   and sends it over the IIS's existing submission interface.
3. The IIS parses and validates the message and returns an acknowledgment
   ([Acknowledgments](#acknowledgments)).
4. Beyond the acknowledgment, the IIS matches the patient and applies the
   roster information by its own rules, which this document does not define.

> **To be drafted:** a context diagram of the actors and a sequence diagram of
> steps 1 to 4.

## Use cases

Each use case states what the exchange must convey. How the message conveys
it is open where shown.

| ID | Use case | What the exchange must convey | Open |
|---|---|---|---|
| UC-1 | A student is reported for the first time | Patient identity, school, grade, and education-assigned identifier | — |
| UC-2 | Current roster information is re-sent unchanged, for example on a regular schedule | That nothing changed; the receiver must not create a duplicate association | [OQ-7](#open-questions), [OQ-9](#open-questions) |
| UC-3 | A student changes grade, by promotion or mid-year change | The new grade and when it applies | [OQ-8](#open-questions) |
| UC-4 | A student transfers to another school | The new school; whether and when the earlier association ends | [OQ-7](#open-questions), [OQ-8](#open-questions) |
| UC-5 | A previously reported value was wrong | That the earlier value is replaced, not that an event occurred | [OQ-9](#open-questions) |
| UC-6 | A student leaves a school without a known new school | That the association has ended | [OQ-7](#open-questions), [OQ-9](#open-questions) |
| UC-7 | The education-assigned identifier is added or corrected | The identifier and its assigning authority | [OQ-5](#open-questions), [OQ-9](#open-questions) |
| UC-8 | A message is duplicated, delayed, or arrives out of order | Enough to let the receiver recognize it | [OQ-9](#open-questions) |
| UC-9 | Roster information is reported for a new school year | The school year the information applies to | [OQ-8](#open-questions) |

## What a message asserts

The use cases need a shared answer to what one roster message means. Three
alternatives are under discussion ([OQ-7](#open-questions)):

- **Current state.** The message states the student's school and grade as of
  the message, replacing what the sender reported before. Simple to send and
  safe to repeat, but it cannot express when a change took effect or that an
  association ended.
- **Event.** The message reports a change, such as an enrollment, transfer, or
  withdrawal. It expresses history, but repeated, missing, or out-of-order
  messages change the result.
- **Bounded association.** The message states an association with a start and,
  when known, an end date, optionally for a school year. It expresses history
  and is safe to repeat, but needs more data from senders and more rules at
  receivers.

One earlier design limited a first implementation to current state, with no
effective dates, school year, or ending of associations, leaving exceptions to
manual correction in the IIS. That was a choice for one implementation's first
scope, not a decision of this topic.

## Periods and the school year {#periods-and-school-year}

Enrollment and grade change at known times, and many school-related IIS
functions are organized by school year. A message sent in the summer, or a
roster re-sent at the start of a year, is ambiguous unless the period it
describes is clear. The elements involved are in the
[information model](#enrollment-and-grade-periods); how they are carried is
[OQ-8](#open-questions).

## Receipt, acknowledgment, matching, and application

These are different outcomes, and a sender must not infer a later one from an
earlier one ([WD-7](#working-decisions)).

| Outcome | Meaning | Visible to the sender |
|---|---|---|
| Delivered | The interface accepted the transmission. | Through the transport |
| Acknowledged | The IIS processed the message to the level its acknowledgment reports. | Through the ACK |
| Matched | The IIS associated the message with a patient record. | Only if the IIS reports it |
| Applied | The IIS stored or used the roster information. | Only if the IIS reports it |

An existing IIS query interface may show that a patient is known to the IIS,
but standard query responses do not return school or grade, so they are not
evidence that roster information was applied.

---

# Information Model

The information model is independent of HL7. The
[message profile](#message-profile) maps each element to
the message. Cardinality is a candidate and open where shown.

| Element | Meaning | Candidate cardinality | Open |
|---|---|---|---|
| Patient identity | Name, date of birth, sex, and other demographics the IIS needs to identify the patient | Required; content open | [OQ-6](#open-questions) |
| Sender's patient identifier | The identifier the school-side system uses for the student | Required | [OQ-6](#open-questions) |
| Education-assigned student identifier | An identifier assigned to the student by an education agency, with its type and assigning authority | Required when known | [OQ-5](#open-questions) |
| Submitting organization | The organization accountable for the submission | Required | [OQ-2](#open-questions) |
| School | The individual school the student is associated with | Required | [OQ-2](#open-questions), [OQ-3](#open-questions) |
| Grade level | The student's grade, coded | Required when known | [OQ-4](#open-questions) |
| Enrollment period | Start and end of the student's association with the school | Open | [OQ-8](#open-questions) |
| Grade period | Start and end of the student's grade | Open | [OQ-8](#open-questions) |
| School year | The school year the information applies to | Open | [OQ-8](#open-questions) |
| As-of date | The date the sender asserts the information is current | Required | [OQ-7](#open-questions) |
| Provenance | Sending application and a unique message identifier | Required | — |

## Submitting organization and school

The submitting organization and the school are separate elements
([WD-4](#working-decisions)). A district may submit one extract covering many
schools, so the submitting organization, or the connection it uses, does not
identify each student's school. Each student's school must be identified in
that student's message.

## School identifier

A school is identified by a code from a named identifier system, such as a
state or national education directory, not by name alone. Names change and
are spelled inconsistently. Portable rules for school identifiers, including
closed, merged, renamed, and private schools and schools outside the IIS's
jurisdiction, are [OQ-3](#open-questions).

> **To be drafted:** candidate school identifier systems and how a namespace
> is expressed for each.

## Grade level

Grade is a coded value from one agreed vocabulary. Grades 1 to 12 map simply;
the vocabulary must also cover at least pre-kindergarten, kindergarten
variants, ungraded students, other, and unknown ([OQ-4](#open-questions)).
A sender maps its local grade values to the agreed vocabulary.

Candidate vocabularies to evaluate, with no choice made, are the
[Common Education Data Standards (CEDS)](https://ceds.ed.gov/) Grade Level
option set and the Ed-Fi Data Standard
[`GradeLevelDescriptor`](https://github.com/Ed-Fi-Alliance-OSS/Ed-Fi-Standard/blob/main/Descriptors/GradeLevelDescriptor.xml).

> **To be drafted:** a candidate grade vocabulary table comparing these
> vocabularies.

## Education-assigned student identifier

The education-assigned identifier supplements, and does not replace, the
patient identifiers the sender already uses ([WD-5](#working-decisions)). Its
assigning authority says who assigned it; its identifier type says what kind of
identifier it is. Both are needed because the same number may be meaningful
only within its assigning authority. Only identifiers assigned by an education
agency belong here; school- or vendor-local student numbers are the sender's
patient identifier.

## Enrollment and grade periods

An enrollment period and a grade period each have a start date and, when
known, an end date. A school year is a named period, such as 2026–2027, whose
dates vary by school. Whether a message carries periods, a school year, both,
or neither depends on what a message asserts ([OQ-7](#open-questions)) and is
[OQ-8](#open-questions).

## Missing, corrected, and cleared values

The model must distinguish:

- a value that is **not sent**, which leaves what the receiver holds unchanged;
- a value that is **unknown**, sent as an explicit unknown code where the
  vocabulary has one;
- a value that **replaces** a previously reported value; and
- a value that is **cleared**, meaning the sender asserts that no value now
  applies.

How each is expressed, and whether clearing is allowed, is
[OQ-9](#open-questions).

---

# Candidate HL7 v2.5.1 Message Profile {#message-profile}

This chapter maps the [information model](#information-model) to an HL7
v2.5.1 VXU based on the national immunization messaging guide, Release 1.5
([WD-2](#working-decisions)). It is a candidate, not a conformance profile.
Each row is labeled:

- **Base:** required or allowed by the Release 1.5 `Z22` VXU profile as it
  stands.
- **Candidate:** proposed for the roster profile; not in the base guide, or a
  roster-specific use of a base element.
- **Unresolved:** no candidate yet.

## Message structure

Roster information is an addition to the `VXU^V04^VXU_V04` message
([WD-9](#working-decisions)). Any VXU may carry it, alongside whatever else a
VXU reports about the patient, including immunizations.

When a message reports roster information and no immunization, as a
school-side system's messages usually will, it is a roster-only VXU
([WD-3](#working-decisions)). The base profile requires an order group even
when no vaccine is reported, so a roster-only VXU carries one `ORC`/`RXA`
group that records no administration, using CVX `998` (no vaccine
administered). Roster observations are `OBX` segments in that group. Where
roster observations go in a VXU that also reports immunizations is
[OQ-13](#open-questions).

## Field mapping

| Location | Content | Example | Label | Open |
|---|---|---|---|---|
| `MSH-4` | Submitting organization | `EXAMPLE-LEA-0123` | Candidate | [OQ-2](#open-questions) |
| `MSH-10` | Unique message identifier | `ROSTER-000002` | Base | [OQ-9](#open-questions) |
| `MSH-21` | Profile identifier | `Z22^CDCPHINVS` | Base; roster identifier unresolved | [OQ-1](#open-questions) |
| `MSH-22` | School | `EXAMPLE-SCHOOL-0456` | Candidate | [OQ-2](#open-questions), [OQ-3](#open-questions) |
| `PID-3`, first repetition | Sender's patient identifier | `SIS-000001^^^EXAMPLE-SIS^MR` | Base | [OQ-6](#open-questions) |
| `PID-3`, another repetition | Education-assigned student identifier | `900000001^^^EXAMPLE-SEA^PI` | Candidate | [OQ-5](#open-questions) |
| `PID-5`, `PID-6`, `PID-7`, `PID-8`, and other `PID` fields | Patient identity | | Base | [OQ-6](#open-questions) |
| `ORC-3` | Filler order number; `9999` when nothing was administered | `9999^EXAMPLE-SIS` | Base | — |
| `RXA-3` | Date of the roster report | `20261026` | Candidate | [OQ-7](#open-questions) |
| `RXA-5` | No vaccine administered | `998^No vaccine administered^CVX` | Base | — |
| `RXA-6` | Amount `999` with CVX `998` | `999` | Base | — |
| `RXA-20` | Not administered | `NA` | Base | — |
| `RXA-21` | Action code | `A` | Base; roster meaning unresolved | [OQ-9](#open-questions) |
| `OBX-3` | Grade observation, LOINC `64990-5` | `64990-5^Grade in school, if applicable^LN` | Candidate | [OQ-4](#open-questions) |
| `OBX-2`, `OBX-5` | Coded grade value | `CE`; `3^Third grade^L` | Candidate; vocabulary unresolved | [OQ-4](#open-questions) |
| `OBX-14` | As-of date of the grade | `20261026` | Candidate | [OQ-7](#open-questions) |
| — | Enrollment period, grade period, school year | | Unresolved | [OQ-8](#open-questions) |

Notes on the candidate rows:

- [LOINC `64990-5`](https://loinc.org/64990-5) has the long common name
  "Grade in school, if applicable" and the short name "School grade". It
  originated as a PhenX survey item, and LOINC attaches no answer list to it
  ([OQ-4](#open-questions)).

- `MSH-22` (sending responsible organization) carries the school in its first
  component, the simplest form an IIS can look up. A fully populated `XON`,
  with a name, assigning authority, and identifier, is an alternative. Whether
  the school belongs in `MSH-22` at all, given that the field describes the
  organization accountable for the message, is part of
  [OQ-2](#open-questions).
- `PI` is an existing HL7 Table 0203 identifier type that the base profile
  permits. It does not itself say "student"; the assigning authority does. A
  dedicated student identifier type would need an HL7 vocabulary request
  ([OQ-5](#open-questions)). `STU` appears in an example below only as a
  placeholder; it is not an HL7 code.
- `MR` is not used for the education-assigned identifier because it is not a
  medical record number.
- The grade code `3` with coding system `L` is a placeholder local value until
  a vocabulary is chosen. It is not a proposed national code.

## Examples

All people, identifiers, organizations, and dates are synthetic. Segments are
shown on separate lines; on the wire they are separated by carriage returns.
Results are from the NIST-based immunization validator, `Z22` VXU send
profile, run on 2026-11-03. Warnings for empty required-but-may-be-empty
elements are omitted.

### Baseline: base guide only {#example-baseline}

Carries the school and education-assigned identifier but no grade, because the
base profile does not allow a grade observation.

```text
MSH|^~\&|EXAMPLE-SIS|EXAMPLE-LEA-0123|EXAMPLE-IIS|EXAMPLE-IIS|20261026100000-0500||VXU^V04^VXU_V04|ROSTER-000001|P|2.5.1|||ER|AL|||||Z22^CDCPHINVS|EXAMPLE-SCHOOL-0456|EXAMPLE-IIS
PID|1||SIS-000001^^^EXAMPLE-SIS^MR~900000001^^^EXAMPLE-SEA^PI||Sample^Jordan^Lee^^^^L|Example^^^^^^M|20180415|F
ORC|RE||9999^EXAMPLE-SIS
RXA|0|1|20261026||998^No vaccine administered^CVX|999||||||||||||||NA|A
```

Result: passes, with no errors. One warning notes that `PI` is a permitted,
rather than required, value in `HL70203_PAT`.

### Candidate roster message {#example-candidate}

Adds the grade observation.

```text
MSH|^~\&|EXAMPLE-SIS|EXAMPLE-LEA-0123|EXAMPLE-IIS|EXAMPLE-IIS|20261026100000-0500||VXU^V04^VXU_V04|ROSTER-000002|P|2.5.1|||ER|AL|||||Z22^CDCPHINVS|EXAMPLE-SCHOOL-0456|EXAMPLE-IIS
PID|1||SIS-000001^^^EXAMPLE-SIS^MR~900000001^^^EXAMPLE-SEA^PI||Sample^Jordan^Lee^^^^L|Example^^^^^^M|20180415|F
ORC|RE||9999^EXAMPLE-SIS
RXA|0|1|20261026||998^No vaccine administered^CVX|999||||||||||||||NA|A
OBX|1|CE|64990-5^Grade in school, if applicable^LN|1|3^Third grade^L||||||F|||20261026
```

Result: one expected extension error. `64990-5` is not in the base profile's
observation value set, `NIP003_Z22`. A roster profile would add it; removing
the grade to pass the base profile would remove information the topic exists
to carry.

### Alternative: dedicated student identifier type {#example-student-id-type}

Same as the candidate message, with a placeholder identifier type in place of
`PI`:

```text
PID|1||SIS-000001^^^EXAMPLE-SIS^MR~900000001^^^EXAMPLE-SEA^STU||Sample^Jordan^Lee^^^^L|Example^^^^^^M|20180415|F
```

Result: two expected errors. `STU` is not in `HL70203_PAT`, and `64990-5` is
not in `NIP003_Z22`.

### Negative: no school {#example-no-school}

The candidate message with `MSH-22` empty. The base profile does not require
`MSH-22`, so this passes base validation; a roster profile would reject it
because the school is required. This example has not been run against a roster
profile because none exists yet.

> **To be drafted:** examples for a transfer, a correction, an invalid grade
> code, and an acknowledgment for each error in
> [Acknowledgments](#acknowledgments), once [OQ-7](#open-questions) to
> [OQ-10](#open-questions) have candidate answers.

---

# Processing, Acknowledgments, and Conformance {#processing-and-conformance}

Formal conformance starts at the HL7 message. How a school-side system builds
it, and what an IIS does internally after acknowledging it, are outside the
candidate profile ([WD-6](#working-decisions)).

## Sender responsibilities

A sender, as a candidate:

- builds one roster message per student, conforming to the
  [message profile](#message-profile);
- maps local school, grade, and identifier values to the agreed identifier
  systems and vocabulary, and does not send values it cannot map;
- gives every message a unique `MSH-10` and keeps it with the acknowledgment;
- reads the acknowledgment and reports errors to whoever can correct the data;
  and
- does not report a positive acknowledgment as proof that the roster
  information was matched or applied
  ([Receipt, acknowledgment, matching, and application](#receipt-acknowledgment-matching-and-application)).

## Receiver responsibilities

An IIS, as a candidate:

- accepts a message that conforms to the roster profile;
- returns an acknowledgment that follows the
  [acknowledgment guidance](#acknowledgments) and reports each problem it
  detects at the interface; and
- does not reject a message only because it reports no vaccination, or only
  because it reports roster information alongside immunizations.

What an IIS does with valid roster information is its own decision.

## Validation layers

| Layer | Checks | Example failure |
|---|---|---|
| Structure | HL7 v2.5.1 syntax, segments, and data types | Missing `RXA` |
| Base profile | Release 1.5 `Z22` usage, cardinality, and value sets, as extended by the roster profile | `ORC-3` missing when `RXA-20` is `NA` |
| Vocabulary | Grade codes and other coded values | Grade code not in the agreed vocabulary |
| Identifiers | Assigning authority, identifier type, and format | Student identifier with an unknown assigning authority |
| Roster rules | Rules specific to roster messages | No school in `MSH-22` |

## Acknowledgments

Acknowledgments follow the
[current AIRA acknowledgment guidance](#related-work) and must remain
compatible with the [updated AIRA acknowledgment specifications](#related-work).
An acknowledgment confirms only the processing level it reports
([WD-7](#working-decisions)).

The current AIRA guidance defines how `MSA-1` and the `ERR-4` severity are
used. An error (`E`) means the sender is expected to correct the data and
resubmit; a warning (`W`) means the sender is expected to correct the data but
not resubmit. The guidance leaves the severity of a given condition to each
IIS and does not assign `ERR-3` codes, although its examples use HL7 Table 0357
values such as `101` (required field missing).

Candidate acknowledgment content for roster failures, with `ERR-3` codes from
HL7 Table 0357 proposed for discussion:

| Condition | `ERR-2` location | Candidate `ERR-3` code | Severity |
|---|---|---|---|
| School missing | `MSH-22` | `101` required field missing | Open |
| School not recognized | `MSH-22` | `103` table value not found | Open |
| Grade code not recognized | `OBX-5` | `103` table value not found | Open |
| Student identifier assigning authority not recognized | `PID-3.4` | `103` table value not found | Open |
| Patient not matched | Open | Open | Open |

Severities, application error codes in `ERR-5`, diagnostics in `ERR-8`, and
whether a receiver reports match outcomes at all are
[OQ-10](#open-questions).

> **To be drafted:** how the current and updated AIRA acknowledgment
> approaches differ for these conditions.

## Conformance targets

| Target | What conforms | How it is tested |
|---|---|---|
| Message | A roster message instance | Validated against the roster profile |
| Sender | A system that produces roster messages | Its messages for a defined scenario set are validated |
| Receiver | An IIS that accepts roster messages | Known messages are submitted and its acknowledgments compared with expected results |
| Acknowledgment | An acknowledgment to a roster message | Validated against the acknowledgment guidance and the expected error content |

## Requirement and test coverage

Each candidate requirement maps to at least one planned test. Requirements are
not yet normative; `SHALL` and `SHOULD` are not used until the community
agrees them.

| ID | Candidate requirement | Target | Planned test |
|---|---|---|---|
| CR-1 | The message is a VXU conforming to Release 1.5 `Z22` except for the roster extensions. | Message | Baseline and candidate examples validated |
| CR-2 | A roster-only message has one `ORC`/`RXA` group reporting CVX `998`. | Message | Structural validation |
| CR-3 | The school is identified in the agreed location and identifier system. | Message | Valid, missing, and unknown school |
| CR-4 | The submitting organization is identified separately from the school. | Message | District submission covering two schools |
| CR-5 | The grade is coded from the agreed vocabulary. | Message | Each grade value; unknown; unmappable |
| CR-6 | The education-assigned identifier has the agreed type and assigning authority. | Message | Valid, missing, and wrong assigning authority |
| CR-7 | Each message has a unique `MSH-10`. | Sender | Batch of messages checked for duplicates |
| CR-8 | The receiver accepts roster information in a roster-only VXU and in a VXU that also reports immunizations. | Receiver | Candidate and combined examples submitted; positive ACK expected |
| CR-9 | The receiver reports each roster failure in the ACK. | Receiver, Acknowledgment | Each condition in [Acknowledgments](#acknowledgments) |
| CR-10 | Periods, school year, corrections, and duplicates behave as agreed. | Message, Receiver | Unresolved until [OQ-7](#open-questions) to [OQ-9](#open-questions) |

> **To be drafted:** the evidence a system provides to claim conformance.

---

# Appendix: Working Decisions and Open Questions

This appendix consolidates the working decisions on the topic and the
questions still open. A working decision is the current position for
discussion, not a final or approved decision. The decisions in Draft 0.1.0
were carried forward from earlier design work as starting points; the
community has not yet reviewed them. Details are in the linked sections.

## Working decisions

| ID | Working decision | Basis |
|---|---|---|
| WD-1 | The topic concerns reporting roster information from school-side systems to an IIS using HL7 v2.5.1 ([scope](#what-this-topic-covers)). | Topic definition |
| WD-2 | Immunization Messaging Release 1.5 is the base guide; roster reporting is an explicit extension of it ([Message Profile](#message-profile)). | Earlier design work |
| WD-3 | A message reporting roster information and no immunization is a roster-only VXU using the base guide's no-vaccination structure ([structure](#message-structure)). | Earlier design work; validator testing |
| WD-4 | The submitting organization and the individual school are separate concepts ([Organizations](#submitting-organization-and-school)). | Earlier design work |
| WD-5 | An education-assigned student identifier supplements ordinary patient identifiers ([identifier](#education-assigned-student-identifier)). | Earlier design work |
| WD-6 | Formal conformance starts at the HL7 message; source extracts and internal IIS behavior are outside the candidate profile ([Conformance](#processing-and-conformance)). | Earlier design work |
| WD-7 | An acknowledgment confirms only the processing level it reports ([outcomes](#receipt-acknowledgment-matching-and-application)). | Earlier design work; AIRA acknowledgment guidance |
| WD-8 | Exemption information is defined in its own Topic Reference and not repeated here ([scope](#what-this-topic-does-not-cover)). | Topic boundary |
| WD-9 | Roster information is an addition to the VXU: anything a VXU can do, it can do with roster information included, including reporting immunizations ([structure](#message-structure)). | Topic direction |

## Open questions

Each question needs discussion before the affected text is settled. Questions
that belong to a related topic are referred there rather than answered here.

| ID | Question | Sources in tension | Status |
|---|---|---|---|
| OQ-1 | What profile identifier should a roster message assert in `MSH-21`, and how does it relate to `Z22`? | Examples assert `Z22` but do not conform to it | Open |
| OQ-2 | How are the submitting organization and the school represented? Is `MSH-4` for the organization and `MSH-22` for the school right, and in what `HD` and `XON` form? | `MSH-22` proposed in earlier design; it describes the organization accountable for the message, not a patient attribute, and in a VXU that also reports immunizations it would normally be the provider organization; an `OBX` or `PID` location was also considered | Open |
| OQ-3 | What school identifier systems and namespace rules are portable across jurisdictions, including closed, renamed, private, and out-of-jurisdiction schools? | Directories differ by state; an IIS may preload its own list | Open |
| OQ-4 | What grade observation and complete grade vocabulary are used, and is `OBX-2` `CE` or `CWE`? | LOINC `64990-5` proposed, but it is a PhenX survey item ("if applicable") with no answer list; local placeholder codes in examples; CEDS and Ed-Fi vocabularies are candidates | Open |
| OQ-5 | What identifier type and assigning authority represent an education-assigned student identifier? Is `PI` enough, or should a dedicated HL7 Table 0203 code be requested? | `PI` passes validation but is generic; a dedicated code is clearer but needs an HL7 request | Open |
| OQ-6 | What patient-identifying content is required, without defining an IIS matching algorithm? | Base `PID` requirements; receivers' matching needs vary | Open |
| OQ-7 | Does a message report current state, an event, or a bounded association ([alternatives](#what-a-message-asserts))? | Earlier first-scope design used current state; transfers and withdrawals need more | Open |
| OQ-8 | How are enrollment start and end, grade start and end, and school year represented? | Earlier first-scope design sent none; school-year functions and year transitions need them | Open |
| OQ-9 | How are transfers, corrections, clearing, duplicates, late messages, and ordering handled, including the meaning of `RXA-21`? | Depends on OQ-7 | Open |
| OQ-10 | Which acknowledgment errors, locations, severities, and diagnostics apply to roster failures, and may a receiver report match outcomes? | Current AIRA guidance covers `MSA-1` and `ERR-4` only and leaves severity to each IIS; updated guidance; receivers differ in what they expose | Open |
| OQ-11 | How does exemption information relate to roster messages, if at all? | [Exemptions Topic Reference](#related-work) defines exemptions separately and also has an open school-year question | Referred in part to the exemptions topic |
| OQ-12 | What formal standards path should carry a mature profile and its terminology? | Not discussed publicly | Open |
| OQ-13 | In a VXU that also reports immunizations, which order group carries the roster observations: an added CVX `998` group, or an existing immunization group? | An HL7 v2.5.1 VXU has no patient-level `OBX`; attaching roster data to an immunization mixes unrelated observations | Open |
