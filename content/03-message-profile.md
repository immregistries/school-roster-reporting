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
