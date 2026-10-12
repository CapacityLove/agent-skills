---
name: capacitylab-first-run
description: "Load test a web service or HTTP API with CapacityLab: a free $0 Smoke through capacitylab.quickstart in four calls (zones.list, quickstart, reports.status, reports.get), then a paid baseline only after the user approves the exact price. Use when the user asks to load test, stress test, smoke test or benchmark their API, or how much traffic it handles, and the capacitylab MCP tools are connected."
metadata:
  source: capacitylab://knowledge/workflows/first-run
---

Use the tools of the CapacityLab MCP server (`capacitylab`). If they are not available, ask the user to connect it from the AI agents page of CapacityLab Console; do not call the API another way. This skill is generated from the resource capacitylab://knowledge/workflows/first-run; when the server serves a newer text, follow that.

# First CapacityLab run

The shortest path from a connected agent to a compared Result. Each step names the exact call; open a linked resource only when a step needs it. Never ask for or pass a secret value, never work around a refusal, and never lower the load behind the member's back.

## 0. Shortcut: one call to a $0 Smoke

`capacitylab.quickstart` needs only `{ baseUrl, zone, idempotencyKey }`; the member picks the zone, from `capacitylab.load_plans.zones.list` when unsure. Do not list applications, targets or workloads first, even for an application already set up by hand: it captures or reuses the Target, smokes the Workload that application last used (else its Quickstart Workload, else a GET-only draft from `endpoints` or `openApi`; `workloadRevisionId` overrides) and names it in `workload`, and resolves, compiles and confirms the Smoke only at exactly $0. Follow its `next`: `needs_authorization`/`needs_proof` give the proof to publish, then call again with the same key and the member's attested `authorization`; `started` gives the `reports.status` call (section 2, steps 4-5), so a set-up service needs four calls: zones.list, quickstart, reports.status, reports.get. Use sections 1-2 only to build or change a Workload by hand.

## 1. Setup (once per service)

1. `capacitylab.system.status`: continue only when `execution.confirm.allowed` is true; otherwise explain its blockers.
2. Reuse what exists: `capacitylab.applications.list` and `capacitylab.targets.list` give the Target handle and its `targetRevisionId`.
3. No Target yet: `capacitylab.targets.capture` with `{ input: { name, baseUrl, authorization: { hosting, attested: true, proof, validUntil, maxPeakRps } } }` after the owner published the proof and attested the hosting. Hosting rules, DNS or file proof, protected origins, test windows and their refusals: [Targets, hosting and proof of control](capacitylab://knowledge/concepts/target-authorization). The guided version for a new repository is the [setup prompt](capacitylab://knowledge/workflows/setup).
4. Workload: `capacitylab.workloads.validate`, show the normalized Workload, then `capacitylab.workloads.create` with a fresh `idempotencyKey`; keep `workloadRevisionId`. Secrets are referenced by name only. Shape: [Workload](capacitylab://knowledge/concepts/workload).

## 2. Smoke ($0, confirm without asking)

1. `capacitylab.load_specs.resolve` with `{ workloadRevisionId, targetRevisionId, objective: { preset: { id: "smoke" } } }`. Show the Load Spec; `trafficSeconds` includes warm-up.
2. `capacitylab.load_plans.zones.list`, then `capacitylab.load_plans.compile` with `{ loadSpecId, zone, idempotencyKey }`. Compile charges nothing.
3. If `loadPlan.commercial.quote.amountMicros` is exactly 0: `capacitylab.runs.confirm` with `{ loadPlanId, digest, acceptedPrice: { currency: "USD", amountMicros: 0 }, idempotencyKey }` using the compiled values. Any other price: stop and ask (section 3).
4. `capacitylab.reports.status` with `{ loadRunId, waitSeconds: 45 }`; repeat until `phase` is `final`. The call returns early only once `final`, so never sleep between calls.
5. `capacitylab.reports.get` with `{ loadRunId }` once. The default summary view is enough to explain the verdict; pass `view: "full"` only for a measurement or assertion the summary lacks. A Smoke that did not attain blocks paid runs: fix the Workload or the service first.

## 3. Baseline (paid, explicit approval)

1. `capacitylab.objectives.presets.get` with the chosen preset (`load_test` checks one rate; `find_capacity` searches for the highest rate that meets the SLO). Agree the knobs with the member; never assume defaults or bounds. Presets: [Objectives and presets](capacitylab://knowledge/concepts/objective-presets).
2. Resolve and compile as in section 2. Show the Load Spec, the zone and its egress addresses, the Load Plan `digest` and the exact US-dollar Quote, and hand over `consoleUrl` for review.
3. Call `capacitylab.runs.confirm` only after the member approved that exact digest and price in this conversation. Silence, an earlier approval or a request to compile is not approval. An expired, stale or mismatched plan needs a new compile and a new approval. Refusals and refunds: [Load Plan](capacitylab://knowledge/concepts/load-plan), [Load Run](capacitylab://knowledge/concepts/load-run).
4. Follow and read it exactly as in section 2, steps 4 and 5.
5. Mark a good `load_test` Result as the baseline when the member asks: `capacitylab.results.baseline.set` with `{ seriesId, resultId }` (the Report's `seriesId` and `result.resultId`), adding `expectedBaselineResultId` only when the series already has one.

## 4. Results and fix

Explain the verdict from the summary: verdict, reasons, per-operation measurements, findings and evidence gaps; keep facts, hypotheses and limitations apart and give each hypothesis a falsifier. Deeper reading: [diagnose a Result](capacitylab://knowledge/workflows/diagnose-result) and, for `find_capacity`, [read a Find Capacity Result](capacitylab://knowledge/workflows/find-capacity). The member fixes the service; CapacityLab measures it as a black box.

## 5. Comparison

After the fix, run the same experiment again, or `no_regression` against the marked baseline, with a new compile and a new price approval. Its Report's `comparisons` hold the previous comparable and the baseline Result; compare only comparable experiments and name requirement changes separately: [compare with a baseline](capacitylab://knowledge/workflows/compare-to-baseline).
