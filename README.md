# Moor Whitepaper

> **Summary:** Formal specification, mathematical derivations, and economic proofs for *Moor: Constant-Leverage Fund Shares with Autonomous Rebalancing on Solana*.

[![Cluster: Devnet](https://img.shields.io/badge/Solana-Devnet-blue)](https://explorer.solana.com)
[![Document: PDF](https://img.shields.io/badge/Document-PDF-red.svg)](moor-whitepaper.pdf)

---

## 1. Overview

This repository contains the LaTeX source and compiled PDF for the Moor protocol whitepaper. The paper provides the formal academic and mathematical foundation for the protocol: fund pricing at Net Asset Value (NAV), proof of NAV-per-share invariance under fees, derivations for autonomous rebalancing trade sizing, and the Ripcord emergency defense model.

📄 **Read the Paper:** [moor-whitepaper.pdf](moor-whitepaper.pdf)

### Key Protocol Repositories

| Repository | Role | Description |
| :--- | :--- | :--- |
| [`moor-program`](https://github.com/moor-protocol/moor-program) | **Protocol Hub** | Core Anchor smart contracts, math libraries, and TypeScript SDK |
| [`moor-frontend`](https://github.com/moor-protocol/moor-frontend) | **Web Interface** | Trading app ([app.moor.fund](https://app.moor.fund)) and landing page |
| [`moor-mobile`](https://github.com/moor-protocol/moor-mobile) | **Mobile App** | Android native application (Expo / React Native) |
| [`moor-backend`](https://github.com/moor-protocol/moor-backend) | **API Service** | Sign-In With Solana, portfolio tracking, and trade indexing |
| [`moor-daemon`](https://github.com/moor-protocol/moor-daemon) | **Keeper Service** | Devnet oracle price pusher, NAV sampler, and rebalance bot |
| [`moor-architecture`](https://github.com/moor-protocol/moor-architecture) | **Architecture Guide** | Visual component diagrams and step-by-step transaction walkthroughs |

---

## 2. Abstract

We present Moor, a protocol for issuing fungible fund shares that track a constant target leverage on an underlying asset. Each fund is backed by a single collateralized debt position—a Kamino Lending "Multiply" obligation—owned by a program-derived vault. Share holders hold a pro-rata claim on the fund's net asset value (NAV), computed from oracle-priced collateral and debt; share issuance and redemption always use program-derived amounts, never caller-declared ones. 

Shares mint and redeem at NAV with fees paid in shares to a treasury, which leaves NAV per share unchanged up to integer rounding. Leverage is held inside a band by a permissionless controller with three modes—Routine, Ripcord, and WindDown—that derives the mode, trade size, slippage allowance, and post-trade bounds from on-chain state; the caller only routes a top-level flash loan and one decentralized-exchange swap. We formalize the NAV and share accounting, prove the NAV-per-share invariance of fees, derive the rebalancing sizing formulas and their fund-favoring rounding directions, and quantify the NAV cost of rebalancing and the inherent volatility drag of constant leverage. Every flow executes as a single atomic transaction with same-slot oracle freshness and flash-loan binding.

---

## 3. Building from Source

### Prerequisites
- pdfLaTeX (TeX Live 2024+ recommended)
- Standard packages: `geometry`, `fontenc`, `lmodern`, `amsmath`, `amssymb`, `amsthm`, `booktabs`, `microtype`, `hyperref`, `tikz`

### Compile Command
```sh
latexmk -pdf moor-whitepaper.tex
```

*Alternative (without `latexmk`):*
```sh
pdflatex moor-whitepaper.tex
pdflatex moor-whitepaper.tex  # Second pass to resolve cross-references
latexmk -c                    # Cleans auxiliary build files
```
