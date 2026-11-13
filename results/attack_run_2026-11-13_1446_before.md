# Attack run — before

- Produced by: `run_attacks.py::main`
- Service: `app.py::submit`
- 56 attacks · 2026-11-13 14:46
- Auto-marked BROKE (5xx, crash, no response): **1**
- Left for you to judge: **55**

The Verdict column is blank where a script can't honestly decide. Whether
an evasion worked depends on where you put your thresholds — read the
`targets` column, look at what came back, and write HELD or BROKE yourself.

Each attack submits as creator_id `attacker_<id>`, and the content_id it
came back with is in the table — either one will match this run against your
audit log when you go looking for the ten entries to paste.

| ID | Family | Targeting | Status | Content ID | Guess | Score | Verdict |
|---|---|---|---|---|---|---|---|
| EV01 | evasion | signal two: typos raise word variety | 200 | `a5b3cb7d-1a83-40da-800e-38e34024486d` | human | 0.296 | BROKE |
| EV02 | evasion | signal two: broken-up sentences raise length spread | 200 | `8b5cae6a-6b8b-48ee-b5d4-ae360706bcc8` | unsure | 0.534 | BROKE |
| EV03 | evasion | signal two: a pasted quotation changes the shape | 200 | `31859e99-1760-4b41-a816-71cc6ac5b467` | unsure | 0.622 | BROKE |
| EV04 | evasion | signal one: unusual words raise perplexity | 200 | `21cf3b46-320a-4d7c-956c-ba37479da3df` | unsure | 0.604 | BROKE |
| EV05 | evasion | both signals: heavy human editing pass | 200 | `2a885dfd-a44c-4502-8874-4e04edc101be` | unsure | 0.352 | BROKE |
| EV06 | evasion | signal two: one very long sentence and one very short | 200 | `e6985e6a-b1bc-4849-9ad2-d135d93eb146` | unsure | 0.515 | BROKE |
| EV07 | evasion | signal two: padding with punctuation | 200 | `4c0e1481-0483-4761-91aa-1067a6a6f7d1` | unsure | 0.557 | BROKE |
| EV08 | evasion | signal one: text below the token window | 200 | `1786adc3-8db3-4e20-baf5-dabbac9ab694` | human | 0.343 | BROKE |
| EV09 | evasion | both: AI text with a human opening paragraph bolted on | 200 | `e16d124e-507d-4855-bfe3-00585bc17045` | unsure | 0.643 | BROKE |
| EV10 | evasion | signal two: repeated stanza structure | 200 | `4eb4d270-5d5c-4e77-917f-9ed3b03938e0` | ai | 0.914 | HELD |
| FP01 | false_positive | plain, clear human writing with no flourishes | 200 | `2827cbed-d40b-40f3-9524-ddd94fd58330` | unsure | 0.609 | HELD |
| FP02 | false_positive | human writing in a formal register | 200 | `e98126b1-025c-44ec-8f1f-1058892eacd9` | ai | 0.718 | BROKE |
| FP03 | false_positive | a non-native English speaker writing carefully | 200 | `6c036c7a-13a0-46eb-9eec-fd74c5e41112` | ai | 0.758 | BROKE |
| FP04 | false_positive | a student essay written to a rubric | 200 | `984ae790-e72d-4b0c-9e5e-78fbca988492` | ai | 0.761 | BROKE |
| FP05 | false_positive | technical documentation written by a person | 200 | `bd1d5f7d-eeff-4850-a108-c79dfa38d247` | ai | 0.765 | BROKE |
| FP06 | false_positive | a person imitating a formal template | 200 | `6ce76321-ac39-46b2-baad-e62c06afdf9a` | ai | 0.786 | BROKE |
| FP07 | false_positive | human writing that repeats a phrase deliberately | 200 | `d213e011-e1db-4513-b1c1-58bd9c4c7c5a` | ai | 0.787 | BROKE |
| FP08 | false_positive | very short genuine human submission | 200 | `c3bb0d14-9fbf-4875-ab02-0916082df247` | unsure | 0.382 | HELD |
| PI01 | prompt_injection | instruction to ignore the check | 200 | `5394ce0b-f36b-4aaf-9c13-8ea740432147` | unsure | 0.464 | HELD |
| PI02 | prompt_injection | instruction disguised as a system message | 200 | `e963f4c1-e58e-4f37-bbb4-cb01b1d1e238` | human | 0.268 | HELD |
| PI03 | prompt_injection | instruction wrapped in the writer's voice | 200 | `971dc721-2fb8-4be2-83a9-6904cb2f9eb3` | unsure | 0.573 | HELD |
| PI04 | prompt_injection | attempt to make the label text hostile | 200 | `9956b8db-8815-4c63-ae15-960ad612e12b` | unsure | 0.498 | HELD |
| PI05 | prompt_injection | attempt to inject into the audit log | 200 | `e67cd56d-233a-49c7-8f48-f1c2a604fcee` | unsure | 0.538 | HELD |
| PI06 | prompt_injection | instruction plus real human text | 200 | `aeb12ae1-adce-483f-879d-f2d19ea6137a` | unsure | 0.570 | HELD |
| MF01 | malformed | text is only whitespace | 400 | `—` | — | — | HELD |
| MF02 | malformed | text is a single character | 400 | `—` | — | — | HELD |
| MF03 | malformed | text is control characters | 200 | `86b83bd7-0848-4d02-95cd-bb0ec89e3a3b` | human | 0.200 | BROKE |
| MF04 | malformed | text is only punctuation | 200 | `55aaa007-94d8-47af-85d4-9f54f65f0bfe` | human | 0.308 | BROKE |
| MF05 | malformed | text is a very long single word | 200 | `f59f42c9-efce-4c39-9d1f-e118f6cb7ead` | ai | 0.898 | BROKE |
| MF06 | malformed | text is emoji only | 200 | `5ec973e8-adba-47f3-a2df-236676a57577` | ai | 0.998 | BROKE |
| MF07 | malformed | text is right-to-left script | 200 | `e29e304c-5ab2-4b4b-a8b5-8e251dbe2511` | ai | 0.934 | BROKE |
| MF08 | malformed | text is 60,000 characters | 200 | `c855e909-c812-433c-93c9-770d82e6248a` | ai | 0.941 | BROKE |
| RQ01 | malformed | no text field at all | 400 | `—` | — | — | HELD |
| RQ02 | malformed | no creator_id | 400 | `—` | — | — | HELD |
| RQ03 | malformed | text is a number | 400 | `—` | — | — | HELD |
| RQ04 | malformed | text is a list | 400 | `—` | — | — | HELD |
| RQ05 | malformed | text is null | 400 | `—` | — | — | HELD |
| RQ06 | malformed | creator_id is an object | 400 | `—` | — | — | HELD |
| RQ07 | malformed | body is a JSON array, not an object | 500 | `—` | — | — | BROKE |
| RQ08 | malformed | body is unparseable JSON | 400 | `—` | — | — | HELD |
| RQ09 | malformed | body is empty | 400 | `—` | — | — | HELD |
| RQ10 | malformed | body is form-encoded, not JSON | 400 | `—` | — | — | HELD |
| RQ11 | malformed | body is JSON claiming to be plain text | 400 | `—` | — | — | HELD |
| RQ12 | malformed | deeply nested JSON | 400 | `—` | — | — | HELD |
| FL01_01 | flood_same_creator | 20 rapid submissions from one creator | 200 | `22b60422-fdbd-4776-b466-ba31254f84e8` | human | 0.220 | HELD |
| FL01_02 | flood_same_creator | 20 rapid submissions from one creator | 200 | `a1aa0f2e-51cd-4d56-af53-bf36871ac188` | human | 0.220 | HELD |
| FL01_03 | flood_same_creator | 20 rapid submissions from one creator | 200 | `8e38dbf4-ee8a-4e94-8d98-ca2821082f1f` | human | 0.220 | HELD |
| FL01_04 | flood_same_creator | 20 rapid submissions from one creator | 200 | `1cf132d9-e086-4bb2-ae7c-ffd63c913bd5` | human | 0.220 | HELD |
| FL01_05 | flood_same_creator | 20 rapid submissions from one creator | 200 | `7feaf6df-ecb4-439a-8a98-99a560f3c10b` | human | 0.220 | HELD |
| FL01_06 | flood_same_creator | 20 rapid submissions from one creator | 200 | `6f95d795-7579-4d7a-84bd-d75c9bb03e95` | human | 0.220 | HELD |
| FL01_07 | flood_same_creator | 20 rapid submissions from one creator | 200 | `842564f6-3b6e-4498-8f3f-57e65a763d91` | human | 0.220 | HELD |
| FL01_08 | flood_same_creator | 20 rapid submissions from one creator | 200 | `1e3d5540-8743-4d55-8cfb-3f068851ef3e` | human | 0.220 | HELD |
| FL01_09 | flood_same_creator | 20 rapid submissions from one creator | 200 | `c8644232-230e-4ed6-b0d3-2ca974de77fa` | human | 0.220 | HELD |
| FL01_10 | flood_same_creator | 20 rapid submissions from one creator | 200 | `c0043e77-18b6-404f-9abb-81e3da1c6277` | human | 0.220 | HELD |
| FL01_11 | flood_same_creator | 20 rapid submissions from one creator | 429 | `—` | — | — | HELD |
| FL01_12 | flood_same_creator | 20 rapid submissions from one creator | 429 | `—` | — | — | HELD |

