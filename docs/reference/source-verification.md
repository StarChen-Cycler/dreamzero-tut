# Source Verification

!!! warning "Read This Before Editing Chapters"

    Start with `.memo/memodocs/user_spec_dream-zero-guide.md`,
    `.memo/memodocs/tech_spec_dream-zero-guide.md`,
    and `CLAUDE.md`, then use this page as the working verification log.
    Treat `DreamZero-Nvidia.pdf` as the final authority.
    Treat `sources/doc2x/DreamZero-Nvidia.md` as the editable-reading source, not the canonical truth.

## Purpose

This page records the first verification pass for the DreamZero paper.

It exists to solve one practical problem:

$$
\text{later documentation work should not depend on unverified OCR text}
$$

## Verification Method

1. Read `DreamZero-Nvidia.pdf` as the canonical source.
2. Use `sources/doc2x/DreamZero-Nvidia.md` as the working source because it preserves equations better than `pdftotext`.
3. Use `pdftotext -layout` on the PDF to confirm:
   - title and author strings
   - product names
   - punctuation and hyphenation
   - claim wording and numeric values
4. If `pdftotext` degrades equations or tables, keep the Doc2x math structure but mark the location for visual PDF recheck.
5. Record fixes in `docs/`, never by silently editing `sources/doc2x/DreamZero-Nvidia.md`.

## Status Summary

- Title and author block: checked against PDF page 1
- Abstract and introduction claims: checked against PDF pages 1-3
- Equations 1-3: checked against PDF pages 6-7 at semantic and numbering level
- Table 1 and pretraining setup claims: checked against PDF pages 9-11
- Seen-task, unseen-task, and transfer claims: checked against PDF pages 13-16

## Equation Checklist

| Item | Extracted source anchor | PDF checkpoint | Status | Notes |
| --- | --- | --- | --- | --- |
| Equation (1) | `sources/doc2x/DreamZero-Nvidia.md:91-95` | PDF page 6 | Partially confirmed | The decomposition and equation number match. The label `DEAMZERO` in the extracted Markdown is an OCR corruption and should be read as `DreamZero`. |
| Equation (2) | `sources/doc2x/DreamZero-Nvidia.md:107-113` | PDF page 7 | Confirmed at structure level | The noisy interpolation structure and equation number match the PDF. |
| Equation (3) | `sources/doc2x/DreamZero-Nvidia.md:115-121` | PDF page 7 | Confirmed at structure level | The weighted expectation and equation number match, but exact glyph-level typography should still be visually rechecked during the equation-refinement task. |

!!! note "Equation Policy For Later Tasks"

    For the equation-refinement task, use the Doc2x equation bodies as the starting point,
    but always spot-check the final rendered formulas against the PDF because `pdftotext`
    collapses vector notation and spacing.

## Verified Chapter-Critical Claims

| Claim | Extracted source anchor | PDF checkpoint | Result |
| --- | --- | --- | --- |
| The paper title is `World Action Models are Zero-shot Policies` and the authorship block ends with Linxi "Jim" Fan and Joel Jang under NVIDIA. | top of extracted source | PDF page 1 | Confirmed |
| DreamZero is presented as a 14B robot foundation model built on a pretrained image-to-video diffusion backbone. | `:23` | PDF page 2 | Confirmed |
| The abstract claims over `2×` improvement on generalization to new tasks and environments versus state-of-the-art VLAs. | `:17` | PDF page 2 | Confirmed |
| The paper claims real-time closed-loop control at `7Hz`. | `:17`, `:35`, `:87` | PDF pages 2-3 and 6 | Confirmed |
| The paper claims over `42%` relative improvement on unseen task performance from cross-embodiment video-only data. | `:17`, `:31`, `:315` | PDF pages 2-3 and 15-16 | Confirmed |
| Figure 4 describes a model that takes visual context, language instructions, and proprioceptive state, and jointly predicts future video frames and actions. | `:81` | PDF pages 5-6 | Confirmed |
| The system-level summary claims a cumulative `38×` inference speedup and reports latency reduction from `5.7s` to `150ms` with DreamZero-Flash. | `:35`, `:179` | PDF pages 3 and 9-10 | Confirmed |
| The pretraining corpus for AgiBot G1 is described as approximately `500 hours` across `22` environments and about `42` subtasks per episode. | `:197`, `:207` | PDF pages 10-11 | Confirmed |
| On seen-task evaluation, DreamZero is reported at `62.2%` average task progress versus the best pretrained VLA baseline at `27.4%`. | `:271` | PDF pages 13-14 | Confirmed |
| On unseen-task evaluation, DreamZero is reported at `39.5%` average task progress versus `16.3%` for the best pretrained VLA baseline on AgiBot G1. | `:283` | PDF page 14 | Confirmed |
| In cross-embodiment transfer, the baseline `38.3%` improves to `54.3%` for human-to-robot transfer and `55.4%` for robot-to-robot transfer. | `:315`, `:317` | PDF pages 15-16 | Confirmed |

## Confirmed OCR And Extraction Ambiguities

The table below records confirmed issues in the extracted Markdown.
These are not all equally severe, but they are all real enough to matter during later editing.

| Source anchor | Extracted form | PDF-confirmed form | Impact |
| --- | --- | --- | --- |
| `:23` | `DREAMZERo` | `DreamZero` | Corrupt product name in first architectural introduction |
| `:31` | `DreamXEro` | `DreamZero` | Corrupt product name in contribution summary |
| `:31` | `DreamAZERO` | `DreamZero` | Corrupt product name in embodiment-adaptation sentence |
| `:45` | `adaptation-DREAMZERO pretrained` | `adaptation--DreamZero pretrained` | Missing dash normalization changes the sentence boundary |
| `:75` | `DreamZEro` | `DreamZero` | Corrupt product name in related-work comparison |
| `:91` | `DreamAZERO jointly predicts` | `DreamZero jointly predicts` | Corrupt product name in the equation setup paragraph |
| `:94` | `DEAMZERO` | `DreamZero` | OCR corruption in the Equation (1) label |
| `:123` | `video generation-a key advantage` | `video generation--a key advantage` | Missing dash alters the inference paragraph punctuation |
| `:153` | `speed gain is minimal-the number` | `speed gain is minimal--the number` | Missing dash in footnote explanation |
| `:179` | `DreamZERo-Flash` | `DreamZero-Flash` | Corrupt product name in the speedup summary |
| `:185` | `Entries marked "_"` | `Entries marked "--"` | Table note is wrong in extracted prose |
| `:191` | `Physical Intelligence,2025` | `Physical Intelligence, 2025` | Missing space in citation text |

## Open Verification Boundaries

- Table layouts degrade under `pdftotext`; use the PDF and extracted HTML tables together.
- Equation typography degrades under `pdftotext`; use the PDF and extracted LaTeX together.
- Figure-heavy appendix pages will need a later pass if the lecture/report starts citing appendix-specific evidence.

## Recommended Workflow For Later Tasks

1. Open this page.
2. Read the relevant checkpoint row for the section you are editing.
3. Re-open the PDF only for the exact pages listed here.
4. Keep corrections in `docs/` and never silently "clean up" the raw extracted source.
5. If you discover a new ambiguity, append it here before using the corrected wording elsewhere.
