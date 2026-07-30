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
