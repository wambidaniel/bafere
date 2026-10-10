# BAFERE: a parallel benchmark for mobile-money scam detection in English, Luganda and Lusoga

*Bafere* is the word Ugandans use for mobile-money fraudsters. This project asks a simple security question:

> **Does a scam become harder to detect when it is written in Luganda or Lusoga instead of English?**

Spam filters and LLM assistants are built and tested mostly in English, but in Uganda many scam messages arrive in local languages. BAFERE measures how much protection is lost when only the language changes.

## Design

- **120 SMS messages**, each in **English, Luganda and Lusoga** (parallel: the same content in all three languages).
- **60 scams** across six fraud types documented by the Uganda Communications Commission and the Uganda Police: mistaken-transfer reversals, fake prizes, impersonation of telcos, police or tax officials, relatives in distress, advance-fee loans and jobs, and account-block or phishing messages.
- **60 legitimate hard negatives**, each category mirroring a scam type: real transaction receipts, genuine promotions, "never share your PIN" warnings, ordinary family money requests. A detector that keys on words like *PIN*, *prize* or *send* will raise false alarms.
- Phone numbers are masked in the same format in both classes, links in scams are fictitious, and messages are text only, so the label must come from the content.
- Labels follow a written codebook ([`codebook.md`](codebook.md)), checked by an independent annotator who labels blind.
- Translations are made by native speakers **without machine translation**, because the systems under test include translation-capable LLMs.

## Evaluation

[`notebooks/BAFERE_evaluation.ipynb`](notebooks/BAFERE_evaluation.ipynb) runs on a free Colab T4 GPU and compares:

| System | Why |
|---|---|
| TF-IDF + logistic regression trained on the English UCI SMS Spam Collection | an off-the-shelf English spam filter |
| Qwen3-14B | a general multilingual LLM |
| Sunflower-14B (Sunbird AI) | the same base model adapted to Ugandan languages, so the comparison isolates language adaptation |
| Translate-test | Luganda/Lusoga translated to English by Sunflower, then classified |

LLMs are scored zero-shot by the log-likelihood of each answer (`SCAM` / `LEGIT`), giving a deterministic probability. Metrics: macro-F1, scam recall, false-alarm rate and AUROC, with stratified bootstrap confidence intervals and exact McNemar tests on paired messages (the parallel design makes the tests paired).

## Status

- [x] English source set and labelling codebook
- [x] Evaluation pipeline, tested end to end on placeholder data
- [ ] Luganda and Lusoga translation (in progress)
- [ ] Independent annotation and results

## Data release

This repository contains a **10-message English sample** ([`data/sample_en.csv`](data/sample_en.csv)), one scam and its matching hard negative from five categories. The full three-language set will be released with the paper. Keeping it out of public repositories until then avoids test-set contamination of models trained on web data.

## Author

Daniel Wambi, Kampala, Uganda: wambidanielcollins256@gmail.com

*Use of AI tools: an LLM assisted with drafting the English source messages and the evaluation code. All messages were reviewed by the author, and all translations and annotations are produced by humans.*
