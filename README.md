# Moor Whitepaper

The Moor whitepaper, *Moor: Constant-Leverage Fund Shares with Autonomous Rebalancing on Solana*, as LaTeX source and a built PDF.

> **Part of Moor** — leveraged funds on Solana.
> The protocol's home is moor-program (private). Try the app at [app.moor.fund](https://app.moor.fund) (devnet; mainnet is not deployed).
> Related: [mso](https://github.com/moor-protocol/mso) · [moor-android](https://github.com/moor-protocol/moor-android)

**Read it:** [moor-whitepaper.pdf](moor-whitepaper.pdf)

## Where it fits

This is the formal description of the protocol. It defines how a Moor fund prices its shares, how mint and redemption work, how the rebalancing controller sizes trades, and how a fund is wound down. The paper describes the on-chain program; the app and the other repos build on that program.

## Abstract

> We present Moor, a protocol for issuing fungible fund shares that track a constant target leverage on an underlying asset. Each fund is backed by a single collateralized debt position—a Kamino Lending "Multiply" obligation—owned by a program-derived vault. Share holders hold a pro-rata claim on the fund's net asset value (NAV), computed from oracle-priced collateral and debt; share issuance and redemption always use program-derived amounts, never caller-declared ones. Shares mint and redeem at NAV with fees paid in shares to a treasury, which leaves NAV per share unchanged up to integer rounding. Leverage is held inside a band by a permissionless controller with three modes—Routine, Ripcord, and WindDown—that derives the mode, trade size, slippage allowance, and post-trade bounds from on-chain state; the caller only routes a top-level flash loan and one decentralized-exchange swap. We formalize the NAV and share accounting, prove the NAV-per-share invariance of fees, derive the rebalancing sizing formulas and their fund-favoring rounding directions, and quantify the NAV cost of rebalancing and the inherent volatility drag of constant leverage. Every flow executes as a single atomic transaction with same-slot oracle freshness and flash-loan binding.

## What's inside

1. **Introduction** — Why fund-level leverage instead of per-user debt, and the paper's five contributions.
2. **System Model**
   - *Kamino obligations* — The lending primitive: one deposit slot and one borrow slot, priced by Scope oracles.
   - *Fund and share token* — The fund PDA, the vault PDA that owns the obligation, and the six-decimal SPL share token.
   - *Lifecycle* — The one-way move from Active to Closing and what each state allows.
   - *Atomic execution* — The single-transaction `begin → step → step → finalize` envelope, with finalize binding, session ordering, and same-slot freshness.
3. **NAV and Share Accounting**
   - *NAV, leverage, and the dust regime* — NAV = D − B, leverage in basis points, and the infinite-leverage sentinel for dust or underwater funds.
   - *Minting* — Shares issued against realized NAV growth, the mint fee, and the blended-leverage check.
   - *Bootstrap* — The first deposit at $1 per share, with 1000 base units locked in a dead account.
   - *Redemption* — Proportional debt and collateral legs, the redeem fee, and paying out exactly what the withdrawal freed.
   - *Redemption capacity and partial fills* — An integer-only cap on shares the reserve's spot liquidity can serve, with no withdrawal queue.
   - *Streaming fee* — The lazily accrued annual management fee, minted to the treasury.
   - *Invariants* — Proofs that mint and redemption preserve NAV per share, and the fund-favoring rounding directions.
4. **Rebalancing**
   - *Bands and mode selection* — How Routine, Ripcord, and WindDown are chosen from leverage, the closing flag, and debt.
   - *Recentering* — Moving part of the way back to target, controlled by the recentering speed.
   - *Trade sizing* — The USD trade value needed to reach the new target, capped by the maximum trade size.
   - *Delever amounts* — Debt repaid, collateral released to the cranker, and the Ripcord bounty.
   - *Relever amounts* — The floored borrow and the ceiled minimum collateral the swap must deliver.
   - *Cooldowns, slippage, and bounty* — Per-mode cooldown clocks, slippage caps, and bounty limits.
   - *Post-state bounds* — The leverage checks `rebalance_finalize` applies before accepting a trade.
   - *Cost of rebalancing* — The NAV cost of each mode and the inherent volatility drag of constant leverage.
5. **Wind-Down** — Closing a fund, delevering to 1.0×, and pro-rata exits through `redeem_closed` and `sweep_collateral`.
6. **Security Considerations** — Atomicity and sessions, oracle freshness, flash-loan binding, no balance at rest, administration limits, and residual risks.
7. **Related Work** — Comparison with ERC-4626 vaults, leveraged ETFs, perpetual futures, and isolated lending markets.
8. **Conclusion** — Summary of the design and what a holder has to trust.

## Building the PDF

The source compiles with pdfLaTeX (the committed PDF was built with pdfTeX from TeX Live 2026). It uses only standard packages: `geometry`, `fontenc`, `lmodern`, `amsmath`/`amssymb`/`amsthm`, `booktabs`, `microtype`, `hyperref`, and `tikz`. The bibliography is inline, so no BibTeX run is needed.

```sh
latexmk -pdf moor-whitepaper.tex
```

Without latexmk, run `pdflatex moor-whitepaper.tex` twice so the cross-references resolve. `latexmk -c` removes the auxiliary files afterwards.
