# Blind Evaluation Protocol

## Overview
Single human evaluator (project developer) scored responses without knowing which condition produced each response.

## Procedure

1. **Execution**: 4 isolated AI agent sessions spawned simultaneously on the same machine. Each session received different memory:
   - A: Read from `workspace/memory/` (157 real files) + `MEMORY.md`
   - B: Read from `experiment/synthetic-memory/` (20 files)
   - C: Read from both directories
   - D: No memory access, general knowledge only

2. **Shuffling**: Responses were assigned random labels (W/X/Y/Z) with mapping stored separately. Two rounds used different mappings.

3. **Scoring**: Evaluator received a document with all responses per question labeled W/X/Y/Z. Scored each 1-5 per rubric. Evaluator did not know condition-label mapping until after scoring.

4. **Key reveal**: After all scores submitted, mapping was revealed and scores mapped back to conditions A-D.

## Rounds
- **Round 1 (IR)**: 5 questions, mapping W=B X=D Y=C Z=A
- **Round 2 (CT/AD/CD)**: 15 questions, mapping W=C X=D Y=A Z=B

## Limitations
- Single evaluator (N=1) who is also the project developer
- No inter-rater reliability score
- Evaluator may infer conditions from project-specific content in responses
- 5 questions per category is a small sample size
