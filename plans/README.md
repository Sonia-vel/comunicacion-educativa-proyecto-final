# Animation plans

| # | Plan | Severity | Status |
| --- | --- | --- | --- |
| 001 | [Animate the quiz feedback entrance](001-quiz-feedback-entrance.md) | MEDIUM | DONE |

## Execution order

1. **001**: no dependencies. It adds the shared `--ease-out` token to `:root`
   and relaxes the reduced-motion rule at `index.html:53` so it allows opacity
   fades. Any later motion plan (completion badge, copy toast, delete
   confirmation, press feedback) should reuse that token and assume 001 has
   already been applied.
