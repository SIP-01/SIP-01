# Master Security Audit & Freshness Review — NostrDanish + SIP-01
**Date:** 2026-10-20 · **Scope:** 71 repos · **Delivery:** per-repo `AUDIT-2026-10.md` on branch `audit/2026-10`

## Executive Summary
- No committed live secrets anywhere across both accounts (nsec, WAX keys, TG tokens, Brave/OpenAI/xAI/pkm_ keys all clean).
- 5 CRITICAL findings — all auth-bypass / default-credential class, none involving leaked secrets.
- 11 HIGH findings — unauthenticated admin surfaces + stale security-relevant forks.
- GHAS secret scanning + push protection enabled on 0 repos — #1 systemic gap.

## CRITICAL
1. **SIP-01/0xSearchstr** — `MEILI_MASTER_KEY` defaults to `change-me` with port 7700 host-exposed.
2. **SIP-01/0xSearchstr** — `ABUSE_ADMIN_TOKEN` defaults to `change-me`, enabling admin API (list/delete index docs).
3. **SIP-01/0xPresearchstr** — same `change-me` compose fallbacks (PR #2 hardened code, not compose).
4. **NostrDanish/nosflare** — `?notify-zap&npub=` marks any pubkey paid with no zap verification (paywall bypass; inherited from upstream; SIP-Booster-Relay already carries the fix — port it back + report upstream).
5. **NostrDanish/Botstr** — entire `/api/bots` unauthenticated; `/api/bots/:id/export` returns plaintext bot nsec.

## HIGH
- Crawlstr-SIP-Relay: 3 releases behind Citrine, missing v3.1.1 NIP-42 AUTH-bypass fix → rebase to v3.2.0.
- Dsearch + Savedd(stale copy) + backup_Searchstr: abuse-api CORS `*` on Bearer admin endpoints.
- 0xsigner + Leef-signer: `publicAuth:"nip98"` unimplemented → selecting it = zero auth.
- 0xbridge: custodial plaintext privkeys in DB; open `/publish`.
- 0xflare: DM Vault reads not scoped to authed pubkey.
- 0xVector: release CI runs unverified glslc.exe from personal server.
- 0xphone-companion: all gateway endpoints unauthenticated incl. `/jobs/{id}/approve` — CRITICAL if tunnel-exposed.
- bitchat/bitchat-android: 76/115 days behind upstream, unmerged security branches.
- Cider: 4.5yr-stale Electron — archive.

## Ecosystem drift (NIPs, Jun–Oct 2026)
- NIP-01 empty-filter semantics changed Oct 17+20 (this week) — verify crawlers/relays never send empty lists.
- NIP-78 requires AUTH for kind 30078 — Savedd ad staking uses 30078 → verify AUTH flow.
- NIP-46 logout + client metadata + timeout fix → implement in 0xsigner/Leef-signer.
- NIP-44 >64KB payloads; NIP-55 simplified; NIP-86 relay mgmt expanded; NIP-67 EOSE completeness hint; NIP-50 churn (autocomplete reverted); NIP-A3 payto kind 10133; NIP-5A site snapshots + aggregate hash (candidate SIP-01 bridge).
- nosflare upstream dormant at v7.9.45 (Jun 2026) — no v8 exists; we are effectively the maintainer.

## Top actions
1. Fix `change-me` defaults (0xSearchstr, 0xPresearchstr). 2. Close nosflare notify-zap. 3. Lock Botstr API. 4. Rebase Crawlstr-SIP-Relay. 5. Enable GHAS + push protection everywhere. 6. Fix abuse-api CORS. 7. Implement/remove NIP-98 publicAuth. 8. Auth-gate 0xphone-companion. 9. Sync-or-archive bitchat forks; archive Cider/Nostr-trail/runelite/crawlstr-v1/indexstr-v1/backup_Searchstr.

Full details in each repo's AUDIT-2026-10.md on this same branch name.