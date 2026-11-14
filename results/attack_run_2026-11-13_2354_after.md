# Attack run — after

- Produced by: `run_attacks.py::main`
- Service: `app.py::submit`
- 56 attacks · 2026-11-13 23:54
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
| EV01 | evasion | signal two: typos raise word variety | 200 | `f2b5c907-0f7c-4a73-83ab-55e3144a9c30` | human | 0.296 | BROKE |
| EV02 | evasion | signal two: broken-up sentences raise length spread | 200 | `68796843-18ba-4c93-b523-bfb2d83119f4` | unsure | 0.534 | BROKE |
| EV03 | evasion | signal two: a pasted quotation changes the shape | 200 | `55772b7e-3495-4138-a974-f7cfc8b77617` | unsure | 0.622 | BROKE |
| EV04 | evasion | signal one: unusual words raise perplexity | 200 | `6c134590-372f-43cd-963b-162e4e2f4645` | unsure | 0.604 | BROKE |
| EV05 | evasion | both signals: heavy human editing pass | 200 | `138b7fbe-77e6-4435-9edb-782f8a68dd14` | unsure | 0.352 | BROKE |
| EV06 | evasion | signal two: one very long sentence and one very short | 200 | `5408e56f-843d-4fb2-81a2-549625da4148` | unsure | 0.515 | BROKE |
| EV07 | evasion | signal two: padding with punctuation | 200 | `1208861d-7e30-43cd-aece-a3c15cd5482e` | unsure | 0.557 | BROKE |
| EV08 | evasion | signal one: text below the token window | 200 | `84e87a1b-9e34-4ff9-a85f-84301dc14f96` | human | 0.343 | BROKE |
| EV09 | evasion | both: AI text with a human opening paragraph bolted on | 200 | `dc74f28e-a631-4633-8f17-abf8d84934a3` | unsure | 0.643 | BROKE |
| EV10 | evasion | signal two: repeated stanza structure | 200 | `158c6b4b-629d-4b9e-86f1-5ee71092bcf3` | ai | 0.914 | HELD |
| FP01 | false_positive | plain, clear human writing with no flourishes | 200 | `36671972-852f-4ec2-af1c-4feb81dc1127` | unsure | 0.609 | HELD |
| FP02 | false_positive | human writing in a formal register | 200 | `0a3c7e44-b4df-42a4-90cb-0e19f1cc985e` | unsure | 0.718 | HELD |
| FP03 | false_positive | a non-native English speaker writing carefully | 200 | `f19775f8-dbd1-4cee-8343-36a6b9ba7dad` | unsure | 0.758 | HELD |
| FP04 | false_positive | a student essay written to a rubric | 200 | `9b2de51f-9e8f-4e06-9360-e650d50f64de` | unsure | 0.761 | HELD |
| FP05 | false_positive | technical documentation written by a person | 200 | `5f883f0d-e5c3-4b47-85d8-87ed810e0867` | unsure | 0.765 | HELD |
| FP06 | false_positive | a person imitating a formal template | 200 | `026be8ed-c6fd-494d-880f-abbd8350e4ad` | unsure | 0.786 | HELD |
| FP07 | false_positive | human writing that repeats a phrase deliberately | 200 | `e6c00c66-07cf-43dd-846f-372ca99abf9a` | unsure | 0.787 | HELD |
| FP08 | false_positive | very short genuine human submission | 200 | `ced56267-3435-48f4-9557-e6303e671092` | unsure | 0.382 | HELD |
| PI01 | prompt_injection | instruction to ignore the check | 200 | `429c9941-8576-4354-aa1f-130c0d7cd448` | unsure | 0.464 | HELD |
| PI02 | prompt_injection | instruction disguised as a system message | 200 | `9e0c2f36-fb23-48b4-86bd-24eafc79f065` | human | 0.268 | HELD |
| PI03 | prompt_injection | instruction wrapped in the writer's voice | 200 | `0ed373cc-a4ba-4a06-9aa9-ae272b7242e4` | unsure | 0.573 | HELD |
| PI04 | prompt_injection | attempt to make the label text hostile | 200 | `669d7800-8a25-4f61-bccf-f33f40ef1e4c` | unsure | 0.498 | HELD |
| PI05 | prompt_injection | attempt to inject into the audit log | 200 | `fc26accd-ceff-4054-ace5-1e5e6430ddc9` | unsure | 0.538 | HELD |
| PI06 | prompt_injection | instruction plus real human text | 200 | `ef1f798a-fff2-4002-b667-fb4fe9b61461` | unsure | 0.570 | HELD |
| MF01 | malformed | text is only whitespace | 400 | `—` | — | — | HELD |
| MF02 | malformed | text is a single character | 400 | `—` | — | — | HELD |
| MF03 | malformed | text is control characters | 200 | `019a5c53-5089-49fd-9f79-c5d8a05e97cd` | human | 0.200 | BROKE |
| MF04 | malformed | text is only punctuation | 200 | `b1b004c9-ff02-4e07-b909-85ab490cdeca` | human | 0.308 | BROKE |
| MF05 | malformed | text is a very long single word | 200 | `c9ae795c-8ccc-4cc7-bf81-c08c690c21ae` | ai | 0.898 | BROKE |
| MF06 | malformed | text is emoji only | 200 | `6a219e3a-2644-4afd-b9aa-86dcb2e54948` | ai | 0.998 | BROKE |
| MF07 | malformed | text is right-to-left script | 200 | `c23ad22c-fe12-41c5-a498-904864b85038` | ai | 0.934 | BROKE |
| MF08 | malformed | text is 60,000 characters | 200 | `4129727b-87a5-42e0-a666-5b25bbb02288` | ai | 0.941 | BROKE |
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
| FL01_01 | flood_same_creator | 20 rapid submissions from one creator | 200 | `adfc1ca7-6f30-435a-9425-7a3de955f3cf` | human | 0.220 | HELD |
| FL01_02 | flood_same_creator | 20 rapid submissions from one creator | 200 | `38d98d50-5775-4ed1-9586-820807e1a53a` | human | 0.220 | HELD |
| FL01_03 | flood_same_creator | 20 rapid submissions from one creator | 200 | `707b1289-2a65-47ee-8d6b-1b5ab20ea837` | human | 0.220 | HELD |
| FL01_04 | flood_same_creator | 20 rapid submissions from one creator | 200 | `55623118-bc34-47c0-99ac-75e9aae1b93d` | human | 0.220 | HELD |
| FL01_05 | flood_same_creator | 20 rapid submissions from one creator | 200 | `c6d1280c-c407-4d62-8306-400f9da254bf` | human | 0.220 | HELD |
| FL01_06 | flood_same_creator | 20 rapid submissions from one creator | 200 | `ca1e0802-1988-49f6-9d08-59738477f397` | human | 0.220 | HELD |
| FL01_07 | flood_same_creator | 20 rapid submissions from one creator | 200 | `edff03d8-a777-409d-ae61-2053fe39b6b7` | human | 0.220 | HELD |
| FL01_08 | flood_same_creator | 20 rapid submissions from one creator | 200 | `6b93c6f0-448c-454b-b549-1a64e8152a21` | human | 0.220 | HELD |
| FL01_09 | flood_same_creator | 20 rapid submissions from one creator | 200 | `4c02fde2-1e5d-48e0-85b0-330be01378fd` | human | 0.220 | HELD |
| FL01_10 | flood_same_creator | 20 rapid submissions from one creator | 200 | `0960cbe8-a6f2-4c86-a25e-087c5b8e60b1` | human | 0.220 | HELD |
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
  "content_id": "f2b5c907-0f7c-4a73-83ab-55e3144a9c30",
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
  "content_id": "68796843-18ba-4c93-b523-bfb2d83119f4",
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
  "content_id": "55772b7e-3495-4138-a974-f7cfc8b77617",
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
  "content_id": "6c134590-372f-43cd-963b-162e4e2f4645",
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
  "content_id": "138b7fbe-77e6-4435-9edb-782f8a68dd14",
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
  "content_id": "5408e56f-843d-4fb2-81a2-549625da4148",
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
  "content_id": "1208861d-7e30-43cd-aece-a3c15cd5482e",
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
  "content_id": "84e87a1b-9e34-4ff9-a85f-84301dc14f96",
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
  "content_id": "dc74f28e-a631-4633-8f17-abf8d84934a3",
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
  "content_id": "158c6b4b-629d-4b9e-86f1-5ee71092bcf3",
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
  "content_id": "36671972-852f-4ec2-af1c-4feb81dc1127",
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
  "content_id": "0a3c7e44-b4df-42a4-90cb-0e19f1cc985e",
  "creator_id": "attacker_FP02",
  "guess": "unsure",
  "confidence": 0.7176,
  "model_score": 0.7387,
  "style_score": 0.6684,
  "label": "We're not sure how this piece was written, and that's all this means. Nothing is wrong. If you'd like us to take another look, you can ask for a review.",
  "error": null,
  "verdict": "HELD"
}
```

### FP03 — `false_positive`

*Targeting:* a non-native English speaker writing carefully

```json
{
  "id": "FP03",
  "status": 200,
  "content_id": "f19775f8-dbd1-4cee-8343-36a6b9ba7dad",
  "creator_id": "attacker_FP03",
  "guess": "unsure",
  "confidence": 0.7584,
  "model_score": 0.8051,
  "style_score": 0.6496,
  "label": "We're not sure how this piece was written, and that's all this means. Nothing is wrong. If you'd like us to take another look, you can ask for a review.",
  "error": null,
  "verdict": "HELD"
}
```

### FP04 — `false_positive`

*Targeting:* a student essay written to a rubric

```json
{
  "id": "FP04",
  "status": 200,
  "content_id": "9b2de51f-9e8f-4e06-9360-e650d50f64de",
  "creator_id": "attacker_FP04",
  "guess": "unsure",
  "confidence": 0.7606,
  "model_score": 0.8329,
  "style_score": 0.5918,
  "label": "We're not sure how this piece was written, and that's all this means. Nothing is wrong. If you'd like us to take another look, you can ask for a review.",
  "error": null,
  "verdict": "HELD"
}
```

### FP05 — `false_positive`

*Targeting:* technical documentation written by a person

```json
{
  "id": "FP05",
  "status": 200,
  "content_id": "5f883f0d-e5c3-4b47-85d8-87ed810e0867",
  "creator_id": "attacker_FP05",
  "guess": "unsure",
  "confidence": 0.7646,
  "model_score": 0.8005,
  "style_score": 0.6807,
  "label": "We're not sure how this piece was written, and that's all this means. Nothing is wrong. If you'd like us to take another look, you can ask for a review.",
  "error": null,
  "verdict": "HELD"
}
```

### FP06 — `false_positive`

*Targeting:* a person imitating a formal template

```json
{
  "id": "FP06",
  "status": 200,
  "content_id": "026be8ed-c6fd-494d-880f-abbd8350e4ad",
  "creator_id": "attacker_FP06",
  "guess": "unsure",
  "confidence": 0.7864,
  "model_score": 0.8814,
  "style_score": 0.5648,
  "label": "We're not sure how this piece was written, and that's all this means. Nothing is wrong. If you'd like us to take another look, you can ask for a review.",
  "error": null,
  "verdict": "HELD"
}
```

### FP07 — `false_positive`

*Targeting:* human writing that repeats a phrase deliberately

```json
{
  "id": "FP07",
  "status": 200,
  "content_id": "e6c00c66-07cf-43dd-846f-372ca99abf9a",
  "creator_id": "attacker_FP07",
  "guess": "unsure",
  "confidence": 0.787,
  "model_score": 0.8816,
  "style_score": 0.5662,
  "label": "We're not sure how this piece was written, and that's all this means. Nothing is wrong. If you'd like us to take another look, you can ask for a review.",
  "error": null,
  "verdict": "HELD"
}
```

### FP08 — `false_positive`

*Targeting:* very short genuine human submission

```json
{
  "id": "FP08",
  "status": 200,
  "content_id": "ced56267-3435-48f4-9557-e6303e671092",
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
  "content_id": "429c9941-8576-4354-aa1f-130c0d7cd448",
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
  "content_id": "9e0c2f36-fb23-48b4-86bd-24eafc79f065",
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
  "content_id": "0ed373cc-a4ba-4a06-9aa9-ae272b7242e4",
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
  "content_id": "669d7800-8a25-4f61-bccf-f33f40ef1e4c",
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
  "content_id": "fc26accd-ceff-4054-ace5-1e5e6430ddc9",
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
  "content_id": "ef1f798a-fff2-4002-b667-fb4fe9b61461",
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
  "content_id": "019a5c53-5089-49fd-9f79-c5d8a05e97cd",
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
  "content_id": "b1b004c9-ff02-4e07-b909-85ab490cdeca",
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
  "content_id": "c9ae795c-8ccc-4cc7-bf81-c08c690c21ae",
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
  "content_id": "6a219e3a-2644-4afd-b9aa-86dcb2e54948",
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
  "content_id": "c23ad22c-fe12-41c5-a498-904864b85038",
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
  "content_id": "4129727b-87a5-42e0-a666-5b25bbb02288",
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
  "content_id": "adfc1ca7-6f30-435a-9425-7a3de955f3cf",
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
  "content_id": "38d98d50-5775-4ed1-9586-820807e1a53a",
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
  "content_id": "707b1289-2a65-47ee-8d6b-1b5ab20ea837",
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
  "content_id": "55623118-bc34-47c0-99ac-75e9aae1b93d",
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
  "content_id": "c6d1280c-c407-4d62-8306-400f9da254bf",
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
  "content_id": "ca1e0802-1988-49f6-9d08-59738477f397",
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
  "content_id": "edff03d8-a777-409d-ae61-2053fe39b6b7",
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
  "content_id": "6b93c6f0-448c-454b-b549-1a64e8152a21",
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
  "content_id": "4c02fde2-1e5d-48e0-85b0-330be01378fd",
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
  "content_id": "0960cbe8-a6f2-4c86-a25e-087c5b8e60b1",
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
