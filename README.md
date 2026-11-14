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

**Moment 3**

- *What I asked for:* I gave Claude my attack-run report from the "before" run and asked it to read and summarize the results by attack family, and to say which pipeline stage each family points at.
- *What came back:* Claude grouped the 56 attacks by family and matched each to a stage: typos beat signal one, evasions that landed on "unsure" came from my label thresholds, malformed inputs exposed missing checks in the route, and the false positives came from both signals reading plain human writing as AI. It also showed that the attack set's own "targeting" labels were not always the real cause, so I checked the scores myself.
- *What I changed:* Because I now knew the cause of each failure, I looked for a fix aimed at one specific stage.


<!-- ═══════════════════════ UNIT 8 — THE TEST ═══════════════════════ -->

---

## Rate Limiting

**My limits:** 10 per minute, 100 per day

**Why those numbers and not others:**

10 a minute is one submission every six seconds, far faster than anyone writing their own work. I could have used 3 and still not caught a real writer, but 10 leaves room for quick drafts and browser retries.
100 a day is what costs an attacker something. Finding my 0.35 and 0.65 boundaries by binary search takes dozens of probes, so 100 a day buys an outline and takes days of visible log. At 500 a day the same script finishes in an afternoon.
Ten a minute for ten minutes is the whole daily budget, so the minute limit shapes bursts and the day limit is the real wall.


**What a caller counts as:** creator_id, falling back to the IP address when a request has no usable creator_id. That stops one creator from flooding, but a script can dodge it by rotating creator_ids, and malformed requests all share the sender's address.
<!-- per address, or per creator_id? They fail
differently — one script can look like a thousand callers, and one household
can look like one. -->

**The run of status codes when I pushed past it:** 

<!-- python run_attacks.py --flood 15 -->

```
200 200 200 200 200 200 200 200 200 200 429 429 429 429 429
```

---

## Attack Run — Before

56 attacks, run with `python run_attacks.py --set attack_set --label before`.
Full report with content ids and every response body:
`results/attack_run_2026-11-13_1446_before.md`.

**HELD 34 · BROKE 22**

| Family | Attacks | HELD | BROKE |
|---|---|---|---|
| `evasion` | 10 | 1 | 9 |
| `false_positive` | 8 | 2 | 6 |
| `prompt_injection` | 6 | 6 | 0 |
| `malformed` | 20 | 13 | 7 |
| `flood_same_creator` | 12 | 12 | 0 |

**Every attack, with what came back and a held-or-broke mark**

| ID | Family | Targeting | Status | Guess | Score | Verdict |
|---|---|---|---|---|---|---|
| EV01 | evasion | signal two: typos raise word variety | 200 | human | 0.296 | BROKE |
| EV02 | evasion | signal two: broken-up sentences raise length spread | 200 | unsure | 0.534 | BROKE |
| EV03 | evasion | signal two: a pasted quotation changes the shape | 200 | unsure | 0.622 | BROKE |
| EV04 | evasion | signal one: unusual words raise perplexity | 200 | unsure | 0.604 | BROKE |
| EV05 | evasion | both signals: heavy human editing pass | 200 | unsure | 0.352 | BROKE |
| EV06 | evasion | signal two: one very long sentence and one very short | 200 | unsure | 0.515 | BROKE |
| EV07 | evasion | signal two: padding with punctuation | 200 | unsure | 0.557 | BROKE |
| EV08 | evasion | signal one: text below the token window | 200 | human | 0.343 | BROKE |
| EV09 | evasion | both: AI text with a human opening paragraph bolted on | 200 | unsure | 0.643 | BROKE |
| EV10 | evasion | signal two: repeated stanza structure | 200 | ai | 0.914 | HELD |
| FP01 | false_positive | plain, clear human writing with no flourishes | 200 | unsure | 0.609 | HELD |
| FP02 | false_positive | human writing in a formal register | 200 | ai | 0.718 | BROKE |
| FP03 | false_positive | a non-native English speaker writing carefully | 200 | ai | 0.758 | BROKE |
| FP04 | false_positive | a student essay written to a rubric | 200 | ai | 0.761 | BROKE |
| FP05 | false_positive | technical documentation written by a person | 200 | ai | 0.765 | BROKE |
| FP06 | false_positive | a person imitating a formal template | 200 | ai | 0.786 | BROKE |
| FP07 | false_positive | human writing that repeats a phrase deliberately | 200 | ai | 0.787 | BROKE |
| FP08 | false_positive | very short genuine human submission | 200 | unsure | 0.382 | HELD |
| PI01 | prompt_injection | instruction to ignore the check | 200 | unsure | 0.464 | HELD |
| PI02 | prompt_injection | instruction disguised as a system message | 200 | human | 0.268 | HELD |
| PI03 | prompt_injection | instruction wrapped in the writer's voice | 200 | unsure | 0.573 | HELD |
| PI04 | prompt_injection | attempt to make the label text hostile | 200 | unsure | 0.498 | HELD |
| PI05 | prompt_injection | attempt to inject into the audit log | 200 | unsure | 0.538 | HELD |
| PI06 | prompt_injection | instruction plus real human text | 200 | unsure | 0.570 | HELD |
| MF01 | malformed | text is only whitespace | 400 | — | — | HELD |
| MF02 | malformed | text is a single character | 400 | — | — | HELD |
| MF03 | malformed | text is control characters | 200 | human | 0.200 | BROKE |
| MF04 | malformed | text is only punctuation | 200 | human | 0.308 | BROKE |
| MF05 | malformed | text is a very long single word | 200 | ai | 0.898 | BROKE |
| MF06 | malformed | text is emoji only | 200 | ai | 0.998 | BROKE |
| MF07 | malformed | text is right-to-left script | 200 | ai | 0.934 | BROKE |
| MF08 | malformed | text is 60,000 characters | 200 | ai | 0.941 | BROKE |
| RQ01 | malformed | no text field at all | 400 | — | — | HELD |
| RQ02 | malformed | no creator_id | 400 | — | — | HELD |
| RQ03 | malformed | text is a number | 400 | — | — | HELD |
| RQ04 | malformed | text is a list | 400 | — | — | HELD |
| RQ05 | malformed | text is null | 400 | — | — | HELD |
| RQ06 | malformed | creator_id is an object | 400 | — | — | HELD |
| RQ07 | malformed | body is a JSON array, not an object | 500 | — | — | BROKE |
| RQ08 | malformed | body is unparseable JSON | 400 | — | — | HELD |
| RQ09 | malformed | body is empty | 400 | — | — | HELD |
| RQ10 | malformed | body is form-encoded, not JSON | 400 | — | — | HELD |
| RQ11 | malformed | body is JSON claiming to be plain text | 400 | — | — | HELD |
| RQ12 | malformed | deeply nested JSON | 400 | — | — | HELD |
| FL01_01 | flood_same_creator | 20 rapid submissions from one creator | 200 | human | 0.220 | HELD |
| FL01_02 | flood_same_creator | 20 rapid submissions from one creator | 200 | human | 0.220 | HELD |
| FL01_03 | flood_same_creator | 20 rapid submissions from one creator | 200 | human | 0.220 | HELD |
| FL01_04 | flood_same_creator | 20 rapid submissions from one creator | 200 | human | 0.220 | HELD |
| FL01_05 | flood_same_creator | 20 rapid submissions from one creator | 200 | human | 0.220 | HELD |
| FL01_06 | flood_same_creator | 20 rapid submissions from one creator | 200 | human | 0.220 | HELD |
| FL01_07 | flood_same_creator | 20 rapid submissions from one creator | 200 | human | 0.220 | HELD |
| FL01_08 | flood_same_creator | 20 rapid submissions from one creator | 200 | human | 0.220 | HELD |
| FL01_09 | flood_same_creator | 20 rapid submissions from one creator | 200 | human | 0.220 | HELD |
| FL01_10 | flood_same_creator | 20 rapid submissions from one creator | 200 | human | 0.220 | HELD |
| FL01_11 | flood_same_creator | 20 rapid submissions from one creator | 429 | — | — | HELD |
| FL01_12 | flood_same_creator | 20 rapid submissions from one creator | 429 | — | — | HELD |


