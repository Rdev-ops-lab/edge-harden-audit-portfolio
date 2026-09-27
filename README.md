# Edge hardening sign-off audit (portfolio sample)

Public write-up of a Harbor agent task I authored in the security category. It passed the platform security review.

The live task bundle is not in this repo. The agent prompt, the synthetic channel log, the Oracle solution, and the expected report stay private so the benchmark cannot be copied.

## What the agent had to do

Reconcile a long, shuffled `#sec-hardening` ops transcript for a 100-host edge fleet and write `/app/report.json`.

The hard part is state over time, not formatting:

- Re-order events by timestamp. File order is not chronological.
- Drop quoted replies and fenced paste excerpts.
- Resolve host aliases and codenames that change as the log moves forward.
- Apply a precedence chain: bulletins, corrections, planned exceptions, named human sign-offs, revocations, and a dependency cascade.
- Emit two inventories: hosts that received a real human sign-off, and hosts still noncompliant at the end.

A near-miss parser shifts both counts. The verifier checks the JSON schema and the exact host lists, not a single integer.

## Layout of the private task

```text
task/
  instruction.md          agent prompt
  task.toml               metadata, timeouts, difficulty notes
  environment/            Dockerfile and the transcript the agent reads
  solution/               Oracle. Must score a reward of 1
  tests/                  Behavioral verifier. NOP agent must score 0
references/
  dynamo-rubric.toml      QC rubric used before submission
```

Local gate before upload:

```bash
harbor run -p task --agent oracle   # reward 1
harbor run -p task --agent nop      # reward 0
```

## QC sheet (how I score a task before submit)

I track this in Excel / Google Sheets, one row per task:

| Column | What I record |
|---|---|
| Task id | Platform id |
| Category | Security, coding, STEM, … |
| Oracle | Pass / fail, reward |
| NOP | Must fail. A passing NOP means the verifier is reward-hackable |
| Instruction | Ambiguous line, missing schema, or hidden rule |
| Verifier | Schema check, exact match vs fuzzy match, flake on rerun |
| Cheat notes | Path the agent could hardcode, leak in the image, or skip |
| Difficulty | Why a strong model still misses |
| Status | Draft / revision / security passed |

Same sheet is what I use when I review someone else’s task: ambiguous instruction, non-reproducible environment, or a grader that passes the wrong answer.

## Metadata I can share

- Category: Security / security hardening
- Objective: analyze a document and emit a report
- Agent timeout: 60 minutes. Verifier timeout: 2 minutes
- Oracle and NOP both run clean before the security pass
