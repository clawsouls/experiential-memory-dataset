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
- **Method**: Each condition was run in a separate, isolated conversation with memory folder swap as the only variable
- **Machine**: Apple Mac mini (arm64, macOS)
- **Memory retrieval**: Semantic search + targeted file read (RAG pipeline)
- **Evaluation**: Single human evaluator, blind (shuffled W/X/Y/Z labels)

## Reproducing the Experiment

This experiment can be reproduced using any Claude interface (API, claude.ai web, or any compatible agent framework). No specific tooling is required.

### Steps

1. **Start a new conversation** with Claude Opus (or comparable model) for each condition.
2. **Set the system prompt** using the soul spec from `soul-spec-anonymized/`.
3. **Load memory files** into the conversation context:
   - **Condition A**: Experiential memory files (request from authors, or use your own agent's memory)
   - **Condition B**: Synthetic memory files from `synthetic-memory/`
   - **Condition C**: Both A + B combined
   - **Condition D**: No memory (baseline)
4. **Send each task** from `taskset.md` and record the response.
5. **Evaluate** responses using the rubric in `evaluation/rubric.md`.

### Using Claude.ai (Web)

- Start a new conversation per condition (use incognito/private mode to avoid context bleed)
- Paste the soul spec as the first message, then paste relevant memory files
- Send tasks one at a time in the same conversation

### Using Claude API

```python
import anthropic

client = anthropic.Anthropic()

# Load soul spec and memory files as system prompt
system_prompt = open("soul-spec-anonymized/SOUL.md").read()
memory_context = "\n\n".join(open(f).read() for f in memory_files)

response = client.messages.create(
    model="claude-opus-4-20250514",
    system=f"{system_prompt}\n\n## Memory Files\n{memory_context}",
    messages=[{"role": "user", "content": task_text}]
)
```

### Key Requirements for Valid Replication

- **Isolation**: Each condition must run in a completely separate session (no cross-contamination)
- **Same model**: Use the same Claude model version across all conditions
- **Blind evaluation**: Shuffle condition labels before scoring to prevent evaluator bias
- **Same tasks**: Use the exact tasks from `taskset.md` without modification

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