**Ten audit log entries from the run**


```json
{"timestamp": "2026-11-13T22:46:29+00:00", "content_id": "6c036c7a-13a0-46eb-9eec-fd74c5e41112", "creator_id": "attacker_FP03", "guess": "ai", "model_score": 0.8051, "style_score": 0.6496, "combined_score": 0.7584, "label": "We think this may have been written with an AI tool, but we could be wrong. If you wrote it yourself, just let us know and we'll take another look.", "status": "decided"}
{"timestamp": "2026-11-13T22:46:30+00:00", "content_id": "d213e011-e1db-4513-b1c1-58bd9c4c7c5a", "creator_id": "attacker_FP07", "guess": "ai", "model_score": 0.8816, "style_score": 0.5662, "combined_score": 0.787, "label": "We think this may have been written with an AI tool, but we could be wrong. If you wrote it yourself, just let us know and we'll take another look.", "status": "decided"}
{"timestamp": "2026-11-13T22:46:30+00:00", "content_id": "e29e304c-5ab2-4b4b-a8b5-8e251dbe2511", "creator_id": "attacker_MF07", "guess": "ai", "model_score": 0.9064, "style_score": 1.0, "combined_score": 0.9345, "label": "We think this may have been written with an AI tool, but we could be wrong. If you wrote it yourself, just let us know and we'll take another look.", "status": "decided"}
{"timestamp": "2026-11-13T22:46:30+00:00", "content_id": "c855e909-c812-433c-93c9-770d82e6248a", "creator_id": "attacker_MF08", "guess": "ai", "model_score": 0.9876, "style_score": 0.8335, "combined_score": 0.9414, "label": "We think this may have been written with an AI tool, but we could be wrong. If you wrote it yourself, just let us know and we'll take another look.", "status": "decided"}
{"timestamp": "2026-11-13T22:46:29+00:00", "content_id": "a5b3cb7d-1a83-40da-800e-38e34024486d", "creator_id": "attacker_EV01", "guess": "human", "model_score": 0.1635, "style_score": 0.6039, "combined_score": 0.2956, "label": "We think this was written by a person. Thanks for sharing it.", "status": "decided"}
{"timestamp": "2026-11-13T22:46:29+00:00", "content_id": "1786adc3-8db3-4e20-baf5-dabbac9ab694", "creator_id": "attacker_EV08", "guess": "human", "model_score": 0.2452, "style_score": 0.5724, "combined_score": 0.3434, "label": "We think this was written by a person. Thanks for sharing it.", "status": "decided"}
{"timestamp": "2026-11-13T22:46:29+00:00", "content_id": "e16d124e-507d-4855-bfe3-00585bc17045", "creator_id": "attacker_EV09", "guess": "unsure", "model_score": 0.7042, "style_score": 0.5018, "combined_score": 0.6435, "label": "We're not sure how this piece was written, and that's all this means. Nothing is wrong. If you'd like us to take another look, you can ask for a review.", "status": "decided"}
{"timestamp": "2026-11-13T22:46:30+00:00", "content_id": "5ec973e8-adba-47f3-a2df-236676a57577", "creator_id": "attacker_MF06", "guess": "ai", "model_score": 0.9969, "style_score": 1.0, "combined_score": 0.9978, "label": "We think this may have been written with an AI tool, but we could be wrong. If you wrote it yourself, just let us know and we'll take another look.", "status": "decided"}
{"timestamp": "2026-11-13T22:46:30+00:00", "content_id": "86b83bd7-0848-4d02-95cd-bb0ec89e3a3b", "creator_id": "attacker_MF03", "guess": "human", "model_score": 0.0, "style_score": 0.6667, "combined_score": 0.2, "label": "We think this was written by a person. Thanks for sharing it.", "status": "decided"}
{"timestamp": "2026-11-13T22:46:31+00:00", "creator_id": "attacker_flood", "event": "rejected", "status": "rejected", "reason": "rate_limited", "limit": "10 per 1 minute"}
```


