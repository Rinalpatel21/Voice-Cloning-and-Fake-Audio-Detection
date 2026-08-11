# Fake Audio Detection — Research Notes

## What Is Fake Audio Detection?

Fake audio detection is the task of looking at a piece of audio and deciding whether it's **genuine human speech** or **synthetically generated** (created by a text-to-speech or voice cloning system, like our XTTS v2 module). It's framed as a binary classification problem: the model listens to a clip and outputs "real" or "fake," usually along with a confidence score.
h
## What Artifacts Make AI-Generated Speech Detectable?

Even very good voice cloning systems tend to leave behind subtle traces that differ from genuine human speech, including:
- **Unnatural or overly smooth prosody** — real speech has small irregularities in pitch, pacing, and rhythm that synthetic speech can flatten out or get slightly wrong.
- **Spectral artifacts** — machine-generated audio can have subtle statistical patterns in its frequency content (visible in things like spectrograms or CQCC features) that don't quite match how real vocal tracts produce sound.
- **Vocoder artifacts** — most modern TTS/voice cloning systems generate audio through a neural vocoder step, which can introduce faint, characteristic noise patterns specific to that vocoder.
- **Overly clean or consistent audio** — real recordings usually have some natural background noise or micro-variation; synthetic clips can sometimes be unnaturally uniform.

Detectors are trained to pick up on these patterns automatically, rather than us hand-engineering rules for each one — this is why deep learning approaches (rather than simple rule-based checks) work much better here, since the exact "tells" vary by which generation system produced the fake.

## Why Use Wav2Vec2?

Wav2Vec2 is a **self-supervised** speech model originally built by Facebook AI Research, and it's a strong fit for this task for several reasons:
- **It already understands speech before we teach it anything about fakes.** It was pretrained by listening to huge amounts of raw audio and learning to predict masked-out (hidden) portions of it — similar in spirit to how BERT learns about text. This pretraining teaches it general speech structure like phonemes and prosody, without needing any "real vs. fake" labels at that stage.
- **It works directly on raw waveforms.** No manual feature engineering (like hand-designing spectrogram filters) is required — the model itself learns useful features from the raw audio signal.
- **Transfer learning saves enormous effort.** Rather than training an audio understanding model from zero, we start from a model that already understands speech, and only need to teach it the much narrower task of "what distinguishes fake speech from real speech."
- **It's a proven approach in this exact research area.** Multiple published fake/deepfake audio detection systems use Wav2Vec2 (or similar self-supervised models) as their front end, often outperforming older hand-crafted-feature approaches.

## Why Fine-Tune Instead of Training From Scratch?

- **Massive head start.** Wav2Vec2's pretraining already required huge amounts of audio and compute that we don't need to redo — fine-tuning lets us build on top of that instead of repeating it.
- **Much smaller labeled dataset needed.** Training a good detector from scratch would likely require far more labeled real/fake examples than we can practically generate and validate. Fine-tuning a model that already understands speech needs comparatively few labeled examples to reach strong performance.
- **Faster and cheaper to train.** Fine-tuning typically means adding a small classification layer on top of the pretrained model and only updating a fraction of its parameters (or all of them, but starting from a strong point) — this trains in a fraction of the time a from-scratch model would need.
- **Better generalization.** Because the pretrained model already has a broad understanding of speech in general (not just our specific fake examples), the fine-tuned detector tends to generalize better to fake audio it hasn't specifically seen before, compared to a narrow model trained only on our dataset.
