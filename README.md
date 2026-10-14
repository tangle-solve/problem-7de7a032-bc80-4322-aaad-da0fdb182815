<!-- solve-problem:7de7a032-bc80-4322-aaad-da0fdb182815 -->
# Collusion-resistant open redemption

A [Tangle Solve problem](https://solve.tangle.tools/problems/7de7a032-bc80-4322-aaad-da0fdb182815).

## What is the problem?

**Setting.** A stablecoin protocol maintains a peg via open redemption: any holder can redeem tokens for the underlying reserve asset (e.g., $1 of collateral per token), possibly with fees, rate limits, or prioritization rules. Separately, a secondary market (CEX/DEX) trades the token at a market price $p_t$. Deviations of $p_t$ from peg are corrected by arbitrageurs who buy below peg and redeem (or mint and sell above peg).

**Problem.** Design the redemption access mechanism — who may redeem, in what order, at what fee, subject to what per-address or identity-based limits — so that the mechanism is *collusion-resistant*: no coalition of participants can (a) manipulate the secondary market price $p_t$ (e.g., wash trading, cornering liquidity during stress) while (b) withholding or monopolizing arbitrage/redemption capacity, in order to extract rents or induce/deepen a run beyond what fundamentals justify.

**Formalization.** Model redemption as a game: at each period, holders choose to redeem or hold; a coalition $C$ of size/mass $\leq \alpha$ observes the state (reserve ratio $r_t$, secondary price $p_t$, queue) and chooses a joint strategy (market orders, redemption requests, withholding). The mechanism $M$ maps redemption requests to allocations. Desired: $M$ such that (i) peg-restoring arbitrage is performed competitively (no coalition earns above-competitive rents), and (ii) the run equilibrium region (in $(r_t, p_t)$ space) is not expanded by strategic withholding. A precise statement requires committing to a model — e.g., a Diamond–Dybvig-style coordination game augmented with a redemption queue and secondary market, with coalitional deviations (strong equilibrium or coalition-proof equilibrium as the solution concept). Ambiguity: the census record does not fix the threat model (Sybil identities? observed coalition formation? T-period vs. infinite horizon?); the solver must state these choices.

## Why does it matter?

Stablecoins with concentrated arbitrageurs (per Ma–Zeng–Zhang 2026) face a stability/run-risk tradeoff: concentration stabilizes the peg in normal times but creates single points of failure and strategic withholding risk under stress. A collusion-resistant redemption design would tell protocols and regulators exactly who should have direct redemption access and on what terms — directly relevant to the analogous policy debate on ETF authorized-participant concentration and to bank-run theory. Failure to solve this leaves major stablecoins vulnerable to engineered depegs by coordinated actors during market stress.

## What's already known?

- **Ma, Zeng and Zhang (2026, *Review of Financial Studies*)**: documents that arbitrage in stablecoins is highly concentrated in a few large arbitrageurs; establishes the tradeoff — concentrated arbitrageurs restore the peg efficiently in normal periods but their strategic behavior (withholding arbitrage, front-running redemptions) amplifies run risk when reserves deteriorate. This is the empirical/positive benchmark; the mechanism-design response is open.
- **Bank-run literature**: Diamond–Dybvig (1983) coordination-game runs; redemption prioritization (e.g., first-come-first-served vs. pro-rata) affects run equilibria. (verify exact pro-rata results)
- **ETF literature**: concentration of authorized participants as a systemic risk factor — analogy, not formalized for on-chain redemption.
- **Approaches that fall short**: naive pro-rata redemption is Sybil-vulnerable; FCFS queues reward latency races and MEV-style extraction; fee-based throttling (e.g., dynamic redemption fees) addresses run velocity but not coalition-driven price manipulation paired with withholding. No published mechanism combines coalitional incentive compatibility with open access. (verify — this is the census's claim, treat as the gap to attack.)

## What would count as solving it?

Any of the following, stated in a committed formal model:

1. **Upper bound / construction**: A redemption mechanism $M$ (e.g., identity-weighted pro-rata queues, bonded arbitrageur roles, stake-slashing withdrawal rights) with a proof that in the associated game, no coalition up to size $\alpha$ can profit from joint price manipulation + arbitrage withholding, and the run region is weakly smaller than under open FCFS redemption.
2. **Lower bound / impossibility**: A proof that no open-access mechanism (with Sybil-identities and anonymous participation) simultaneously achieves competitive arbitrage and coalition-proof peg stability — thereby justifying concentrated bonded intermediaries.
3. **Tradeoff characterization**: A tight bound quantifying the minimum concentration/bonding required to deter coalitional manipulation as a function of reserve ratio and stress severity.

An experiment demonstrating coalition-profitability or its absence on empirical stablecoin stress data (May 2022 UST/Terra, March 2023 USDC depeg) would support, not substitute for, the theory.

## How to start

1. **Read Ma–Zeng–Zhang (2026) first** — especially its model section and empirical measures of arbitrage concentration; your model must nest theirs as a special case so your mechanism's gains are comparable.
2. **Commit to a threat model early**: decide whether participants are Sybil-anonymous (this kills identity-based limits and pushes you toward bonding/staking) and define the coalition solution concept (coalition-proof Nash or strong equilibrium).
3. **Start from Diamond–Dydvig with a queue**: augment the classic run game with (i) a secondary market price set by coalition trades and (ii) a redemption allocation rule; characterize run equilibria under FCFS vs. pro-rata as the warm-up, then add coalitions.
4. **Main obstacle**: any "open" access rule is Sybil-fragmentable, so coalition size limits are unenforceable without costly identity or bonds — the natural escape is bonded redemption rights with slashing, and the analysis becomes a bonding-vs-liquidity tradeoff.

## Key references

- **Ma, Y., Zeng, M., & Zhang, A. (2026). "Stablecoin Runs and the Centralization of Arbitrage." *Review of Financial Studies*. DOI:10.1093/rfs/hhag072.** Empirically documents concentration of stablecoin arbitrage and models the tradeoff between peg stability and run risk induced by concentrated arbitrageurs; the direct motivation for this problem. https://doi.org/10.1093/rfs/hhag072
- **Diamond, D. & Dybvig, P. (1983). "Bank Runs, Deposit Insurance, and Liquidity." *JPE* 91(3).** Foundational coordination-game model of runs against demandable deposits; the base run model to augment with secondary markets and coalitions. https://doi.org/10.1086/261155
- **Related (not paper-specific)**: literature on ETF authorized-participant concentration and systemic risk, and on MEV/latency races in on-chain ordering — search these for the price-manipulation and queue-priority primitives; no single canonical citation. (verify)

## Warnings

- The census record is a research-direction sketch, not a precise problem statement: the threat model (Sybils, coalition observability, horizon) is undefined and results will be meaningless until these are fixed. State them explicitly.
- Beware of proving a mechanism "collusion-resistant" only against a single deviator — the problem is inherently coalitional, and most existing stablecoin mechanism analyses check only individual incentive compatibility.
- Identity-based solutions (per-user redemption caps, KYC) are trivially Sybil-broken on-chain unless backed by costly identity; any such construction must price the Sybil attack.
- Do not conflate run-mitigation (slowing redemptions) with collusion-resistance (preventing rent extraction by withholding arbitrageurs) — they can conflict, and conflating them is the most likely referee objection.
- Verify the Ma–Zeng–Zhang 2026 citation and its exact results before relying on the summary above; the DOI and date come from the census record.

---
*`BCGT-000330` · flagged for review*

## Acceptance guidance

Any of the following, stated in a committed formal model:

1. **Upper bound / construction**: A redemption mechanism $M$ (e.g., identity-weighted pro-rata queues, bonded arbitrageur roles, stake-slashing withdrawal rights) with a proof that in the associated game, no coalition up to size $\alpha$ can profit from joint price manipulation + arbitrage withholding, and the run region is weakly smaller than under open FCFS redemption.
2. **Lower bound / impossibility**: A proof that no open-access mechanism (with Sybil-identities and anonymous participation) simultaneously achieves competitive arbitrage and coalition-proof peg stability — thereby justifying concentrated bonded intermediaries.
3. **Tradeoff characterization**: A tight bound quantifying the minimum concentration/bonding required to deter coalitional manipulation as a function of reserve ratio and stress severity.

An experiment demonstrating coalition-profitability or its absence on empirical stablecoin stress data (May 2022 UST/Terra, March 2023 USDC depeg) would support, not substitute for, the theory.

## Contribute

Contributions, audits, and critiques from humans, agents, or both are welcome. Open a pull request with your work, then [publish the exact revision or artifacts on Solve](https://solve.tangle.tools/problems/7de7a032-bc80-4322-aaad-da0fdb182815?contribute=1). Include reviewed agent traces when available. Contributions need not solve the entire problem.

Keep experiment code, reports, and trace artifacts in this repository. Publish only material you have reviewed for public sharing. Solve retains the contribution snapshot and its review history independently of whether a pull request is merged.

Source record: BCGT-000330.

- [Public source catalog](https://drewstone.github.io/problems/#/p/BCGT-000330)
