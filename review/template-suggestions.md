# Template Suggestions

Ideas for the shared templates
([`immregistries/content-publishing-templates`](https://github.com/immregistries/content-publishing-templates))
noticed while working on this Topic Reference. Record them here so they are
not lost, then propose the ones worth keeping to the templates repository as
an issue. Do not edit `process/` to try them.

| ID | Date | Suggestion | Why | Status |
|---|---|---|---|---|
| TS-1 | 2026-11-03 | Recognize a visible "To be drafted" note for intended content gaps, and have the content check list them as it lists `CONFIRM` items. | Early drafts deliberately leave gaps for the community to see. `CONFIRM` is hidden in the page and means "check this claim", not "write this". | Not proposed |
| TS-2 | 2026-11-03 | Have `validate.ps1` fail on internal links to anchors that do not exist in the built guide. | A link to a misspelled heading ID passed validation and was found only by checking the built page. | Not proposed |
| TS-3 | 2026-11-03 | Document that chapter anchors and `navLabels` keys come from the content file name without its number, not from the heading text. | A `navLabels` key written from the heading text silently dropped the chapter from the top menu. | Not proposed |
| TS-4 | 2026-11-03 | Make Related Work a section of Status and Scope in the generated skeleton, rather than a separate chapter. | The guidance already says to fold it in when short, which is the usual case. | Not proposed |
| TS-5 | 2026-11-03 | Strip blockquote markers from `CONFIRM` text in the content check's printed list. | A `CONFIRM` comment inside a blockquote printed with stray `>` characters. | Not proposed |
