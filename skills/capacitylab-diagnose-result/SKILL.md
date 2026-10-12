---
name: capacitylab-diagnose-result
description: "Read and explain one CapacityLab Report or Result without changing anything: verdict, how it was evaluated, per-operation measurements, findings, hypotheses with falsifiers and limitations. Use when the user asks why a load test failed or passed, or to read, summarize or diagnose a CapacityLab run."
metadata:
  source: capacitylab://knowledge/workflows/diagnose-result
---

Use the tools of the CapacityLab MCP server (`capacitylab`). If they are not available, ask the user to connect it from the AI agents page of CapacityLab Console; do not call the API another way. This skill is generated from the resource capacitylab://knowledge/workflows/diagnose-result; when the server serves a newer text, follow that.

# Diagnose a CapacityLab Result

CapacityLab measures the Target as a black box and returns verifiable facts and their limits. The useful conclusion is drawn by the member's own AI agent; CapacityLab promises no code recommendation, no root-cause diagnosis and no confirmation that a fix worked.

1. Read the Result with `capacitylab.results.get` by `resultId`. If it answers `outcome: "result_pending"`, the Load Run has not been analysed yet: read the Load Run with `capacitylab.load_runs.get` and wait; never diagnose a pending Result.
2. Record as observed facts: the `verdict`, the `inconclusiveCause` when inconclusive, the `runOutcome`, every `measurement`, every `assertion` with its applied threshold, source and observed value, the `findings` and the `evidenceGaps`. Read `howItWasEvaluated` to say which rule, rule and measurement digests and thresholds produced the verdict; never re-evaluate it against today's catalog.
3. Read the Load Run with `capacitylab.load_runs.get` for how it ended (`endReason`, `failureAttribution`, `faults`, `stopRequests`, `cleanup`) and the Load Spec with `capacitylab.load_specs.get` for what was asked and where each value came from.
4. Report three separate sections: observed facts, hypotheses and limitations. A missing signal is an evidence gap, never an observed cause. Every latency is the k6 request duration seen from the generator's zone (sending + waiting + receiving, without DNS, connect or TLS), not a round-trip time and not server time.
5. Hypotheses about the Target are the member's to test. Correlate requests in the member's own APM, logs or traces by the `x-capacitylab-run-id` header the generators send. For every hypothesis cite the Result field that supports it and state one concrete falsifier. Without the member's metrics, logs or traces the root cause stays unproven; say so.
6. An inconclusive Result names one cause: An inconclusive Result names one cause: CapacityLab before the member before the Target. A CapacityLab failure attribution always gives CapacityLab, a member stop gives the member unless CapacityLab lost evidence, and an unreachable Target gives the Target. Refunds follow only the closed list: A charged Load Run is refunded in full, once, to the same Account when the generator failed to start, a deploy interrupted it, an operator stopped it, or it ran with intact data but CapacityLab could not produce a verdict. When the generator was unhealthy or run data was lost, it is refunded in full only if the Load Run ended without a verdict because of CapacityLab; when the affected seconds were left out and the verdict stands, the Result is kept and nothing is refunded for them. When a stop condition ends a Load Run early and no full refund applies, the unused traffic is refunded instead: the price of the minutes and request blocks the plan had not yet started when the stop condition triggered, scaled to the charged price; the launch fee is kept. A member's own stop and a target failure are not refunded. Name `refundFacts` as recorded; never promise a refund outside that list. A Load Run a stop condition ended early shows its unused-traffic refund as `billing.status` `partially_refunded` with `amountMicros` in `capacitylab.load_runs.get`.
7. Do not resolve, compile, confirm, retry or stop anything as part of diagnosis. A new Load Run needs a separate member request, a new Load Plan and the member's explicit approval of that exact plan digest and US-dollar price before `capacitylab.runs.confirm`.

Diagnosis is client-side analysis over immutable reads. It creates no record and changes no CapacityLab state.
