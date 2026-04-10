# Source Map

## Primary Sources

- `DreamZero-Nvidia.pdf`
- `sources/doc2x/DreamZero-Nvidia.md`
- `sources/doc2x/images/`
- `docs/reference/source-verification.md`

## Comparison Sources

- `lingbot-vla.pdf`
- `sources/doc2x/lingbot-vla/lingbot-vla.md`
- `LingBot_VA_paper.pdf`
- `sources/doc2x/lingbot-va-paper/lingbot-va-paper.md`
- `lingbot-va-main/README.md`
- `lingbot-va-main/wan_va/modules/model.py`
- `lingbot-va-main/wan_va/train.py`
- `dreamzero-main/README.md`
- `dreamzero-main/groot/vla/model/dreamzero/base_vla.py`
- `dreamzero-main/groot/vla/model/dreamzero/action_head/wan_flow_matching_action_tf.py`

## Context Entrypoints

If the current session is gone and a new contributor needs to recover intent quickly, read these first:

1. `.memo/memodocs/user_spec_dream-zero-guide.md`
2. `.memo/memodocs/tech_spec_dream-zero-guide.md`
3. `CLAUDE.md`
4. `docs/reference/source-verification.md`
5. `docs/report/dreamzero-standard-reference.md`

## Planning Constraints

- `system-development-principles-canonical.md`
- `question-problem-routing-system-v3.md`

## Interpretation Policy

- Use the PDF as the final authority when OCR output is ambiguous.
- Treat the extracted Markdown as the working text for chapter drafting.
- Keep derived teaching statements separate from source statements.
- When `pdftotext` and Doc2x disagree, prefer the PDF and log the discrepancy in `docs/reference/source-verification.md`.

## Known Risks

- OCR artifacts exist in the extracted Markdown.
- Some symbols and names may be slightly corrupted.
- Equations should be checked before final publication.
- `pdftotext` degrades equations and table layout, so it is a cross-check tool, not the final math source.

## Concrete Checkpoints

| Section | Source anchor | PDF pages | Purpose |
| --- | --- | --- | --- |
| Title, authors, abstract, main contribution claims | top of extracted source through early introduction | 1-3 | Verify naming, headline claims, and contribution summary |
| Figure 4 plus Equations (1)-(3) | `sources/doc2x/DreamZero-Nvidia.md:81-123` | 5-7 | Verify architecture description and math anchors |
| Table 1 and pretraining setup | `sources/doc2x/DreamZero-Nvidia.md:179-213` | 9-11 | Verify speedup numbers, 500-hour dataset claims, and setup wording |
| Seen-task and unseen-task results | `sources/doc2x/DreamZero-Nvidia.md:271-283` | 13-14 | Verify main quantitative comparison claims |
| Cross-embodiment transfer | `sources/doc2x/DreamZero-Nvidia.md:315-317` | 15-16 | Verify transfer gains and task-progress numbers |

## Recommended Verification Workflow

1. Start from `docs/reference/source-verification.md`.
2. Read the checkpoint row for the section you plan to edit.
3. Use `DreamZero-Nvidia.pdf` as the final authority.
4. Use `sources/doc2x/DreamZero-Nvidia.md` for equation-friendly working text.
5. Record any newly discovered ambiguities before propagating corrected wording into lecture or report pages.

## Synthesis Rule

When moving from lecture chapters to the standard reference:

- preserve the verified root cause
- preserve the verified logic root
- keep evidence limits visible
- keep the context entrypoints above intact for future sessions

For cross-project comparison work:

- distinguish paper claims from code-release claims
- distinguish the older LingBot-VLA paper from the newer LingBot-VA paper
- state when two names refer to non-identical systems
- prefer direct repo or extracted-paper evidence over memory

## Recommended Workflow

$$
\text{PDF check} \rightarrow \text{source note} \rightarrow \text{lecture chapter} \rightarrow \text{report synthesis}
$$
