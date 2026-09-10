# Submission — Vi Tran (Full-Stack)

**Vi Tran** · videv93@gmail.com · applying for **Full-Stack Engineer**

All six problems. Each lives in its own folder with its own README, tests and run
instructions, and installs independently — there is no root install.

| # | Problem | Deliverable | Tests |
|---|---|---|---|
| 1 | [Three ways to sum to n](./problem1) | 3 JavaScript implementations | 65 |
| 2 | [Fancy Form](./problem2) | Currency swap app — Vite + React 19 + TS + Tailwind 4 | 101 |
| 3 | [Messy React](./problem3) | 24 findings + refactored component | 29 |
| 4 | [Three ways to sum to n](./problem4) | 3 TypeScript implementations + complexity analysis | 58 |
| 5 | [A Crude Server](./problem5) | Express 5 + TypeScript + Prisma CRUD service | 55 |
| 6 | [Architecture](./problem6) | Live scoreboard module specification + diagrams | — |

**308 tests, all passing.** Every folder runs with `npm install && npm test`.

- **Live demo (Problem 2):** https://s5tech.duelcode.online
- **Live API (Problem 5):** https://api.duelcode.online/docs
- **Repository with CI:** https://github.com/videv93/s5tech-code-challenge

---

## Quick tour

**Problems 1 & 4** — the same task in JavaScript and TypeScript, answered with
different sets of strategies so the two are not a copy-paste of each other.
Problem 4's complexity claims are backed by a runnable benchmark, and both
declare their assumptions about negative input up front.

**Problem 2** — a swap form built against the *live* price feed, including the
two things the real data actually does that a tidied-up fixture would hide:
the feed returns 36 entries for 32 currencies (duplicates with different prices,
so the most recent quote wins), and the icon repository's filenames disagree with
the feed's casing for the Stride tokens (`STATOM` vs `stATOM.svg`), which would
otherwise blank five tokens. No monetary amount is ever a JavaScript number.

**Problem 3** — the analysis is the deliverable. 24 findings, led by the fact
that **the component as written crashes on mount** (`lhsPriority` is undeclared),
and with that fixed renders the wrong wallets with blank amount columns (the
filter predicate is inverted, and the formatted array is computed then never
used). Each fix carries a test that fails against the original behaviour —
including the performance claim, which is usually left as prose.

**Problem 5** — swap orders, chosen to line up with Problem 2 so the two halves
describe the same domain — and **the swap form submits to it**, so a completed
swap is a row in this service and the receipt links to the record. Deployed at
https://api.duelcode.online/docs, behind a reverse proxy that terminates TLS,
with the container bound to loopback only and migrating itself on start. Tests run against a real database rather than a mocked
ORM. The OpenAPI document is generated from the same Zod schemas the routes
validate with, so the docs cannot drift from the implementation.

**Problem 6** — a module specification written for a team to implement, with four
diagrams. Its centre of gravity is the honest answer to "prevent malicious
users": a client-dispatched score increment cannot be trusted in principle, so
the design turns the question into *"is this the redemption of a specific,
server-issued, single-use permit that has not already been spent?"* — and the
residual risk is stated plainly rather than overclaimed.

---

## Running it

```bash
# Problems 1, 3, 4 — pure logic, no setup
(cd src/problem1 && npm install && npm test)
(cd src/problem3 && npm install && npm test)
(cd src/problem4 && npm install && npm test && npm run bench)

# Problem 2 — the swap app
(cd src/problem2 && npm install && npm test && npm run dev)

# Problem 5 — the API service
(cd src/problem5 && npm install && cp .env.example .env && npm run db:migrate && npm run db:seed && npm test && npm run dev)
# then open http://localhost:3000/docs

# Problem 6 — documentation
open src/problem6/README.md
```

---

## Notes on approach

**Assumptions are declared, not hidden.** Where a brief is ambiguous — what
`sum_to_n(-5)` should return, whether `amount <= 0` in Problem 3 was a typo,
which resource Problem 5 should expose — every README has an "Assumptions
declared" section stating the reading I took and why.

**Tests pin the reasoning, not just the happy path.** The assertions that matter
are the ones that would fail against a plausible wrong implementation: that a
price tick does not re-run a sort (Problem 3), that `MAX` yields exactly the
balance rather than a rounded one (Problem 2), that a settled order cannot be
reopened (Problem 5), that no row appears on two pages (Problem 5), that the
closed form reads `n` exactly once (Problem 4).

**Problem 2's skeleton files were replaced.** The brief says to feel free to
disregard them for this problem, so `script.js` and `style.css` are removed and
`index.html` is the Vite entry point for a fresh application.

**Stack:** TypeScript throughout, React 19, Vite, Tailwind 4, Radix, TanStack
Query, React Hook Form + Zod, Express 5, Prisma, Vitest, Testing Library, MSW.