**The single worst outcome** — the one I'd least want to explain to a writer
whose work got caught by it:

**FP03**, a non-native English speaker writing carefully, scored 0.7584 and was
told we think an AI tool wrote it. MF08 scored higher at 0.9414, but FP03 costs
more. Someone writing in a second language keeps their words common and their
sentences even, and that is exactly what signal one reads as machine-like. Its
`model_score` was 0.8051, the second highest of any human text in the run. So
the system is not making a random mistake here. It is punishing careful
English, and the people most likely to write that way are the people least able
to argue back.



---

## Run Log and Verdicts

| Criterion | Target | Run 1 | Run 2 | Run 3 | Verdict |
|---|---|---|---|---|---|
| 1. Human writing labelled AI | <= 1 of 10 | 5/10 | 5/10 | 5/10 | **MISSED** |
| 2. Mean AI score minus mean human score | >= 0.5 | 0.0075 | 0.0075 | 0.0075 | **MISSED** |
| 3. All three labels reachable | 3 of 3 | 3/3 | 3/3 | 3/3 | MET |
| 4. AI passages not labelled AI | <= 2 of 10 | 7/10 | 7/10 | 7/10 | **MISSED** |
| 5. Empty or missing text refused | 10 of 10 | 10/10 | 10/10 | 10/10 | MET |

All three trials returned identical numbers. Neither signal samples: GPT-2 runs
without sampling and the style signal is arithmetic over the text, so the same
input scores the same every time. Three matching trials are the expected result
here, not a sign that a trial was skipped.

**Real output from one trial**, pasted as text. Produced by
`run_eval.py::main`, which posts each body to `app.py::submit`; the scores come
from `scoring.py::combine_signals`. Full output for all three trials is in
`results/run_2026-11-13_1703_before.md`.

**Criterion 1** — ten human passages. Five came back `ai`, against a target of
at most one.

```json
[
  {
    "status": 200,
    "guess": "human",
    "confidence": 0.3433,
    "model_score": 0.2927,
    "style_score": 0.4613,
    "label": "We think this was written by a person. Thanks for sharing it.",
    "sent": "In your breakout: bring your worst failure. Share the input and what y"
  },
  {
    "status": 200,
    "guess": "unsure",
    "confidence": 0.5292,
    "model_score": 0.493,
    "style_score": 0.6136,
    "label": "We're not sure how this piece was written, and that's all this means. Nothing is wrong. If you'd like us to take another look, you can ask for a review.",
    "sent": "Prorated by the second. If a service is active for ten seconds in a gi"
  },
  {
    "status": 200,
    "guess": "ai",
    "confidence": 0.6595,
    "model_score": 0.7661,
    "style_score": 0.4107,
    "label": "We think this may have been written with an AI tool, but we could be wrong. If you wrote it yourself, just let us know and we'll take another look.",
    "sent": "Dogs have been bred for specific behaviors, sensory abilities, and phy"
  },
  {
    "status": 200,
    "guess": "unsure",
    "confidence": 0.4192,
    "model_score": 0.4357,
    "style_score": 0.3808,
    "label": "We're not sure how this piece was written, and that's all this means. Nothing is wrong. If you'd like us to take another look, you can ask for a review.",
    "sent": "What makes your program special? Intentional Breeding, Not Mass Produc"
  },
  {
    "status": 200,
    "guess": "unsure",
    "confidence": 0.4952,
    "model_score": 0.551,
    "style_score": 0.365,
    "label": "We're not sure how this piece was written, and that's all this means. Nothing is wrong. If you'd like us to take another look, you can ask for a review.",
    "sent": "I would assume #1 but only because dog meat is not commonly eaten in t"
  },
  {
    "status": 200,
    "guess": "unsure",
    "confidence": 0.5666,
    "model_score": 0.5865,
    "style_score": 0.5203,
    "label": "We're not sure how this piece was written, and that's all this means. Nothing is wrong. If you'd like us to take another look, you can ask for a review.",
    "sent": "Since the beginning of humankind, we have gone all the way from caves "
  },
  {
    "status": 200,
    "guess": "ai",
    "confidence": 0.705,
    "model_score": 0.7907,
    "style_score": 0.5051,
    "label": "We think this may have been written with an AI tool, but we could be wrong. If you wrote it yourself, just let us know and we'll take another look.",
    "sent": "Imagine yourself as a glass statue. It tips over and smashes into a mi"
  },
  {
    "status": 200,
    "guess": "ai",
    "confidence": 0.6985,
    "model_score": 0.7808,
    "style_score": 0.5064,
    "label": "We think this may have been written with an AI tool, but we could be wrong. If you wrote it yourself, just let us know and we'll take another look.",
    "sent": "The more important question, from our perspective, is the one you didn"
  },
  {
    "status": 200,
    "guess": "ai",
    "confidence": 0.6698,
    "model_score": 0.7186,
    "style_score": 0.5558,
    "label": "We think this may have been written with an AI tool, but we could be wrong. If you wrote it yourself, just let us know and we'll take another look.",
    "sent": "Modern AI is rapidly shifting the burden of computing from local machi"
  },
  {
    "status": 200,
    "guess": "ai",
    "confidence": 0.7436,
    "model_score": 0.8329,
    "style_score": 0.5354,
    "label": "We think this may have been written with an AI tool, but we could be wrong. If you wrote it yourself, just let us know and we'll take another look.",
    "sent": "The Dietary Guidelines Advisory Committee, which meets every five year"
  }
]
```

