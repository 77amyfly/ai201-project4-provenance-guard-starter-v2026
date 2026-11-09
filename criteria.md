# Acceptance criteria — Provenance Guard

Five criteria that say what "working" means for this service, written in unit 7
**before** you built it.

**All five are yours.** None are given.

An acceptance criterion names a number or something plainly observable. *"The
detector is accurate"* is an opinion — and perfect AI detection isn't possible
anyway, so it's an opinion about something nobody can deliver. *"Of 10 pieces I
wrote myself, at most 1 is labelled high-confidence AI"* is a criterion.

Under each, write a sentence or two on **why that target**. *"I allow at most 1
false positive in 10 because on a writing site a wrong accusation costs more
than a missed detection"* is a real answer.

> Missing your own targets next unit costs you nothing. Setting a target so
> easy you can't miss it does.

---

## Pick numbers you can defend

**Cover at least three of these five areas.** They're prompts, not a form.

| Area | A question it could answer |
|---|---|
| False positives | How often may a human's writing get called AI? |
| Score spread | How far apart should clearly-AI and clearly-human text score? |
| Label coverage | Is every one of your three labels actually reachable? |
| The appeal path | Does an appeal always change status and always get recorded? |
| Bad input | What happens when `text` is missing, empty, or enormous? |

Two things worth knowing before you pick numbers:

- **The expensive mistake is one-sided.** Missing some AI text costs the site a
  little. Accusing a real writer costs that person a lot. A criterion that
  treats both errors as equally bad is describing a system nobody would ship.
- **Unit 8 tests across three trials, and the target has to hold across all
  three.** Your first signal runs a model, so identical input can score
  differently twice. A target of "at most 1 in 10" against trials of 2, 1, 2 is
  a **miss**.

---
## 1.

Take 10 randomly chosen passages of human-written English from public media, each 80 to 150 words. At most 1 may be labeled "high-confidence AI".

**Why this target:** A false accusation costs a writer more than a missed AI text, so this target is strict.


---

## 2.

Take 10 clearly-AI passages and 10 clearly-human passages. The average AI score minus the average human score must be at least 0.5.

**Why this target:** If the two groups barely separate on average, the signals can't tell them apart and the labels mean nothing.

---

## 3.

Run 10 human passages, 10 AI passages, and 10 human/AI mixed passages through /submit. Each of the three labels appears at least once.

**Why this target:** A label no text can reach is a promise the system can't keep.

---

## 4.

Take 10 AI-generated passages, each 80 to 150 words. At most 2 may fail to be labeled "high-confidence AI".

**Why this target:** This is a false negative, and a missed AI text costs less than a false accusation, so the target is looser than criterion 1. The looseness is deliberate: it leaves room to keep criterion 1 strict.

---

## 5.

Send `/submit` 10 requests with `text` missing or empty. All 10 must be rejected with an error message, and none may be scored.

**Why this target:** There is nothing to judge in an empty submission, so the system must refuse instead of guessing.

---

<!-- ─────────────────────────────────────────────────────────────────────────
     UNIT 8 — read this before you change anything above.

     If a criterion turns out to be BROKEN rather than merely unmet, you can
     revise it, and that earns credit. But never delete or edit the original
     line. Add the revision underneath, like this:

         ## 2. Uncertain cases land in the unsure band

         The system handles uncertain cases well.

         **Why this target:** ...

         > **Revised in unit 8:** Text I judged borderline scores between
         > 0.4 and 0.6 in at least 4 of 5 cases.
         >
         > **Why revised:** "well" gave me nothing to check. I couldn't
         > reach a verdict from it at all.

     That's a revision because the criterion couldn't be MEASURED.

     Lowering a target because you missed it is not a revision, and it costs
     you the point:

         ✗ "At most 1 of 10 of my own pieces flagged as AI" → "at most 3
            of 10", because 1 of 10 turned out to be hard.

     A number you missed stays where it is, gets diagnosed, and gets a fix
     attempted. That's where the points are.
     ───────────────────────────────────────────────────────────────────────── -->
