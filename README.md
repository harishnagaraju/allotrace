# AlloTrace
Multi-tenant telemetry cost attribution and OTel control plane

> **Know who's costing you.**

AlloTrace is an open-source multi-tenant telemetry cost attribution and OTel control plane. It tells you exactly which customer, tenant, or team is driving up your observability bill — in real time, without rewriting your infrastructure.

---

## The Problem

Your Datadog bill just spiked 40%. You have no idea which customer caused it.

Modern SaaS companies running microservices generate millions of trace spans per day. But standard observability tools — Datadog, Grafana, Honeycomb — tell you *how much* you're spending. They don't tell you *who* is causing it.

The result: engineering teams manually dig through YAML configs, homegrown scripts break under load, and finance has no answer for "which customers are unprofitable?"

---

## What AlloTrace Does

AlloTrace sits as an intelligent control plane over your existing [OpenTelemetry Collectors](https://opentelemetry.io/docs/collector/). It reads tenant metadata from trace headers, measures byte volume and span count per tenant in real time, and lets you dynamically adjust sampling rules — without restarting your infrastructure.

```
┌─────────────────────────────────────────────────────┐
│                  AlloTrace Control Plane             │
│                                                      │
│   Tenant Policy Engine   │   FinOps Cost Dashboard   │
│   Dynamic Config (OpAMP) │   Volume-by-Tenant View   │
└─────────────────────────────────────────────────────┘
                           │
              (OpAMP — zero restart config push)
                           ▼
┌─────────────────────────────────────────────────────┐
│              Your Existing OTel Collectors           │
│                                                      │
│   Gateway Collector (routing)   │   Agent Collectors  │
└─────────────────────────────────────────────────────┘
                           │
              ┌────────────┴────────────┐
              ▼                         ▼
   [ High-value traces ]        [ Dropped: noise ]
   [ Datadog / ClickHouse ]     [ Saves $$$ ]
```

---

## Core Features

### Tenant Volume Attribution
Reads standard trace context headers (`tenant.id`, `org_id`, `api_key`) and aggregates byte volume and span counts per tenant. Answers the question: *"Tenant X generated 42% of all trace volume this week."*

### Dynamic Sampling Control
Push new sampling rules to your entire OTel Collector fleet via [OpAMP](https://github.com/open-telemetry/opamp-spec) — no restarts, no Helm rollouts, no manual YAML edits. Change a noisy customer's log rate in seconds from the UI.

### Stateful Trace Correlation
Unlike simple OTTL scripts that evaluate spans in isolation, AlloTrace correlates across the full trace lifecycle. A fast `200 OK` span is not dropped if it belongs to a trace that ends in a `500 Error` — the full trace context is preserved.

### FinOps Cost Dashboard
Overlay your actual observability vendor rates against per-tenant volume to surface per-customer margin impact. Built for VPs of Engineering and Platform teams who need real numbers, not estimates.

---

## Who This Is For

AlloTrace is built for **B2B SaaS companies** that:

- Run microservices at scale with OpenTelemetry already instrumented
- Pay $15,000–$50,000/month or more in observability vendor costs (Datadog, Honeycomb, New Relic)
- Have a platform team of 2–5 engineers managing a large OTel Collector fleet
- Cannot tell which specific customer or tenant is driving their cloud bill

---

## Architecture

AlloTrace is built on open standards. No proprietary agents. No vendor lock-in.

| Layer | Technology |
|---|---|
| Agent protocol | [OpAMP](https://github.com/open-telemetry/opamp-spec) |
| Data collection | [OpenTelemetry Collector](https://opentelemetry.io/docs/collector/) |
| Trace protocol | OTLP (gRPC / HTTP) |
| Control plane | Go |
| State management | Embedded key-value store |

---

## Project Status

AlloTrace is in **active early development (Phase 0 — Discovery)**.

We are currently:
- Interviewing platform engineers and SREs about observability cost pain
- Validating the OTel routing and tenant attribution architecture
- Building the Phase 1 local PoC (Docker Compose demo)

**We are not yet accepting feature requests or PRs.** Watch this repo to follow progress.

---

## Roadmap

| Phase | Description | Status |
|---|---|---|
| Phase 0 | Customer discovery — 10 SRE/VP interviews | 🔄 In Progress |
| Phase 1 | Local PoC — OTel routing by tenant header via Docker Compose | ⏳ Upcoming |
| Phase 2 | OpAMP control plane — remote config push to OTel Collectors | ⏳ Upcoming |
| Phase 3 | Volume engine — per-tenant span/byte aggregation | ⏳ Upcoming |
| Phase 4 | Alpha UI — cost dashboard, sampling rule controls | ⏳ Upcoming |

---

## Get Involved

We are looking to speak with **Platform Engineers and SREs** at B2B SaaS companies.

If your team has ever asked *"which customer caused this Datadog spike?"* and had no good answer — we'd love 20 minutes of your time.

**Open an issue** tagged `[discovery]` or reach out directly via GitHub Discussions.

---

## License

AlloTrace core is licensed under the [Apache 2.0. License](LICENSE).

---

*Built on open standards. Designed for the teams who keep the pipes running.*
