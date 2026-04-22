---
name: rebuttal-drafter
description: >-
  Drafts point-by-point reviewer responses and an optional Area-Chair
  meta-response for a conference rebuttal. Parses each reviewer
  comment, classifies it by dimension and severity (CRITICAL, MAJOR,
  MINOR), diagnoses root cause (genuine flaw, misreading, legitimate
  disagreement), and emits per-reviewer response files with summary
  tables and manuscript revision markers. Use when the user asks to
  'draft a rebuttal', 'respond to reviewers', 'organise reviewer
  responses', 'prepare discussion-period replies', or during a
  NeurIPS, ICLR, ICML, or ACL rebuttal window.
license: CC-BY-4.0
---

# Rebuttal Drafter

## Overview

A rebuttal decides whether a borderline paper moves up or drops. The
author-response window is short, the format is tight (per-reviewer
letters plus optional Area-Chair note), and reviewers re-read only
what the author organised clearly. This skill takes a set of reviewer
reports and produces a full rebuttal package: each comment parsed,
classified, and answered in an acknowledge-fix-evidence pattern,
plus a per-reviewer response file laid out as meta.tex / r1.tex /
r2.tex / r3.tex with summary tables, plus manuscript revision
markers (`\revision{<revised text>}` and
`\marginpar[]{\revision{R1.W1}}{\revision{<revised text>}}`).

The output is not a rewrite of the paper. It is a prioritised,
tone-controlled set of responses that a human author can paste into
the response files. CRITICAL reviewer concerns are surfaced first;
legitimate disagreements are argued with evidence, not deflected;
misreadings are handled with a charitable framing.

## When to use this skill

- Within the author-response or discussion window of NeurIPS, ICLR,
  ICML, ACL, EMNLP, CVPR, or a similar venue.
- The user asks to 'draft a rebuttal', 'respond to reviewers',
  'organise reviewer responses', 'prepare discussion-period replies'.
- After a major-revision decision where per-reviewer response files
  are required.
- When reviewers disagree and the user needs an Area-Chair
  meta-response to resolve the conflict.
- After any experimental rerun that answers a reviewer's concern and
  the result must be folded back into a response.

## When NOT to use this skill

- Reviews have not arrived. Use `pre-submission-reviewer` to audit
  the draft instead.
- The user wants to rewrite the paper from scratch. Use
  `tech-paper-template`, `intro-drafter`, or
  `benchmark-paper-template` (separate plugin).
- The user wants a one-line email reply to a program chair. This
  skill is for structured per-reviewer responses.
- The camera-ready has already been submitted. Use
  `pre-submission-reviewer` on the camera-ready instead.

## Core procedure

### Step 1: Parse and classify reviewer comments

For each reviewer report, extract every distinct concern as a
separate item. A single paragraph in the review may contain two or
three concerns; split them.

Assign each item a stable identifier:

- `R<i>.W<j>` for every distinct concern raised by Reviewer i,
  numbered j in the order the author chooses to address them. Use
  `R<i>.W<j>` regardless of whether the reviewer phrased the concern
  as a written comment, an explicit question, or a score-justification
  sentence: flatten them all under the W namespace so every concern
  has one stable identifier that the manuscript revision markers can
  point back to.

Classify each item along two axes:

- **Dimension**: logic (flowchart, motivation, claim-evidence link),
  novelty (positioning versus prior art), experiments (baselines,
  metrics, ablations, reproducibility), writing (clarity,
  organisation, typos), baselines (missing or weak), figures
  (readability, chartjunk, caption).
- **Severity**: CRITICAL, MAJOR, MINOR (see Severity taxonomy
  below).

Record the reviewer's recommendation score next to each R<i> group
so severity can be calibrated against whether the reviewer is
leaning accept or reject.

### Step 2: Root-cause diagnosis per comment

For each item, mark exactly one of three root causes:

- **Genuine flaw**: the paper as written does not support the
  claim, or the reviewer's concern exposes a real gap. The response
  must fix the paper, not argue.
- **Misreading**: the paper does support the claim but the reviewer
  missed the relevant passage or read a different sentence than the
  one intended. The response must point to the existing passage and
  offer a charitable explanation for why it was missed (for example,
  the passage was buried in Section 4.2 and will be surfaced in the
  Introduction).
- **Legitimate disagreement**: the reviewer interpreted the evidence
  reasonably but reached a different conclusion than the author. The
  response must concede the reviewer's interpretation is reasonable,
  then argue the author's position with evidence (new experiment,
  theoretical argument, citation to prior work).

Record what evidence is available to support the chosen diagnosis:
an existing paper section, a new experiment result, a citation, a
mathematical derivation, or none. If the answer is "none" and the
diagnosis is "genuine flaw", the response must either promise a
fix in the camera-ready or concede the limitation.

