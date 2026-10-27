# MATACS-TH-2026-02 | AI Proposal Triage Design

**Candidate:** Sajjad Hussain  
**Role:** Associate Software Developer  
**Reference:** MATACS-TH-2026-02  
**Time actually spent:** Approximately 1 hour

## Submission contents

- [Design write-up](WRITEUP.md) — answers to the five assessment questions.
- [AI extraction prompt](PROMPTS.md) — proposed production prompt, including instruction-injection handling.
- [Expected ranking](ranking.json) — all five sample proposals, statuses, scores, and reasons.
- [Process flow diagram](diagram/process-flow.png) — upload-to-ranking flow with AI, rules, and human checkpoints.

## AI use

I used ChatGPT to help organize and edit this design and to check that the proposed flow addresses the assessment questions. I reviewed the proposal facts against the supplied summaries; one important correction was to treat the instruction embedded in Proposal C as untrusted document content, not as a command to the triage system. The final mandatory checks, scoring rules, and ranking are documented so a reviewer can reproduce and challenge them.

## Before submission

Read the write-up and make sure you can explain and defend the scoring choices in your own words during the follow-up discussion. Keep the repository public as requested by the brief, and do not add the assessment brief or copied proposal text to this repository.
