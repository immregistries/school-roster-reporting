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