**Criterion 2** — ten AI passages (the first ten) and ten human
passages (the last ten). Mean confidence was 0.5905 for the AI group and 0.5830
for the human group, a gap of 0.0075 against a target of 0.5.

```json
[
  {
    "status": 200,
    "guess": "ai",
    "confidence": 0.7767,
    "model_score": 0.8534,
    "style_score": 0.5976,
    "label": "We think this may have been written with an AI tool, but we could be wrong. If you wrote it yourself, just let us know and we'll take another look.",
    "sent": "Urban green spaces play a vital role in improving the quality of life "
  },
  {
    "status": 200,
    "guess": "unsure",
    "confidence": 0.5395,
    "model_score": 0.5396,
    "style_score": 0.5393,
    "label": "We're not sure how this piece was written, and that's all this means. Nothing is wrong. If you'd like us to take another look, you can ask for a review.",
    "sent": "Remote work has fundamentally changed the way organizations approach c"
  },
  {
    "status": 200,
    "guess": "ai",
    "confidence": 0.7245,
    "model_score": 0.789,
    "style_score": 0.574,
    "label": "We think this may have been written with an AI tool, but we could be wrong. If you wrote it yourself, just let us know and we'll take another look.",
    "sent": "Regular physical activity offers a wide range of health benefits that "
  },
  {
    "status": 200,
    "guess": "ai",
    "confidence": 0.726,
    "model_score": 0.7715,
    "style_score": 0.6198,
    "label": "We think this may have been written with an AI tool, but we could be wrong. If you wrote it yourself, just let us know and we'll take another look.",
    "sent": "Renewable energy sources have become increasingly cost-competitive wit"
  },
  {
    "status": 200,
    "guess": "unsure",
    "confidence": 0.6039,
    "model_score": 0.6245,
    "style_score": 0.5557,
    "label": "We're not sure how this piece was written, and that's all this means. Nothing is wrong. If you'd like us to take another look, you can ask for a review.",
    "sent": "Effective time management begins with a clear understanding of priorit"
  },
  {
    "status": 200,
    "guess": "unsure",
    "confidence": 0.5315,
    "model_score": 0.5179,
    "style_score": 0.5631,
    "label": "We're not sure how this piece was written, and that's all this means. Nothing is wrong. If you'd like us to take another look, you can ask for a review.",
    "sent": "Learning a second language provides cognitive benefits that persist th"
  },
  {
    "status": 200,
    "guess": "unsure",
    "confidence": 0.4344,
    "model_score": 0.3725,
    "style_score": 0.5789,
    "label": "We're not sure how this piece was written, and that's all this means. Nothing is wrong. If you'd like us to take another look, you can ask for a review.",
    "sent": "Food safety in commercial kitchens depends on consistent adherence to "
  },
  {
    "status": 200,
    "guess": "unsure",
    "confidence": 0.4604,
    "model_score": 0.4447,
    "style_score": 0.497,
    "label": "We're not sure how this piece was written, and that's all this means. Nothing is wrong. If you'd like us to take another look, you can ask for a review.",
    "sent": "Financial planning for retirement requires balancing current spending "
  },
  {
    "status": 200,
    "guess": "unsure",
    "confidence": 0.4846,
    "model_score": 0.4682,
    "style_score": 0.5228,
    "label": "We're not sure how this piece was written, and that's all this means. Nothing is wrong. If you'd like us to take another look, you can ask for a review.",
    "sent": "Public libraries have adapted considerably to changes in how people ac"
  },
  {
    "status": 200,
    "guess": "unsure",
    "confidence": 0.6231,
    "model_score": 0.6461,
    "style_score": 0.5693,
    "label": "We're not sure how this piece was written, and that's all this means. Nothing is wrong. If you'd like us to take another look, you can ask for a review.",
    "sent": "Sleep quality has a measurable effect on cognitive performance, emotio"
  },
  {
    "status": 200,
    "guess": "human",
    "confidence": 0.3433,
    "model_score": 0.2927,
    "style_score": 0.4613,
    "label": "We think this was written by a person. Thanks for sharing it.",
    "sent": "In your breakout: bring your worst failure. Share the input and what y"
  },
  {
    "status": 200,
    "guess": "unsure",
    "confidence": 0.5292,
    "model_score": 0.493,
    "style_score": 0.6136,
    "label": "We're not sure how this piece was written, and that's all this means. Nothing is wrong. If you'd like us to take another look, you can ask for a review.",
    "sent": "Prorated by the second. If a service is active for ten seconds in a gi"
  },
  {
    "status": 200,
    "guess": "ai",
    "confidence": 0.6595,
    "model_score": 0.7661,
    "style_score": 0.4107,
    "label": "We think this may have been written with an AI tool, but we could be wrong. If you wrote it yourself, just let us know and we'll take another look.",
    "sent": "Dogs have been bred for specific behaviors, sensory abilities, and phy"
  },
  {
    "status": 200,
    "guess": "unsure",
    "confidence": 0.4192,
    "model_score": 0.4357,
    "style_score": 0.3808,
    "label": "We're not sure how this piece was written, and that's all this means. Nothing is wrong. If you'd like us to take another look, you can ask for a review.",
    "sent": "What makes your program special? Intentional Breeding, Not Mass Produc"
  },
  {
    "status": 200,
    "guess": "unsure",
    "confidence": 0.4952,
    "model_score": 0.551,
    "style_score": 0.365,
    "label": "We're not sure how this piece was written, and that's all this means. Nothing is wrong. If you'd like us to take another look, you can ask for a review.",
    "sent": "I would assume #1 but only because dog meat is not commonly eaten in t"
  },
  {
    "status": 200,
    "guess": "unsure",
    "confidence": 0.5666,
    "model_score": 0.5865,
    "style_score": 0.5203,
    "label": "We're not sure how this piece was written, and that's all this means. Nothing is wrong. If you'd like us to take another look, you can ask for a review.",
    "sent": "Since the beginning of humankind, we have gone all the way from caves "
  },
  {
    "status": 200,
    "guess": "ai",
    "confidence": 0.705,
    "model_score": 0.7907,
    "style_score": 0.5051,
    "label": "We think this may have been written with an AI tool, but we could be wrong. If you wrote it yourself, just let us know and we'll take another look.",
    "sent": "Imagine yourself as a glass statue. It tips over and smashes into a mi"
  },
  {
    "status": 200,
    "guess": "ai",
    "confidence": 0.6985,
    "model_score": 0.7808,
    "style_score": 0.5064,
    "label": "We think this may have been written with an AI tool, but we could be wrong. If you wrote it yourself, just let us know and we'll take another look.",
    "sent": "The more important question, from our perspective, is the one you didn"
  },
  {
    "status": 200,
    "guess": "ai",
    "confidence": 0.6698,
    "model_score": 0.7186,
    "style_score": 0.5558,
    "label": "We think this may have been written with an AI tool, but we could be wrong. If you wrote it yourself, just let us know and we'll take another look.",
    "sent": "Modern AI is rapidly shifting the burden of computing from local machi"
  },
  {
    "status": 200,
    "guess": "ai",
    "confidence": 0.7436,
    "model_score": 0.8329,
    "style_score": 0.5354,
    "label": "We think this may have been written with an AI tool, but we could be wrong. If you wrote it yourself, just let us know and we'll take another look.",
    "sent": "The Dietary Guidelines Advisory Committee, which meets every five year"
  }
]
```

