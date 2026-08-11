# Voice Cloning with XTTS v2 — Research Notes

## What Is Voice Cloning?

Voice cloning is the process of generating new speech that sounds like a specific person's voice, even though that person never actually said the words being generated. You give the system: (1) some text to say, and (2) a sample of the target person's voice, and it produces new audio of "them" saying the text.

## What Is Zero-Shot Voice Cloning?

Traditionally, cloning a voice meant training or fine-tuning a whole model on that one specific speaker, using a large amount of their recorded speech — expensive and slow, and you'd need to repeat the whole process for every new speaker.

**Zero-shot** voice cloning skips that. The model is trained once, in a general way, so that at actual use time you can hand it a short clip (just a few seconds) of a brand-new speaker it has never seen before, and it clones that voice immediately — no retraining, no fine-tuning, no waiting. The model learns a general-purpose way to represent "what makes any voice sound like itself" and then applies that on the fly to whichever new sample you give it.

## Why XTTS v2?

XTTS v2 (built by Coqui AI) is a strong fit for this project for a few concrete reasons:
- **True zero-shot cloning** — it can clone a voice from as little as a **3–6 second** reference clip, with no fine-tuning or per-speaker training required.
- **Multilingual** — supports around 17 languages, useful if the project ever expands beyond English.
- **Open source and free to run locally** — no per-use API cost, and it can run on a normal machine (a GPU speeds it up, but it can run on CPU too).
- **Actively maintained** — originally from Coqui AI, now maintained as an open-source fork, with a large community and existing tooling (Python package, Hugging Face model page) that make it easy to plug into a pipeline.
- **Good enough quality out of the box** — produces natural-sounding, recognizable cloned speech without needing custom training, which matters since this project's real focus is the *fake audio detector*, not building a brand-new TTS model.

## What Inputs Does XTTS Need?

- **Reference audio** — a short clip (a few seconds up to about 6–12 seconds is typically used) of the target speaker's voice. XTTS extracts a **speaker embedding** from this — a compact numerical summary of what makes that voice sound like itself. Longer reference clips beyond a certain point don't add much extra benefit, since the model only uses the first several seconds when computing the embedding.
- **Text to synthesize** — the actual sentence(s) you want spoken in the target voice.
- **Target language** — selected explicitly, since the model supports multiple languages.

Quality of the reference clip matters more than quantity — a few seconds of clean, clear audio outperforms minutes of noisy or low-quality recording.

## What Outputs Does It Generate?

XTTS v2 outputs a synthesized **waveform** (an actual audio file) of the given text, spoken in a voice that matches the reference speaker's timbre, pitch, and general vocal character — even though that person never recorded those specific words.

## Why Don't We Train It Ourselves?

- **It's already trained on large-scale, multilingual data** — building something comparable from scratch would require enormous datasets and compute resources, well beyond the scope of this project.
- **Zero-shot cloning is the whole point** — the point of using XTTS is that it *already* generalizes to new speakers without retraining; training our own version would defeat that advantage.
- **Our actual contribution is elsewhere** — the interesting, novel part of this project is the fake audio *detector*, not reinventing voice cloning. Reusing a strong, proven, pretrained model here lets us focus effort on building and evaluating the detection system.
- **Fine-tuning is only needed in edge cases** — the base XTTS v2 model performs well out-of-the-box for most speakers; fine-tuning is really only useful if the model happens to perform poorly on a specific, unusual voice, which isn't the core problem we're solving.
