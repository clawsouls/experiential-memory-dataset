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
├── README.md                       # This file
├── taskset.md                      # 20 evaluation tasks (English)
├── taskset_KO.md                   # 20 evaluation tasks (Korean, original)
├── evaluation/
│   ├── rubric.md                   # Scoring rubric (1-5 Likert, English)
│   ├── rubric_KO.md                # Scoring rubric (Korean)
│   ├── scores.csv                  # Raw scores per condition per task
│   └── protocol.md                 # Blind evaluation protocol
├── experiment/
│   └── spawn-prompts.md            # Exact prompts used per condition (sanitized)
├── synthetic-memory/               # Condition B: 20 synthetic memory files
│   ├── nextjs-supabase.md
│   ├── soul-spec.md
│   └── ... (18 more files)
├── responses/
│   ├── condition-B-responses.md    # Synthetic condition responses (English)
│   ├── condition-B-responses_KO.md # Synthetic condition responses (Korean)
│   ├── condition-D-responses.md    # Baseline condition responses (English)
│   └── condition-D-responses_KO.md # Baseline condition responses (Korean)
├── experiential-memory-stats.md    # Statistics about Condition A (no raw data)
└── soul-spec-anonymized/           # Anonymized Soul Spec used across conditions
    ├── SOUL.md
    ├── IDENTITY.md
    ├── AGENTS.md
    └── soul.json
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

- **LLM**: Claude Opus 4 (Anthropic, `claude-opus-4-6`)
- **Method**: Each condition was run in a separate, isolated session with memory folder swap as the only variable
- **Memory retrieval**: Semantic search (RAG) over the memory corpus — the agent queries relevant memory files per task, not the entire corpus at once
- **Evaluation**: Single human evaluator, blind (shuffled W/X/Y/Z labels)
- **Exact prompts**: See `experiment/spawn-prompts.md` for the sanitized prompts used per condition

## Reproducing the Experiment

### Method 1: Using an AI Agent Framework (Most Faithful)

This experiment was originally run using an agent framework that provides:
- Isolated session spawning (one session per condition)
- RAG-based memory retrieval (`memory_search` → `memory_get`)
- Tool access (file read, exec, web search)

To reproduce faithfully:

1. Set up any agent framework that supports isolated sessions and RAG (e.g., OpenClaw, LangChain, or custom)
2. Place experiential memory files in the agent's memory directory
3. Place synthetic memory files in a separate directory
4. Configure the same LLM (Claude Opus 4 or comparable)
5. Use the prompts from `experiment/spawn-prompts.md`, replacing `<MEMORY_DIR>`, `<SYNTHETIC_DIR>`, and `<TASK_FILE>` with your paths
6. Run each condition in isolation and record responses

**Note**: RAG introduces non-determinism — the exact memory snippets retrieved per query may vary. This is inherent to the experimental design and reflects real-world agent behavior.

### Method 2: Using Claude API (Simplified)

For a simplified reproduction without RAG (all memory in context):

```python
import anthropic
from pathlib import Path

client = anthropic.Anthropic()

def load_all_files(directory):
    """Load all .md files from a directory."""
    texts = []
    for f in sorted(Path(directory).glob("*.md")):
        texts.append(f"## {f.name}\n\n{f.read_text()}")
    return "\n\n---\n\n".join(texts)

soul_spec = open("soul-spec-anonymized/SOUL.md").read()
tasks = open("taskset.md").read()

# Condition B (Synthetic) — fully reproducible with public data
synthetic_memory = load_all_files("synthetic-memory/")

response = client.messages.create(
    model="claude-opus-4-20250514",
    system=f"{soul_spec}\n\n## Memory Files\n\n{synthetic_memory}",
    messages=[{"role": "user", "content": tasks}]
)
print(response.content[0].text)

# Condition D (Baseline) — no memory
response = client.messages.create(
    model="claude-opus-4-20250514",
    system=soul_spec,
    messages=[{"role": "user", "content": tasks}]
)
```

### Method 3: Using Claude.ai Web UI (Conditions B and D only)

1. Open [claude.ai](https://claude.ai) in incognito/private mode
2. Select Claude Opus with Extended Thinking
3. **Condition B**: Paste soul spec + all 20 synthetic memory files + tasks as first message
4. **Condition D**: Paste soul spec + tasks only (no memory)
5. Record responses and evaluate using `evaluation/rubric.md`

**Limitation**: Conditions A and C require ~157 experiential memory files (~300KB), which is impractical to paste manually. Use Method 1 or 2 for these conditions.

### Key Requirements for Valid Replication

- **Isolation**: Each condition must run in a completely separate session (no cross-contamination)
- **Same model**: Use the same model version across all four conditions
- **Blind evaluation**: Shuffle condition labels before scoring to prevent evaluator bias
- **Same tasks**: Use the exact tasks from `taskset.md` without modification
- **Language**: Use Korean tasks (`taskset_KO.md`) for faithful replication, or English (`taskset.md`) for cross-language replication

## Citation

```bibtex
@misc{lee2026experiential,
  title={Experiential vs Synthetic Memory in Long-Running AI Agents: 
         A Controlled Experiment in Software Development},
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
