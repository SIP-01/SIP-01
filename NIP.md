# SIP-01 — Search Index Protocol (kind 39697)

This project is the **canonical documentation and exploration site** for
SIP-01, the Search Index Protocol. The protocol itself defines one custom
event kind:

| Schema | Kind | Type | Defined in |
|--------|------|------|------------|
| Web Index Observation (SIP-01) | **39697** | addressable | [`public/spec/SIP-01.md`](public/spec/SIP-01.md) |
| Crawler node heartbeat (indexstr + Crawlstr) | **16919** | replaceable | read-only consumer — schema: [indexstr `src/crawler/heartbeat.ts`](https://github.com/NostrDanish/indexstr/blob/main/src/crawler/heartbeat.ts) (Crawlstr emits the same shape; the `source` tag distinguishes them) |

This site reads both kinds for the public `/dashboard` (index statistics +
crawler network health). It publishes neither.

## Summary

One addressable event per `(indexer pubkey, normalized URL)` — an indexer's
signed statement: *"I observed this web page at this time, and here is its
public metadata."*

- `d` = `"widx:" + sha256(normalized_url)[0:32]` — deterministic URL identity,
  identical across all independent indexers (group-by-`d` / count-`pubkey`
  gives the independent-observation count).
- `u` = canonical URL, normalized per SIP-01 §7 (www-stripping, tracking-param
  removal, query-param sorting, trailing-slash removal).
- `x` = `sha256(title + "\n" + description)` — content-agreement hash.
- `v` = `"1"` — schema version.
- `content` = `{ title, description?, image? }` JSON.
- Modularity: optional core tags (`t`, `l`, `published`, `source`) plus
  registered extension tags (`type`, `platform`, `category`, `network`,
  `country`, `mime`, `role`, repeatable `proto`) with an `x-` prefix rule
  for experiments. The v1.3 `role`/`proto` tags are optional; older readers
  may ignore them without changing kind-39697 interpretation.

All events carry an `alt` tag with a human-readable description
("Web index observation: …").


## Optional capability companion

ORBSTR defines a separate, draft capability-announcement profile at kind
**39698**. It is not a second kind in the SIP-01 web-observation schema and
does not change kind 39697 or its `v="1"` wire schema. A kind-39697 service
page may point to a kind-39698 listing with a standard NIP-01 `a` coordinate;
role/proto hints on the web observation itself remain optional. Capability
announcements describe provider claims; task execution and payment are out
of scope. See [the companion profile](docs/CAPABILITY-DISCOVERY.md).


## This site

- **/spec** — the full submission-ready specification (also downloadable as
  raw markdown from `/spec/SIP-01.md`), including the §20.1 NIP dependency
  table: every referenced NIP, what SIP-01 uses from it, whether the reference
  is normative, and what breaks when it's unsupported.
- **/registry** — the core + extension tag registry and how to extend it.
- **/query** — query reference (NIP-01 filters, NIP-50 operators, NIP-11
  capability advertisement, NIP-77 federation).
- **/audit** — the cross-implementation fact-check / bug-check report.
- **/explorer** — live kind 39697 explorer with a client-side validator and a
  URL → `d`-tag calculator implementing SIP-01 §7/§8 byte-compatibly.
- **/settings** — the app relay list: every relay the dashboard and explorer
  read from. Fully editable (add/remove/disable) with a reset button that
  restores the shipped defaults. Stored locally, never published to Nostr,
  and deliberately NOT synced from the visitor's NIP-65 list.

The default read set is the union of the known crawler publish pools
(Crawlstr + indexstr), the NIP-50 search relays, and a general fallback —
kind 39697 lives on any relay, and the dashboard's per-relay coverage panel
shows exactly where each window of data came from.

## Reference implementations (cross-checked by the audit)

- [0xSearchstr](https://github.com/NostrDanish/0xSearchstr) — search engine + reference client impl
- [0xPresearchstr](https://github.com/NostrDanish/0xPresearchstr) — search engine (clearnet + tor/i2p)
- [UNCAGED-ENGINE](https://github.com/NostrDanish/UNCAGED-ENGINE) — engine template / reference impl
- [Crwalstr](https://github.com/NostrDanish/Crwalstr) — browser crawler (pure SIP-01 publisher)
- [UNCAGED-Index-Relay](https://github.com/NostrDanish/UNCAGED-Index-Relay) — validating index relay (NIP-50/NIP-77)

This site only **reads** kind 39697 from public relays (plus a local
validator/calculator). It publishes nothing.
