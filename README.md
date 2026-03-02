# Experiential vs Synthetic Memory in AI Agents — Dataset

Dataset for the paper: **"Experiential vs Synthetic Memory in Long-Running AI Agents: A Controlled Experiment in Software Development"**

📄 Paper: [Zenodo DOI 10.5281/zenodo.18798227](https://zenodo.org/records/18798227)

## Overview

This dataset accompanies a controlled experiment comparing four memory conditions for AI coding agents:

| Condition | Description | Data Available |
|-----------|-------------|----------------|
| A: Experiential | Real memory from 2-week collaboration (~157 files) | Statistics only (contains sensitive info) |
| B: Synthetic | Documentation-style knowledge (~20 files) | ✅ Full data |
| C: Hybrid | A + B combined | On request (contains sensitive info) |
| D: Baseline | No memory (LLM general knowledge only) | ✅ Full responses |

## Results Summary

| Category | A (Experiential) | B (Synthetic) | C (Hybrid) | D (Baseline) |
|----------|:---:|:---:|:---:|:---:|
| Information Retrieval (5) | 4.2 | 1.4 | **5.0** | 2.0 |
| Coding Tasks (5) | 4.2 | 3.2 | **5.0** | 4.0 |
| Architecture Decisions (5) | 4.8 | 3.4 | **5.0** | 4.0 |
| Context-Dependent (5) | **5.0** | 2.6 | 4.8 | 3.2 |
| **Overall (20)** | **4.55** | **2.65** | **4.95** | **3.30** |

**Key Finding**: Hybrid memory (experiential + synthetic) achieves near-perfect scores (4.95/5.0), outperforming either source alone. Surprisingly, synthetic memory alone performs *worse* than no memory at all (2.65 < 3.30).

## Repository Structure

```
├── README.md                    # This file
├── taskset.md                   # 20 evaluation tasks (5 per category)
├── evaluation/
│   ├── rubric.md                # Scoring rubric (1-5 Likert)
│   ├── scores.csv               # Raw scores per condition per task
│   └── protocol.md              # Blind evaluation protocol
├── synthetic-memory/            # Condition B: 20 synthetic memory files
│   ├── nextjs-supabase.md
│   ├── soul-spec.md
│   ├── age-encryption.md
│   └── ... (17 more files)
├── responses/
│   ├── condition-B-responses.md # Full responses from Synthetic condition
│   └── condition-D-responses.md # Full responses from Baseline condition
├── experiential-memory-stats.md # Statistics about Condition A (no raw data)
└── soul-spec-anonymized/        # Anonymized Soul Spec used across conditions
```

## Requesting Private Data

Conditions A (Experiential) and C (Hybrid) contain personally sensitive information including API credentials, employment details, and business strategy. These are available to researchers upon request:

📧 **contact@clawsouls.ai**

We will provide anonymized versions within 2 weeks of request. Please include:
- Your affiliation and research purpose
- How the data will be stored and protected
- Expected publication/use of the data

## Language & Reproducibility Notice

> **Original experiment was conducted in Korean.** Both the agent's working memory and the evaluation tasks were in Korean, as this reflects the natural working language of the human-agent collaboration.
>
> English translations of the taskset, rubric, and responses are provided for international accessibility (see `*_KO.md` files for Korean originals). Memory files in `synthetic-memory/` and private experiential memory remain in Korean — this is an inherent part of the experimental conditions, not a limitation to be corrected.
>
> **Replication in English** may yield different absolute scores due to language-dependent LLM behavior, but is expected to preserve relative rankings across conditions. We welcome cross-language replication studies.

## Experiment Environment

- **LLM**: Claude Opus 4 (Anthropic)
- **Framework**: Isolated sessions with Claude Opus 4 (each condition run in a separate session with memory folder swap as the only variable)
- **Machine**: Apple Mac mini (arm64, macOS)
- **Memory retrieval**: Semantic search (`memory_search`) + targeted read (`memory_get`)
- **Evaluation**: Single human evaluator, blind (shuffled W/X/Y/Z labels)

## Citation

```bibtex
@misc{lee2026experiential,
  title={Experiential vs Synthetic Memory in Long-Running AI Agents: A Controlled Experiment in Software Development},
  author={Tom Lee},
  year={2026},
  publisher={Zenodo},
  doi={10.5281/zenodo.18798227}
}
```

## License

- **Dataset**: CC-BY-4.0
- **Synthetic memory files**: CC-BY-4.0
- **Paper**: CC-BY-4.0
