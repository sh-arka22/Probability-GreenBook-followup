# 1. The Coin Toss Game

## The Premise

> Gambler A has $(n+1)$ fair coins; Gambler B has $n$ fair coins. Both flip all their coins. What is the probability that A will have **strictly more** heads than B?

---

## What the Interviewer Wants to Hear

We are testing your ability to **spot symmetry** rather than blindly applying combinatorics or binomial expansions.

### Core Insight — The Symmetry Argument

**Step 1: Isolate A's extra coin.**

Split A's $(n+1)$ coins into two groups:

- **First $n$ coins** — call these $A_{1 \ldots n}$
- **The $(n+1)^{th}$ coin** — call this $A_{extra}$

**Step 2: Compare $A_{1 \ldots n}$ vs $B_{1 \ldots n}$.**

Both sets have $n$ fair coins. There are exactly three mutually exclusive and exhaustive outcomes:

| Outcome | Notation |
|---|---|
| A's first $n$ coins show more heads | $A_{1 \ldots n} > B$ |
| B's $n$ coins show more heads | $B > A_{1 \ldots n}$ |
| They tie | $A_{1 \ldots n} = B$ |

By **symmetry** (identical coin counts, all fair):

$$P(A_{1 \ldots n} > B) = P(B > A_{1 \ldots n})$$

Let:

$$p = P(A_{1 \ldots n} > B), \quad t = P(A_{1 \ldots n} = B)$$

so $2p + t = 1$, i.e. $t = 1 - 2p$.

**Step 3: When does the extra coin matter?**

| Scenario | Extra coin needed? | A wins? |
|---|---|---|
| $A_{1 \ldots n} > B$ | No | ✅ Always (extra coin is just gravy) |
| $B > A_{1 \ldots n}$ | Extra coin can gain at most 1 head. But B is ahead by ≥ 1, so at best we tie — **not** strictly more | ❌ Never |
| $A_{1 \ldots n} = B$ | A wins **iff** $A_{extra} = H$ | ✅ With probability $\frac{1}{2}$ |

**Step 4: Combine.**

$$P(A > B) = p + \frac{1}{2} \cdot t = p + \frac{1}{2}(1 - 2p) = p + \frac{1}{2} - p = \boxed{\frac{1}{2}}$$

> **Elegant conclusion:** No matter how large $n$ is, the probability that A gets strictly more heads is always exactly $\frac{1}{2}$.

### Intuition Check

This makes intuitive sense: A's extra coin is either heads or tails with equal probability. By symmetry, it either breaks a tie (giving A the win) or doesn't matter — and these balance out perfectly.

---

## Follow-Up Questions

---

### Follow-Up 1: Generalization to $n + k$ Coins

> What if Gambler A has $n + k$ coins and B has $n$ coins? What is the probability that A gets strictly more heads?

#### Solution

Split A's coins into two groups: the first $n$ coins ($A_{1 \ldots n}$) and the remaining $k$ coins ($A_{\text{extra}\_1}, \ldots, A_{\text{extra}\_k}$).

Let $X$ = heads from $A_{1 \ldots n}$ and $Y$ = heads from $B_{1 \ldots n}$. By symmetry, $X$ and $Y$ are identically distributed (both $\text{Binomial}(n, 0.5)$).

Let $Z$ = heads from A's extra $k$ coins, so $Z \sim \text{Binomial}(k, 0.5)$.

**A wins iff $X + Z > Y$, i.e. $Z > Y - X$.**

Let $D = X - Y$. By symmetry, $D$ is symmetric about 0, meaning $P(D = d) = P(D = -d)$ for all $d$.

$$P(A > B) = P(Z + D > 0) = \sum_{d} P(D = d) \cdot P(Z > -d)$$

For a closed-form result, note that the total heads for A follow $\text{Binomial}(n+k, 0.5)$ and for B follow $\text{Binomial}(n, 0.5)$.

**Special cases:**
- $k = 0$: $P(A > B) = \frac{1 - P(\text{tie})}{2} < \frac{1}{2}$ (by symmetry, less than half since ties are possible)
- $k = 1$: $P(A > B) = \frac{1}{2}$ (the original problem)
- $k \to \infty$: $P(A > B) \to 1$ (A overwhelms B)

**General formula:**

$$P(A > B) = \sum_{j=0}^{n} \sum_{i=j+1}^{n+k} \binom{n+k}{i}\binom{n}{j} \left(\frac{1}{2}\right)^{2n+k}$$

This doesn't simplify to a clean closed form for arbitrary $k$, but can be computed efficiently. An equivalent formulation uses the CDF of the difference of two binomials.