---

## By family

| Family | Attacks | Auto-BROKE | Left to judge |
|---|---|---|---|
| `evasion` | 10 | 0 | 10 |
| `false_positive` | 8 | 0 | 8 |
| `flood_same_creator` | 12 | 0 | 12 |
| `malformed` | 20 | 1 | 19 |
| `prompt_injection` | 6 | 0 | 6 |

> The **false_positive** family is the one to look at hardest. Those are
> written to be genuine human writing that reads as machine-like. Every one
> your service calls AI is a real writer it would have accused.

---

## What came back

The detail behind the table. Paste the interesting failures into your
README — the ones that show something, not the ten easiest.

### EV01 — `evasion`

*Targeting:* signal two: typos raise word variety

```json
{
  "id": "EV01",
  "status": 200,
  "content_id": "a5b3cb7d-1a83-40da-800e-38e34024486d",
  "creator_id": "attacker_EV01",
  "guess": "human",
  "confidence": 0.2956,
  "model_score": 0.1635,
  "style_score": 0.6039,
  "label": "We think this was written by a person. Thanks for sharing it.",
  "error": null,
  "verdict": "BROKE"
}
```

### EV02 — `evasion`

*Targeting:* signal two: broken-up sentences raise length spread

```json
{
  "id": "EV02",
  "status": 200,
  "content_id": "8b5cae6a-6b8b-48ee-b5d4-ae360706bcc8",
  "creator_id": "attacker_EV02",
  "guess": "unsure",
  "confidence": 0.534,
  "model_score": 0.5943,
  "style_score": 0.3934,
  "label": "We're not sure how this piece was written, and that's all this means. Nothing is wrong. If you'd like us to take another look, you can ask for a review.",
  "error": null,
  "verdict": "BROKE"
}
```