**Criterion 3** — all three labels were reached. One entry per
label from the thirty-passage run:

```json
[
  {
    "status": 200,
    "guess": "human",
    "confidence": 0.3433,
    "model_score": 0.2927,
    "style_score": 0.4613,
    "label": "We think this was written by a person. Thanks for sharing it.",
    "sent": "In your breakout: bring your worst failure. Share the input and what y"
  },
  {
    "status": 200,
    "guess": "unsure",
    "confidence": 0.5292,
    "model_score": 0.493,
    "style_score": 0.6136,
    "label": "We're not sure how this piece was written, and that's all this means. Nothing is wrong. If you'd like us to take another look, you can ask for a review.",
    "sent": "Prorated by the second. If a service is active for ten seconds in a gi"
  },
  {
    "status": 200,
    "guess": "ai",
    "confidence": 0.6595,
    "model_score": 0.7661,
    "style_score": 0.4107,
    "label": "We think this may have been written with an AI tool, but we could be wrong. If you wrote it yourself, just let us know and we'll take another look.",
    "sent": "Dogs have been bred for specific behaviors, sensory abilities, and phy"
  }
]
```

**Criterion 4** — ten AI passages. Seven were not labelled `ai`,
against a target of at most two.

```json
[
  {
    "status": 200,
    "guess": "ai",
    "confidence": 0.7767,
    "model_score": 0.8534,
    "style_score": 0.5976,
    "label": "We think this may have been written with an AI tool, but we could be wrong. If you wrote it yourself, just let us know and we'll take another look.",
    "sent": "Urban green spaces play a vital role in improving the quality of life "
  },
  {
    "status": 200,
    "guess": "unsure",
    "confidence": 0.5395,
    "model_score": 0.5396,
    "style_score": 0.5393,
    "label": "We're not sure how this piece was written, and that's all this means. Nothing is wrong. If you'd like us to take another look, you can ask for a review.",
    "sent": "Remote work has fundamentally changed the way organizations approach c"
  },
  {
    "status": 200,
    "guess": "ai",
    "confidence": 0.7245,
    "model_score": 0.789,
    "style_score": 0.574,
    "label": "We think this may have been written with an AI tool, but we could be wrong. If you wrote it yourself, just let us know and we'll take another look.",
    "sent": "Regular physical activity offers a wide range of health benefits that "
  },
  {
    "status": 200,
    "guess": "ai",
    "confidence": 0.726,
    "model_score": 0.7715,
    "style_score": 0.6198,
    "label": "We think this may have been written with an AI tool, but we could be wrong. If you wrote it yourself, just let us know and we'll take another look.",
    "sent": "Renewable energy sources have become increasingly cost-competitive wit"
  },
  {
    "status": 200,
    "guess": "unsure",
    "confidence": 0.6039,
    "model_score": 0.6245,
    "style_score": 0.5557,
    "label": "We're not sure how this piece was written, and that's all this means. Nothing is wrong. If you'd like us to take another look, you can ask for a review.",
    "sent": "Effective time management begins with a clear understanding of priorit"
  },
  {
    "status": 200,
    "guess": "unsure",
    "confidence": 0.5315,
    "model_score": 0.5179,
    "style_score": 0.5631,
    "label": "We're not sure how this piece was written, and that's all this means. Nothing is wrong. If you'd like us to take another look, you can ask for a review.",
    "sent": "Learning a second language provides cognitive benefits that persist th"
  },
  {
    "status": 200,
    "guess": "unsure",
    "confidence": 0.4344,
    "model_score": 0.3725,
    "style_score": 0.5789,
    "label": "We're not sure how this piece was written, and that's all this means. Nothing is wrong. If you'd like us to take another look, you can ask for a review.",
    "sent": "Food safety in commercial kitchens depends on consistent adherence to "
  },
  {
    "status": 200,
    "guess": "unsure",
    "confidence": 0.4604,
    "model_score": 0.4447,
    "style_score": 0.497,
    "label": "We're not sure how this piece was written, and that's all this means. Nothing is wrong. If you'd like us to take another look, you can ask for a review.",
    "sent": "Financial planning for retirement requires balancing current spending "
  },
  {
    "status": 200,
    "guess": "unsure",
    "confidence": 0.4846,
    "model_score": 0.4682,
    "style_score": 0.5228,
    "label": "We're not sure how this piece was written, and that's all this means. Nothing is wrong. If you'd like us to take another look, you can ask for a review.",
    "sent": "Public libraries have adapted considerably to changes in how people ac"
  },
  {
    "status": 200,
    "guess": "unsure",
    "confidence": 0.6231,
    "model_score": 0.6461,
    "style_score": 0.5693,
    "label": "We're not sure how this piece was written, and that's all this means. Nothing is wrong. If you'd like us to take another look, you can ask for a review.",
    "sent": "Sleep quality has a measurable effect on cognitive performance, emotio"
  }
]
```