---

### Follow-Up 2: Biased Coins

> Suppose both players use biased coins with $P(\text{Heads}) = p \neq 0.5$. Does the probability that A wins remain $\frac{1}{2}$?

#### Solution

**Yes — the result remains exactly $\frac{1}{2}$, regardless of the bias $p$!**

The proof follows identically:

**Step 1:** Compare A's first $n$ coins to B's $n$ coins. Both use the same biased coins, so:

$$P(A_{1 \ldots n} > B) = P(B > A_{1 \ldots n})$$

This symmetry holds for **any** $p \in (0, 1)$, since both sets of $n$ coins are i.i.d. $\text{Bernoulli}(p)$.

**Step 2:** Let $p_{\text{win}} = P(A_{1 \ldots n} > B)$ and $t = P(\text{tie})$.

The extra coin is heads with probability $p$, tails with probability $1 - p$.

When $B > A_{1 \ldots n}$: B leads by at least 1. The extra coin can gain at most 1 head.
- If B leads by exactly 1 and the extra coin is H → **tie** (not a strict win for A)
- If B leads by ≥ 2 → A still loses

**Wait** — this is where the biased case differs subtly. Let's be more careful.

Let $D = A_{1 \ldots n} - B$ be the head-count difference. By symmetry of using the same coins:

$$P(D = d) = P(D = -d) \quad \text{for all } d$$

A wins iff $D + A_{\text{extra}} > 0$ where $A_{\text{extra}} \sim \text{Bernoulli}(p)$.

$$P(A > B) = P(D > 0) + p \cdot P(D = 0) + p \cdot P(D = -1) \cdot 0$$

Wait, let me redo this properly. A's total heads $= (A_{1 \ldots n} \text{ heads}) + A_{\text{extra}}$.
B's total heads $= B$ heads.

A wins iff $D + A_{\text{extra}} > 0$ where $D = A_{1 \ldots n} - B$.

- If $D \geq 1$: A wins regardless → probability $p_{\text{win}}$
- If $D = 0$: A wins iff $A_{\text{extra}} = 1$ → contributes $t \cdot p$
- If $D = -1$: A wins iff $A_{\text{extra}} = 1$, giving $D + 1 = 0$ → **tie, not win**
- If $D \leq -1$: A cannot win (extra coin adds at most 1)

So:

$$P(A > B) = p_{\text{win}} + p \cdot t$$

Now, $p_{\text{win}} + p_{\text{lose}} + t = 1$ and $p_{\text{win}} = p_{\text{lose}}$ by symmetry.

So $t = 1 - 2p_{\text{win}}$.

$$P(A > B) = p_{\text{win}} + p(1 - 2p_{\text{win}}) = p_{\text{win}}(1 - 2p) + p$$

**This equals $\frac{1}{2}$ only when $p = \frac{1}{2}$!**

For biased coins ($p \neq 0.5$):

$$\boxed{P(A > B) = p_{\text{win}}(1 - 2p) + p}$$

where $p_{\text{win}} = P(A_{1..n} > B)$ depends on $n$ and $p$.

**Example:** For $n = 1$, $p = 0.7$:
- $p_{\text{win}} = P(A_1 = H, B = T) = 0.7 \times 0.3 = 0.21$
- $t = P(\text{same}) = p^2 + (1-p)^2 = 0.49 + 0.09 = 0.58$
- $P(A > B) = 0.21 + 0.7 \times 0.58 = 0.21 + 0.406 = 0.616 \neq 0.5$

**So no, the answer does NOT remain $\frac{1}{2}$ for biased coins.** The key is that the extra coin's head probability $p$ no longer matches the "half" needed to perfectly balance the symmetry.

---

### Follow-Up 3: Expected Difference in Heads

> What is the expected difference in the number of heads between A and B?

#### Solution

Let $H_A \sim \text{Binomial}(n+1, \frac{1}{2})$ and $H_B \sim \text{Binomial}(n, \frac{1}{2})$.

By linearity of expectation:

$$E[H_A - H_B] = E[H_A] - E[H_B] = \frac{n+1}{2} - \frac{n}{2} = \boxed{\frac{1}{2}}$$

**Variance of the difference** (since $H_A$ and $H_B$ are independent):

$$\text{Var}(H_A - H_B) = \text{Var}(H_A) + \text{Var}(H_B) = \frac{n+1}{4} + \frac{n}{4} = \frac{2n+1}{4}$$

So the standard deviation is $\frac{\sqrt{2n+1}}{2}$, which grows with $n$.