### EV03 — `evasion`

*Targeting:* signal two: a pasted quotation changes the shape

```json
{
  "id": "EV03",
  "status": 200,
  "content_id": "31859e99-1760-4b41-a816-71cc6ac5b467",
  "creator_id": "attacker_EV03",
  "guess": "unsure",
  "confidence": 0.6223,
  "model_score": 0.6662,
  "style_score": 0.5199,
  "label": "We're not sure how this piece was written, and that's all this means. Nothing is wrong. If you'd like us to take another look, you can ask for a review.",
  "error": null,
  "verdict": "BROKE"
}
```

### EV04 — `evasion`

*Targeting:* signal one: unusual words raise perplexity

```json
{
  "id": "EV04",
  "status": 200,
  "content_id": "21cf3b46-320a-4d7c-956c-ba37479da3df",
  "creator_id": "attacker_EV04",
  "guess": "unsure",
  "confidence": 0.604,
  "model_score": 0.6114,
  "style_score": 0.5867,
  "label": "We're not sure how this piece was written, and that's all this means. Nothing is wrong. If you'd like us to take another look, you can ask for a review.",
  "error": null,
  "verdict": "BROKE"
}
```

### EV05 — `evasion`

*Targeting:* both signals: heavy human editing pass

```json
{
  "id": "EV05",
  "status": 200,
  "content_id": "2a885dfd-a44c-4502-8874-4e04edc101be",
  "creator_id": "attacker_EV05",
  "guess": "unsure",
  "confidence": 0.352,
  "model_score": 0.3212,
  "style_score": 0.424,
  "label": "We're not sure how this piece was written, and that's all this means. Nothing is wrong. If you'd like us to take another look, you can ask for a review.",
  "error": null,
  "verdict": "BROKE"
}
```

### EV06 — `evasion`

*Targeting:* signal two: one very long sentence and one very short

```json
{
  "id": "EV06",
  "status": 200,
  "content_id": "e6985e6a-b1bc-4849-9ad2-d135d93eb146",
  "creator_id": "attacker_EV06",
  "guess": "unsure",
  "confidence": 0.515,
  "model_score": 0.5727,
  "style_score": 0.3804,
  "label": "We're not sure how this piece was written, and that's all this means. Nothing is wrong. If you'd like us to take another look, you can ask for a review.",
  "error": null,
  "verdict": "BROKE"
}
```

### EV07 — `evasion`

*Targeting:* signal two: padding with punctuation

```json
{
  "id": "EV07",
  "status": 200,
  "content_id": "4c0e1481-0483-4761-91aa-1067a6a6f7d1",
  "creator_id": "attacker_EV07",
  "guess": "unsure",
  "confidence": 0.5574,
  "model_score": 0.5862,
  "style_score": 0.4903,
  "label": "We're not sure how this piece was written, and that's all this means. Nothing is wrong. If you'd like us to take another look, you can ask for a review.",
  "error": null,
  "verdict": "BROKE"
}
```

### EV08 — `evasion`

*Targeting:* signal one: text below the token window

