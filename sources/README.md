# Sources

Source material informs the canonical content but is not itself authoritative.
Where sources disagree, the disagreement is recorded as an open question in
[the working decisions appendix](../content/90-working-decisions.md) rather than
resolved silently.

Source documents are not copied into this repository. Content drawn from them
is rewritten, reviewed, and placed in `content/`. Nothing private is committed;
`sources/private/` is ignored by Git as a safeguard.

## Public sources

| Source | Date | Use |
|---|---|---|
| [InteropHub topic 77: HL7 v2.5.1 School Roster Reporting](https://informatics.immregistries.org/hub/es/topic/77) | Current | Community coordination, meetings, and discussion notes |
| [InteropHub topic 37: School Data Exchange](https://informatics.immregistries.org/hub/es/topic/37) | Current | Broader topic context |
| [HL7 Version 2.5.1 Implementation Guide for Immunization Messaging, Release 1.5, and addendum](https://www.cdc.gov/iis/technical-guidance/hl7.html) | Current | Base VXU structure and `Z22` profile |
| [AIRA Guidance for HL7 Acknowledgement Messages to Support Interoperability](https://repository.immregistries.org/resource/guidance-for-hl7-acknowledgement-messages-to-support-interoperability/) | Current | Acknowledgment behavior |
| [AIRA Technical Assistance Bulletin resources](https://repository.immregistries.org/resource/tab-resources/) | Current | Updated acknowledgment specifications |
| [AIRA HL7 v2.5.1 Immunization Update and ACK Messages Course](https://repository.immregistries.org/resource/hl7-v2-5-1-immunization-update-ack-messages-course/) | Current | Orientation to VXU and ACK |
| [Vaccine Exemptions and Deferrals Topic Reference](https://immregistries.github.io/vaccine-exemptions-and-deferrals/latest/) | Current | Related topic; linked, not repeated |
| NIST-based immunization validator (Valitheus), `Z22` VXU send profile | Run 2026-11-03 | Validation results for the synthetic examples |

## Working documents

Private working documents informed Draft 0.1.0. They are listed by role only
and are not published; their reusable technical content is rewritten in
`content/`. Jurisdiction, system, vendor, participant, contract, schedule, and
project-process material in them is excluded.

| Document | Date | Use | Status |
|---|---|---|---|
| Project scope document | 2026-03 | Roster elements and interface boundary | Implementation scope; not a topic decision |
| Technical plan | 2026-09 | Earlier first-scope design choices | Working positions, generalized as WD and OQ items |
| Draft technical guidance and open questions | 2026-10 | Candidate recommendations and questions | Primary inventory; relabeled as candidates or OQ items |
| Draft example messages and validator notes | 2026-10 | Message structure and expected findings | Regenerated with generic identifiers and revalidated |
| Design presentations and one-page summary | 2024 | Options considered for `MSH-22`, `PID-3`, and grade `OBX` | Historical; partly superseded |
| Operational roster-upload guide and school module reference | 2024 | Existing non-HL7 roster processes | Context only |