**Criterion 5** — ten requests with no usable text. All ten
returned 400 and none were scored.

```json
[
  {
    "status": 400,
    "guess": null,
    "confidence": null,
    "model_score": null,
    "style_score": null,
    "label": null,
    "sent": "(empty text)"
  },
  {
    "status": 400,
    "guess": null,
    "confidence": null,
    "model_score": null,
    "style_score": null,
    "label": null,
    "sent": "(empty text)"
  },
  {
    "status": 400,
    "guess": null,
    "confidence": null,
    "model_score": null,
    "style_score": null,
    "label": null,
    "sent": "(empty text)"
  },
  {
    "status": 400,
    "guess": null,
    "confidence": null,
    "model_score": null,
    "style_score": null,
    "label": null,
    "sent": "{\"creator_id\": \"eval_bad_missing_1\"}"
  },
  {
    "status": 400,
    "guess": null,
    "confidence": null,
    "model_score": null,
    "style_score": null,
    "label": null,
    "sent": "{\"creator_id\": \"eval_bad_missing_2\"}"
  },
  {
    "status": 400,
    "guess": null,
    "confidence": null,
    "model_score": null,
    "style_score": null,
    "label": null,
    "sent": "{\"creator_id\": \"eval_bad_missing_3\"}"
  },
  {
    "status": 400,
    "guess": null,
    "confidence": null,
    "model_score": null,
    "style_score": null,
    "label": null,
    "sent": "{\"text\": null, \"creator_id\": \"eval_bad_null_1\"}"
  },
  {
    "status": 400,
    "guess": null,
    "confidence": null,
    "model_score": null,
    "style_score": null,
    "label": null,
    "sent": "{\"text\": null, \"creator_id\": \"eval_bad_null_2\"}"
  },
  {
    "status": 400,
    "guess": null,
    "confidence": null,
    "model_score": null,
    "style_score": null,
    "label": null,
    "sent": "   "
  },
  {
    "status": 400,
    "guess": null,
    "confidence": null,
    "model_score": null,
    "style_score": null,
    "label": null,
    "sent": "\n\t  \n"
  }
]
```

**Diagnoses**

**The pattern: 18 of the 25 failures are one problem.** Signal one runs
backwards, and my combining rule gives it a weight of 0.7.

### Signal one — it measures the wrong thing (18 failures)

Covers all three missed criteria, seven of the nine evasion attacks, all six
false positives, MF05 and MF08.

Signal one scores GPT-2's perplexity: the easier the text is to predict, the
more AI it is called. But GPT-2 is a 2019 model trained on web pages, news and
encyclopaedia articles, which is exactly what edited published prose looks
like. I measured the raw perplexity of my two groups: the human passages came
in at a median of 30.6 and the AI passages at 36.7. The human writing was the
more predictable of the two.

So this signal is measuring how much a text resembles 2019 web writing, not who
wrote it. The two groups overlap almost completely and there is no usable line
between them. FP03 is the clearest consequence: someone writing in a second
language keeps their words common and their sentences even, and that is the
text GPT-2 predicts best of all.

### The combining rule — it puts the weight on the broken signal (3 failures)

Covers criterion 2, EV03 and EV09.

The rule is `0.7 x signal one + 0.3 x signal two`. Signal two actually points
the right way, scoring the AI group 0.076 higher than the human group, but it
only counts for 0.3. Signal one points the wrong way and counts for 0.7. The
two very nearly cancel, which is why criterion 2 came out at a gap of 0.0075
instead of the 0.5 I asked for.

EV03 and EV09 show it concretely. On both of those, signal one was right: it
scored them 0.666 and 0.704, over my 0.65 line. Signal two pulled the combined
score back under. The rule is not computing anything incorrectly; it is
trusting the wrong one of the two.

### The route — it never checks that the input is scoreable prose (5 failures)

Covers RQ07, MF03, MF04, MF06 and MF07.

RQ07 is a crash. When the body is a JSON array, `get_json` returns a list, the
`or {}` fallback does not fire because a non-empty list is truthy, and the next
line calls `.get` on a list and returns a 500. Worse, it crashes before any
logging call, so that attack left no entry in my audit log at all.