```json
{
  "id": "EV08",
  "status": 200,
  "content_id": "1786adc3-8db3-4e20-baf5-dabbac9ab694",
  "creator_id": "attacker_EV08",
  "guess": "human",
  "confidence": 0.3434,
  "model_score": 0.2452,
  "style_score": 0.5724,
  "label": "We think this was written by a person. Thanks for sharing it.",
  "error": null,
  "verdict": "BROKE"
}
```

### EV09 — `evasion`

*Targeting:* both: AI text with a human opening paragraph bolted on

```json
{
  "id": "EV09",
  "status": 200,
  "content_id": "e16d124e-507d-4855-bfe3-00585bc17045",
  "creator_id": "attacker_EV09",
  "guess": "unsure",
  "confidence": 0.6435,
  "model_score": 0.7042,
  "style_score": 0.5018,
  "label": "We're not sure how this piece was written, and that's all this means. Nothing is wrong. If you'd like us to take another look, you can ask for a review.",
  "error": null,
  "verdict": "BROKE"
}
```

### EV10 — `evasion`

*Targeting:* signal two: repeated stanza structure

```json
{
  "id": "EV10",
  "status": 200,
  "content_id": "4eb4d270-5d5c-4e77-917f-9ed3b03938e0",
  "creator_id": "attacker_EV10",
  "guess": "ai",
  "confidence": 0.914,
  "model_score": 0.9792,
  "style_score": 0.7619,
  "label": "We think this may have been written with an AI tool, but we could be wrong. If you wrote it yourself, just let us know and we'll take another look.",
  "error": null,
  "verdict": "HELD"
}
```

### FP01 — `false_positive`

*Targeting:* plain, clear human writing with no flourishes

```json
{
  "id": "FP01",
  "status": 200,
  "content_id": "2827cbed-d40b-40f3-9524-ddd94fd58330",
  "creator_id": "attacker_FP01",
  "guess": "unsure",
  "confidence": 0.6092,
  "model_score": 0.6175,
  "style_score": 0.5897,
  "label": "We're not sure how this piece was written, and that's all this means. Nothing is wrong. If you'd like us to take another look, you can ask for a review.",
  "error": null,
  "verdict": "HELD"
}
```

### FP02 — `false_positive`

*Targeting:* human writing in a formal register

```json
{
  "id": "FP02",
  "status": 200,
  "content_id": "e98126b1-025c-44ec-8f1f-1058892eacd9",
  "creator_id": "attacker_FP02",
  "guess": "ai",
  "confidence": 0.7176,
  "model_score": 0.7387,
  "style_score": 0.6684,
  "label": "We think this may have been written with an AI tool, but we could be wrong. If you wrote it yourself, just let us know and we'll take another look.",
  "error": null,
  "verdict": "BROKE"
}
```

### FP03 — `false_positive`

*Targeting:* a non-native English speaker writing carefully

```json
{
  "id": "FP03",
  "status": 200,
  "content_id": "6c036c7a-13a0-46eb-9eec-fd74c5e41112",
  "creator_id": "attacker_FP03",
  "guess": "ai",
  "confidence": 0.7584,
  "model_score": 0.8051,
  "style_score": 0.6496,
  "label": "We think this may have been written with an AI tool, but we could be wrong. If you wrote it yourself, just let us know and we'll take another look.",
  "error": null,
  "verdict": "BROKE"
}
```

### FP04 — `false_positive`

*Targeting:* a student essay written to a rubric

```json
{
  "id": "FP04",
  "status": 200,
  "content_id": "984ae790-e72d-4b0c-9e5e-78fbca988492",
  "creator_id": "attacker_FP04",
  "guess": "ai",
  "confidence": 0.7606,
  "model_score": 0.8329,
  "style_score": 0.5918,
  "label": "We think this may have been written with an AI tool, but we could be wrong. If you wrote it yourself, just let us know and we'll take another look.",
  "error": null,
  "verdict": "BROKE"
}
```

### FP05 — `false_positive`

*Targeting:* technical documentation written by a person

```json
{
  "id": "FP05",
  "status": 200,
  "content_id": "bd1d5f7d-eeff-4850-a108-c79dfa38d247",
  "creator_id": "attacker_FP05",
  "guess": "ai",
  "confidence": 0.7646,
  "model_score": 0.8005,
  "style_score": 0.6807,
  "label": "We think this may have been written with an AI tool, but we could be wrong. If you wrote it yourself, just let us know and we'll take another look.",
  "error": null,
  "verdict": "BROKE"
}
```

### FP06 — `false_positive`

*Targeting:* a person imitating a formal template

```json
{
  "id": "FP06",
  "status": 200,
  "content_id": "6ce76321-ac39-46b2-baad-e62c06afdf9a",
  "creator_id": "attacker_FP06",
  "guess": "ai",
  "confidence": 0.7864,
  "model_score": 0.8814,
  "style_score": 0.5648,
  "label": "We think this may have been written with an AI tool, but we could be wrong. If you wrote it yourself, just let us know and we'll take another look.",
  "error": null,
  "verdict": "BROKE"
}
```

