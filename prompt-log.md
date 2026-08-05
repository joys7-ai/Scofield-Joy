[prompt-log.md](https://github.com/user-attachments/files/30203688/prompt-log.md)
# Prompt Log

This log documents the AI-assisted drafting process for `README.md` and `RESUME.md`. Claude (Anthropic) was used as a first-pass drafter; content was reviewed and edited by Joy Scofield.

---

### Prompt 1
**Request:** "add resume on github Draft your bio (`README.md`) and `RESUME.md` — LLM as drafter, you as editor; log the prompts in `prompt-log.md`"

**Source material provided:** Uploaded PDF resume (`Joy_Scofield_Resume_1.pdf`) containing work experience, education, and language skills.

**Output:** Drafted `README.md` (GitHub profile bio, first-person, narrative tone) and `RESUME.md` (structured, reverse-chronological resume in Markdown), using only content present in the uploaded PDF. No experience, skills, or dates were invented.

**Editorial notes:**
- Corrected "Magnum Cum Laude" to "Magna Cum Laude" (likely a typo in the source document — flagged for confirmation).
- Reworded bullet points for consistency in tense and voice across entries.
- Condensed the "Skills" section of the original into a summary paragraph (RESUME.md) and a bulleted highlights list (README.md).

---

## Still to review
- Confirm "Magna Cum Laude" correction is accurate
- Confirm whether LinkedIn/portfolio links should be added to either file
- Confirm phone number should be included in the public GitHub README (currently omitted from README.md, kept in RESUME.md only)

---
## Stage 0 — Portfolio Repository Setup

**Date:** 2026-07-30

**Prompt 1:** Asked for feedback comparing my repo structure against the course's 
required skeleton (README.md, RESUME.md, prompt-log.md, docs/, models/, data/, 
analysis/).
- **AI output:** Identified missing folders (models/, data/, analysis/), a 
  naming mismatch (repo named after the course instead of firstname-lastname), 
  and a file-casing issue (Resume.md vs RESUME.md).
- **My edit:** Confirmed the gaps against a course-provided folder diagram, 
  renamed/restructured accordingly.

**Prompt 2:** Asked for stub README.md content for the missing folders 
(models/, models/templates/, models/builds/, data/, analysis/).
- **AI output:** One-line descriptions for each folder's purpose.
- **My edit:** Ended up not using it and input it manually

**Prompt 3:** Asked for help improving my main README.md bio, then requested 
a longer (100+ word) version.
- **AI output:** Drafted a bio incorporating my Shidler background, Flair 
  Restaurant social media role, and creator-economy content work.
- **My edit:** Tweaked some of the wording but liked what it output

**Prompt 4:** Asked for a stub README for docs/plans/templates/ and guidance 
on reorganizing/removing the memo/ folder.
- **AI output:** Drafted a stub pointing to canonical course templates; 
  suggested consolidating memo/ into docs/decisions/.
- **My edit:** [note what you actually did with memo/]

---
## Stage 1 — Executive Memo (FX Hedging)

**Date:** 2026-07-30

**Prompt 1:** Shared the Stage 1 assignment (300–400 word memo to CFO on FX 
receivable exposure) and asked for help drafting it, without yet providing 
my assigned scenario.
- **AI output:** Asked for my specific scenario details (currency, amount, 
  timing) and the decision-memo template, since a generic draft wouldn't 
  meet the "precisely stated" rubric criterion.
- **My edit:** N/A — no draft yet at this point.

**Prompt 2:** Provided my scenario (#20, U.S. Aerospace Manufacturer, 
$20,000,000 receivable, EURUSD forward 1.0935) via the class roster screenshot.
- **AI output:** Confirmed my scenario and asked for the settlement timing, 
  which wasn't visible in the roster.
- **My edit:** N/A.

**Prompt 3:** Shared links to the course's Stage 1 assignment page and the 
`memo-template.md` file.
- **AI output:** Read both pages and drafted a full memo using the template's 
  required frontmatter (title/to/from/date/re) and sections (Executive 
  Summary, Background, Findings, Implications, Limitations & Next Steps, 
  References), using an assumed 12-month settlement.
- **My edit:** Double-checked the work and put it towards the executive memo.

**Prompt 4:** Shared scenario details confirming 1-year settlement and the 
option premiums ($0.019 put / $0.024 call).
- **AI output:** Corrected the memo — fixed the receivable description 
  (USD-denominated, not EUR-denominated), updated settlement timing to 
  1 year, and added the put premium to the options trade-off.
- **My edit:** Made sure the tone was like me and committed it to the memo.
**Deliverable:** `docs/decisions/2026-07-30-Scofield-aerospace-hedge-framing.md`

---
 
## Stage 2 — Model Specification
 
**Date:** 2026-07-30
 
**Prompt 1:** Asked for a full technical spec draft from the Stage 1 memo + scenario, before providing the actual course template — following a generic 8-section structure.
- **AI output:** First draft used the assignment page's described 8 sections, but carried over the Stage 1 memo's phrasing that the receivable was "$20,000,000, USD-denominated," setting `FC_AMT` in USD.
- **Gap found:** This is inconsistent with the named-range contract, which defines `FC_AMT` as a EUR-denominated foreign-currency amount — if the receivable were already in USD, there'd be no FX exposure to specify a hedge for.
- **Fix:** Corrected `FC_AMT` to EUR 20,000,000 throughout the problem statement, inputs table, and calculation flow.

**Prompt 2:** Provided the actual course template (template-spec.md, UH Mānoa-branded, with named-range conventions, a §6 self-review section, and brand formatting standards).
- **Gap found:** My first spec draft used a simplified structure that didn't match the course's actual named-range conventions (missing derived-value definitions like DF_USD/DF_FC/FV_PREM_PUT), the §6 self-review section, or the brand formatting appendix.
- **Fix:** Rebuilt the spec section-by-section against the real template, initially splitting the day-count basis into `BASIS_USD` (360) and `BASIS_FC` (365) per the template's "rigorous variant" suggestion.

**Prompt 3:** Pasted the assignment page's literal money-market formula (`R_FC × T_DAYS/360`, `R_USD × T_DAYS/360`).
- **Gap found:** My spec's `BASIS_USD`/`BASIS_FC` split contradicted the assignment page itself, which specifies a single `/360` for both legs.
- **Fix:** Reverted to one shared `BASIS = 360` named range, updated the affected formulas (DF_FC, Step 1, Step 3) and base-case output figures, and moved the USD/EUR day-count split into §6.2 as a considered-but-not-adopted improvement rather than baseline behavior.
  
**Prompt 4:** Uploaded the actual template-spec.md file to confirm it matched what had been used, and asked to add Appendix B (brand formatting standards) and the brand footer, which were still missing from the file.
- **AI output:** Confirmed the uploaded template matched exactly, then added the full Appendix B (color palette, typography, workbook color coding, chart styling, accessibility rules, file-naming conventions) and the brand footer block to the spec.
- **My edit:** N/A
  
**Deliverable:** `docs/specs/2026-07-30-Scofield-aerospace-spec.md`

## Stage 3 — AI-Assisted Build + Audit

**Date:** 2026-07-30

**Prompt 1:** Asked for the workbook to be generated directly from the committed Stage 2 spec, following the Stage 3 build contract (all ten named ranges, formulas-only, Cover/Legend/Inputs/hedge tabs/Sensitivity/Notes, color convention, live validation checks).
- **AI output:** Built the full workbook via `openpyxl` — 8 tabs, all named ranges attached, sensitivity grid + chart, parity check.
- **Gap found (self-caught during build):** Initial recalc returned 3 `#N/A` errors — three annotation cells (meant as plain-text formula notes) had been written starting with a literal `=`, so LibreOffice tried to evaluate them as real formulas.
- **Fix:** Rewrote the annotation cells to avoid a leading `=`. Recalc returned 0 errors across 161 formulas.

**Prompt 2:** Asked to verify key computed values against hand calculations before treating the build as final.
- **Gap found:** `USD_FLOOR_PUT` on the Sensitivity tab referenced `Options!D17` (a blank cell) instead of `Options!D18` (where the actual `MIN()` formula lived) — an off-by-one error from placing the summary label two rows below the grid instead of one. This silently showed $0 instead of the real floor value, with no error flag.
- **Fix:** Corrected the reference to `Options!D18`; verified `USD_FLOOR_PUT` now shows $22,658,014, matching the put proceeds at $S_T \le K\_PUT$.

**Prompt 3:** Asked for the required audit note (≥3 documented findings) per the Stage 3 submission checklist.
- **AI output:** Documented 4 findings: the off-by-one reference bug, the annotation-cell parsing error, the parity-check gap (confirmed as an expected consequence of the course-assigned `F0_in`, not a build error), and a passing kink-verification check.
- **My edit:** I manually went through and made sure my sheets looked good and tried my best to verify it.

**Deliverables:** `models/builds/2026-08-05-Scofield-aerospace-model.xlsx`, `analysis/2026-08-05-Scofield-build-audit.md`