The other four should never have been scored. Control characters, bare
punctuation, emoji and Arabic are not English prose, and both my signals are
only defined on English prose. The route checks that `text` is a non-empty
string and nothing else, so all four went straight through to the signals.

### Signal two — its "no data" default points the wrong way (3 failures)

Covers MF04, MF06 and MF07.

The word-splitting regex in `stylometry.words` only matches ASCII letters and
digits, so Arabic, emoji and bare punctuation produce no words at all. All
three sub-measures return their 0.0 "nothing to measure" value, `style_signal`
flips each one with `1.0 - x`, and the result is 1.0.

"I cannot measure this" comes out as "I am completely certain this is AI".
MF07 is real Arabic prose accused at 0.9345, and the only reason is that my
regex cannot see Arabic letters.

### The label mapping — one genuine threshold failure (1 failure)

Covers EV09, which also appears under the combining rule.

EV09 scored 0.6435 against an AI threshold of 0.65, missing by 0.0065. It is
the only failure in the run that moving a threshold slightly would have caught.
Every other gap is far too wide for a threshold change to matter.



---

## The Improvement

**What I changed:** `AI_THRESHOLD` in `config.py`, from 0.65 to 0.80. 

**Which diagnosis pointed at it:** the signal one diagnosis. The raw perplexity
of my two groups came in at a median of 30.6 for the human passages and 36.7 for
the AI ones, with the ranges almost entirely overlapping. There is no usable
line between them, so the system should not be making confident accusations off
that score. Six of the eight false positives landed between 0.71 and 0.79, and
five of my own ten human passages landed between 0.66 and 0.75. Moving the line
to 0.80 puts all of that human writing back under it.

### Attack Run — After

56 attacks, run with `python run_attacks.py --set attack_set --label after`.
Full report: `results/attack_run_2026-11-13_2354_after.md`.

**HELD 40 · BROKE 16** (before: HELD 34 · BROKE 22)

| Family | Attacks | HELD | BROKE |
|---|---|---|---|
| `evasion` | 10 | 1 | 9 |
| `false_positive` | 8 | 8 | 0 |
| `prompt_injection` | 6 | 6 | 0 |
| `malformed` | 20 | 13 | 7 |
| `flood_same_creator` | 12 | 12 | 0 |

Six attacks changed, all of them in the `false_positive` family, all from `ai`
to `unsure`:

| ID | Before | After |
|---|---|---|
| FP02 | ai 0.7176 | unsure 0.7176 |
| FP03 | ai 0.7584 | unsure 0.7584 |
| FP04 | ai 0.7606 | unsure 0.7606 |
| FP05 | ai 0.7646 | unsure 0.7646 |
| FP06 | ai 0.7864 | unsure 0.7864 |
| FP07 | ai 0.7870 | unsure 0.7870 |

Every one of the 56 confidence scores is identical before and after. Only the
label mapping moved, which is what makes the two runs comparable.

**Every attack, with what came back and a held-or-broke mark**

