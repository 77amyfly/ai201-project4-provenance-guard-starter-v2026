# Provenance Guard

> ### 👋 Start here
>
> **New to this repo? Read [RUNNING.md](RUNNING.md) first.**
>
> Once `python test.py` passes, in one terminal:
>
> ```bash
> python app.py
> ```
>
> and in another:
>
> ```bash
> curl -X POST http://127.0.0.1:5000/ping \
>   -H "Content-Type: application/json" \
>   -d '{"message": "hello"}'
> ```
>
> That's a web service. Something sent it text and it sent something back.
> Everything this unit is filling in the middle.
>
> **The rest of this file is your submission.** Fill it in as you go.

---

<!-- ─────────────────────────────────────────────────────────────────────────
     Unit 7 asks for the first five sections. Unit 8 adds the five below.

     Everything is pasted as TEXT. A pasted curl response gets full credit;
     a picture of your terminal gets none.
     ───────────────────────────────────────────────────────────────────────── -->

<!-- ═══════════════════════ UNIT 7 — THE BUILD ═══════════════════════ -->

## What This Does


A writing site sends Provenance Guard a piece of text. Seven steps:

1. **Receive and check.** `POST /submit` validates the input.
2. **Signal one.** A local language model scores the text.
3. **Signal two.** My style program scores its shape.
4. **Combine.** Both scores become one confidence and a guess.
5. **Label.** The confidence picks one of three labels.
6. **Log.** The scores, guess, and status go in the audit log.
7. **Respond.** The site gets the result.


---

## Signals and Scoring


### Signal one — the model signal

**What it measures:** How predictable the wording is to a small local language model (GPT-2). The model reads the text and computes perplexity, a measure of how surprised it is by each next word. A logistic function, `1 / (1 + (perplexity / 45) ** 1.6)`, turns that into a score from 0 to 1, where higher means more likely AI.

**Why that might differ between human and AI writing:** AI text is built by choosing likely next words, so another language model finds it easy to predict. Human writing tends to include odd word choices, personal details, and tangents that a model finds more surprising.

**What it can't see:** Who actually wrote the text. It measures how predictable the wording is, not authorship. Plain, common words in short sentences score as machine-like, and many real people write that way, such as second-language writers. Human work that passes through AI wording, like a translation, also looks partly AI. In the other direction, text full of rare words, jargon, or filenames scores as human, because GPT-2 cannot predict those words.

### Signal two — the style signal

**What it measures:** The shape of the text, not its meaning: how much sentence length varies, how much vocabulary repeats, and how much punctuation is used. The three are combined into one score from 0 to 1, where higher means more likely AI.

**Why that might differ between human and AI writing:** AI sentences tend to be even and mid-length with a narrow word range. Human writing is lumpier: short and long sentences mixed together.

**What it can't see:** What the text says. Adding typos, splitting long sentences, or pasting in a quotation changes the shape without changing the substance, so the score moves.

### The False Positive, Traced

A writer who keeps their wording simple writes a short paragraph about their dog (short sentences, common words, a few typos) and submits it. They wrote every word.

1. `POST /submit` receives the text.
2. Signal one scores it 0.40. Common words and short sentences are easy for GPT-2 to predict, though the typos pull the score down.
3. Signal two scores it 0.56. Short, even sentences and simple vocabulary look machine-like.
4. The combined score is 0.45, so the system says "unsure" instead of "human".

**What the label says:** "We're not sure how this piece was written, and that's all this means. Nothing is wrong. If you'd like us to take another look, you can ask for a review."

**What they can do:** `POST /appeal` with the `content_id` and an explanation. The item moves to `under_review` and the appeal is logged next to the original decision.

**What this means for thresholds:** None of my human-written or AI-translated samples crossed 0.65, but plain writing lands in the middle, so a writer like this gets "unsure" at best. I set the AI line high so accusing a writer takes strong evidence. The price is that more AI text lands in "unsure" or "human".

### Endpoint Contracts

**POST /submit**
- In: `text`, `creator_id`
- Out: `content_id`, `guess`, `confidence`, `label`, `model_score`, `style_score`

**POST /appeal**
- In: `content_id`, `reasoning`
- Out: a confirmation that the item is now `under_review`
- Checks that the `content_id` exists; records the appeal in the log


### The combining rule

<!-- Write the rule in words, then give the numbers. What happens when the two
     signals disagree? -->
**The rule:** A fixed weighted average. Signal one (the model signal) counts for most of the score, and signal two (the style signal) counts for less. Both run from 0 to 1 and both mean "higher = more likely AI", so they can be averaged directly. There is no special rule for disagreement: when the signals split, the average lands between them.

**The numbers:** `0.7 × model_score + 0.3 × style_score`. Signal one separated my samples much better (0.17 to 0.84), while signal two stayed between 0.40 and 0.56 on all four, so it gets the smaller weight. I kept it above zero because it looks at different properties of the text, so it is a small check on signal one's blind spot. The weights are set by hand from four samples, not learned from data.