### FP07 — `false_positive`

*Targeting:* human writing that repeats a phrase deliberately

```json
{
  "id": "FP07",
  "status": 200,
  "content_id": "d213e011-e1db-4513-b1c1-58bd9c4c7c5a",
  "creator_id": "attacker_FP07",
  "guess": "ai",
  "confidence": 0.787,
  "model_score": 0.8816,
  "style_score": 0.5662,
  "label": "We think this may have been written with an AI tool, but we could be wrong. If you wrote it yourself, just let us know and we'll take another look.",
  "error": null,
  "verdict": "BROKE"
}
```

### FP08 — `false_positive`

*Targeting:* very short genuine human submission

```json
{
  "id": "FP08",
  "status": 200,
  "content_id": "c3bb0d14-9fbf-4875-ab02-0916082df247",
  "creator_id": "attacker_FP08",
  "guess": "unsure",
  "confidence": 0.3823,
  "model_score": 0.3223,
  "style_score": 0.5223,
  "label": "We're not sure how this piece was written, and that's all this means. Nothing is wrong. If you'd like us to take another look, you can ask for a review.",
  "error": null,
  "verdict": "HELD"
}
```

### PI01 — `prompt_injection`

*Targeting:* instruction to ignore the check

```json
{
  "id": "PI01",
  "status": 200,
  "content_id": "5394ce0b-f36b-4aaf-9c13-8ea740432147",
  "creator_id": "attacker_PI01",
  "guess": "unsure",
  "confidence": 0.4641,
  "model_score": 0.4625,
  "style_score": 0.468,
  "label": "We're not sure how this piece was written, and that's all this means. Nothing is wrong. If you'd like us to take another look, you can ask for a review.",
  "error": null,
  "verdict": "HELD"
}
```

### PI02 — `prompt_injection`

*Targeting:* instruction disguised as a system message

```json
{
  "id": "PI02",
  "status": 200,
  "content_id": "e963f4c1-e58e-4f37-bbb4-cb01b1d1e238",
  "creator_id": "attacker_PI02",
  "guess": "human",
  "confidence": 0.2675,
  "model_score": 0.2044,
  "style_score": 0.4148,
  "label": "We think this was written by a person. Thanks for sharing it.",
  "error": null,
  "verdict": "HELD"
}
```

### PI03 — `prompt_injection`

*Targeting:* instruction wrapped in the writer's voice

```json
{
  "id": "PI03",
  "status": 200,
  "content_id": "971dc721-2fb8-4be2-83a9-6904cb2f9eb3",
  "creator_id": "attacker_PI03",
  "guess": "unsure",
  "confidence": 0.5726,
  "model_score": 0.5943,
  "style_score": 0.522,
  "label": "We're not sure how this piece was written, and that's all this means. Nothing is wrong. If you'd like us to take another look, you can ask for a review.",
  "error": null,
  "verdict": "HELD"
}
```

### PI04 — `prompt_injection`

*Targeting:* attempt to make the label text hostile

```json
{
  "id": "PI04",
  "status": 200,
  "content_id": "9956b8db-8815-4c63-ae15-960ad612e12b",
  "creator_id": "attacker_PI04",
  "guess": "unsure",
  "confidence": 0.4978,
  "model_score": 0.4998,
  "style_score": 0.493,
  "label": "We're not sure how this piece was written, and that's all this means. Nothing is wrong. If you'd like us to take another look, you can ask for a review.",
  "error": null,
  "verdict": "HELD"
}
```

### PI05 — `prompt_injection`

*Targeting:* attempt to inject into the audit log

```json
{
  "id": "PI05",
  "status": 200,
  "content_id": "e67cd56d-233a-49c7-8f48-f1c2a604fcee",
  "creator_id": "attacker_PI05",
  "guess": "unsure",
  "confidence": 0.5376,
  "model_score": 0.6401,
  "style_score": 0.2984,
  "label": "We're not sure how this piece was written, and that's all this means. Nothing is wrong. If you'd like us to take another look, you can ask for a review.",
  "error": null,
  "verdict": "HELD"
}
```

### PI06 — `prompt_injection`

*Targeting:* instruction plus real human text

```json
{
  "id": "PI06",
  "status": 200,
  "content_id": "aeb12ae1-adce-483f-879d-f2d19ea6137a",
  "creator_id": "attacker_PI06",
  "guess": "unsure",
  "confidence": 0.5696,
  "model_score": 0.5583,
  "style_score": 0.5959,
  "label": "We're not sure how this piece was written, and that's all this means. Nothing is wrong. If you'd like us to take another look, you can ask for a review.",
  "error": null,
  "verdict": "HELD"
}
```

### MF01 — `malformed`

*Targeting:* text is only whitespace

