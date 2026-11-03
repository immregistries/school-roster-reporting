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
