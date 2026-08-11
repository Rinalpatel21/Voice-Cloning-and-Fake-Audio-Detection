# TIMIT Dataset — Research Notes

## What is TIMIT?

TIMIT is a well-known speech dataset made of people reading short sentences out loud, with every recording carefully labeled. The name comes from the two organizations that built it  Texas Instruments and MIT.

In short: hundreds of speakers, each reading 10 sentences, with each recording labeled all the way down to individual speech sounds.

## Why Was It Created?

In the late 1980s, speech recognition researchers had no shared, standard dataset  every lab used its own private recordings, so nobody could fairly compare whose system actually worked better. TIMIT was built to fix that: a common, high-quality benchmark everyone could train and test on.

It was designed for two specific purposes:
- Studying how different sounds in American English are actually produced
- Training and evaluating early automatic speech recognition (ASR) systems

## How Many Speakers Does It Have?

- **630 speakers** total
- Drawn from **8 major dialect regions** across the United States
- Each speaker reads **10 sentences**
- Total: **6,300 recordings** (630 × 10)

The speakers are a mix of men and women, deliberately chosen to represent a range of American regional accents.

## What Files Does It Contain?

For every recording, TIMIT provides four aligned files, not just audio:

| File type | What's inside |
|---|---|
| `.wav` | The actual audio recording |
| `.txt` | The full text of the sentence spoken |
| `.wrd` | Word-level timing — exactly when each word starts and ends |
| `.phn` | Phoneme-level timing — exactly when each individual sound starts and ends |


## How Is It Organized?

Files sit in a folder structure like this:

```
/timit/train/dr1/fcjf0/sa1.wav
```

Breaking it down:
- `train` — this file belongs to the training split (a separate `test` split also exists)
- `dr1` — dialect region 1 (out of 8 total)
- `fcjf0` — the speaker's ID code (the leading `f` means female, `m` means male)
- `sa1.wav` — the specific sentence recording

The folder path itself tells you the speaker's identity and accent region, without needing to consult a separate lookup table.

## Why Is It Good for Voice Cloning?

- **Many different speakers (630)** — voice cloning needs to learn what makes one voice sound different from another, so having variety matters.
- **Clean, studio-quality audio** — no background noise or overlapping speech, so a model learns actual voice characteristics instead of accidentally learning microphone or noise artifacts.
- **Precise phoneme timing** — because you always know exactly which sound was being made, it's easier to separate "what was said" (content) from "who said it" (voice identity) — which is exactly the split a voice cloning system needs to make internally.
- **Consistent, predictable format** — every file follows the same structure, which makes building a data-loading pipeline much simpler.

## What Are Its Limitations?

- **Very little audio per speaker** — only 10 short sentences per person (a few seconds each). Modern deep learning voice cloning models often want minutes of audio per speaker, so TIMIT alone is thin for that purpose.
- **Not parallel across speakers** — different speakers don't necessarily read the same set of sentences, so there aren't many "same sentence, different speaker" pairs, which some cloning techniques rely on.
- **Old and small by modern standards** — recorded in 1990 with older equipment
- **Limited accent/language diversity** — only American English, so a model trained solely on TIMIT won't generalize well to other languages or accents.
- **Read speech, not natural conversation** — people are reading prepared sentences aloud, which sounds more careful and less spontaneous than everyday conversational speech.

This is why, in this project, TIMIT is used specifically for the **voice cloning** component (where clean audio and phoneme labels help), while a larger, more varied dataset like **CommonVoice** is used for the **fake audio detection** component (where lots of natural, real-world speech is needed as the "genuine" class).
