# HL7 v2.5.1 School Roster Reporting

> **Status:** Working draft for community discussion. This is not a published
> standard, an HL7 ballot, or a final decision.

HL7 v2.5.1 messaging from school-side systems to immunization information
systems (IIS) to establish and update student roster information: a student's
school association, grade level, and education-assigned identifier. National
immunization messaging guidance does not yet say how to report this, so IIS
rely on manual entry, separate uploads, or free-text notes fields.

This repository is the Topic Reference for
[HL7 v2.5.1 School Roster Reporting, InteropHub topic 77](https://informatics.immregistries.org/hub/es/topic/77). It records where
the topic currently stands: its definitions, principles, diagrams, working
decisions, and open questions.

## Start here

1. Read the [Topic Reference](https://immregistries.github.io/school-roster-reporting/latest/), where you can also leave
   comments, or the [GitHub reading copy](CoreDocument.md), or browse the
   [canonical source sections](content/README.md).
2. See the [working decisions and open questions](content/90-working-decisions.md).
3. Read [how to contribute](CONTRIBUTING.md).

## What belongs here, and what does not

This topic covers use cases, an information model, a candidate message
profile, acknowledgments, and conformance for roster reporting. Broader school
data exchange belongs to
[InteropHub topic 37](https://informatics.immregistries.org/hub/es/topic/37),
and exemptions to the
[Vaccine Exemptions and Deferrals](https://immregistries.github.io/vaccine-exemptions-and-deferrals/latest/)
Topic Reference. See [Status and Scope](content/00-status-and-scope.md).

## Community coordination

The community works on this topic through InteropHub:
[HL7 v2.5.1 School Roster Reporting, topic 77](https://informatics.immregistries.org/hub/es/topic/77). Meetings, agendas, and
discussion notes are tracked there.

## Build

Initialize the shared templates after cloning:

```powershell
git submodule update --init
```

Then build the reading copy, Word and HTML review copies, and the web guide in
`output/`:

```powershell
powershell.exe -NoProfile -ExecutionPolicy Bypass -File .\build.ps1
```

Validate source and generated files:

```powershell
powershell.exe -NoProfile -ExecutionPolicy Bypass -File .\validate.ps1
```

Pushing to `main` publishes the guide at https://immregistries.github.io/school-roster-reporting/main/. Pushing a release
tag such as `v0.1.0` publishes it at that version and at
https://immregistries.github.io/school-roster-reporting/latest/.

## Repository structure

```text
content/            canonical Markdown for the core document
products/           derived products, including the web guide
review/             review inputs awaiting adjudication
sources/            list of source material and how it is used
process/            submodule: shared public templates and tools
output/             generated files; not committed
SchoolRosterReporting.qmd    assembly authority for section order
CoreDocument.md     generated single-file reading copy
guide.json          web guide settings
```