### Step 3: Prioritise by severity and addressability

Sort the items within each reviewer by the rule:

1. CRITICAL comments first, even if the reviewer listed them last.
2. Within the same severity, follow the reviewer's original
   numbering so the reviewer can re-check in order.
3. Items the author cannot address (no evidence available, no
   camera-ready room) are still listed, but with an honest concession
   rather than a deflection.

Across reviewers, record which CRITICAL concerns overlap (for
example, both R1 and R3 flag a missing baseline). Overlapping
CRITICAL concerns are candidates for the Area-Chair meta-response
in Step 7.

### Step 4: Draft point-by-point responses

Each response follows an acknowledge-fix-evidence structure:

1. **Acknowledge**: one sentence that restates the reviewer's
   concern in the author's own words. Do not quote long passages
   from the review.
2. **Fix or position**: one to three sentences stating what the
   paper now does (for a genuine flaw), what the paper already does
   and where (for a misreading), or what the author's position is
   and why (for a legitimate disagreement).
3. **Evidence**: a specific pointer. New experiment result with a
   number. Section and line reference in the revised paper. Citation
   to prior work. A new equation. For a legitimate disagreement, the
   evidence must be load-bearing, not a restated claim.

Tone rules:

- Respectful throughout. No defensive language ("the reviewer
  failed to notice"), no dismissive language ("this is trivial"),
  no flattery ("we greatly appreciate the reviewer's brilliant
  insight").
- For misreadings, the charitable framing is mandatory: "We
  apologise that Section X.Y was not prominent enough; we have
  moved it to..."
- For legitimate disagreements, concede the reasonable
  interpretation before arguing: "The reviewer is correct that A
  could be interpreted as B; we chose C because..."
- Every quantitative claim cites a number, not a qualitative word.
  "The ablation shows a 3.2 point drop on dataset D" is correct;
  "the ablation shows a significant drop" is not.

### Step 5: Mark revisions in the manuscript

For every response that cites a change to the paper, add the
matching markers in the main manuscript:

- Wrap the revised text as `\revision{<revised text>}` (the macro
  renders its argument in blue, as the handbook demonstrates with
  `\revision{blue}`; the command is defined in the project's
  commands.tex as `\newcommand{\revision}[1]{\textcolor{blue}{#1}}`).
- Add a `\marginpar[]{\revision{R1.W1}}{\revision{<revised text>}}`
  next to the changed block so a reader can trace the change back to
  the reviewer comment that motivated it. Use the left-column variant
  `\marginpar[\revision{R3.W2}]{}{\revision{<revised text>}}` for
  changes in the left column of two-column formats.
- Every `\revision{<revised text>}` block corresponds to one or more
  comment IDs; record the mapping in the manuscript revision log
  (output table below) so no change is orphaned.

If a reviewer concern was addressed without changing the paper
text (for example, by running a new experiment that only appears in
the response), note "response-only, no manuscript change" in the
revision log.

### Step 6: Organise per-reviewer response files

Build the response directory layout from
handbook/03_Paper_Writing/3.5:

- `response/meta.tex`: an AUTHOR-INTERNAL planning file. It holds
  the cross-reviewer severity tally, the overlap map (which CRITICAL
  concerns appear in multiple reviewers, from Step 3), and any notes
  for the Area-Chair meta-response drafted in Step 7. It is a working
  document for the author team and is NOT `\input`'d into main.tex.
- `response/r1.tex`: Reviewer 1 responses. Starts with a summary
  table mapping comment ID to change to paper section (columns:
  Comment, Change, Section). Then point-by-point responses in the
  order from Step 3.
- `response/r2.tex`, `response/r3.tex`: same shape.

The opening letter lives directly in `main.tex` per the handbook,
not in meta.tex. Wire the response files into `main.tex` with the
following block (verbatim from handbook/03_Paper_Writing/3.5):

```
\section*{Response to Reviewer Comments}

Dear Reviewers,

Thank you for your insightful reviews and constructive suggestions, based on which 
we have significantly improved our paper. The major revised parts are highlighted 
in \revision{blue} in the paper.

\input{response/r1}
\input{response/r2}
\input{response/r3}
```

Do not add `\input{response/meta}` to main.tex; meta.tex is an
author-internal working document, not a compiled source.

The summary table at the top of each r<i>.tex lets a reviewer skim
what changed in response to them before reading the detail.

### Step 7: Optional Area-Chair meta-response

Produce an AC meta-response when at least one of these holds:

- Two or more reviewers raised the same CRITICAL concern (from Step
  3's overlap record).
- Reviewers directly conflict (R1 says the method is too simple,
  R2 says it is too complex). The AC needs a framing the per-reviewer
  files cannot give.
- A systemic concern cuts across multiple comments (for example,
  several reviewers questioned the evaluation protocol). The AC note
  presents the unified fix.

The AC meta-response does not repeat per-reviewer detail verbatim.
It summarises the shared concern in one paragraph, the unified fix
in one paragraph, and points the AC to the per-reviewer files for
the specifics. Keep it under 300 words.

If none of the three conditions holds, emit "N/A" in the output.

### Step 8: Integrity gate

Before emitting the rebuttal package, run the checks in the
Integrity gate section below.

### Step 9: Output

Emit the rebuttal package in the Output format below.

## Severity taxonomy

- **CRITICAL**: reviewer states the concern is blocking, or the
  concern appears in the reviewer's score-justification as the main
  reason for a reject or weak-reject score. Example: "I cannot
  recommend acceptance without a comparison against baseline X";
  "the proof in Theorem 2 has a gap"; "the main claim is not
  supported by the experiments shown".
- **MAJOR**: reviewer flags the issue and will lower the score if
  it is not addressed, but does not call it blocking. Example:
  missing ablation on component X; unclear writing in Section 4;
  baseline Y is cited but not compared.
- **MINOR**: polish. Example: a typo, a citation format, a figure
  that could be larger, a sentence that could be split. The
  response can be one line.

Calibrate severity against the reviewer's score. A concern from a
reject-leaning reviewer is more likely CRITICAL than the same
concern from an accept-leaning reviewer.

## Integrity gate

All eight bullets are **[inspection]** class: the LLM verifies each
directly from its own output (counting, pattern-matching, or
comparing sections). No user-side attestation required.

Before returning the rebuttal package:

1. **[inspection]** Every reviewer comment extracted in Step 1 has
   a matching response in the per-reviewer file. The count of
   responses equals the count of comment IDs.
2. **[inspection]** Every response cites either a specific manuscript
   section (Section X.Y or line reference) or new evidence (experiment
   result with a number, citation, derivation). No response is pure
   restatement of the paper's existing claim.
3. **[inspection]** Every `\revision{<revised text>}` change listed
   in the manuscript revision log maps to at least one comment ID in
   a per-reviewer response.
4. **[inspection]** No response accuses the reviewer of misreading
   without providing a charitable alternative explanation (buried
   section, ambiguous phrasing, unclear figure caption).
5. **[inspection]** Tone is consistently respectful: no defensive
   language, no dismissive language, no flattery. Scan for phrases
   like "the reviewer failed to", "this is obvious", "greatly
   appreciate the brilliant".
6. **[inspection]** Severity-CRITICAL responses appear first in each
   per-reviewer file, ahead of MAJOR and MINOR, regardless of the
   reviewer's original ordering.
7. **[inspection]** Within each severity band, responses follow the
   reviewer's original numbering so the reviewer can re-check in
   order.
8. **[inspection]** The AC meta-response, if present, does not
   repeat per-reviewer detail verbatim; it summarises and points to
   the per-reviewer files.

If any check fails, mark the affected comment or file as "needs
user attention" and do not claim the rebuttal is complete.

## Summary
- CRITICAL: <n>
- MAJOR: <m>
- MINOR: <k>
- Top three responses first: <list of three comment IDs with a
  one-line description>

## Per-reviewer responses

### Reviewer 1
| # | Comment | Severity | Response | Paper section |
|---|---|---|---|---|
| 1.W1 | <one-line paraphrase> | CRITICAL | <one-line response> | <Section X.Y> |
| 1.W2 | ... | MAJOR | ... | ... |
| 1.W3 | ... | MINOR | ... | ... |

### Reviewer 2
| # | Comment | Severity | Response | Paper section |
|---|---|---|---|---|
| 2.W1 | ... | ... | ... | ... |

### Reviewer 3
| # | Comment | Severity | Response | Paper section |
|---|---|---|---|---|
| 3.W1 | ... | ... | ... | ... |

## Manuscript revision log
| Change | Reviewer comment(s) addressed | Paper section | Revision marker |
|---|---|---|---|
| <one-line description of the edit> | R1.W1, R3.W2 | Section 3.2 | `\marginpar[]{\revision{R1.W1}}{\revision{<revised text>}}` |

## Integrity gate result
- Gate 1: <pass or fail>
- Gate 2: <pass or fail>
- Gate 3: <pass or fail>
- Gate 4: <pass or fail>
- Gate 5: <pass or fail>
- Gate 6: <pass or fail>
- Gate 7: <pass or fail>
- Gate 8: <pass or fail>

## AC meta-response (optional)
<text of up to 300 words, or "N/A" if none of the three conditions
in Step 7 holds>