```json
{
  "id": "MF01",
  "status": 400,
  "content_id": null,
  "creator_id": "attacker_MF01",
  "guess": null,
  "confidence": null,
  "model_score": null,
  "style_score": null,
  "label": null,
  "error": null,
  "verdict": "HELD"
}
```

### MF02 — `malformed`

*Targeting:* text is a single character

```json
{
  "id": "MF02",
  "status": 400,
  "content_id": null,
  "creator_id": "attacker_MF02",
  "guess": null,
  "confidence": null,
  "model_score": null,
  "style_score": null,
  "label": null,
  "error": null,
  "verdict": "HELD"
}
```

### MF03 — `malformed`

*Targeting:* text is control characters

```json
{
  "id": "MF03",
  "status": 200,
  "content_id": "86b83bd7-0848-4d02-95cd-bb0ec89e3a3b",
  "creator_id": "attacker_MF03",
  "guess": "human",
  "confidence": 0.2,
  "model_score": 0.0,
  "style_score": 0.6667,
  "label": "We think this was written by a person. Thanks for sharing it.",
  "error": null,
  "verdict": "BROKE"
}
```

### MF04 — `malformed`

*Targeting:* text is only punctuation

```json
{
  "id": "MF04",
  "status": 200,
  "content_id": "55aaa007-94d8-47af-85d4-9f54f65f0bfe",
  "creator_id": "attacker_MF04",
  "guess": "human",
  "confidence": 0.3078,
  "model_score": 0.0112,
  "style_score": 1.0,
  "label": "We think this was written by a person. Thanks for sharing it.",
  "error": null,
  "verdict": "BROKE"
}
```

### MF05 — `malformed`

*Targeting:* text is a very long single word

```json
{
  "id": "MF05",
  "status": 200,
  "content_id": "f59f42c9-efce-4c39-9d1f-e118f6cb7ead",
  "creator_id": "attacker_MF05",
  "guess": "ai",
  "confidence": 0.8983,
  "model_score": 0.9976,
  "style_score": 0.6667,
  "label": "We think this may have been written with an AI tool, but we could be wrong. If you wrote it yourself, just let us know and we'll take another look.",
  "error": null,
  "verdict": "BROKE"
}
```

### MF06 — `malformed`

*Targeting:* text is emoji only

```json
{
  "id": "MF06",
  "status": 200,
  "content_id": "5ec973e8-adba-47f3-a2df-236676a57577",
  "creator_id": "attacker_MF06",
  "guess": "ai",
  "confidence": 0.9978,
  "model_score": 0.9969,
  "style_score": 1.0,
  "label": "We think this may have been written with an AI tool, but we could be wrong. If you wrote it yourself, just let us know and we'll take another look.",
  "error": null,
  "verdict": "BROKE"
}
```

### MF07 — `malformed`

*Targeting:* text is right-to-left script

```json
{
  "id": "MF07",
  "status": 200,
  "content_id": "e29e304c-5ab2-4b4b-a8b5-8e251dbe2511",
  "creator_id": "attacker_MF07",
  "guess": "ai",
  "confidence": 0.9345,
  "model_score": 0.9064,
  "style_score": 1.0,
  "label": "We think this may have been written with an AI tool, but we could be wrong. If you wrote it yourself, just let us know and we'll take another look.",
  "error": null,
  "verdict": "BROKE"
}
```

### MF08 — `malformed`

*Targeting:* text is 60,000 characters

```json
{
  "id": "MF08",
  "status": 200,
  "content_id": "c855e909-c812-433c-93c9-770d82e6248a",
  "creator_id": "attacker_MF08",
  "guess": "ai",
  "confidence": 0.9414,
  "model_score": 0.9876,
  "style_score": 0.8335,
  "label": "We think this may have been written with an AI tool, but we could be wrong. If you wrote it yourself, just let us know and we'll take another look.",
  "error": null,
  "verdict": "BROKE"
}
```

### RQ01 — `malformed`

*Targeting:* no text field at all

```json
{
  "id": "RQ01",
  "status": 400,
  "content_id": null,
  "creator_id": "attacker_RQ01",
  "guess": null,
  "confidence": null,
  "model_score": null,
  "style_score": null,
  "label": null,
  "error": null,
  "verdict": "HELD"
}
```

### RQ02 — `malformed`

*Targeting:* no creator_id

```json
{
  "id": "RQ02",
  "status": 400,
  "content_id": null,
  "creator_id": null,
  "guess": null,
  "confidence": null,
  "model_score": null,
  "style_score": null,
  "label": null,
  "error": null,
  "verdict": "HELD"
}
```

### RQ03 — `malformed`

*Targeting:* text is a number

```json
{
  "id": "RQ03",
  "status": 400,
  "content_id": null,
  "creator_id": "attacker_RQ03",
  "guess": null,
  "confidence": null,
  "model_score": null,
  "style_score": null,
  "label": null,
  "error": null,
  "verdict": "HELD"
}
```

