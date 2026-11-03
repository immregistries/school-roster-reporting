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
