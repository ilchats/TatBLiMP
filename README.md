---
language:
  - tt
license: cc-by-nc-4.0
pretty_name: TatBLiMP
size_categories:
  - 1K<n<10K
task_categories:
  - text-classification
tags:
  - minimal-pairs
  - grammaticality
  - acceptability
  - morphosyntax
  - linguistic-probing
  - tatar
  - turkic
  - low-resource
  - benchmark
---

# TatBLiMP, a Tatar Benchmark of Linguistic Minimal Pairs

> The current release is version 1.0 (2026-06-19). It covers 16 phenomena across 1248 ratified pairs, with md5 `7659770f`. Fifteen phenomena are adapted from TurBLiMP and one is specific to Tatar. The data file is `test.jsonl`, one pair per line.

## Summary

TatBLiMP measures Tatar (`tt`, Cyrillic) morphosyntax with minimal pairs. It follows BLiMP (Warstadt et al. 2020) and adapts most of TurBLiMP's phenomena to Tatar. Each item is a pair of sentences that differ by one minimal edit, one grammatical (`good`) and one ungrammatical (`bad`). A model passes an item when it assigns higher probability to the grammatical sentence, so that `logprob(good) > logprob(bad)`. Scoring compares probabilities the model already assigns. It therefore needs no text generation and no syntactic parser, and it runs on base models and on mid-training checkpoints.

Beyond the adapted TurBLiMP set, the benchmark adds one phenomenon specific to Tatar that shows up as a documented calque in Russian- and English-influenced usage. In Tatar the counted noun after a numeral or quantifier stays singular (*биш китап*, "five book"). Pluralising it (*\*биш китаплар*) copies the Russian and English pattern, and this contrast has no counterpart in the TurBLiMP inventory.

## Method

The grammatical member comes from three kinds of source. About 32% are hand-adapted TurBLiMP items, with government and lexicon adjusted to Tatar norms and false friends between Turkish and Tatar kept as deliberately hard items. About 26% are attested sentences from a curated pool of Tatar prose and poetry. About 42% are hand-composed from corpus-verified vocabulary and collocations.

The ungrammatical member is a clone of the grammatical one with exactly one edit. The edit is a morpheme swap built with the `apertium-tat` transducer, a licensed insertion, a stripped or replaced affix, an over-marked plural, or a word-order permutation. The result is always a real Tatar word form that is wrong in this context, never a nonce word. This is the plausibility principle. The bad member is a realistic error rather than an absurd string, the kind that Indo-European influence (English, Russian) produces in Tatar. A model that fails a pair therefore fails on grammar rather than on the rarity of the string. Every pair is ratified by a native speaker, who removes items where the bad member is accidentally grammatical or where meaning shifts.

## Coverage (v1.0)

| phenomenon | pairs |
| --- | --- |
| anaphor agreement | 79 |
| argument structure, ditransitive | 78 |
| argument structure, transitive | 78 |
| binding (Principle B) | 78 |
| ellipsis (backward gapping) | 85 |
| irregular forms | 80 |
| island effects (wh-word at clause boundary) | 80 |
| nominalization | 70 |
| NPI licensing | 80 |
| numeral–noun number (bare noun after numeral/quantifier) † | 85 |
| passives (agent phrase added to an impersonal passive) | 83 |
| quantifiers (partitive *күбесе/күпчелеге* before a bare noun) | 85 |
| relative clauses (possessive agreement on the head dropped or mismatched) | 88 |
| subject–verb agreement | 77 |
| suspended affixation (finite affix stripped from the first conjunct) | 47 |
| word order (scrambling in embedded clause) | 75 |
| **total** | **1248** |

† This phenomenon is specific to Tatar. It is the addition described in the Summary above.

The one TurBLiMP phenomenon not carried over is determiners. The Turkish obligatory-*bir* contrast has no structural counterpart in Tatar by native-speaker verdict, so it is excluded by design rather than left pending.

## Format and metric

The data are JSONL, one pair per line, with the `good` and `bad` sentences, the `phenomenon`, and a `subtype` giving the fine-grained condition where one applies. The primary metric is `acc_norm`, the byte-normalized log-probability, which is tokenizer-independent. The secondary metric is raw `acc`. Chance is 0.5 for both.

## Results

### TatBLiMP v1.0 — grammaticality (acc ↑, chance 0.5)

Minimal pairs, 1248 pairs, same harness for all. acc = `logP(good) > logP(bad)`; acc_norm = byte-normalized. "Tatar": `trained` = trained/adapted on Tatar, `documented` = Tatar in the model's official language list, `—` = not documented.

| Model | Tatar | acc | acc_norm |
|---|:---:|--:|--:|
| **TATlit-478M** | ✓ trained | **0.9752** | **0.9575** |
| goldfish-125M | ✓ trained | 0.9736 | **0.9575** |
| Tweety-7B | ✓ trained | 0.9559 | 0.9151 |
| Gemma-4-31B | — | 0.9239 | 0.8389 |
| Llama-4-Scout | — | 0.8894 | 0.8061 |
| Qwen3-32B | ✓ documented | 0.8109 | 0.7075 |
| gpt-oss-120b | — | 0.8029 | 0.6771 |
| mGPT-1.3B | ✓ trained | 0.7356 | 0.6386 |

Frontier chat models expose no token log-probabilities, so `logP(good) > logP(bad)` cannot be computed. They are evaluated with a forced-choice protocol instead — both sentences are shown as A and B and the model names the grammatical one, with every pair asked in both orderings to control for position bias.

| Model | Release | Accuracy | Both-orders |
|---|---|--:|--:|
| gemini-3-flash-preview | 2025-12 | 0.9776 | 0.9575 |
| gpt-4.1 | 2025-04 | 0.9471 | 0.9006 |
| claude-sonnet-4-5 | 2025-09 | 0.8714 | 0.7508 |

Accuracy is over all 2,496 items (1,248 pairs × 2 orderings); both-orders counts pairs correct in both orderings.

## Leak control

The sentences are unlikely to be in any model's training data, so a model cannot pass items by memorizing them. The attested carriers are scanned from printed Tatar prose and poetry that is not available online, the adapted items are reworked from TurBLiMP into Tatar, and the hand-composed items are written for the benchmark.

## Intended use and limitations

TatBLiMP is a diagnostic of Tatar grammatical competence for language models and for tokenizer or training ablations. A high score shows that a model detects violations. It does not show that the model produces native Tatar. Coverage is limited to the listed phenomena, and the literary-prose register may under-represent colloquial usage.

## License

The dataset is released under CC BY-NC 4.0. It is free to use, share and adapt for non-commercial purposes with attribution. For commercial licensing, contact the author. Items adapted from TurBLiMP derive from CC BY 4.0 material and are attributed accordingly.

## Citation

```bibtex
@misc{tatblimp2026,
  title  = {TatBLiMP: A Benchmark of Linguistic Minimal Pairs for Tatar},
  author = {Ilshat Saetov, Dmitry Gaynullin},
  year   = {2026},
  url    = {https://huggingface.co/datasets/ilchats/TatBLiMP}
}
```

Built on [BLiMP](https://aclanthology.org/2020.tacl-1.25/) (Warstadt et al. 2020), [TurBLiMP](https://arxiv.org/abs/2506.13487) (arXiv 2506.13487) and the `apertium-tat` transducer ([Washington, Salimzyanov and Tyers, LREC 2014](https://aclanthology.org/L14-1143/)).
