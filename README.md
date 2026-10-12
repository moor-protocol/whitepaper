# Moor Whitepaper

*Moor: Constant-Leverage Fund Shares with Autonomous Rebalancing on Solana*, as LaTeX source and a built PDF.

> **Part of Moor** — leveraged funds on Solana.
> The protocol's home is moor-program (private). Try the app at [app.moor.fund](https://app.moor.fund) (devnet; mainnet is not deployed).
> Related: [mso](https://github.com/moor-protocol/mso) · [moor-android](https://github.com/moor-protocol/moor-android)

**Read it:** [moor-whitepaper.pdf](moor-whitepaper.pdf)

## Where it fits

The formal description of the on-chain program: how a fund prices its shares, how mint and redemption work, how the rebalancing controller sizes trades, and how a fund winds down. The app and the other repos build on that program.

## Building the PDF

```sh
latexmk -pdf moor-whitepaper.tex
```

Without latexmk, run `pdflatex moor-whitepaper.tex` twice so cross-references resolve; `latexmk -c` removes the auxiliary files. Needs pdfLaTeX (the committed PDF was built with pdfTeX from TeX Live 2026) and only standard packages: `geometry`, `fontenc`, `lmodern`, `amsmath`/`amssymb`/`amsthm`, `booktabs`, `microtype`, `hyperref`, `tikz`. The bibliography is inline, so no BibTeX run is needed.

## Abstract

> We present Moor, a protocol for issuing fungible fund shares that track a constant target leverage on an underlying asset. Each fund is backed by a single collateralized debt position—a Kamino Lending "Multiply" obligation—owned by a program-derived vault. Share holders hold a pro-rata claim on the fund's net asset value (NAV), computed from oracle-priced collateral and debt; share issuance and redemption always use program-derived amounts, never caller-declared ones. Shares mint and redeem at NAV with fees paid in shares to a treasury, which leaves NAV per share unchanged up to integer rounding. Leverage is held inside a band by a permissionless controller with three modes—Routine, Ripcord, and WindDown—that derives the mode, trade size, slippage allowance, and post-trade bounds from on-chain state; the caller only routes a top-level flash loan and one decentralized-exchange swap. We formalize the NAV and share accounting, prove the NAV-per-share invariance of fees, derive the rebalancing sizing formulas and their fund-favoring rounding directions, and quantify the NAV cost of rebalancing and the inherent volatility drag of constant leverage. Every flow executes as a single atomic transaction with same-slot oracle freshness and flash-loan binding.

## Contents

1. **Introduction** — Fund-level leverage instead of per-user debt; the five contributions.
2. **System Model** — Kamino obligations (one deposit and one borrow slot, Scope-priced); the fund PDA, vault PDA and six-decimal SPL share token; the one-way Active → Closing lifecycle; the single-transaction `begin → step → step → finalize` envelope with finalize binding, session ordering and same-slot freshness.
3. **NAV and Share Accounting** — NAV = D − B, leverage in basis points and the infinite-leverage sentinel; minting against realized NAV growth, the mint fee and blended-leverage check; bootstrap at $1 per share with 1000 base units locked in a dead account; proportional redemption and the redeem fee; integer-only redemption capacity with partial fills and no withdrawal queue; the lazily accrued streaming fee; invariance proofs and fund-favoring rounding.
4. **Rebalancing** — Band-based selection of Routine, Ripcord and WindDown; recentering speed; trade sizing capped by maximum trade size; delever and relever amounts, the Ripcord bounty; per-mode cooldowns, slippage caps and bounty limits; the `rebalance_finalize` post-state bounds; the NAV cost of rebalancing and volatility drag.
5. **Wind-Down** — Closing a fund, delevering to 1.0×, pro-rata exits through `redeem_closed` and `sweep_collateral`.
6. **Security Considerations** — Atomicity and sessions, oracle freshness, flash-loan binding, no balance at rest, administration limits, residual risks.
7. **Related Work** — ERC-4626 vaults, leveraged ETFs, perpetual futures, isolated lending markets.
8. **Conclusion** — The design and what a holder has to trust.
