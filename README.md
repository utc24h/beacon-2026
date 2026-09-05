# beacon-2026

Emission anchor for the year.

This is an **anchor** repository: it holds timestamped cryptographic proofs, and its entire value
depends on **nothing that enters it ever being modified afterwards**.

> 🇪🇸 En espanol: [`README.es.md`](README.es.md)

## What is inside

Each turn leaves two signed files:

```
emissions/YYYY/MM/DD/HHMM-commitment.jws   the commitment, published BEFORE the input exists
emissions/YYYY/MM/DD/HHMM-emission.jws     the result, published after
```

When a turn cannot be completed, a `-failure.jws` takes the place of the `-emission`. It states why,
and reveals the seed so anyone can compute what the result would have been.

## How to check this is append-only

You do not have to take our word for it. Ask:

```bash
# The branch is protected — no credentials needed
curl -s https://api.github.com/repos/utc24h/beacon-2026/branches/main

# Nothing was modified after publication: additions only
git log --diff-filter=M --oneline -- emissions/
```

The second command **must return nothing**. If it returns anything, a file was touched after it was
signed.

## How to check the commitment came first

The proof is not our clock. It is that the commitment was already stored **before the drand round
it names came into existence**. Two questions, no credentials:

```bash
# 1. When did the provider store the commitment? This date is theirs, not ours.
curl -s "https://api.github.com/repos/utc24h/beacon-2026/commits?path=emissions/2026/01/01/0000-commitment.jws&per_page=1"

# 2. When was that drand round born? Nominal time = genesis_time + (round - 1) x period
curl -s https://api.drand.sh/8990e7a9aaed2ffed73dbd7092123d6f289930540d7651336225dc172e51b2ce/info
```

The first date must be **earlier** than the second. Replace the path with the turn you are looking
at; the round number is inside the file, in `drand_round`.

The `signed_at_utc` written inside the file is ours, and it proves nothing to you. Use it to
measure, never as evidence.

## Cloning on Windows

The `.gitattributes` at the root already handles this. Do not change `core.autocrlf` and do not
convert the files: `.jws` files are verified **byte for byte**, and a single converted line ending
breaks the signature.

---

Created by `provision/`, not by hand.
