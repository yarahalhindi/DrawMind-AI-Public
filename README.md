# DrawMind AI

A children's drawing companion. A child draws on a canvas, taps *analyze*, and
gets a warm, spoken reading of how the picture feels — plus a structured summary
a parent can act on.

> **Not a medical device.** DrawMind produces a preliminary reading for parents
> and educators. It is not a clinical diagnosis, psychiatric assessment, or
> medical recommendation, and it does not replace professional evaluation.

---

## The problem

Children don't always have the words for how they feel. They do have crayons.

Most emotion-recognition tools either ask a child to fill in a questionnaire or
run one large model over every input and present the result as certain. Neither
suits a 5-year-old, and neither is honest about when it's guessing.

## The approach

Draw a picture. Get a reading you can actually talk about.

```
child's drawing
      │
┌─────┴──────┐
▼            ▼
happy/sad   angry/fear      two small, fast CNNs
  model       model
  └─────┬─────┘
        │
   agree? ──── yes ──► confident reading ─────────┐
        │                                         │
        no                                        │
        ▼                                         │
  contradiction gate                              │
   ├── no conflict ─► spend nothing, keep verdict │
   └── conflict ────► vision tie-breaker ─────────┤
                                                  │
        ┌─────────────────────────────────────────┘
        ▼
  8 measured visual features + 8 Grad-CAM region scores
        │
        ▼
  language model ──► 3-tab report ──► spoken narration
```

Two ideas carry the design:

**1. A cascade, not one big model.** Each CNN predicts between two classes
only, so both stay small and cheap enough to run on every drawing. The
expensive vision model isn't called by default — a local gate checks whether
the parent's context and the CNN verdict actually disagree. When they don't, no
image tokens are spent at all. Most drawings never pay for the expensive step.

**2. Numbers before prose.** Eight features are measured locally with OpenCV —
canvas spatial usage, edge density, line sharpness, brightness, contrast,
dominant tone, dominant colours — and eight Grad-CAM scores say where each model
looked. The generated report is required to quote those figures inline rather
than describe feelings in the abstract. Every claim in the explanation tab
points back to something measured.

## What it produces

A three-tab report, narrated aloud so the child hears it too, not just an adult
reading a screen:

- **Insights** — primary emotion, psychological framing, emotional trends over
  time
- **Action** — guidance for the parent, questions worth asking, a suggested
  next step
- **Explanation** — the raw metrics, the Grad-CAM activations, and how the
  numbers combine into the verdict

Plus a playful, child-facing Arabic challenge chosen to match the concluded
emotion — the only field addressed to the child directly.

## Scope

- Four classes: happy, sad, angry, fear, with an explicit "not sure" outcome
- English and Arabic throughout — UI, reports, prompts, and narration
- Narration via Microsoft Edge neural TTS, voice selected by companion gender
- Five companions, per-child profiles, drawing history, emotional trends
- Exportable summary for a professional

## Limitations

Stated plainly, because it matters:

- **Drawing-based emotion inference is a heuristic, not evidence.** The
  projective-drawing literature is contested, and readings vary substantially
  across cultures and ages.
- **Four classes only.** Ambiguous drawings will be misread; the system says so
  when it can.
- **Grad-CAM shows where a model looked, not why it concluded what it did.**
  It is a debugging aid, not an explanation of a child's inner state.
- **AI can be wrong.** Every output should start a conversation, not settle one.

## Status

An early-stage project. See [`LICENSE`](LICENSE) for terms, including the data
handling obligations that apply if you ever deploy this with real children's
data.

---

*The implementation is maintained privately. This repository holds the
overview, design rationale, and license.*