**Where it lives:** `scoring.py::combine_signals` (weights in `config.py`)

<!-- ⚠️ The grader checks your code against that line. If your rule lives
     somewhere else, say where. -->

**A case where my two signals split, and what my rule does with it:**  A course-instruction paragraph written by a human scored 0.17 on signal one (jargon and filenames are hard for GPT-2 to predict) but 0.52 on signal two (its sentences are ordinary in length and vocabulary). The weighted average gives 0.27, which falls on the human side, so signal one's confidence carries through and signal two's lack of opinion doesn't drag it toward "AI".

---


## Label Variants

| Label | Score range | The exact text a reader sees |
|---|---|---|
| high-confidence human | below 0.35 | We think this was written by a person. Thanks for sharing it. |
| unsure | 0.35 to 0.65 | We're not sure how this piece was written, and that's all this means. Nothing is wrong. If you'd like us to take another look, you can ask for a review. |
| high-confidence AI | above 0.65 | We think this may have been written with an AI tool, but we could be wrong. If you wrote it yourself, just let us know and we'll take another look. |

**Why I worded the "unsure" one this way:**
The person reading it may have written every word themselves, so it says plainly that we are not sure, and that this is all it means, before anything else. "Nothing is wrong" keeps it from sounding like an accusation, and "ask for a review" points to the appeal path, which moves the item to `under_review`. I left out any promise that the piece stays published, because that is the site's decision, not this service's. I also left out any claim about which part looks like AI, because the system scores the whole text and cannot say which part.


---
## Sample Run

**A submission**

```bash
$ curl -X POST http://127.0.0.1:5000/submit \
  -H "Content-Type: application/json" \
  -d '{"text": "I have a dog whose name is buu. he is 6ys old. He like playing ball with me and swimming in the lake. When we went to the beach, he dived in the water and stay there for whole day. He is really kind and nice, never mad with us. I cant imagin in the futere I will oh no, never!", "creator_id": "me"}'
```

```json
{
  "confidence": 0.4511,
  "content_id": "8ac54785-c261-44f7-85c0-3abb7050eabd",
  "guess": "unsure",
  "label": "We're not sure how this piece was written, and that's all this means. Nothing is wrong. If you'd like us to take another look, you can ask for a review.",
  "model_score": 0.4046,
  "style_score": 0.5597
}
```

**An appeal**

```bash
$ curl -X POST http://127.0.0.1:5000/appeal \
  -H "Content-Type: application/json" \
  -d '{"content_id": "8ac54785-c261-44f7-85c0-3abb7050eabd", "reasoning": "I wrote this myself about my own dog."}'
```

```json
{
  "content_id": "8ac54785-c261-44f7-85c0-3abb7050eabd",
  "message": "Thanks. We've recorded your appeal and the piece is now under review.",
  "original_decision": {
    "confidence": 0.4511,
    "guess": "unsure",
    "label": "We're not sure how this piece was written, and that's all this means. Nothing is wrong. If you'd like us to take another look, you can ask for a review."
  },
  "status": "under_review",
  "your_reasoning": "I wrote this myself about my own dog."
}
```

**What the log shows afterwards**

```bash
$ curl "http://127.0.0.1:5000/log?limit=2"
```

```json
{
  "count": 2,
  "entries": [
    {
      "combined_score": 0.4511,
      "content_id": "8ac54785-c261-44f7-85c0-3abb7050eabd",
      "creator_id": "me",
      "guess": "unsure",
      "label": "We're not sure how this piece was written, and that's all this means. Nothing is wrong. If you'd like us to take another look, you can ask for a review.",
      "model_score": 0.4046,
      "status": "decided",
      "style_score": 0.5597,
      "timestamp": "2026-11-09T19:24:56+00:00"
    },
    {
      "content_id": "8ac54785-c261-44f7-85c0-3abb7050eabd",
      "creator_id": "me",
      "event": "appeal",
      "reasoning": "I wrote this myself about my own dog.",
      "status": "under_review",
      "timestamp": "2026-11-09T19:25:56+00:00"
    }
  ]
}
```


---

## How I Used AI

**Moment 1**

- *What I asked for:* I asked Claude to search the web for how existing products tell users whether a text was written by AI or by a person.
- *What came back:* Claude summarized the wording used by products that check student assignments and papers, and noted I couldn't copy it directly, because those results are written for teachers, while my labels are read by the writer themselves.
- *What I changed:* I decided to make my labels more gentle, since they speak directly to the writer. 


**Moment 2**

