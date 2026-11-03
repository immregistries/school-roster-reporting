# Repository Instructions

## What this repository is

- This is the Topic Reference for InteropHub topic 77,
  [HL7 v2.5.1 School Roster Reporting](https://informatics.immregistries.org/hub/es/topic/77): the current, versioned statement of the
  topic's definitions, principles, diagrams, working decisions, and open
  questions. See `process/guidance/TopicReference.md`.
- The published Topic Reference is the web guide, `output/guide/index.html`,
  built by `process/tools/build_guide.py` with the shared AIRA Web template and
  published to https://immregistries.github.io/school-roster-reporting/latest/ by `.github/workflows/publish-guide.yml`.
  Do not write a separate HTML page, stylesheet, or site generator.
  `output/SchoolRosterReporting.html` is Quarto's review copy, not the published page.

## Public repository

- This repository is public. Everything committed here may be read, copied,
  and indexed by anyone.
- Do not commit private material: internal plans, funding or partner
  proposals, contact information, email, meeting recordings, unpublished
  partner data, credentials, or personal metadata in Office files.
- Source material in private working folders is not copied here. Draw on it by
  writing reviewed, public-safe prose in `content/`.

## Authority

- Markdown in `content/`, with `SchoolRosterReporting.qmd`, `_quarto.yml`, and cited
  assets, is the canonical source for the core document.
- `SchoolRosterReporting.qmd` defines section order. `CoreDocument.md` is generated;
  never edit it directly.
- Files in `output/` are generated artifacts, not source.
- Comments made on the web guide, in GitHub issues, in meetings, or on other
  copies are review inputs. Adjudicate accepted changes into `content/`.

## Writing the source

- Keep the source concise. State each fact, rule, code, and decision in one
  place and link to it from elsewhere. Do not restate tables in prose, add
  recaps, or repeat the open-questions table in a section.
- Keep the document to about six top-level chapters, including the appendix.
  Merge short or related chapters and use second-level headings within them.
- These limits apply to `content/` only. Derived products may repeat and
  expand as their audience needs.

## Status and certainty

- Until a maintainer releases a version as final, this is a working draft.
  Never describe it, or any part of it, as a standard, approved, balloted,
  adopted, or final unless a maintainer has said so.
- Working decisions are current positions for discussion. Record each in
  `content/90-working-decisions.md` with its basis.
- Do not silently introduce a new claim, decision, commitment, number, owner,
  date, or change in certainty. Mark it with `<!-- CONFIRM: ... -->` beside the
  affected text for human resolution.
- When sources conflict, record an open question rather than choosing silently.
  Later sources are not automatically more authoritative.

## Project boundaries

- This topic owns the use cases, information model, candidate HL7 v2.5.1
  message profile, acknowledgment behavior, and conformance expectations for
  reporting school roster information (school association, grade, and
  education-assigned student identifier) from school-side systems to an IIS.
- This topic defines how roster information is communicated. IIS matching and
  persistence, source extracts, returning immunization histories to schools,
  and who may send, receive, use, or disclose information are out of scope;
  state that briefly and do not add warnings about misuse.
- Exemption definitions, vocabulary, and message design belong to the
  Vaccine Exemptions and Deferrals Topic Reference. Link to it; do not repeat
  it.
- This is a technical template, not a report on a project. Do not name
  jurisdictions, their systems, vendors, or people, or describe any project's
  contract, schedule, demonstration, or process. Refer to implementations only
  as "at least one IIS".
- Use only synthetic examples with generic identifiers such as
  `EXAMPLE-SCHOOL-0456`. Label placeholder values such as `STU` or local grade
  codes as placeholders, never as assigned codes.
- Label every message-profile element as base guide, candidate, or
  unresolved. Do not let a candidate read as an approved requirement.

- Do not create clinical policy or silently settle open design questions.

## Shared templates

- `process/` is a Git submodule of
  `immregistries/content-publishing-templates`, pinned to a release tag. Do not
  edit or copy files under it. Adopt changes by moving the pointer, rebuilding,
  reviewing the output, and committing.

## Validation

- After editing `content/` or `SchoolRosterReporting.qmd`, run
  `python process/tools/build_core_markdown.py --assembly SchoolRosterReporting.qmd`,
  then `validate.ps1`.
- Run `build.ps1` and open `output/guide/index.html` before any release.
- Review the `CONFIRM` list printed by the content check.
- Successful generation is a mechanical check, not editorial approval.
- Do not commit, push, tag, publish, or update InteropHub without explicit
  instruction.