**Key insight:** While A wins exactly half the time, the *expected* advantage is always $\frac{1}{2}$ — exactly one extra coin's expected heads contribution. This is a beautiful application of linearity of expectation: the expected difference doesn't depend on the game's complex win/loss structure.

---

### Follow-Up 4: Conditional Expected Heads Given A Wins

> Given that A won the game, what is the expected number of heads A rolled?

#### Solution

We want $E[H_A \mid H_A > H_B]$.

**Using symmetry and decomposition:**

Since $P(H_A > H_B) = \frac{1}{2}$, we can write:

$$E[H_A] = E[H_A \mid H_A > H_B] \cdot P(H_A > H_B) + E[H_A \mid H_A \leq H_B] \cdot P(H_A \leq H_B)$$

$$\frac{n+1}{2} = \frac{1}{2} E[H_A \mid H_A > H_B] + \frac{1}{2} E[H_A \mid H_A \leq H_B]$$

We also know by the symmetry of the problem structure that conditioning on winning increases A's expected heads. However, finding a clean closed form requires careful computation.

**Approach via direct computation:**

$$E[H_A \mid H_A > H_B] = \frac{\sum_{i>j} i \cdot P(H_A = i) \cdot P(H_B = j)}{P(H_A > H_B)}$$

$$= 2 \sum_{i=1}^{n+1} \sum_{j=0}^{i-1} i \binom{n+1}{i} \binom{n}{j} \left(\frac{1}{2}\right)^{2n+1}$$

**For small $n$, we can compute directly:**

*$n = 1$:* A has 2 coins, B has 1 coin.

| $H_A$ | $H_B$ | $P$ | A wins? |
|---|---|---|---|
| 0 | 0 | $\frac{1}{8}$ | No |
| 0 | 1 | $\frac{1}{8}$ | No |
| 1 | 0 | $\frac{2}{8}$ | Yes |
| 1 | 1 | $\frac{2}{8}$ | No |
| 2 | 0 | $\frac{1}{8}$ | Yes |
| 2 | 1 | $\frac{1}{8}$ | Yes |

$P(A \text{ wins}) = \frac{2 + 1 + 1}{8} = \frac{4}{8} = \frac{1}{2}$ ✓

$$E[H_A \mid A \text{ wins}] = \frac{1 \cdot \frac{2}{8} + 2 \cdot \frac{1}{8} + 2 \cdot \frac{1}{8}}{\frac{1}{2}} = \frac{\frac{6}{8}}{\frac{1}{2}} = \frac{3}{2}$$

Compare to $E[H_A] = 1$. So conditioning on winning increases the expected heads from 1 to $\frac{3}{2}$.

**General pattern:** $E[H_A \mid A \text{ wins}] = \frac{n+1}{2} + \delta(n)$ where $\delta(n) > 0$ represents the boost from conditioning on winning.

---

### Follow-Up 5: Recursive Game — Expected Flips Until Termination

> If A and B play this game repeatedly, stopping only when A has strictly more heads, what is the expected number of total coin flips before the game terminates?

#### Solution

**Each round:** A flips $(n+1)$ coins, B flips $n$ coins → total of $(2n + 1)$ flips per round.

**Probability A wins (terminates) in each round:** $P(\text{stop}) = P(A > B) = \frac{1}{2}$.

**The number of rounds $R$ is geometric** with success probability $\frac{1}{2}$:

$$P(R = k) = \left(\frac{1}{2}\right)^{k-1} \cdot \frac{1}{2} = \left(\frac{1}{2}\right)^k, \quad k = 1, 2, 3, \ldots$$

$$E[R] = \frac{1}{P(\text{stop})} = \frac{1}{1/2} = 2$$

**Total expected flips:**

$$E[\text{Total flips}] = E[R] \times (2n + 1) = \boxed{2(2n+1)}$$

**Variance analysis:**

$$\text{Var}(R) = \frac{1 - p}{p^2} = \frac{1/2}{1/4} = 2$$

$$\text{Var}(\text{Total flips}) = (2n+1)^2 \cdot \text{Var}(R) = 2(2n+1)^2$$

**Example:** For $n = 4$ (A has 5 coins, B has 4 coins):
- Each round uses $2(4) + 1 = 9$ coin flips
- Expected rounds to terminate: 2
- Expected total flips: $2 \times 9 = 18$
- There is still a $\frac{1}{4}$ chance of needing 3+ rounds, requiring 27+ flips

> **Note on independence:** This solution assumes each round is independent, which holds because all coins are re-flipped each round. The memoryless property of the geometric distribution means that no matter how many rounds have passed, the expected remaining rounds is always 2.
