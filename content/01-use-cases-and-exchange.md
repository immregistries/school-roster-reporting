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