### RQ04 — `malformed`

*Targeting:* text is a list

```json
{
  "id": "RQ04",
  "status": 400,
  "content_id": null,
  "creator_id": "attacker_RQ04",
  "guess": null,
  "confidence": null,
  "model_score": null,
  "style_score": null,
  "label": null,
  "error": null,
  "verdict": "HELD"
}
```

### RQ05 — `malformed`

*Targeting:* text is null

```json
{
  "id": "RQ05",
  "status": 400,
  "content_id": null,
  "creator_id": "attacker_RQ05",
  "guess": null,
  "confidence": null,
  "model_score": null,
  "style_score": null,
  "label": null,
  "error": null,
  "verdict": "HELD"
}
```

### RQ06 — `malformed`

*Targeting:* creator_id is an object

```json
{
  "id": "RQ06",
  "status": 400,
  "content_id": null,
  "creator_id": {
    "id": "x"
  },
  "guess": null,
  "confidence": null,
  "model_score": null,
  "style_score": null,
  "label": null,
  "error": null,
  "verdict": "HELD"
}
```

### RQ07 — `malformed`

*Targeting:* body is a JSON array, not an object

```json
{
  "id": "RQ07",
  "status": 500,
  "content_id": null,
  "creator_id": null,
  "guess": null,
  "confidence": null,
  "model_score": null,
  "style_score": null,
  "label": null,
  "error": "response wasn't JSON: <!doctype html>\n<html lang=en>\n  <head>\n    <title>AttributeError: &#39;list&#39; object has no attribute &#39;get&#39;\n // Werkzeug Debugger</title>\n    <link rel=\"stylesheet\" href=\"?__debugger__=yes",
  "verdict": "BROKE"
}
```

### RQ08 — `malformed`

*Targeting:* body is unparseable JSON

```json
{
  "id": "RQ08",
  "status": 400,
  "content_id": null,
  "creator_id": null,
  "guess": null,
  "confidence": null,
  "model_score": null,
  "style_score": null,
  "label": null,
  "error": null,
  "verdict": "HELD"
}
```

### RQ09 — `malformed`

*Targeting:* body is empty

```json
{
  "id": "RQ09",
  "status": 400,
  "content_id": null,
  "creator_id": null,
  "guess": null,
  "confidence": null,
  "model_score": null,
  "style_score": null,
  "label": null,
  "error": null,
  "verdict": "HELD"
}
```

### RQ10 — `malformed`

*Targeting:* body is form-encoded, not JSON

```json
{
  "id": "RQ10",
  "status": 400,
  "content_id": null,
  "creator_id": null,
  "guess": null,
  "confidence": null,
  "model_score": null,
  "style_score": null,
  "label": null,
  "error": null,
  "verdict": "HELD"
}
```

### RQ11 — `malformed`

*Targeting:* body is JSON claiming to be plain text

```json
{
  "id": "RQ11",
  "status": 400,
  "content_id": null,
  "creator_id": null,
  "guess": null,
  "confidence": null,
  "model_score": null,
  "style_score": null,
  "label": null,
  "error": null,
  "verdict": "HELD"
}
```

### RQ12 — `malformed`

*Targeting:* deeply nested JSON

```json
{
  "id": "RQ12",
  "status": 400,
  "content_id": null,
  "creator_id": "attacker_RQ12",
  "guess": null,
  "confidence": null,
  "model_score": null,
  "style_score": null,
  "label": null,
  "error": null,
  "verdict": "HELD"
}
```

### FL01_01 — `flood_same_creator`

*Targeting:* 20 rapid submissions from one creator

```json
{
  "id": "FL01_01",
  "status": 200,
  "content_id": "22b60422-fdbd-4776-b466-ba31254f84e8",
  "creator_id": "attacker_flood",
  "guess": "human",
  "confidence": 0.2201,
  "model_score": 0.0287,
  "style_score": 0.6667,
  "label": "We think this was written by a person. Thanks for sharing it.",
  "error": null,
  "verdict": "HELD"
}
```

### FL01_02 — `flood_same_creator`

*Targeting:* 20 rapid submissions from one creator

```json
{
  "id": "FL01_02",
  "status": 200,
  "content_id": "a1aa0f2e-51cd-4d56-af53-bf36871ac188",
  "creator_id": "attacker_flood",
  "guess": "human",
  "confidence": 0.2201,
  "model_score": 0.0287,
  "style_score": 0.6667,
  "label": "We think this was written by a person. Thanks for sharing it.",
  "error": null,
  "verdict": "HELD"
}
```

### FL01_03 — `flood_same_creator`

*Targeting:* 20 rapid submissions from one creator

