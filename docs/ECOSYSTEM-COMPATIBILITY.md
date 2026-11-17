# SIP-01 ecosystem compatibility matrix

**Checked:** 2026-11-17  
**Canonical revision:** SIP-01 v1.3  
**Kind-39697 wire schema:** `v="1"`

This matrix distinguishes code that can build/parse optional v1.3 hints from a product that actually publishes capability announcements, indexes them, or invokes a discovered service. It records the implementation surface observed in repository source and tests; it is not a claim that every deployment or endpoint is healthy.

## Version rule

| Spec revision | Change | Kind-39697 `v` |
|---|---|---|
| v1 | Initial Web Index Observation | `"1"` |
| v1.1 | Extension registry and reproducible identity vectors | `"1"` |
| v1.2 | NIP dependency/reference audit | `"1"` |
| v1.3 | Optional `role`, repeatable `proto`, optional NIP-01 `a` pointer to kind 39698 | `"1"` |

Crawler and heartbeat versions are independent. Never put a product version such as `2` or `3` in the kind-39697 `v` tag.

## Repository behaviour

| Repository | Kind 39697 v1.3 hints | Kind 39698 | Actual workflow role |
|---|---|---|---|
| [sip-01-core](https://github.com/SIP-01/sip-01-core) | Reference builder/parser supports optional role/proto/a | Separate capability build/parse layer | Shared contracts and deterministic local capability matching; no automatic invocation |
| [0xSearchstr](https://github.com/SIP-01/0xSearchstr), [0xPresearchstr](https://github.com/SIP-01/0xPresearchstr), [Dsearch](https://github.com/SIP-01/Dsearch), [UNCAGED-ENGINE](https://github.com/SIP-01/UNCAGED-ENGINE) | Builders/parsers support optional role/proto/a | Not the capability directory authority | Search engines; use ORBSTR for provider discovery |
| [Crawlstr v1/v2](https://github.com/SIP-01/crawlstr) / [v2](https://github.com/SIP-01/crawlstr-v2) | Builder accepts optional role/proto hints | Do not publish kind 39698 | Browser crawlers; ordinary observations omit service hints |
| [Indexstr v1/v2](https://github.com/SIP-01/indexstr) / [v2](https://github.com/SIP-01/indexstr-v2) | Builder accepts optional role/proto hints | Do not publish kind 39698 | Indexers; kind 16919 is separate health/coordination metadata |
| [Crawlstr v3](https://github.com/SIP-01/Crawlstr-v3), [Indexstr v3](https://github.com/SIP-01/indexstr-v3) | Publish optional role/proto hints for submitted services | Publish kind-39698 service listings on service submission | Crawler/indexer with service-submission UI |
| [ORBSTR](https://github.com/SIP-01/Orbstr) | Consumes optional service hints | Capability builder/parser and directory | Role/capability filtering and local suggestions; approval still required |
| [orbstr-discover](https://github.com/SIP-01/orbstr-discover) | Reads public kind-39697 evidence separately | Reads and filters kind 39698 | CLI discovery only; never invokes a discovered endpoint |
| [UNCAGED Index Relay](https://github.com/SIP-01/UNCAGED-Index-Relay) | Accepts unknown optional tags | No kind-39698 directory; role/proto not dedicated OpenSearch fields | Validates/indexes kind-39697 observations |
| [SIP Booster Relay](https://github.com/SIP-01/SIP-Booster-Relay) | Accepts unknown optional tags and records notices | No kind-39698 directory; role/proto not structured fields | Serverless kind-39697 relay/search profile |
| [Crawlstr SIP Relay](https://github.com/SIP-01/Crawlstr-SIP-Relay) | Accepts unknown optional tags | No kind-39698 directory; role/proto not structured fields | Android validating index relay |
| [SIP Dashboard](https://github.com/SIP-01/SIP-dashboard) | Reads/displays kind 39697 | Does not execute discovered services | Monitoring and inspection UI |
| [sip-01-skill](https://github.com/SIP-01/sip-01-skill) | Documents v1.3 contract | Documents ORBSTR companion profile | Guides agents through approval-gated discovery/use |

Acceptance of an unknown optional tag is forward compatibility. It does not mean a relay extracts, indexes, filters or ranks that tag.

## Stable role taxonomy

Roles are provider classes, not professions or trust ratings:

| Role | Provider class |
|---|---|
| `agent` | Planner, coordinator or delegated executor |
| `tool` | Bounded callable operation |
| `model` | Inference, embedding, vision or reranking endpoint |
| `api` | Domain API or structured service |
| `compute` | CPU/GPU or other execution capacity |
| `dataset` | Data collection, corpus or index |
| `gateway` | Protocol, identity or network bridge |

Specialisations such as research, coding, design, debugging, security audit and code review belong in capability/topic tags, not new role values. `proto` identifies an advertised transport; it does not guarantee a common method schema.

## Safe agent workflow

`discover → verify → shortlist → approve → invoke → validate → settle → audit`

- Verify event id/signature, kind, schema, publisher, required fields and expiry.
- Filter by every required role, capability, protocol and price constraint.
- Require explicit approval for private-data disclosure, privileged actions and paid work. A listing or `price` tag grants no permission.
- Invoke only through a scoped adapter and trusted egress gateway. Listing-time URL parsing is not SSRF protection.
- Validate result schema and provenance; reconcile unknown outcomes before retrying non-idempotent actions.
- Verify settlement independently before marking paid work complete.
- Keep prompts, credentials, private inputs/outputs and workflow state out of public kind-39697 and kind-39698 events.

See the [ORBSTR workflow guide](https://github.com/SIP-01/Orbstr/blob/main/docs/AGENT-WORKFLOWS.md) and [capability profile](https://github.com/SIP-01/Orbstr/blob/main/spec/CAPABILITY.md).

## Test gates

Use each repository's own declared build/test command. The gates inspected in this pass include:

| Layer | Gate |
|---|---|
| TypeScript browser/search apps | `npm test` / `npm run test` as declared in each package |
| sip-01-core | `npm run test` (type-check, lint, Vitest, build) |
| ORBSTR app + package | ORBSTR quality workflow, isolated tests and package `npm test` |
| orbstr-discover | `npm test` (Node test suite) |
| UNCAGED Index Relay | `bun test`, type-check and lint |
| SIP Booster Relay | `npm test`, type-check and build |
| Crawlstr SIP Relay | Android Gradle unit-test and build workflows |

For kind 39697 changes, require canonical §13 vectors in addition to application tests. For kind 39698, test signature/schema validation, required singleton tags, expiry, withdrawal, capability filters, and that discovery never invokes endpoints. A documentation-only PR with no CI workflow must be recorded as such; do not infer a passing build from a green docs PR.

## Registry status

The official `nostr-protocol/nips` event-kind table and `nostr-protocol/registry-of-kinds` schema were checked on **2026-11-17**. Kind 39698 is absent from both and remains a draft companion allocation here. Re-check before publishing or assigning it; if an upstream NIP claims the kind, stop publication and move the allocation.
