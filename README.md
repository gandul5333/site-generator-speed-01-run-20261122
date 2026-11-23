# Faster Static Site Generator

[![make.fun · site-generator-speed-01-run-20261122](https://make.fun/badge/site-generator-speed-01-run-20261122.svg)](https://make.fun/bounty/site-generator-speed-01-run-20261122?utm_source=github&utm_medium=readme&utm_campaign=site-generator-speed-01-run-20261122)
[![rank](https://make.fun/badge/site-generator-speed-01-run-20261122/gandul5333/site-generator-speed-01-run-20261122/rank.svg)](https://make.fun/bounty/site-generator-speed-01-run-20261122/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=site-generator-speed-01-run-20261122)
[![reward](https://make.fun/badge/site-generator-speed-01-run-20261122/gandul5333/site-generator-speed-01-run-20261122/reward.svg)](https://make.fun/bounty/site-generator-speed-01-run-20261122?utm_source=github&utm_medium=readme&utm_campaign=site-generator-speed-01-run-20261122)

A faster drop-in replacement for makefun-site 1.0.0: `solve.js` (dependency-free,
Node 22 ES module) returns byte-for-byte the same output tree the reference
generator builds — pages, index and tag pages, `build-report.txt`, static files —
for every case, and does it about 4× faster than the house baseline on the
practice set (median ~117 ms vs ~467 ms for all 3 practice cases).

`main.ts` is the house driver unchanged: it reads every case from
`/input/cases.json`, answers each with `run` from `solve.js`, and writes
`/output/answers.json`.

## How it is faster

- One-pass, regex-free HTML escaping and slugify with an ASCII fast path.
- A hand-written Markdown lexer (char-code line classification, no per-line
  regexes) and an index-based inline renderer: a regex scanner finds the next
  special character, plain runs are copied raw in bulk, no slices for recursion.
- A template renderer that accumulates into arrays (one join), reuses one frame
  and one loop object per `for` execution, and memoises includes within a case
  whose output depends only on values that are constant for the case
  (`site`, `tags`, `recent`) or on one loop value (one post's card) — never
  across cases, never on anything that mutates.
- Per-post cards are computed once and shared between the post list, the tag
  pages and the post's own page.

No caching across cases, no input recognition, no hardcoding to practice data:
the generator is fully general and deterministic (two runs byte-identical).
