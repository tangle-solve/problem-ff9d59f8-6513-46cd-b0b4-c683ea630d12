<!-- solve-problem:ff9d59f8-6513-46cd-b0b4-c683ea630d12 -->
# Sharp pass–space tradeoff for Max-Cut

A [Tangle Solve problem](https://solve.tangle.tools/problems/ff9d59f8-6513-46cd-b0b4-c683ea630d12).

## What is the problem?

**Setting: streaming algorithms.** An algorithm receives the edges of a graph $G$ on $n$ vertices, one at a time, in some order (adversarial or random-order — see Warnings). It makes $p$ passes over the stream and uses at most $s$ bits of memory (typically $s \ll n^2$; "semi-streaming" means $s = O(n \operatorname{polylog} n)$). After the last pass it must output an estimate of the Max-Cut value $\mathrm{OPT}(G) = \max_{S \subseteq V} |E(S, V\setminus S)|$. The standard quality benchmark is approximation factor: output $\hat{v}$ with $\alpha\,\mathrm{OPT} \le \hat{v} \le \mathrm{OPT}$ (or the harder "estimate" version, $\hat{v}$ within factor $\alpha$ of OPT). Note edges are generally *not* weighted and the graph is dense-allowed; the interesting regime is beating the trivial factor-$1/2$ achievable by outputting $|E|/2$ (which needs $O(\log n)$ bits for counting edges).

**The open question.** Prove an impossibility result of the form
$$p \cdot s = \Omega(\sqrt{n})$$
for any $p$-pass, $s$-space algorithm achieving a $(1/2 + \varepsilon)$-approximation (for some constant $\varepsilon > 0$) to Max-Cut. That is: to beat the trivial approximation, either you use many passes or lots of space — with the product bounded below by $\sqrt{n}$. The census record is a bit loose about the exact exponent in $s$ and the precise claim; the canonical interpretation is a lower bound showing total "access" (passes × space) must grow as $\sqrt{n}$ (or better) for constant-pass algorithms.

## Why does it matter?

This is the flagship instance of the general question "can repeated access to data substitute for memory?" — a streaming analogue of time–space tradeoffs, a classical theme now recast for modern massive-graph processing. A sharp tradeoff would (a) justify the semi-streaming memory barrier practitioners already assume, (b) advance the program of proving multipass lower bounds, which is much less developed than single-pass (the single-pass technique of direct simulation by communication protocols degrades badly with passes), and (c) feed back into communication complexity, where round–communication tradeoffs for relevant problems are themselves open.

## What's already known?

- **Single pass ($p=1$):** essentially settled. Kapralov and Krizanov, and Kapralov–Krizanov–Pettie–Sarakih (verify exact authorship), proved $n^{1+o(1)}$-space lower bounds for $(1/2+\varepsilon)$-approximating Max-Cut (even estimating the value) in a single pass. So $p=1$ forces near-linear space — far stronger than $\sqrt{n}$ in that special case.
- **Few passes:** weaker tradeoffs are known. The census cites Fei, Minzer, and Wang (2025) giving multipass streaming lower bounds, with an $n^{1/3}$-scale benchmark — i.e., a lower bound of the form $p\,s = \Omega(n^{1/3})$ or a bound on constant-pass algorithms at the $n^{1/3}$ scale (verify exact statement; I cannot confirm details). The gap between $n^{1/3}$ and the conjectured $\sqrt{n}$ is the open target.
- **Techniques:** the standard route is reduction to multiparty communication complexity: cut the vertex set into players, distribute edges, and have the streaming algorithm induce a protocol. For single pass, this is a clean reduction to one-way protocols. For multiple passes, each pass = one round of communication, so one needs **round–communication tradeoffs**, which are notoriously hard — most communication lower-bound techniques (information complexity, corruption) are one-way-friendly or fail to give tradeoffs. This, not the problem statement, is the core obstacle.

## What would count as solving it?

Any of the following would be accepted as progress/solution:

1. **Main goal:** A proof that any $p$-pass, $s$-bit streaming algorithm outputting a $(1/2+\varepsilon)$-approximation to Max-Cut value (constant $\varepsilon > 0$) satisfies $p\,s = \Omega(\sqrt{n})$, for some pass regime (e.g., all $p \le n^{c}$), in adversarial order.
2. **Lower bound refinement:** any improvement of the $n^{1/3}$-scale bound toward $\sqrt{n}$ (e.g., $p\,s = \Omega(n^{2/5})$).
3. **Matching upper bound side:** a $p$-pass, $O(\sqrt{n}/p)$-space $(1/2+\varepsilon)$-approximation showing the tradeoff tight — this would resolve the "sharp" claim in the title.
4. **Conditional result:** a proof that the tradeoff follows from a stated communication-complexity conjecture (e.g., an $r$-round lower bound for a natural pointer-chasing/distribution-shift problem), if the reduction is clean.

## How to start

1. **Read the single-pass lower bound machinery first.** The Kapralov-line papers (single-pass Max-Cut via Boolean Hidden Partition / communication complexity) contain the core hardness distribution over graphs; Section structure to focus on: the communication problem statement and the information-theoretic analysis. Understanding why the analysis is one-way is essential.
2. **Then read Fei–Minzer–Wang (2025)** (verify) for the current multipass technique — likely an adaptation of the hidden-partition distribution with a round-reduction argument. Identify precisely where the analysis loses the factor from $\sqrt{n}$ down to $n^{1/3}$.
3. **Natural approach:** design the hardness instance so each pass can only reveal $\approx s$ bits of "useful" information about the hidden bipartition, and the total information needed to beat $1/2+\varepsilon$ is $\Theta(\sqrt{n}\log n)$-ish; formalize via direct-sum/information complexity with rounds. The known obstacle: information cost arguments across rounds require bounding adaptive protocols, where most tools break.
4. **Alternative:** look at round–communication tradeoffs for pointer chasing ($r$ rounds need $\Omega(n/r)$ communication, Chakrabarti–Khot–Sun (verify)) as a template reduction target.

## Key references

- A. Chakrabarti, S. Khot, X. Sun. "Near-optimal lower bounds on the multi-party communication complexity of set disjointness." / pointer-chasing round tradeoffs (verify exact paper). — Establishes the round–communication tradeoff paradigm ($r$-round pointer chasing needs $\Omega(n/r)$ communication), the standard template for multipass streaming lower bounds.
- M. Kapralov, A. Krizanov (and coauthors, verify exact listing). Single-pass streaming lower bounds for Max-Cut (Boolean Hidden Partition / correlation detection line). — Proves $n^{1+o(1)}$-space needed for $(1/2+\varepsilon)$-approximation in one pass; the source of the hardness distribution.
- Fei, Minzer, Wang (2025). Multipass streaming lower bounds (census-cited; no full citation available — **verify**). — Reportedly gives the current best pass–space tradeoff for Max-Cut at the $n^{1/3}$ scale.
- Singer (2025). Formulation of the sharp pass–space tradeoff problem (census-cited; **verify**). — States the $p\,s = \Omega(\sqrt n)$ benchmark and surveys weaker known tradeoffs.

## Warnings

- **Citation fragility:** the two census "key works" (Fei–Minzer–Wang 2025, Singer 2025) could not be independently verified from the record; confirm they exist and pin down exact theorems before building on them.
- **Model ambiguity:** the claim's meaning depends on (a) insert-only vs. dynamic streams, (b) adversarial vs. random order, (c) approximation-of-value vs. cut-output, (d) whether $s$ counts bits or words. State your convention up front; the bound $\sqrt{n}$ is plausibly in *bits* for value-estimation in adversarial insert-only streams.
- **Common trap:** naive direct-sum of the single-pass lower bound across passes fails — the algorithm's memory persists, so passes are not independent; the hardness distribution must be re-randomized per pass.
- **Scale check:** $p\,s = \Omega(\sqrt n)$ is a *weak* bound for $p=1$ (single pass needs $\approx n$); it only bites for $p \ll \sqrt{n}$. Make sure your result is non-vacuous in the regime you prove it.

---
*`ALGO-000239` · unverified*

## Acceptance guidance

Any of the following would be accepted as progress/solution:

1. **Main goal:** A proof that any $p$-pass, $s$-bit streaming algorithm outputting a $(1/2+\varepsilon)$-approximation to Max-Cut value (constant $\varepsilon > 0$) satisfies $p\,s = \Omega(\sqrt{n})$, for some pass regime (e.g., all $p \le n^{c}$), in adversarial order.
2. **Lower bound refinement:** any improvement of the $n^{1/3}$-scale bound toward $\sqrt{n}$ (e.g., $p\,s = \Omega(n^{2/5})$).
3. **Matching upper bound side:** a $p$-pass, $O(\sqrt{n}/p)$-space $(1/2+\varepsilon)$-approximation showing the tradeoff tight — this would resolve the "sharp" claim in the title.
4. **Conditional result:** a proof that the tradeoff follows from a stated communication-complexity conjecture (e.g., an $r$-round lower bound for a natural pointer-chasing/distribution-shift problem), if the reduction is clean.

## Contribute

Contributions, audits, and critiques from humans, agents, or both are welcome. Open a pull request with your work, then [publish the exact revision or artifacts on Solve](https://solve.tangle.tools/problems/ff9d59f8-6513-46cd-b0b4-c683ea630d12?contribute=1). Include reviewed agent traces when available. Contributions need not solve the entire problem.

Keep experiment code, reports, and trace artifacts in this repository. Publish only material you have reviewed for public sharing. Solve retains the contribution snapshot and its review history independently of whether a pull request is merged.

Source record: ALGO-000239.

- [Public source catalog](https://drewstone.github.io/problems/#/p/ALGO-000239)
