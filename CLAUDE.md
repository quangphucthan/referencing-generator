# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

A website that formats references (citations) in APA 7, Cite Them Right Harvard, RMIT Harvard, MLA 9, Chicago 18 (notes-bibliography and author-date), Vancouver and IEEE.

It is at the research stage. There is no application code yet, so there are no build, lint or test commands. Add them here when the first code lands.

App copy is in Australian English.

## Repository Structure

```
docs/
  referencing-styles.md   # The spec: formatting rules for each style, with six sample sources
README.md
```

## Stack

| Part | Choice |
|---|---|
| Language | TypeScript with no framework. Node must be able to strip the types: no `enum` or `namespace`, and `.ts` extensions on imports |
| Build | esbuild |
| Type check | `tsc --noEmit` |
| Tests | `node --test` |
| Engine | CSL styles rendered by citeproc-js 2.4.63 (pinned) |
| Hosting | Cloudflare Workers with static assets |

## The spec: docs/referencing-styles.md

It holds the formatting rules for each style, plus a short survey of other styles.

Read its marks before implementing a rule:

- ✅ was checked against an official source or a maintained CSL style.
- ⚠️ is doubtful or disputed.
- **Unmarked was not individually checked.** This covers most of APA 7, MLA 9 and the Cite Them Right reference-list formats.

Check anything without a ✅ against the style's own authority first, and see "Open questions" at the end of the doc. When a rule is confirmed, update its mark.

Every style formats the same six fictional sources (S1–S6). The entries were constructed from the rules, not copied from style guides, so an entry is only as reliable as the rules behind it. Don't use one as a test's expected output until those rules are confirmed.

## Design constraints

- **The engine is CSL**, chosen after two spikes. Build one engine per style and reuse it. Building one takes about 0.5 s for APA or Chicago, while rendering takes milliseconds.
- **CSL doesn't flag missing details.**
  - Gaps show only through placeholder values, so the app keeps its own list of the details each style needs for each source type.
  - Filling every empty field creates false gaps.
  - A placeholder shows only if the style's layout prints that detail.
- **Placeholder markers contain no letters**, for example `⟦3⟧`. Styles change the case of text: APA re-cased a placeholder made of letters.
- **The app supplies short titles and journal abbreviations.** citeproc doesn't shorten titles for Chicago short notes or abbreviate journal names for IEEE.
- **Sources not cited in the text drop out of the reference list** unless the app registers them separately.
- **Don't use the published `rmit-university-harvard.csl` unpatched.** It follows superseded rules. The live RMIT Easy Cite guide is the authority.
- **"Harvard" is not one style.** Cite Them Right and RMIT Harvard differ in "et al." thresholds, author names, page locators and DOI format. Keep them as separate named styles.
- **Chicago 17 and 18 have different author limits**, so the edition is part of the style's identity.
- **One source can need several strings.** Chicago notes-bibliography needs a full note, a short note and a bibliography entry, with different punctuation in each.
- **In-text output depends on the whole document, not one source.** Vancouver and IEEE number sources by first citation. Author-date a/b suffixes depend on the full reference list.
- **Some rules vary by institution or journal**, such as Vancouver's author limit and number format, and Cite Them Right's reference-list "et al.". The doc says which should be settings.
- **Never invent missing metadata** (place, publisher, month). Omit it or use the style's own placeholder.