| ID | Family | Targeting | Status | Guess | Score | Verdict |
|---|---|---|---|---|---|---|
| EV01 | evasion | signal two: typos raise word variety | 200 | human | 0.296 | BROKE |
| EV02 | evasion | signal two: broken-up sentences raise length spread | 200 | unsure | 0.534 | BROKE |
| EV03 | evasion | signal two: a pasted quotation changes the shape | 200 | unsure | 0.622 | BROKE |
| EV04 | evasion | signal one: unusual words raise perplexity | 200 | unsure | 0.604 | BROKE |
| EV05 | evasion | both signals: heavy human editing pass | 200 | unsure | 0.352 | BROKE |
| EV06 | evasion | signal two: one very long sentence and one very short | 200 | unsure | 0.515 | BROKE |
| EV07 | evasion | signal two: padding with punctuation | 200 | unsure | 0.557 | BROKE |
| EV08 | evasion | signal one: text below the token window | 200 | human | 0.343 | BROKE |
| EV09 | evasion | both: AI text with a human opening paragraph bolted on | 200 | unsure | 0.643 | BROKE |
| EV10 | evasion | signal two: repeated stanza structure | 200 | ai | 0.914 | HELD |
| FP01 | false_positive | plain, clear human writing with no flourishes | 200 | unsure | 0.609 | HELD |
| FP02 | false_positive | human writing in a formal register | 200 | unsure | 0.718 | HELD |
| FP03 | false_positive | a non-native English speaker writing carefully | 200 | unsure | 0.758 | HELD |
| FP04 | false_positive | a student essay written to a rubric | 200 | unsure | 0.761 | HELD |
| FP05 | false_positive | technical documentation written by a person | 200 | unsure | 0.765 | HELD |
| FP06 | false_positive | a person imitating a formal template | 200 | unsure | 0.786 | HELD |
| FP07 | false_positive | human writing that repeats a phrase deliberately | 200 | unsure | 0.787 | HELD |
| FP08 | false_positive | very short genuine human submission | 200 | unsure | 0.382 | HELD |
| PI01 | prompt_injection | instruction to ignore the check | 200 | unsure | 0.464 | HELD |
| PI02 | prompt_injection | instruction disguised as a system message | 200 | human | 0.268 | HELD |
| PI03 | prompt_injection | instruction wrapped in the writer's voice | 200 | unsure | 0.573 | HELD |
| PI04 | prompt_injection | attempt to make the label text hostile | 200 | unsure | 0.498 | HELD |
| PI05 | prompt_injection | attempt to inject into the audit log | 200 | unsure | 0.538 | HELD |
| PI06 | prompt_injection | instruction plus real human text | 200 | unsure | 0.570 | HELD |
| MF01 | malformed | text is only whitespace | 400 | — | — | HELD |
| MF02 | malformed | text is a single character | 400 | — | — | HELD |
| MF03 | malformed | text is control characters | 200 | human | 0.200 | BROKE |
| MF04 | malformed | text is only punctuation | 200 | human | 0.308 | BROKE |
| MF05 | malformed | text is a very long single word | 200 | ai | 0.898 | BROKE |
| MF06 | malformed | text is emoji only | 200 | ai | 0.998 | BROKE |
| MF07 | malformed | text is right-to-left script | 200 | ai | 0.934 | BROKE |
| MF08 | malformed | text is 60,000 characters | 200 | ai | 0.941 | BROKE |
| RQ01 | malformed | no text field at all | 400 | — | — | HELD |
| RQ02 | malformed | no creator_id | 400 | — | — | HELD |
| RQ03 | malformed | text is a number | 400 | — | — | HELD |
| RQ04 | malformed | text is a list | 400 | — | — | HELD |
| RQ05 | malformed | text is null | 400 | — | — | HELD |
| RQ06 | malformed | creator_id is an object | 400 | — | — | HELD |
| RQ07 | malformed | body is a JSON array, not an object | 500 | — | — | BROKE |
| RQ08 | malformed | body is unparseable JSON | 400 | — | — | HELD |
| RQ09 | malformed | body is empty | 400 | — | — | HELD |
| RQ10 | malformed | body is form-encoded, not JSON | 400 | — | — | HELD |
| RQ11 | malformed | body is JSON claiming to be plain text | 400 | — | — | HELD |
| RQ12 | malformed | deeply nested JSON | 400 | — | — | HELD |
| FL01_01 | flood_same_creator | 20 rapid submissions from one creator | 200 | human | 0.220 | HELD |
| FL01_02 | flood_same_creator | 20 rapid submissions from one creator | 200 | human | 0.220 | HELD |
| FL01_03 | flood_same_creator | 20 rapid submissions from one creator | 200 | human | 0.220 | HELD |
| FL01_04 | flood_same_creator | 20 rapid submissions from one creator | 200 | human | 0.220 | HELD |
| FL01_05 | flood_same_creator | 20 rapid submissions from one creator | 200 | human | 0.220 | HELD |
| FL01_06 | flood_same_creator | 20 rapid submissions from one creator | 200 | human | 0.220 | HELD |
| FL01_07 | flood_same_creator | 20 rapid submissions from one creator | 200 | human | 0.220 | HELD |
| FL01_08 | flood_same_creator | 20 rapid submissions from one creator | 200 | human | 0.220 | HELD |
| FL01_09 | flood_same_creator | 20 rapid submissions from one creator | 200 | human | 0.220 | HELD |
| FL01_10 | flood_same_creator | 20 rapid submissions from one creator | 200 | human | 0.220 | HELD |
| FL01_11 | flood_same_creator | 20 rapid submissions from one creator | 429 | — | — | HELD |
| FL01_12 | flood_same_creator | 20 rapid submissions from one creator | 429 | — | — | HELD |

**Did it help, and how do I know:**

| Criterion | Before (3 trials) | After (3 trials) |
|---|---|---|
| 1. Human writing labelled AI | 5/10, 5/10, 5/10 — MISSED | 0/10, 0/10, 0/10 — **MET** |
| 2. Mean score gap | 0.0075 x3 — MISSED | 0.0075 x3 — MISSED |
| 3. All three labels reachable | 3/3 x3 — MET | 2/3 x3 — **MISSED** |
| 4. AI passages not labelled AI | 7/10 x3 — MISSED | 10/10 x3 — MISSED |
| 5. Empty or missing text refused | 10/10 x3 — MET | 10/10 x3 — MET |

Produced by `run_eval.py::main`; full output in
`results/run_2026-11-13_2354_after.md`. All three trials matched again, for the same reason as before: neither signal samples.

It helped in the sense that human writing is no longer accused. But the `ai`label became unreachable across all thirty of my evaluation passages, so one of the three labels promised in Label Variants is now an empty promise, and the AI
passages went from seven missed to ten. That amounts to the detector losing the ability to detect. I do not consider this a real improvement.

---

## What's Still Broken

<!-- For each attack still getting through and each criterion still missed:
     what you'd do, and why you stopped. -->
Sixteen attacks still break and three criteria are still missed. They fall into four groups.

**Signal one cannot separate the two groups** — criteria 2 and 4, the nine evasion attacks, MF08.

What I would do: replace signal one. GPT-2 perplexity carries no signal on 2026-era AI text, so this needs a detector trained on current model output, or a different approach entirely.

Why I stopped: this is not a tuning problem, and this unit allows one change. Replacing signal one means rewriting half of what I built in unit 7.

**The `ai` label is unreachable** — criterion 3.

What I would do: move the threshold back once signal one can separate the two groups.

Why I stopped: moving it back now simply returns the six false positives. This label is the price I paid for criterion 1, and until signal one works there is nothing cheaper to pay with.

**The route does not check its input** — RQ07, MF03, MF04, MF05, MF06, MF07.

What I would do: require the body to be a JSON object, and require the text to contain at least five recognisable words. I measured that threshold against every input I have: it rejects exactly those five inputs and touches nothing legitimate, including FP08, a genuine eight-word human submission.

Why I stopped: this and the threshold change were the two candidates and I took the threshold. This is the clearest and cheapest group of the four, and it is what I would do next.



**The trade I'd make if this ran for real:**

<!-- Two sentences. Fewer wrong accusations means more AI text getting through.
     Say which way you'd go and WHO PAYS FOR IT. -->

I would keep moving toward fewer accusations even though it means most AI text gets through. The site pays for that, by accepting AI writing on its pages or finding another way to deal with it, rather than a writer paying for it by being accused wrongly and having to prove otherwise.

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
