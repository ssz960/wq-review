# KNOW-20260718-003 Correlation Practice Review

## Scope

This review contains sanitized candidate knowledge about correlation retrieval and research-system patterns. It does not contain raw forum content, complete code, credentials, cookies, tokens, account identifiers, Alpha expressions, submitted pools, PnL, request headers, endpoints, or real WQ results.

## Directory

- `wqc/practice_cards.jsonl`: 10 candidate cards for result state, shared rate limits, cache/de-duplication, layered-screening hypothesis, PP/Prod diagnostic ambiguity, parent/child state, and safe external architecture patterns.
- `wqc/correlation_pipeline_evidence.csv`: six evidence-matrix rows. Every threshold is pending local validation.
- `wqc/external_system_patterns.md`: state-machine and non-adoption guidance only.

## Collection Summary

- Sources: 11 (9 forum, 2 public code repositories).
- Code-bearing sources: 6; code is non-adoptable and not reproduced.
- Layered-screening support: 1 explicitly unsupported experience source.
- Counterexample/context sources: 2.
- Evidence labels used in cards: 3 `FORUM_STRONG`, 6 `FORUM_EXPERIENCE`, 1 `INFERENCE`.

## Conflicts And Open Questions

- `Self < PP < Prod` and PP-first filtering are a hypothesis only, never an admission rule.
- A PP path may display a Prod-looking failure; displayed text is not a root-cause taxonomy.
- Result freshness, server rate-limit scope, not-ready semantics, parent/child lifecycle, and PP/regular fallback behavior need current authoritative confirmation.

## Pages Requiring Human Opening

- `33601626915351`: direct Prod retrieval code, retained as non-adoptable evidence only.
- `32223192365207`: PP/Prod-looking error semantics.
- `33089748320791`: unsupported ordering hypothesis.
- `32970978828951`: direct retrieval/storage code, architecture only.
- `39118594803095`: scoped experiment and tradeoff discussion.
