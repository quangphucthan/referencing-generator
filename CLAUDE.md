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
| Engine | CSL styles rendered by citeproc-js, for now (see Design constraints) |
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

- **The engine is CSL for now, not final.** A second spike must pass before anything is built on it: Chicago notes, IEEE numbering, the APA placeholder policy, and citeproc running in a browser. If it fails, write the rules by hand in TypeScript.
- **CSL doesn't flag missing details.** In the first spike, gaps showed only through placeholder values. So the app must decide, for each style and source type, which empty fields get a placeholder.
- **Don't use the published `rmit-university-harvard.csl` unpatched.** It follows superseded rules. The live RMIT Easy Cite guide is the authority.
- **"Harvard" is not one style.** Cite Them Right and RMIT Harvard differ in "et al." thresholds, author names, page locators and DOI format. Keep them as separate named styles.
- **Chicago 17 and 18 have different author limits**, so the edition is part of the style's identity.
- **One source can need several strings.** Chicago notes-bibliography needs a full note, a short note and a bibliography entry, with different punctuation in each.
- **In-text output depends on the whole document, not one source.** Vancouver and IEEE number sources by first citation. Author-date a/b suffixes depend on the full reference list.
- **Some rules vary by institution or journal**, such as Vancouver's author limit and number format, and Cite Them Right's reference-list "et al.". The doc says which should be settings.
- **Never invent missing metadata** (place, publisher, month). Omit it or use the style's own placeholder.
