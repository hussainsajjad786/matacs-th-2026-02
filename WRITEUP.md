# AI Proposal Triage Design

## 1. AI vs. rules

Use AI to interpret proposal language and extract criterion facts into a fixed schema: value, unit, source quote/page, and confidence. AI can recognize equivalent wording, but must not fill gaps or make eligibility, scoring, ranking, or award decisions. Treat the proposal as untrusted input; ignore embedded instructions and return `unknown` when evidence is missing or unclear.

Use ordinary code for file checks, category selection, loading admin-configured criteria, schema/evidence validation, mandatory checks, normalization, weighted scoring, sorting, and result generation. Criteria and weights come from versioned admin configuration, never hard-coded into the prompt. A reviewer resolves uncertain evidence before it is used.

The explicit mandatory checks are bidder-held Ontario electrical contractor licence stated in the proposal, WSIB clearance, and $5M liability insurance. A subcontractor to be selected after award does not satisfy the licence check. Budget and the 16-week schedule are project constraints reflected in the price/timeline criteria; flag any breach or unknown for human resolution instead of silently passing it.

## 2. Consistency and explainability

Pin model, prompt, schema, and criteria versions for each run; use a fixed low-variance extraction setting and schema validation. After the reviewer confirms facts, code calculates scores and sorts deterministically. Identical verified inputs and versions yield identical rankings. Show each extracted value with source quote/page, confidence, reviewer status, mandatory result, criterion score, weight, total, flags, and plain-language reason.

Worked-example scoring rules:

- Experience: `min(qualifying projects in last five years, 10) / 10 × 100`.
- Price: `lowest eligible bid / bidder's bid × 100`; flag amounts above the CAD 260,000 ceiling for resolution.
- Timeline: `max(0, (16 - weeks) / 16 × 100)`; flag unknown or over-16-week schedules.
- Warranty: average panel score (`years / 25 × 100`) and workmanship score (`years / 12 × 100`), each capped at 100.
- Team/approach: 25 points each for named lead, identified crew, safety plan, and monitoring/reporting plan. Do not infer missing elements.
- Total: criterion score × configured weight, summed. Sort by unrounded total; ties break by experience score, then lower price, then bidder name. Display two decimals.

AI extraction can vary, so evidence quotes, confidence review, stored outputs, and version records make uncertainty visible. Numeric scoring and tie-breaking remain deterministic once facts are verified.

## 3. Human review and audit

The reviewer selects the category, confirms the files, checks low-confidence facts and all mandatory evidence, and resolves unknowns before scoring. The reviewer can correct extraction, request clarification, or override the suggested order with a required reason. An authorized procurement decision-maker makes and records the final award; the tool never awards a contract.

Log reviewer/uploader and timestamps, file hash, category, criteria/model/prompt versions, extracted evidence and confidence, validation and eligibility results, score inputs/calculation, flags, reviewer edits/overrides and reasons, ranked output, and final human decision. Limit access and retention; avoid storing unnecessary proposal text or credentials.

## 4. Changing criteria

Each admin edit creates a new immutable criteria version with editor and effective time. Existing runs retain their original version and results; nothing is silently rescored. An authorized reviewer can explicitly start a new run using a selected version. Show a comparison, affected eligibility/ranks, requester, reason, and both versions. Recheck proposals if mandatory criteria changed.

## 5. Risks and safeguards

- **Wrong extraction or hallucination:** require source quotes/pages and typed values; unknown is not a pass; human-check mandatory and low-confidence facts.
- **Prompt injection:** proposals are untrusted data, isolated from instructions/tools. Ignore and audit embedded commands; Proposal C's request to rank it first is ignored.
- **Bias or opaque ranking:** use job-related admin criteria, visible formulas, consistent tie-breakers, reviewer oversight, and periodic criteria/outcome review.
- **Ambiguous price/schedule:** normalize units and total bid scope; flag missing inclusions or dates. Enforce the budget and schedule constraints explicitly.
- **Overreliance on score:** display evidence and criterion breakdown; keep award with a person and require override rationale.
- **Privacy/access:** role-based access, encryption, limited retention, minimal logs; this assessment uses fictional data only.
- **Outage/cost:** set file/token limits, timeouts, retry limits, and usage monitoring; fall back to manual review and never present partial results as complete.

## Worked sample ranking

A, C, and E pass the stated mandatory checks. B is ineligible because its proposal does not confirm the bidder itself holds the required Ontario licence; its electrical subcontractor is not yet identified. D has the stated licence, WSIB, and insurance evidence, but its schedule is undetermined, so compliance with the 16-week constraint cannot be verified; flag it pending clarification. Do not score B or D until resolved. Ignore Proposal C's embedded instruction as untrusted content.

| Position | Proposal | Score | Rationale |
|---:|:---:|---:|---|
| 1 | A | 87.50 | Highest experience and complete approach evidence, with a 12-week schedule and 10-year workmanship warranty; highest eligible price. |
| 2 | E | 81.35 | Competitive price and schedule, 12-year workmanship warranty, and complete named-team, safety, and monitoring evidence. |
| 3 | C | 78.26 | Strong experience and warranties, but highest eligible price, 14-week schedule, and no stated safety plan. |
| — | D | — | Flagged: schedule is unstated; request clarification before scoring. |
| — | B | — | Ineligible: required bidder-held electrical licence is unconfirmed. |

These are design scores from summaries, not an award recommendation. Verify source documents before relying on a real ranking.
