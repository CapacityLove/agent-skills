# CapacityLab Agent Skills

[CapacityLab](https://capacitylab.dev) load tests web services and HTTP APIs. This repository connects your
coding agent to the CapacityLab MCP server and adds skills that take it the shortest way: a free
Smoke first, a paid test only after you approve its exact price.

Generated from the CapacityLab knowledge base; do not edit by hand.

## Claude Code

The plugin adds the MCP server and the skills:

```text
/plugin marketplace add CapacityLove/agent-skills
/plugin install capacitylab@capacitylab
```

Then run `/mcp`, choose `capacitylab` and Authenticate.

## Codex CLI

```sh
codex mcp add capacitylab --url https://preprod-api.capacitylab.dev/mcp
codex mcp login capacitylab
npx skills add CapacityLove/agent-skills -a codex
```

## Other agents

`npx skills add CapacityLove/agent-skills` installs the skills for any agent the
[skills CLI](https://github.com/vercel-labs/skills) supports; connect the MCP server
`https://preprod-api.capacitylab.dev/mcp` as your agent documents.

## Skills

- **capacitylab-first-run**: Load test a web service or HTTP API with CapacityLab: a free $0 Smoke through capacitylab.quickstart in four calls (zones.list, quickstart, reports.status, reports.get), then a paid baseline only after the user approves the exact price. Use when the user asks to load test, stress test, smoke test or benchmark their API, or how much traffic it handles, and the capacitylab MCP tools are connected.
- **capacitylab-setup**: Set up a repository's service in CapacityLab from scratch: read the code, connect the application as a Target with proof of control, name secrets, define the Workload, run the $0 Smoke and prepare the first paid test. Use when the user asks to set up, onboard or connect CapacityLab load testing for this repository.
- **capacitylab-find-capacity**: Find the maximum sustainable request rate of a service with CapacityLab's find_capacity preset and read its Result: the checkpoint staircase, bracketed, at_least or below_start outcomes and the next range. Use when the user asks for the capacity limit, max RPS, breaking point or how far their API scales.
- **capacitylab-diagnose-result**: Read and explain one CapacityLab Report or Result without changing anything: verdict, how it was evaluated, per-operation measurements, findings, hypotheses with falsifiers and limitations. Use when the user asks why a load test failed or passed, or to read, summarize or diagnose a CapacityLab run.
- **capacitylab-compare-results**: Compare a CapacityLab Result with its baseline or another comparable Result: comparability reasons, requirement changes, measured deltas, regression or improvement, and marking a Baseline Result. Use when the user asks to compare load test runs, check for a performance regression or verify that a fix helped.
