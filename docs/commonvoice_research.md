# Mozilla Common Voice — Research Notes

## What is Mozilla Common Voice?

Common Voice is a free, public speech dataset run by Mozilla. Instead of a lab recording speakers in a studio (like TIMIT), Common Voice is **crowdsourced** — anyone with a microphone can go to the Common Voice website, read a sentence out loud, and donate that recording to the dataset. Other volunteers then listen to submitted clips and vote on whether they sound correct, which is how the data gets validated as trustworthy.

The project's stated goal is to make voice technology more inclusive, since most existing speech datasets historically underrepresented women, non-native accents, and many of the world's languages.

## How Many Languages?

Common Voice has grown enormously since its first release in November 2017, which contained just 500 hours of English. It's now one of the largest open multilingual speech datasets in the world, with its scripted-speech dataset spanning close to 300 languages as of its mid-2026 release, and tens of thousands of hours of validated speech overall. New languages and hours are added with every release, since the whole project depends on ongoing volunteer contributions.

## Why Use It?

- **Scale** — far more speakers and hours than TIMIT, which matters because a fake-audio detector needs to see lots of natural, varied real speech to learn what "genuine" sounds like.
- **Natural, everyday speakers** — recorded by ordinary volunteers on their own devices, not actors in a studio, so it reflects more realistic, messy real-world audio conditions.
- **Free and open license** — released under a CC0 (public domain-style) license, so it can be used and redistributed with no cost or usage restriction.
- **Diversity** — many languages, accents, ages, and genders, which helps a detection model generalize instead of only working on one narrow type of voice.

## Why Only Use a Subset?

The full dataset is enormous — tens of thousands of hours across hundreds of languages. For this project:
- We only need **English** speech, since our fake-audio detector is being built and evaluated on English for now.
- Downloading, storing, and processing the entire dataset isn't necessary or practical — it would take far more disk space and compute time than the project needs.
- A **stratified sample** (a few thousand to tens of thousands of clips, balanced across speakers, gender, and accents) gives enough real-speech variety to train and evaluate the detector well, without the overhead of the full corpus.

## What Information Comes With Each Recording?

Each clip in Common Voice comes with metadata beyond just the audio, including:
- The **transcript** — the sentence text that was read
- **Speaker demographics** (when volunteered) — age range, gender, accent
- **Client/speaker ID** — an anonymized identifier so multiple clips from the same contributor can be grouped
- **Validation votes** — how many listeners marked the clip as correct/matching the transcript, used to filter out bad or mismatched recordings
- **Up-votes/down-votes** — a simple quality signal from the community review process

## How Is It Organized?

Common Voice releases are distributed as a set of audio clips (typically `.mp3` files) plus accompanying **TSV (tab-separated value) metadata files** that list, for each clip: the filename, transcript, speaker ID, demographic info (if provided), and validation status. This is different from TIMIT's folder-based organization — here, the audio files themselves usually sit in one flat folder, and all the structure/labeling lives in the metadata table rather than in the file path.

A typical workflow: read the TSV file to decide which clips to use (e.g., filter to only "validated" clips), then load the corresponding `.mp3` files listed there.
