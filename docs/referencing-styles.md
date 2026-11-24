# Referencing styles: research notes

Research for building a referencing generator. It covers how each style formats in-text citations and reference lists, including the punctuation, capitalisation and "et al." rules a generator has to get exactly right.

- **Compiled:** 2026-11-21.
- **Method:** three VoltAgent `research-analyst` agents researched the styles in parallel. Their disputed points were then checked against the live RMIT Easy Cite guide, the official CSE Quick Guide and the [CSL style repository](https://github.com/citation-style-language/styles).
- **Legend:** ✅ = checked against an official source or a maintained CSL style. ⚠️ = known to be doubtful or disputed; check it before implementing. **Unmarked = from the agents' own knowledge or a secondary guide, not individually checked.** That covers most of APA 7, MLA 9 and the Cite Them Right reference-list formats.
- **Italics** are shown as `*asterisks*` inside the examples.

## Contents

1. [Implementation note](#implementation-note)
2. [Sample sources](#sample-sources)
3. [Quick comparison](#quick-comparison)
4. Author-date styles: [APA 7](#apa-7th-edition) · [Harvard (Cite Them Right)](#harvard-generic--cite-them-right) · [RMIT Harvard](#rmit-harvard-easy-cite)
5. Author-page and notes styles: [MLA 9](#mla-9th-edition) · [Chicago 18 notes-bibliography](#chicago-18th-edition--notes-and-bibliography) · [Chicago 18 author-date](#chicago-18th-edition--author-date) · [Turabian](#turabian)
6. Numeric styles: [Vancouver](#vancouver) · [IEEE](#ieee)
7. [Other styles worth supporting](#other-styles-worth-supporting)
8. [Open questions](#open-questions)
9. [Sources](#sources)

---

## Implementation note

Don't hand-code these rules if you can avoid it. The [Citation Style Language](https://citationstyles.org/) (CSL) has maintained, machine-readable styles for nearly every style below. [citeproc-js](https://github.com/Juris-M/citeproc-js) renders them from CSL-JSON metadata, and it's the engine Zotero uses.

| Style | CSL file | Edition named in the file |
|---|---|---|
| APA 7 | `apa.csl` | 7th ✅ |
| MLA 9 | `modern-language-association.csl` | 9th ✅ |
| Chicago 18 (both systems) | `chicago-notes-bibliography.csl`, `chicago-author-date.csl` | 18th ✅ |
| IEEE | `ieee.csl` | Reference Guide v11.29.2023 ✅ |
| Cite Them Right | `harvard-cite-them-right.csl` | **12th ed.** (a 13th ed. exists ⚠️) |
| RMIT Harvard | `rmit-university-harvard.csl` | **out of date**, see below |

The RMIT CSL file still follows the old RMIT Harvard rules: `(Smith 2020, p. 14)`, with "et al." from 4 authors. The live Easy Cite guide now uses `(Smith 2020:14)`, with "et al." from 3 authors. You would have to patch it.

Hand-code a style only where no maintained CSL style exists or the existing one is wrong.

---

## Sample sources

These sources are fictional. Every style below formats the same six, so the styles can be compared side by side.

| ID | Type | Metadata |
|---|---|---|
| S1 | Journal article, 3 authors | Thi Lan Nguyen, John A. Smith, Ravi Patel (2021). "Machine learning approaches to citation analysis". *Journal of Information Science* 47(3), 215–230. DOI 10.1234/jis.2021.0473 |
| S2 | Book, 2nd ed. | Margaret E. Brown (2019). "Academic writing in the digital age". 2nd ed. Oxford University Press, Oxford, UK |
| S3 | Chapter in edited book | David K. Lee (2020). "Plagiarism and academic integrity", pp. 45–67, in "Handbook of research ethics", eds Maria Garcia and Wei Chen. Routledge, London |
| S4 | Webpage, corporate author | Australian Government Department of Education (2023, 14 March). "Higher education statistics". https://www.example.gov.au/higher-education-statistics, accessed 2 November 2026 |
| S5 | Journal article, 8 authors | Anna Kowalski, Ben Ortiz, Chloe Martin, Daniel Ito, Emma Rossi, Farid Haddad, Grace Okafor, Hugo Lambert (2022). "Open peer review at scale". *Research Integrity* 9(1), article e1042. DOI 10.1234/ri.2022.e1042 |
| S6 | Conference paper | Sofia Alvarez, Kenji Tanaka (2023). "Automated reference extraction from PDFs". *Proceedings of the International Conference on Digital Libraries (ICDL 2023)*, Lisbon, Portugal, pp. 112–119. DOI 10.1234/icdl.2023.112 |

---

## Quick comparison

| Style | System | In-text, S1 | "et al." in text | "et al." in reference list | Article title | DOI format | Access date |
|---|---|---|---|---|---|---|---|
| APA 7 | author-date | (Nguyen et al., 2021) | 3+ authors | never. Up to 20 listed; 21+: first 19, then `. . .`, then the last ✅ | sentence case, plain | `https://doi.org/…` | only for pages designed to change |
| Harvard (CTR) | author-date | (Nguyen, Smith and Patel, 2021) | 4+ ✅ | 4+ in CSL; some universities say list all ⚠️ | sentence case, 'single quotes' | `Available at: https://doi.org/…` ✅ | web sources: `(Accessed: 2 November 2026)` |
| RMIT Harvard | author-date | (Nguyen et al. 2021) | 3+ ✅ | none, all listed ✅ | sentence case, 'single quotes' | `doi:10.…` ✅ | `accessed 2 November 2026.` ✅ |
| MLA 9 | author-page | (Nguyen et al. 220) | 3+ ✅ | 3+ ✅ | Title Case, "double quotes" | `https://doi.org/…` | optional |
| Chicago 18 notes-bibliography | notes | ¹ footnote | 3+ in notes ✅ | 7+: first 3 + et al. ✅ | Title Case, "double quotes" | `https://doi.org/…` | only if undated |
| Chicago 18 author-date | author-date | (Nguyen et al. 2021, 220) | 3+ ✅ | 7+: first 3 + et al. ✅ | Title Case, "double quotes" | `https://doi.org/…` | only if undated |
| Vancouver | numeric | (1), [1] or ¹ | n/a | more than 6: first 6 + et al. (ICMJE); NLM now lists all ⚠️ | sentence case, plain | `doi:10.…` | `[cited 2026 Nov 2]` |
| IEEE | numeric | [1] | n/a | more than 6: first + et al. ✅ | sentence case, "double quotes" | `doi: 10.…` ✅ | `Accessed: Nov. 2, 2026.` ✅ |

---

# Author-date styles

## APA 7th edition

### Overview
- **Name:** *Publication Manual of the American Psychological Association*, 7th ed. (2019/2020), APA.
- **URL:** <https://apastyle.apa.org>
- **Used in:** psychology, education, social sciences, nursing and health. Used worldwide.
- **System:** author-date.

### In-text citations
- **Two forms:** parenthetical citations use `&`; narrative citations use "and". A comma separates author and year.

| Case | Parenthetical | Narrative |
|---|---|---|
| 1 author | (Brown, 2019) | Brown (2019) |
| 2 authors | (Alvarez & Tanaka, 2023) | Alvarez and Tanaka (2023) |
| 3+ authors | (Nguyen et al., 2021) | Nguyen et al. (2021) |
| Group author | (Australian Government Department of Education, 2023) | same |
| No date | (Brown, n.d.) | Brown (n.d.) |
| Multiple works | (Brown, 2019; Lee, 2020), alphabetical, semicolons | n/a |
| Direct quote | (Brown, 2019, p. 34), (Lee, 2020, pp. 45–67) | Brown (2019) … (p. 34) |
| Same author and year | (Brown, 2020a, 2020b) | Brown (2020a) |

- **3+ authors:** use "et al." from the first citation, with no comma before it.
- **Group-author abbreviation:** the first citation is (Australian Government Department of Education [DoE], 2023); later ones are (DoE, 2023).
- **No page numbers:** use `para. 4` or a section heading instead.

### Reference list
- **Heading:** "References", bold and centred.
- **Order:** alphabetical by first author. For the same first author, single-author works come first, then by year.
- **Layout:** hanging indent of 0.5 in, double-spaced.
- **Authors:**
  - Name format is `Surname, A. B.`
  - The serial comma always comes before `&`: `Alvarez, S., & Tanaka, K.`
  - List up to 20 authors.
  - For 21 or more, list the first 19, then `. . .`, then the last author ✅.
  - "Et al." is never used in the reference list.
- **Titles:**
  - Articles and chapters: sentence case, plain text.
  - Books, reports and webpages: sentence case, *italic*.
  - Journal names: Title Case, *italic*. The volume number is italic too; the issue number is not.
- **DOI:** `https://doi.org/…`, with no trailing full stop. No "Retrieved from" (an APA 6 habit) and no database names.
- **Place of publication:** omitted.

### Examples
| | Reference list entry | In-text |
|---|---|---|
| S1 | Nguyen, T. L., Smith, J. A., & Patel, R. (2021). Machine learning approaches to citation analysis. *Journal of Information Science, 47*(3), 215–230. https://doi.org/10.1234/jis.2021.0473 | (Nguyen et al., 2021) |
| S2 | Brown, M. E. (2019). *Academic writing in the digital age* (2nd ed.). Oxford University Press. | (Brown, 2019) |
| S3 | Lee, D. K. (2020). Plagiarism and academic integrity. In M. Garcia & W. Chen (Eds.), *Handbook of research ethics* (pp. 45–67). Routledge. | (Lee, 2020) |
| S4 | Australian Government Department of Education. (2023, March 14). *Higher education statistics*. https://www.example.gov.au/higher-education-statistics | (Australian Government Department of Education, 2023) |
| S5 | Kowalski, A., Ortiz, B., Martin, C., Ito, D., Rossi, E., Haddad, F., Okafor, G., & Lambert, H. (2022). Open peer review at scale. *Research Integrity, 9*(1), Article e1042. https://doi.org/10.1234/ri.2022.e1042 | (Kowalski et al., 2022) |
| S6 | Alvarez, S., & Tanaka, K. (2023). Automated reference extraction from PDFs. In *Proceedings of the International Conference on Digital Libraries (ICDL 2023)* (pp. 112–119). https://doi.org/10.1234/icdl.2023.112 | (Alvarez & Tanaka, 2023) |

- **S4:** the site name is omitted because it is the same as the author. Use `Retrieved November 2, 2026, from URL` only for pages designed to change, such as a live dataset.
- **S6:** publisher omitted because it is not in the metadata.

### Gotchas
- Using `&` in narrative citations, or "and" in parenthetical citations and the reference list.
- Missing the comma before `&`.
- Italicising the issue number.
- Title-casing article titles.
- Adding `doi:` or "Retrieved from".
- Putting a full stop after a DOI or URL.
- Using "et al." in the reference list.
- Forgetting `Article` before an article number.
- a/b suffixes are assigned in reference-list order, by title, ignoring "A" and "The".

---

## Harvard (generic) = Cite Them Right

"Harvard" has no governing body; each institution publishes its own variant. Model each variant as its own named style, not as one "Harvard". *Cite Them Right* (Pears & Shields, Bloomsbury) is the usual UK baseline.

### Overview
- **Edition:** the CSL file targets the 12th ed. Sussex's guide is based on a **13th ed.** ⚠️ Check the current edition at <https://www.citethemrightonline.com>.
- **System:** author-date.

### In-text citations
From the UWE CTR guide, as read by an agent. The 4+ threshold and "no date" also match the CTR CSL file ✅.

| Case | Format |
|---|---|
| 1 author | (Brown, 2019) or Brown (2019) |
| 2 authors | (Alvarez and Tanaka, 2023): "and", never `&` |
| 3 authors | (Nguyen, Smith and Patel, 2021): all named |
| 4+ authors | (Kowalski *et al.*, 2022): "*et al.*" in italics |
| No date | (Brown, no date) |
| Multiple works | (Brown, 2019; Lee, 2020): semicolons, in chronological order |
| Direct quote | (Brown, 2019, p. 34) |
| Same author and year | (Brown, 2020a) |
| Secondary source | (Melack and Thompson, 1971, cited in Rowley, 1991) |

### Reference list
- **Heading:** "Reference list". A "Bibliography" is a separate list of sources read but not cited.
- **Order:** alphabetical, then by year.
- **Authors:**
  - Name format is `Surname, A.B.`, with full stops and no space between initials.
  - Separate with `, ` and put `and` before the last author, with no serial comma.
- **Author limit:** the CSL style uses "et al." for 4+ authors in the reference list too. Newcastle's CTR guide says list all authors (using et al. only for 10+), and the Open University allows either. ⚠️ Make this a setting.
- **Titles:**
  - Articles and chapters: sentence case, in 'single quotes'.
  - Books and webpages: sentence case, *italic*.
  - Journal names: Title Case, *italic*.
- **Editors:** `(ed.)` for one, `(eds)` for two or more ✅.
- **Edition:** `2nd edn`.
- **Books:** include the place: `Oxford: Oxford University Press`.
- **DOI:** `Available at: https://doi.org/…`, with no access date ✅. Older editions used `doi:`.
- **Web sources:** `Available at: URL (Accessed: 2 November 2026).` Give the year only in the date.
- **Punctuation:** every entry ends with a full stop ✅.

### Examples
| | Reference list entry | In-text |
|---|---|---|
| S1 | Nguyen, T.L., Smith, J.A. and Patel, R. (2021) 'Machine learning approaches to citation analysis', *Journal of Information Science*, 47(3), pp. 215–230. Available at: https://doi.org/10.1234/jis.2021.0473. | (Nguyen, Smith and Patel, 2021) |
| S2 | Brown, M.E. (2019) *Academic writing in the digital age*. 2nd edn. Oxford: Oxford University Press. | (Brown, 2019) |
| S3 | Lee, D.K. (2020) 'Plagiarism and academic integrity', in Garcia, M. and Chen, W. (eds) *Handbook of research ethics*. London: Routledge, pp. 45–67. | (Lee, 2020) |
| S4 | Australian Government Department of Education (2023) *Higher education statistics*. Available at: https://www.example.gov.au/higher-education-statistics (Accessed: 2 November 2026). | (Australian Government Department of Education, 2023) |
| S5 | Kowalski, A. *et al.* (2022) 'Open peer review at scale', *Research Integrity*, 9(1), article number e1042. Available at: https://doi.org/10.1234/ri.2022.e1042. | (Kowalski *et al.*, 2022) |
| S6 | Alvarez, S. and Tanaka, K. (2023) 'Automated reference extraction from PDFs', in *Proceedings of the International Conference on Digital Libraries (ICDL 2023)*. Lisbon, Portugal, pp. 112–119. Available at: https://doi.org/10.1234/icdl.2023.112. | (Alvarez and Tanaka, 2023) |

- **S5:** if listing all authors, the entry starts `Kowalski, A., Ortiz, B., … Okafor, G. and Lambert, H.` "article number" follows Sussex's guide, the only source found ⚠️.
- **S6:** the conference-paper layout comes from secondary guides ⚠️.

### Gotchas
- Using `&`.
- Adding a serial comma before `and`.
- Writing "n.d." instead of "no date".
- Non-italic "et al.".
- Applying "et al." from 3 authors (that's RMIT).
- Ordering multiple works alphabetically (CTR orders them chronologically).
- Using `doi:` instead of `Available at: https://doi.org/`.
- Giving the full day and month for web pages.

---

## RMIT Harvard (Easy Cite)

### Overview
- **Owner:** RMIT University Library, <https://www.lib.rmit.edu.au/easy-cite/>.
- **Basis:** the [Australian Government Style Manual](https://www.stylemanual.gov.au/referencing). There is no edition number; the live page is the authority.
- **Used in:** Australia.
- **System:** author-date.
- ⚠️ The Zotero/CSL `rmit-university-harvard.csl` follows the **old** rules. Don't trust it.

### In-text citations
Verified against the live Easy Cite page, 2026-11-21 ✅.

| Case | Format |
|---|---|
| 1 author | (Brown 2019): no comma before the year |
| 2 authors | (Hill and Martinez-Diez 2020): "and", in brackets and in running text |
| 3+ authors | (Haslam et al. 2021) or Haslam et al. (2021) |
| Direct quote | (OECD 2019:15): a colon, then the page |
| Group author | (Australian Government Department of Education 2023). Use a well-known abbreviation such as (WHO 2020) if there is one. |
| No date | (Brown n.d.) ✅ |
| Multiple works | (Brown 2019; Lee 2020): semicolons, alphabetical ✅ |

- **Quotes:** direct quotes go in 'single quotes'. Quotes of more than 30 words become block quotes without quotation marks ✅.
- **No page numbers:** use alternative locators, such as a section or paragraph.

### Reference list
- **Heading:** "References" ⚠️.
- **Authors:**
  - Name format is `Surname AB`: no full stops, no spaces between initials, no comma after the surname.
  - Use `and` before the last author.
  - **List all authors** ✅.
- **Year:** in parentheses after the authors.
- **Titles:** articles and chapters in 'single quotes'; journal, book and website names in *italic*; sentence case throughout.
- **Journals:** `volume(issue):pages` (e.g. `32(11):590-620`). An article number is written `518:Article 145905` ✅.
- **DOI:** `doi:10.…` at the end ✅.
- **Webpages:** `*Title*, Site Name website, accessed 9 December 2021. URL`, with no full stop after the URL ✅.
- **Editors:** `(ed)`/`(eds)`, with no full stop ✅.
- **Edition:** `6th edn` ✅.
- **Books:** `Publisher, Place.` Drop the place when there is a DOI ✅.
- **Chapter page ranges:** not part of RMIT's chapter rule ✅.

### Examples
| | Reference list entry | In-text |
|---|---|---|
| S1 | Nguyen TL, Smith JA and Patel R (2021) 'Machine learning approaches to citation analysis', *Journal of Information Science*, 47(3):215-230, doi:10.1234/jis.2021.0473. | (Nguyen et al. 2021), quote (Nguyen et al. 2021:220) |
| S2 | Brown ME (2019) *Academic writing in the digital age*, 2nd edn, Oxford University Press, Oxford. | (Brown 2019) |
| S3 | Lee DK (2020) 'Plagiarism and academic integrity', in Garcia M and Chen W (eds) *Handbook of research ethics*, Routledge, London. | (Lee 2020) |
| S4 | Australian Government Department of Education (2023) *Higher education statistics*, Department of Education website, accessed 2 November 2026. https://www.example.gov.au/higher-education-statistics | (Australian Government Department of Education 2023) |
| S5 | Kowalski A, Ortiz B, Martin C, Ito D, Rossi E, Haddad F, Okafor G and Lambert H (2022) 'Open peer review at scale', *Research Integrity*, 9(1):Article e1042, doi:10.1234/ri.2022.e1042. | (Kowalski et al. 2022) |
| S6 | Alvarez S and Tanaka K (Day Month 2023) 'Automated reference extraction from PDFs' [conference paper], *International Conference on Digital Libraries*, Lisbon, Portugal, accessed Day Month Year. URL ⚠️ | (Alvarez and Tanaka 2023) |

- **S6:** follows the guide's rule `Author (Day Month Year of conference) 'Title of paper' [format], *Name of Conference*, Place of Conference, accessed Day Month Year. URL`. The sample has no conference day and month, access date or URL, so those stay as placeholders. The `[conference paper]` label and the use of this rule for a paper with a DOI are ⚠️.

### RMIT Harvard vs Cite Them Right

| | Cite Them Right | RMIT Harvard |
|---|---|---|
| Author and year in-text | (Brown, 2019) | (Brown 2019) |
| Page in-text | (Brown, 2019, p. 34) | (Brown 2019:34) |
| In-text "et al." | 4+, italic | 3+ |
| No date | (no date) | (n.d.) |
| Reference authors | Brown, M.E. | Brown ME |
| Journal details | 47(3), pp. 215–230 | 47(3):215-230 |
| Publisher | Oxford: Oxford University Press | Oxford University Press, Oxford |
| Editors | (ed.)/(eds) | (ed)/(eds) |
| Edition | 2nd edn. | 2nd edn |
| DOI | Available at: https://doi.org/… | doi:… |
| Web access | Available at: URL (Accessed: date). | …website, accessed date. URL |

### Gotchas
- Adding a comma between author and year.
- Using `p.` instead of `:`.
- Putting full stops or spaces in initials.
- Truncating the reference list with "et al.".
- Forgetting "website" after the site name.
- Writing "viewed": the current guide says "accessed". Older RMIT material said "viewed".
- Putting a full stop after the URL.
- Using the outdated CSL file.

---

# Author-page and notes styles

## MLA 9th edition

### Overview
- **Name:** *MLA Handbook*, 9th ed. (April 2021), Modern Language Association.
- **URL:** <https://style.mla.org>
- **Used in:** humanities (literature, languages, cultural studies), US-centred.
- **System:** author-page.

### In-text citations
`(Author page)`, with no comma and no `p.`.

| Case | Format |
|---|---|
| 1 author | (Brown 34) |
| 2 authors | (Alvarez and Tanaka 115) |
| 3+ authors | (Nguyen et al. 220) |
| Multiple works | (Brown 34; Lee 50) |
| Two works by the same author | add a short title: (Brown, *Academic Writing* 34) |
| No page number | author only |

- **Dates:** no year in-text.
- **Author named in the sentence:** give only the page.
- **Block quotes:** prose over 4 lines is indented, with the full stop *before* the parenthetical.

### Works Cited
- **Heading:** "Works Cited", centred.
- **Order:** alphabetical, ignoring A/An/The.
- **Layout:** hanging indent of 0.5 in, double-spaced.
- **Repeated author:** `---.` (three hyphens) replaces the name in later entries. The CSL file outputs `———` ✅.
- **Authors:**
  - The first author is inverted (`Last, First`); later authors are not.
  - Use full names as given.
  - 2 authors: `Last, First, and First Last.`
  - 3+ authors: `Last, First, et al.` ✅
- **Titles:**
  - Title Case for everything.
  - Standalone works (books, journals, websites, proceedings) in *italic*.
  - Parts of works (articles, chapters, pages) in "double quotes".
- **Core elements**, in order:
  1. Author.
  2. Title of source.
  3. Title of container,
  4. Other contributors,
  5. Version,
  6. Number,
  7. Publisher,
  8. Publication date,
  9. Location.

  Elements 3–9 repeat for a second container, such as a database.
- **DOI and URLs:** DOIs as `https://doi.org/…`. URLs drop `https://` but keep `www.`.
- **Access date:** optional (`Accessed 2 Nov. 2026.`). Use it for undated or changeable pages.
- **Months:** abbreviated (Jan., Feb., Mar., Apr., Aug., Sept., Oct., Nov., Dec.); May, June and July in full.
- **Pages:** `pp. 215–30`. Above 99, drop the repeated digits.
- **Publishers:** "University Press" becomes `UP`.
- **Place:** omitted.
- **Organisation as both author and publisher:** start with the title and list the organisation as publisher only ⚠️.

### Examples
| | Works Cited entry | In-text |
|---|---|---|
| S1 | Nguyen, Thi Lan, et al. "Machine Learning Approaches to Citation Analysis." *Journal of Information Science*, vol. 47, no. 3, 2021, pp. 215–30, https://doi.org/10.1234/jis.2021.0473. | (Nguyen et al. 220) |
| S2 | Brown, Margaret E. *Academic Writing in the Digital Age*. 2nd ed., Oxford UP, 2019. | (Brown 34) |
| S3 | Lee, David K. "Plagiarism and Academic Integrity." *Handbook of Research Ethics*, edited by Maria Garcia and Wei Chen, Routledge, 2020, pp. 45–67. | (Lee 50) |
| S4 | "Higher Education Statistics." Australian Government Department of Education, 14 Mar. 2023, www.example.gov.au/higher-education-statistics. Accessed 2 Nov. 2026. | ("Higher Education Statistics") |
| S5 | Kowalski, Anna, et al. "Open Peer Review at Scale." *Research Integrity*, vol. 9, no. 1, 2022, e1042, https://doi.org/10.1234/ri.2022.e1042. | (Kowalski et al.) |
| S6 | Alvarez, Sofia, and Kenji Tanaka. "Automated Reference Extraction from PDFs." *Proceedings of the International Conference on Digital Libraries (ICDL 2023)*, 2023, pp. 112–19, https://doi.org/10.1234/icdl.2023.112. | (Alvarez and Tanaka 115) |

- **S1, S5, S6:** a comma or a full stop before the DOI is unconfirmed ⚠️. A comma treats the DOI as the first container's location.
- **S4:** follows the author-is-publisher rule, which was not checked against MLA ⚠️.
- **S5:** the presentation of article numbers is ⚠️.

### Gotchas
- Not converting sentence-case titles to Title Case.
- Using `p.` in-text.
- Writing `215–230` instead of `215–30`.
- Abbreviating September as `Sep.` (MLA uses `Sept.`).
- Repeating a corporate author as the publisher.
- Forcing an access date.
- Leaving `https://` on non-DOI URLs.

---

## Chicago 18th edition: notes and bibliography

### Overview
- **Name:** *The Chicago Manual of Style*, 18th ed. (September 2024), University of Chicago Press.
- **URL:** <https://www.chicagomanualofstyle.org>. Mostly behind a paywall; the "What's New" page is free.
- **Used in:** history, arts and humanities.
- **System:** superscript note numbers with footnotes or endnotes, plus a bibliography.

### Notes
- **Full first note, article:** `1. First Last, "Title," *Journal* vol, no. issue (year): page, DOI.`
- **Short note:** `Last, "Short Title," page.` The short title is 4 words or fewer.
- **Authors in notes:** 1–2 authors are listed; 3+ is the first author + "et al." (the 17th edition listed up to 3) ✅.
- **Ibid.:** the 18th prefers shortened notes, in author-title, author-only or title-only form. Don't emit "ibid." by default; offer it as an option.
- **Elements given in the text:** may be dropped from a footnote, but usually not from an endnote.

### Bibliography
- **Heading:** "Bibliography".
- **Order:** alphabetical.
- **Layout:** hanging indent.
- **Repeated author:** repeat the name. The 3-em dash is no longer the default.
- **Authors:**
  - Full first names.
  - The first author is inverted; later authors are not.
  - List up to 6 authors.
  - For 7+, list the first 3 + `et al.` (the 17th edition: up to 10; for 11+, the first 7) ✅.
- **Et al. punctuation:** no comma before it in notes (`Nguyen et al.`), a comma in the bibliography (`…, et al.`).
- **Titles:** Title Case. The 18th edition capitalises prepositions of 5+ letters. Italics for standalone works; "double quotes" for parts.
- **DOI:** `https://doi.org/…`, preferred over a URL.
- **Access date:** only when no publication or revision date exists ⚠️. Some guides recommend one for websites.
- **Place of publication:** now optional.
- **Chapter page range:** now optional in the bibliography. Journal articles still need one.
- **Journal month/season:** may be omitted.
- **Notes vs bibliography:** notes use commas and parentheses; the bibliography uses full stops. Don't reuse one string for both.

### Examples
N = full note, SN = short note, B = bibliography entry.

- **S1**
  - N: 1. Thi Lan Nguyen et al., "Machine Learning Approaches to Citation Analysis," *Journal of Information Science* 47, no. 3 (2021): 220, https://doi.org/10.1234/jis.2021.0473.
  - SN: Nguyen et al., "Machine Learning Approaches," 222.
  - B: Nguyen, Thi Lan, John A. Smith, and Ravi Patel. "Machine Learning Approaches to Citation Analysis." *Journal of Information Science* 47, no. 3 (2021): 215–30. https://doi.org/10.1234/jis.2021.0473.
- **S2**
  - N: 3. Margaret E. Brown, *Academic Writing in the Digital Age*, 2nd ed. (Oxford University Press, 2019), 34.
  - SN: Brown, *Academic Writing*, 41.
  - B: Brown, Margaret E. *Academic Writing in the Digital Age*. 2nd ed. Oxford University Press, 2019.
  - Place is optional; with it, the note reads `(Oxford: Oxford University Press, 2019)`.
- **S3**
  - N: 5. David K. Lee, "Plagiarism and Academic Integrity," in *Handbook of Research Ethics*, ed. Maria Garcia and Wei Chen (Routledge, 2020), 50.
  - SN: Lee, "Plagiarism," 52.
  - B: Lee, David K. "Plagiarism and Academic Integrity." In *Handbook of Research Ethics*, edited by Maria Garcia and Wei Chen. Routledge, 2020.
- **S4**
  - N: 7. Australian Government Department of Education, "Higher Education Statistics," March 14, 2023, https://www.example.gov.au/higher-education-statistics.
  - SN: Australian Government Department of Education, "Higher Education Statistics."
  - B: Australian Government Department of Education. "Higher Education Statistics." March 14, 2023. https://www.example.gov.au/higher-education-statistics.
- **S5**
  - N: 9. Anna Kowalski et al., "Open Peer Review at Scale," *Research Integrity* 9, no. 1 (2022): e1042, https://doi.org/10.1234/ri.2022.e1042.
  - SN: Kowalski et al., "Open Peer Review."
  - B: Kowalski, Anna, Ben Ortiz, Chloe Martin, et al. "Open Peer Review at Scale." *Research Integrity* 9, no. 1 (2022): e1042. https://doi.org/10.1234/ri.2022.e1042.
- **S6** ⚠️ (built on the chapter-in-proceedings pattern)
  - N: 11. Sofia Alvarez and Kenji Tanaka, "Automated Reference Extraction from PDFs," in *Proceedings of the International Conference on Digital Libraries (ICDL 2023)* (2023), 115, https://doi.org/10.1234/icdl.2023.112.
  - SN: Alvarez and Tanaka, "Automated Reference Extraction," 117.
  - B: Alvarez, Sofia, and Kenji Tanaka. "Automated Reference Extraction from PDFs." In *Proceedings of the International Conference on Digital Libraries (ICDL 2023)*, 112–19. 2023. https://doi.org/10.1234/icdl.2023.112.

### What changed from 17th to 18th
Many generators still implement the 17th edition. The first four rows below are the ones most likely to break an implementation.

| | 17th | 18th |
|---|---|---|
| Bibliography/reference-list authors | up to 10; 11+: first 7 + et al. | up to 6; 7+: first 3 + et al. |
| Authors in notes and author-date in-text | up to 3; 4+: first + et al. | up to 2; 3+: first + et al. |
| Repeated author | 3-em dash | repeat the name |
| Ibid. | discouraged but allowed | shortened notes preferred |
| Place of publication | required | optional |
| Capitalisation | "headline style" | "title case"; prepositions of 5+ letters capitalised |
| Chapter page range in the bibliography | required | optional |
| Self-published | "self-published" | "pub. by author" |

---

## Chicago 18th edition: author-date

Uses the same manual as notes-bibliography and the same author thresholds. It is common in the sciences and social sciences.

- **In-text:** `(Author Year, page)`, with no comma before the year and a comma before the page.
  - Examples: (Brown 2019, 34), (Alvarez and Tanaka 2023, 115), (Nguyen et al. 2021, 220), (Brown n.d.).
  - Multiple works are separated by semicolons.
  - With the author named in the sentence: Brown (2019, 34).
- **Reference list:**
  - Heading "References".
  - The year comes straight after the author: `Author. Year. "Title."`
  - Journal issues are written `47 (3): 215–30`.
- **Examples:**
  - S1: Nguyen, Thi Lan, John A. Smith, and Ravi Patel. 2021. "Machine Learning Approaches to Citation Analysis." *Journal of Information Science* 47 (3): 215–30. https://doi.org/10.1234/jis.2021.0473.
  - S2: Brown, Margaret E. 2019. *Academic Writing in the Digital Age*. 2nd ed. Oxford University Press.
  - S3: Lee, David K. 2020. "Plagiarism and Academic Integrity." In *Handbook of Research Ethics*, edited by Maria Garcia and Wei Chen. Routledge.
  - S4: Australian Government Department of Education. 2023. "Higher Education Statistics." March 14, 2023. https://www.example.gov.au/higher-education-statistics.
  - S5: Kowalski, Anna, Ben Ortiz, Chloe Martin, et al. 2022. "Open Peer Review at Scale." *Research Integrity* 9 (1): e1042. https://doi.org/10.1234/ri.2022.e1042.
  - S6: Alvarez, Sofia, and Kenji Tanaka. 2023. "Automated Reference Extraction from PDFs." In *Proceedings of the International Conference on Digital Libraries (ICDL 2023)*, 112–19. https://doi.org/10.1234/icdl.2023.112. ⚠️
- **Gotchas:**
  - Writing `47, no. 3` instead of `47 (3)`.
  - Putting a comma between author and year.
  - Mismatched a/b suffixes between the text and the list.

---

## Turabian

- **Name:** *A Manual for Writers of Research Papers, Theses, and Dissertations*, University of Chicago Press. Chicago for students: the same two systems, plus guidance on formatting papers.
- **Editions:** the 9th ed. (2018) follows Chicago 17. A **10th edition** is listed by the University of Chicago Press (ISBN 978-0-226-84519-7 at one bookseller). Its publication date and which Chicago edition it follows are unconfirmed ⚠️.
- **For a generator:** treat "Turabian 9" as Chicago 17 rules. "Turabian 10" probably follows Chicago 18, but confirm that first ⚠️.

---

# Numeric styles

## Vancouver

### Overview
- **Name:** the ICMJE-recommended format, based on NLM's *Citing Medicine*, 2nd ed. (2007, updated online), which adapts NISO Z39.29.
- **URLs:** <https://www.ncbi.nlm.nih.gov/books/NBK7256/> and <https://www.nlm.nih.gov/bsd/uniform_requirements.html>.
- **Used in:** medicine and biomedical sciences, internationally.
- **System:** numbered in order of first citation.
- **Variants:** there are many institutional variants (number format, author limit, page elision, DOI format, journal italics). Make these settings, defaulting to *Citing Medicine*.

### In-text citations
- **Number format:** (1), [1] or superscript ¹. The standard doesn't mandate one.
- **Ranges:** (1-3); non-consecutive (1,3,5); combined (1-3,5).
- **Repeat citations:** reuse a source's number.
- **Page numbers:** not standardised; e.g. (3 p. 45).
- **Author names in text:** the number is still given: "Nguyen et al. (1)".

### Reference list
- **Heading:** "References".
- **Order:** numbered in order of first citation.
- **Authors:**
  - Name format is `Surname AB`, with no full stops in initials.
  - Separate with commas, with no "and".
- **Author limit:** for more than 6, ICMJE lists the first 6 + `et al.` NLM and *Citing Medicine* now say list all ⚠️. Make it a setting.
- **Titles:** sentence case, no quotes, no italics.
- **Journals:** names abbreviated per the NLM Catalog, with no full stops (`J Inf Sci`). Treat abbreviations as data, not an algorithm.
- **Dates:** `2023 Mar 14`.
- **Page ranges:** elided: `215-30`.
- **Books:** `2nd ed.` comes before the place: `Oxford: Oxford University Press; 2019.`
- **DOI:** `doi:10.…`, with no trailing full stop.
- **Web sources:** add `[Internet]` after the title, `[cited 2026 Nov 2]`, and `Available from: URL`.
- **Missing metadata:** use `[place unknown]` and `[publisher unknown]`; never guess.

### Examples
| | Reference list entry |
|---|---|
| S1 | Nguyen TL, Smith JA, Patel R. Machine learning approaches to citation analysis. J Inf Sci. 2021;47(3):215-30. doi:10.1234/jis.2021.0473 |
| S2 | Brown ME. Academic writing in the digital age. 2nd ed. Oxford: Oxford University Press; 2019. |
| S3 | Lee DK. Plagiarism and academic integrity. In: Garcia M, Chen W, editors. Handbook of research ethics. London: Routledge; 2020. p. 45-67. |
| S4 | Australian Government Department of Education. Higher education statistics [Internet]. [place unknown]: Australian Government Department of Education; 2023 Mar 14 [cited 2026 Nov 2]. Available from: https://www.example.gov.au/higher-education-statistics |
| S5 | Kowalski A, Ortiz B, Martin C, Ito D, Rossi E, Haddad F, et al. Open peer review at scale. Res Integr. 2022;9(1):e1042. doi:10.1234/ri.2022.e1042 |
| S6 | Alvarez S, Tanaka K. Automated reference extraction from PDFs. In: Proceedings of the International Conference on Digital Libraries (ICDL 2023); 2023; Lisbon, Portugal. [place unknown]: [publisher unknown]; 2023. p. 112-9. doi:10.1234/icdl.2023.112 |

- **In-text:** "…evidence (1,3-5), as noted earlier (1)."
- **S5:** with the "list all" setting, all 8 authors are shown.

---

## IEEE

### Overview
- **Name:** *IEEE Reference Guide*, IEEE Publication Operations.
- **Version:** **v11.29.2023**, the version the maintained CSL style targets ✅. Third-party copies cite a v3.28.2025 ⚠️. The official PDF URL now redirects to a Google Doc behind a login, so check the cover date by hand.
- **URL:** <https://journals.ieeeauthorcenter.ieee.org/your-role-in-article-production/ieee-editorial-style-manual/>
- **Used in:** engineering, computer science and electronics.
- **System:** numbered in order of first citation.

### In-text citations
- **Format:** `[1]` in square brackets, never superscript. Place it inside the sentence punctuation: "…shown in [1]."
- **Multiple sources:** `[1], [3], [5]`, not `[1, 3, 5]`.
- **Ranges:** `[1]–[3]`, with an en dash.
- **Combined:** `[1], [3]–[5]`.
- **Locators:** `[1, p. 5]`, `[1, Sec. II]`.
- **Author names in text:** the number is still required: "Nguyen et al. [1]".
- **As a noun:** "in [1]". At the start of a sentence, write "Reference [1]".

### Reference list
- **Heading:** "References".
- **Numbering:** `[n]` set flush left, in citation order.
- **Authors:**
  - Initials come first, with full stops and spaces: `T. L. Nguyen`.
  - 2 authors: `A and B`, with no comma.
  - 3+ authors: `A, B, and C`, with a serial comma.
  - **More than 6 authors:** first author + `et al.` ✅ (`et-al-min="7"` in the CSL file).
- **Titles:**
  - Articles and chapters: sentence case, in "double quotes", with the comma inside the closing quote.
  - Books: *Title Case, italic*.
  - Journals: abbreviated with full stops, *italic* (`*J. Inf. Sci.*`).
  - Conferences: `in *Proc. …*`.
- **Page ranges:** `pp. 215–230`, with an en dash and no elision.
- **Article numbers:** `Art. no. e1042`.
- **DOI:** `doi: 10.…` (lowercase, with a space), followed by a full stop ✅.
- **Web sources:** `Accessed: Nov. 2, 2026. [Online]. Available: URL` ✅.
- **Months:** Jan., Feb., Mar., Apr., May, Jun., Jul., Aug., Sep., Oct., Nov., Dec.

### Examples
| | Reference list entry |
|---|---|
| S1 | [1] T. L. Nguyen, J. A. Smith, and R. Patel, "Machine learning approaches to citation analysis," *J. Inf. Sci.*, vol. 47, no. 3, pp. 215–230, 2021, doi: 10.1234/jis.2021.0473. |
| S2 | [2] M. E. Brown, *Academic Writing in the Digital Age*, 2nd ed. Oxford, U.K.: Oxford Univ. Press, 2019. |
| S3 | [3] D. K. Lee, "Plagiarism and academic integrity," in *Handbook of Research Ethics*, M. Garcia and W. Chen, Eds. London, U.K.: Routledge, 2020, pp. 45–67. |
| S4 | [4] Australian Government Department of Education. "Higher education statistics." Accessed: Nov. 2, 2026. [Online]. Available: https://www.example.gov.au/higher-education-statistics ⚠️ |
| S5 | [5] A. Kowalski et al., "Open peer review at scale," *Res. Integr.*, vol. 9, no. 1, Art. no. e1042, 2022, doi: 10.1234/ri.2022.e1042. |
| S6 | [6] S. Alvarez and K. Tanaka, "Automated reference extraction from PDFs," in *Proc. Int. Conf. Digit. Libraries (ICDL)*, Lisbon, Portugal, 2023, pp. 112–119, doi: 10.1234/icdl.2023.112. |

- **S4:** where the publication date (Mar. 14, 2023) goes is ⚠️.

### Gotchas
- Using "et al." at exactly 6 authors (it's *more than* 6).
- Putting a comma before "and" with 2 authors.
- Writing `[1, 3]` instead of `[1], [3]`.
- Eliding page ranges.
- Using a DOI URL instead of `doi:`.
- Writing "Retrieved" instead of "Accessed:".
- Writing `Sept.` (IEEE uses `Sep.`).

---

## Other styles worth supporting

Sample S1 in each style. Status: ✅ checked against an official source or a reputable library guide; ⚠️ from the agents' own knowledge.

| Style | Edition | Field / region | System | S1 example | Status |
|---|---|---|---|---|---|
| **AMA** | 11th (2020) | medicine, US | numeric, superscript | Nguyen TL, Smith JA, Patel R. Machine learning approaches to citation analysis. *J Inf Sci*. 2021;47(3):215-230. doi:10.1234/jis.2021.0473 | author rule ✅ (library guides): 6 or fewer, list all; more than 6, first 3 + `et al` (no full stop) |
| **CSE** | 9th (2024) | life and physical sciences | citation-sequence / citation-name / name-year | Nguyen TL, Smith JA, Patel R. Machine learning approaches to citation analysis. J Inf Sci. 2021;47(3):215–230. https://doi.org/10.1234/jis.2021.0473 | ✅ official Quick Guide: more than 5 authors, first + "et al."; no place of publication; full page ranges |
| **AGLC4** | 4th (2018) | law, Australia | footnotes | Thi Lan Nguyen, John A Smith and Ravi Patel, 'Machine Learning Approaches to Citation Analysis' (2021) 47(3) *Journal of Information Science* 215 | ✅ Monash: journal name italic; more than 3 authors, first + "et al"; check whether an AGLC5 has been released ⚠️ |
| **ACS** | ACS Guide (2020) | chemistry | numeric or author-date | Nguyen, T. L.; Smith, J. A.; Patel, R. Machine Learning Approaches to Citation Analysis. *J. Inf. Sci.* **2021**, *47* (3), 215–230. DOI: 10.1234/jis.2021.0473 | ⚠️ |
| **OSCOLA** | 4th (2012) | law, UK | footnotes | Thi Lan Nguyen, John A Smith and Ravi Patel, 'Machine Learning Approaches to Citation Analysis' (2021) 47(3) Journal of Information Science 215 | ⚠️ journal name roman; more than 3 authors, "and others"; check for a 5th ed. |
| **Bluebook** | 21st (2020); a 22nd (2025) is reported | law, US | footnotes | Thi Lan Nguyen, John A. Smith & Ravi Patel, *Machine Learning Approaches to Citation Analysis*, 47 J. Info. Sci. 215 (2021) | ⚠️ |
| **ASA** | 6th (2019) or later | sociology | author-date | Nguyen, Thi Lan, John A. Smith, and Ravi Patel. 2021. "Machine Learning Approaches to Citation Analysis." *Journal of Information Science* 47(3):215–30. doi:10.1234/jis.2021.0473 | ⚠️ edition |
| **Australian Government Style Manual** | digital, continuously updated | Australian government (the basis of RMIT Harvard) | author-date, plus note and numeric options | Nguyen TL, Smith JA and Patel R (2021) 'Machine learning approaches to citation analysis', *Journal of Information Science*, 47(3):215–230, doi:10.1234/jis.2021.0473 | ⚠️ in-text "et al." threshold unverified (RMIT uses 3+) |

URLs: [AMA](https://www.amamanualofstyle.com), [CSE](https://www.csemanual.org/Tools/CSE-Citation-Quick-Guide.html), [AGLC4](https://law.unimelb.edu.au/mulr/aglc), [ACS](https://pubs.acs.org/doi/book/10.1021/acsguide), [OSCOLA](https://www.law.ox.ac.uk/research-subject-groups/publications/oscola), [Bluebook](https://www.legalbluebook.com), [ASA](https://www.asanet.org/publications/asa-style-guide), [Style Manual](https://www.stylemanual.gov.au/referencing).

---

## Open questions

Resolve these before implementing the affected style.

1. **Cite Them Right:**
   - Confirm the current edition (12th or 13th).
   - Does the reference list list all authors or use "et al." from 4+?
   - How should article numbers be written?
2. **IEEE:** confirm the Reference Guide version (v11.29.2023 or v3.28.2025) and where a webpage's publication date goes.
3. **Vancouver:** default author limit (first 6 + et al., per ICMJE, or list all, per NLM).
4. **RMIT Harvard:**
   - Layout for a conference paper that has a DOI, and the `[format]` label.
5. **MLA 9:** comma or full stop before a DOI, and the author-is-publisher rule.
6. **Chicago 18:**
   - Access-date policy for websites.
   - Proceedings-paper layout.
7. **Turabian 10:** publication date, and which Chicago edition it follows.
8. **Part B styles:** whether AGLC5, OSCOLA 5 or a newer ASA edition has been released.

**General rule:** never invent missing metadata (place, publisher, month). Omit it, or use the style's placeholder, such as Vancouver's `[place unknown]` or Harvard's `no date`.

---

## Sources

**Read directly while checking the agents' work**
- RMIT Easy Cite, RMIT Harvard guide (live page, fetched 2026-11-21): <https://www.lib.rmit.edu.au/easy-cite/?styleguide=styleguide-1>
- CSE Citation Quick Guide: <https://www.csemanual.org/Tools/CSE-Citation-Quick-Guide.html>
- CSL styles repository (`apa`, `harvard-cite-them-right`, `rmit-university-harvard`, `ieee`, `modern-language-association`, `chicago-notes-bibliography`, `chicago-author-date`): <https://github.com/citation-style-language/styles>

**Fetched by the agents**
- Chicago 18 "What's New": <https://www.chicagomanualofstyle.org/help-tools/what-s-new.html>
- Chicago 18 changes, Widener guide: <https://widener.libguides.com/Chicago18/New>
- UWE, Cite Them Right in-text citations: <https://www.uwe.ac.uk/study/study-support/study-skills/referencing/cite-them-right-harvard/in-text-citations>
- NLM, ICMJE sample references: <https://www.nlm.nih.gov/bsd/uniform_requirements.html>

**Secondary guides, from the agents or search results**
- Cite Them Right author lists and DOIs:
  - Newcastle: <https://www.ncl.ac.uk/mediav8/academic-skills-kit/file-downloads/manage-info/2025%20Cite%20Them%20Right%20Harvard%20Style%20Sheet.pdf>
  - Sussex: <https://www.sussex.ac.uk/skills-hub/referencing-and-academic-integrity/harvard>
  - Open University: <https://university.open.ac.uk/library/referencing-and-plagiarism/quick-guide-to-harvard-referencing-cite-them-right>
- IEEE:
  - University of Melbourne: <https://library.unimelb.edu.au/recite/referencing-styles/ieee>
  - ACU: <https://libguides.acu.edu.au/ieee/author-layout>
  - SJSU: <https://www.sjsu.edu/writingcenter/docs/handouts/IEEE%20Citation%20Guide_Updated%202025.pdf>
- AMA: JCU <https://libguides.jcu.edu.au/ama/authors>
- AGLC4:
  - Monash: <https://guides.lib.monash.edu/aglc4/journal-articles>
  - UTS: <https://www.lib.uts.edu.au/sites/default/files/2020-10/AGLC4%20Condensed%20Guide.pdf>
- Chicago 18 ibid. and access dates:
  - Purdue OWL: <https://owl.purdue.edu/owl/research_and_citation/chicago_manual_18th_edition/cmos_formatting_and_style_guide/web_sources.html>
- Turabian 10th ed. listing: <https://mitpressbookstore.mit.edu/book/9780226845197>
- MLA core elements: <https://style.mla.org/works-cited-a-quick-guide/>
