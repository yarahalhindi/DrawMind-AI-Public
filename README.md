# DrawMind AI

An AI companion that reads a child's drawing and turns it into a gentle,
spoken conversation — not a diagnosis.

> **Not a medical device.** DrawMind produces a preliminary reading for parents
> and educators. It is not a clinical diagnosis, psychiatric assessment, or
> medical recommendation, and it does not replace professional evaluation.

---

## The idea

Children don't always have the words for how they feel. They do have crayons.

A child draws on the canvas and taps *analyze*. DrawMind returns a plain-language
emotional reading, narrates it aloud in Arabic and English, and hands the parent
a short, structured summary they can bring to a professional if they want one.

## How it decides

```
  child's drawing
        │
   ┌────┴─────┐
   ▼          ▼
 happy/sad  angry/fear        two small, fast CNNs
   model      model
   └────┬─────┘
        │
   agree? ──yes──► confident read ────────┐
        │                                │
        no                               │
        ▼                                │
   vision model (tie-break)              │
        │                                │
        ▼                                ▼
   14 visual features ──────► language model ──► report ──► spoken narration
```

Small models do the work. An expensive vision model is only called when the two
fast ones disagree, so most drawings never pay for it.

The 14 extracted features describe the *marks*, never the child: canvas spatial
use, edge density, stroke sharpness, dominant colour tone.

## What makes it different

- **Two-stage cascade** instead of one large model — cheap enough to run on
  every drawing, and honest about when it isn't confident.
- **Speaks back** — Edge TTS narrates the result, so a child hears it too, not
  just an adult reading a screen.
- **Arabic and English** — bilingual companions, prompts, and narration.
- **Built around real rate limits** — bounded retries, reduced token usage, and
  a vision cap, so the analysis doesn't fail mid-session.

## Limitations, stated plainly

- Emotion detection from drawings is a **heuristic, not evidence**. Research on
  projective drawing interpretation is contested, and results vary across
  cultures and ages.
- Four classes only: happy, sad, angry, fear. Ambiguous drawings will be
  misread.
- Every output should start a conversation, not settle one.

## Status

University expo project. See [`LICENSE`](LICENSE) for terms and for the data
handling obligations that apply if you deploy this with real children's data.
