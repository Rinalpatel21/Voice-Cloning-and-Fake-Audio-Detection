# Evaluation Metrics — Research Notes

## What Is WER?

**WER (Word Error Rate)** measures how accurately spoken content was preserved, by comparing a transcript to a reference (ground-truth) text. It's calculated by taking the minimum number of word-level edits — insertions, deletions, and substitutions — needed to turn the predicted transcript into the correct one, divided by the total number of words in the reference:

```
WER = (Substitutions + Deletions + Insertions) / Total words in reference
```

A WER of 0 means a perfect match; higher numbers mean more errors. In this project, WER is used to check the **voice cloning** system: we take the cloned audio, run it through a speech recognition model (Whisper) to get a transcript, and compare that transcript to the original text that was supposed to be spoken. A low WER means the cloned audio still says the right words clearly, even though the voice has changed.

## What Is Speaker Similarity?

Speaker similarity measures whether the generated (cloned) audio actually **sounds like the intended target speaker**, separate from whether the words are correct. It's typically computed by:
1. Extracting a **speaker embedding** (a numerical "voice fingerprint") from the reference/target speaker's real audio
2. Extracting the same kind of embedding from the newly generated cloned audio
3. Comparing the two embeddings — usually with cosine similarity — to get a score of how close they are

A high similarity score means the cloned voice really does sound like the target speaker; a low score means the cloning failed to capture that person's vocal identity, even if the words themselves came out correct. This is why the project plan tracks **both** WER and speaker similarity/accuracy together — one checks *what* was said, the other checks *who* it sounds like.

## Why F1-Score?

**F1-score** is the metric used to evaluate the fake audio detector. It combines two other metrics — **precision** and **recall** — into a single number using their harmonic mean:

```
F1 = 2 × (Precision × Recall) / (Precision + Recall)
```

It's a good fit here because fake audio detection is a case where **both** false alarms and missed detections matter:
- A **false positive** (calling real audio "fake") could wrongly flag someone's genuine voice as a scam or deepfake.
- A **false negative** (calling fake audio "real") means an actual deepfake slips through undetected — arguably the more dangerous failure in a security context.

F1-score forces both precision and recall to be reasonably good at the same time — a model can't get a high F1 by excelling at only one while ignoring the other.

## Why Not Accuracy Alone?

Plain accuracy (percentage of all predictions that were correct) can be misleading, especially with **imbalanced datasets** — and this project is likely to have exactly that problem, since the amount of real audio (from CommonVoice) and generated fake audio (from our own VC pipeline) may not be perfectly balanced.

A concrete example: if 90% of a test set is real audio and only 10% is fake, a detector that just predicts "real" for every single clip would score 90% accuracy — while completely failing at the one job it exists to do (catching fakes). Accuracy doesn't distinguish between "getting the easy majority class right" and "actually being useful."

This is exactly why **precision, recall, and F1-score** are preferred: they specifically measure how well the model performs on catching the class we actually care about identifying (fake audio), rather than being inflated by how common the "easy" class happens to be in the test set.
