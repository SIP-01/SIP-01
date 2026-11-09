# Master Security Audit v2 — NostrDanish + SIP-01 (2026-10-21 re-run)
**Scope:** 71 repos · per-repo AUDIT-2026-10-21.md on this branch name in every repo

## Delta vs 2026-10-20
**RESOLVED:** bitchat + bitchat-android forks synced to upstream HEAD — 2 HIGH freshness findings closed.

**NEW:**
- HIGH Leef-signer: providers.js on main does not parse (orphaned duplicate block ~L476-502) — generator.ts inlines it into every generated worker → newly deployed gateways ship broken JS. Fix + port generator.test.ts + CI syntax gate.
- MEDIUM Dsearch: committed live ppq.ai key sk-VPVV… (aiConfig.ts:64, documented public-by-design) — confirm provider caps or rotate.
- MEDIUM leef-trader: new api/jev.ts is a second unauthenticated quota-spend endpoint.
- Crawlstr-SIP-Relay HIGH now code-verified: validateAuthEvent never calls checkSignature() → AUTH as any pubkey.
- SIP-Booster: bundle-hash enforcement shipped but DEPLOY_BUNDLE_SHA256="" still skips it.
- UNCAGED-Index-Relay: no per-IP REQ rate limit confirmed in src/relay.ts — port SIP-Booster token bucket.

## Standing CRITICALs (all UNFIXED, re-verified today)
1. 0xSearchstr — MEILI_MASTER_KEY change-me + port 7700 exposed
2. 0xSearchstr — ABUSE_ADMIN_TOKEN change-me enables admin API
3. 0xPresearchstr — same compose defaults
4. nosflare — ?notify-zap&npub= paywall bypass (upstream v7.9.45 still latest)
5. Botstr — /api/bots/* unauthenticated; export returns plaintext nsec

## Standing HIGHs (UNFIXED)
Crawlstr-SIP-Relay NIP-42 bypass · Dsearch abuse-api CORS*+injection · Savedd stale abuse-api · 0xsigner+Leef-signer NIP-98 no-op · 0xbridge plaintext keys · 0xflare DM scoping · 0xVector unverified glslc.exe · 0xphone-companion open gateway (release-blocker) · Cider archive

## Secrets posture
No live secrets committed (except documented ppq.ai community key + PotMonsters Firebase key, restrictions unverified). GHAS secret scanning still 0/71 — top systemic fix.

## Frozen-repo integrity
Zero unexpected commits across all archive candidates. Opengrantindex bot cadence normal. hermes-0xdevbot-skill security PR reviewed: net-positive.

Full v2 report: MASTER-AUDIT-2026-10-21.md (this file) + Drive folder Kimi/repo audits.