```json
{
  "id": "FL01_03",
  "status": 200,
  "content_id": "8e38dbf4-ee8a-4e94-8d98-ca2821082f1f",
  "creator_id": "attacker_flood",
  "guess": "human",
  "confidence": 0.2201,
  "model_score": 0.0287,
  "style_score": 0.6667,
  "label": "We think this was written by a person. Thanks for sharing it.",
  "error": null,
  "verdict": "HELD"
}
```

### FL01_04 — `flood_same_creator`

*Targeting:* 20 rapid submissions from one creator

```json
{
  "id": "FL01_04",
  "status": 200,
  "content_id": "1cf132d9-e086-4bb2-ae7c-ffd63c913bd5",
  "creator_id": "attacker_flood",
  "guess": "human",
  "confidence": 0.2201,
  "model_score": 0.0287,
  "style_score": 0.6667,
  "label": "We think this was written by a person. Thanks for sharing it.",
  "error": null,
  "verdict": "HELD"
}
```

### FL01_05 — `flood_same_creator`

*Targeting:* 20 rapid submissions from one creator

```json
{
  "id": "FL01_05",
  "status": 200,
  "content_id": "7feaf6df-ecb4-439a-8a98-99a560f3c10b",
  "creator_id": "attacker_flood",
  "guess": "human",
  "confidence": 0.2201,
  "model_score": 0.0287,
  "style_score": 0.6667,
  "label": "We think this was written by a person. Thanks for sharing it.",
  "error": null,
  "verdict": "HELD"
}
```

### FL01_06 — `flood_same_creator`

*Targeting:* 20 rapid submissions from one creator

```json
{
  "id": "FL01_06",
  "status": 200,
  "content_id": "6f95d795-7579-4d7a-84bd-d75c9bb03e95",
  "creator_id": "attacker_flood",
  "guess": "human",
  "confidence": 0.2201,
  "model_score": 0.0287,
  "style_score": 0.6667,
  "label": "We think this was written by a person. Thanks for sharing it.",
  "error": null,
  "verdict": "HELD"
}
```

### FL01_07 — `flood_same_creator`

*Targeting:* 20 rapid submissions from one creator

```json
{
  "id": "FL01_07",
  "status": 200,
  "content_id": "842564f6-3b6e-4498-8f3f-57e65a763d91",
  "creator_id": "attacker_flood",
  "guess": "human",
  "confidence": 0.2201,
  "model_score": 0.0287,
  "style_score": 0.6667,
  "label": "We think this was written by a person. Thanks for sharing it.",
  "error": null,
  "verdict": "HELD"
}
```

### FL01_08 — `flood_same_creator`

*Targeting:* 20 rapid submissions from one creator

```json
{
  "id": "FL01_08",
  "status": 200,
  "content_id": "1e3d5540-8743-4d55-8cfb-3f068851ef3e",
  "creator_id": "attacker_flood",
  "guess": "human",
  "confidence": 0.2201,
  "model_score": 0.0287,
  "style_score": 0.6667,
  "label": "We think this was written by a person. Thanks for sharing it.",
  "error": null,
  "verdict": "HELD"
}
```

### FL01_09 — `flood_same_creator`

*Targeting:* 20 rapid submissions from one creator

```json
{
  "id": "FL01_09",
  "status": 200,
  "content_id": "c8644232-230e-4ed6-b0d3-2ca974de77fa",
  "creator_id": "attacker_flood",
  "guess": "human",
  "confidence": 0.2201,
  "model_score": 0.0287,
  "style_score": 0.6667,
  "label": "We think this was written by a person. Thanks for sharing it.",
  "error": null,
  "verdict": "HELD"
}
```

### FL01_10 — `flood_same_creator`

*Targeting:* 20 rapid submissions from one creator

```json
{
  "id": "FL01_10",
  "status": 200,
  "content_id": "c0043e77-18b6-404f-9abb-81e3da1c6277",
  "creator_id": "attacker_flood",
  "guess": "human",
  "confidence": 0.2201,
  "model_score": 0.0287,
  "style_score": 0.6667,
  "label": "We think this was written by a person. Thanks for sharing it.",
  "error": null,
  "verdict": "HELD"
}
```

### FL01_11 — `flood_same_creator`

*Targeting:* 20 rapid submissions from one creator

```json
{
  "id": "FL01_11",
  "status": 429,
  "content_id": null,
  "creator_id": "attacker_flood",
  "guess": null,
  "confidence": null,
  "model_score": null,
  "style_score": null,
  "label": null,
  "error": null,
  "verdict": "HELD"
}
```

### FL01_12 — `flood_same_creator`

*Targeting:* 20 rapid submissions from one creator

```json
{
  "id": "FL01_12",
  "status": 429,
  "content_id": null,
  "creator_id": "attacker_flood",
  "guess": null,
  "confidence": null,
  "model_score": null,
  "style_score": null,
  "label": null,
  "error": null,
  "verdict": "HELD"
}
```