- *What I asked for:* I wasn't sure what the criteria prompt "how far apart should clearly-AI and clearly-human text score?" meant. I read it as how to draw the label ranges, and asked Claude whether that was right.
- *What came back:* Claude said no. The label ranges are thresholds, which I choose. The score gap is measured: I score a batch of clearly-AI and clearly-human texts and see how far apart the two groups land. Scores come from the two signals and the combining rule, so moving the thresholds cannot change the gap. It only changes which label each score gets.
- *What I changed:* I rewrote criterion 2 as something I can measure: the average AI score minus the average human score must be at least 0.5. I first tried "lowest AI score minus highest human score" at 0.4, then switched to averages, because one misjudged text would break the lowest-minus-highest version and I wanted this criterion to test whether the two groups separate overall. Individual misjudgments are covered by criteria 1 and 4. I also raised the target from 0.4 to 0.5 so it is not too easy.

<!-- ═══════════════════════ UNIT 8 — THE TEST ═══════════════════════ -->

---

## Rate Limiting

**My limits:** ___ per minute, ___ per day

**Why those numbers and not others:**
<!-- Think about two people: a real writer submitting their own work a few
     times an hour, and a script sending a thousand variations to map your
     thresholds. Your numbers have to be liveable for the first and hostile to
     the second. -->

**What a caller counts as:** <!-- per address, or per creator_id? They fail
differently — one script can look like a thousand callers, and one household
can look like one. -->

**The run of status codes when I pushed past it:**

<!-- python run_attacks.py --flood 15 -->

```

```

---

## Attack Run — Before

<!-- Every attack with its outcome and a held-or-broke mark.
     `run_attacks.py --label before` produces the table. -->



**Ten audit log entries from the run**

<!-- Choose entries that show the interesting failures, not the ten easiest. -->

```json

```

**The single worst outcome** — the one I'd least want to explain to a writer
whose work got caught by it:



---

## Run Log and Verdicts

<!-- Your five criteria across three trials. `run_eval.py --label before`. -->

| Criterion | Target | Run 1 | Run 2 | Run 3 | Verdict |
|---|---|---|---|---|---|
| 1.  |  |  |  |  |  |
| 2.  |  |  |  |  |  |
| 3.  |  |  |  |  |  |
| 4.  |  |  |  |  |  |
| 5.  |  |  |  |  |  |

**Real output from one trial**, pasted as text, naming the file and function
that produced it:

```json

```

**Diagnoses**

<!-- For each miss AND each broken attack: the stage and the mechanism.

     Your service has five stages: the route, signal one, signal two, the
     combining rule, and the label mapping.

     Not a diagnosis:  "The evasion attacks got through."
     A diagnosis:      "Four evasion inputs got through. All four had typos
                        added to AI-generated text. My second signal measures
                        word variety, and typos raise word variety — so it read
                        them as more human. My combining rule weights that
                        signal at 0.6, so it dragged the whole score down."

     Look for a pattern. Five failures on one signal is one problem, not five. -->



---

## The Improvement

**What I changed:**

**Which diagnosis pointed at it:**

### Attack Run — After

<!-- Same format. `run_attacks.py --label after` -->



**Did it help, and how do I know:**

<!-- Widening the unsure band usually fixes one problem and creates another.
     Reporting that trade honestly is worth full credit. -->



---

## What's Still Broken

<!-- For each attack still getting through and each criterion still missed:
     what you'd do, and why you stopped. -->



**The trade I'd make if this ran for real:**

<!-- Two sentences. Fewer wrong accusations means more AI text getting through.
     Say which way you'd go and WHO PAYS FOR IT. -->



<!-- ═════════════════════════════════════════════════════════════════════

     SUBMISSION CHECKLIST — unit 7

       [ ] criteria.md has five numbered criteria, each naming a target
       [ ] Each has a reason underneath
       [ ] POST /submit returns content_id, guess, confidence, label,
           model_score and style_score
       [ ] Two signals that measure DIFFERENT things, each with a named blind spot
       [ ] Three label variants, all reachable, written out in full
       [ ] POST /appeal changes status and writes to the log
       [ ] Signals and Scoring names scoring.py::combine_signals
       [ ] Sample Run: a submission AND an appeal, as pasted text
       [ ] At least four new commits
       [ ] Repository URL submitted — WRITE IT DOWN

     SUBMISSION CHECKLIST — unit 8

       [ ] Rate limiting on /submit, with your numbers justified
       [ ] The pasted run of status codes showing 429
       [ ] Audit log with all nine fields, recording rejections too
       [ ] The attack set run IN FULL, every input with an outcome
       [ ] Ten audit log entries pasted as text
       [ ] Five criteria across three trials
       [ ] A diagnosis for every miss and every broken attack — stage AND mechanism
       [ ] One improvement, with Attack Run — After
       [ ] What's Still Broken, including the production trade
       [ ] At least four new commits
       [ ] The SAME repository URL as last unit

     Do not delete and recreate this repository.
     ═════════════════════════════════════════════════════════════════════ -->

---

📖 **How to run this project: [RUNNING.md](RUNNING.md)**
