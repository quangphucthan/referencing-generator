# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Current state

A referencing (citation) generator, at the research stage. There is no application code yet: no language or framework has been chosen, and there are no build, lint or test commands. Add them here when the first code lands.

## docs/referencing-styles.md is the spec

It holds the formatting rules for APA 7, Cite Them Right Harvard, RMIT Harvard, MLA 9, Chicago 18 (both systems), Vancouver and IEEE, plus a short survey of other styles.

Read its marks before implementing a rule:

- ✅ was checked against an official source or a maintained CSL style.
- ⚠️ is doubtful or disputed.
- **Unmarked was not individually checked.** This covers most of APA 7, MLA 9 and the Cite Them Right reference-list formats.

Check anything without a ✅ against the style's own authority first, and see "Open questions" at the end of the doc. When a rule is confirmed, update its mark.

Every style formats the same six fictional sources (S1–S6). The entries were constructed from the rules, not copied from style guides, so an entry is only as reliable as the rules behind it. Don't use one as a test's expected output until those rules are confirmed.

## Design constraints from the research

- **CSL or hand-written rules is undecided.** The doc recommends CSL styles rendered by citeproc-js over hand-coding.
- **Don't use the published `rmit-university-harvard.csl` unpatched.** It follows superseded rules. The live RMIT Easy Cite guide is the authority.
- **"Harvard" is not one style.** Cite Them Right and RMIT Harvard differ in "et al." thresholds, author names, page locators and DOI format. Keep them as separate named styles.
- **Chicago 17 and 18 have different author limits**, so the edition is part of the style's identity.
- **One source can need several strings.** Chicago notes-bibliography needs a full note, a short note and a bibliography entry, with different punctuation in each.
- **In-text output depends on the whole document, not one source.** Vancouver and IEEE number sources by first citation. Author-date a/b suffixes depend on the full reference list.
- **Some rules vary by institution or journal**, such as Vancouver's author limit and number format, and Cite Them Right's reference-list "et al.". The doc says which should be settings.
- **Never invent missing metadata** (place, publisher, month). Omit it or use the style's own placeholder.

## Git

Don't commit `.claude/` or `CLAUDE.local.md`. The rule that ignores them may not be in this repo, so check `git status` before staging. `CLAUDE.md` and `AGENTS.md` are meant to be tracked. Research and project docs go in `docs/`.
