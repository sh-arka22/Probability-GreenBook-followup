# 2. The Card Game (High Card Wins)

## The Premise

> You and the dealer draw one card each from a shuffled 52-card deck **without replacement**. If you have a larger number, you win. On ties or dealer having a larger number, the **house wins**. What is your probability of winning?

---

## What the Interviewer Wants to Hear

Do **not** iterate through the 13 card ranks — that shows a brute-force mindset. We want to see you use **complementary events and symmetry**.

### Core Insight — Symmetry + Tie-Breaking

**The key observation:** You and the dealer are in a perfectly symmetric game. Neither of you has any advantage in drawing a higher card.

**Step 1: Identify the symmetry.**

There are exactly three mutually exclusive outcomes:

| Outcome | Who wins? |
|---|---|
| Your card > Dealer's card | You win |
| Dealer's card > Your card | House wins |
| Same rank (tie) | House wins |

By symmetry, your probability of drawing a higher card equals the dealer's probability:

$$P(\text{You higher}) = P(\text{Dealer higher})$$

**Step 2: Compute the tie probability.**

Once you draw your card, there are 51 cards remaining. Exactly 3 of them share your rank (since each rank has 4 suits, and you hold one).

$$P(\text{Tie}) = \frac{3}{51} = \frac{1}{17}$$

**Step 3: Use complementary events.**

Since the three outcomes are exhaustive:

$$P(\text{You higher}) + P(\text{Dealer higher}) + P(\text{Tie}) = 1$$

By symmetry, $P(\text{You higher}) = P(\text{Dealer higher})$ , so:

$$2 \cdot P(\text{You higher}) + \frac{1}{17} = 1$$

$$P(\text{You higher}) = \frac{1 - \frac{1}{17}}{2} = \frac{16/17}{2} = \boxed{\frac{8}{17} \approx 0.4706}$$

> **The house edge:** You win 47.06% of the time — the house's entire edge comes from owning the ties. Without the tie rule, it would be a perfectly fair game.

### Why This Approach Is Superior

A brute-force approach would enumerate all $52 \times 51 = 2{,}652$ ordered pairs. The symmetry approach reduces it to a single calculation: find $P(\text{Tie})$ , and everything else follows. This is exactly the interview signal they're looking for.

---

## Follow-Up Questions

---

### Follow-Up 1: With Replacement — Edge and Fair Price

> How does your edge change if the game is played **with replacement**, and how much should you be willing to pay if the payoff is \$100?

#### Solution

**With replacement**, both cards are drawn independently from the full 52-card deck.

**Step 1: Tie probability changes.**

Now the dealer draws from all 52 cards (not 51), and there are 4 cards matching your rank:

$$P(\text{Tie}) = \frac{4}{52} = \frac{1}{13}$$

**Step 2: Apply symmetry (still works!).**

The game is still symmetric — you and the dealer draw from identical distributions:

$$P(\text{Win}) = \frac{1 - \frac{1}{13}}{2} = \frac{12/13}{2} = \frac{6}{13} \approx 0.4615$$

**Comparison:** With replacement your win probability **drops** from $\frac{8}{17} \approx 0.4706$ to $\frac{6}{13} \approx 0.4615$ .

**Intuition:** With replacement, ties are *more likely* ( $\frac{1}{13}$ vs $\frac{1}{17}$ ), and since the house owns all ties, replacement hurts you.

**Fair price for the game:**

$$E[\text{Payout}] = \frac{6}{13} \times 100 + \frac{7}{13} \times 0 = \frac{600}{13} \approx \$46.15$$

You should pay **at most \$46.15** to play. Anything above that gives the house a positive expected edge.

---

### Follow-Up 2: $N$ Decks Shuffled Together — Limiting Probability

> Suppose the game is played with $N$ decks shuffled together. What is the limit of your winning probability as $N \to \infty$ ?

#### Solution

With $N$ decks, there are $52N$ total cards. Each rank has $4N$ cards (one per suit per deck).

**Step 1: Tie probability with** $N$ **decks (without replacement).**

After you draw your card, there are $52N - 1$ cards left, and $4N - 1$ of them match your rank:

$$P(\text{Tie}) = \frac{4N - 1}{52N - 1}$$

**Step 2: Win probability.**

$$P(\text{Win}) = \frac{1 - P(\text{Tie})}{2} = \frac{1}{2}\left(1 - \frac{4N - 1}{52N - 1}\right)$$

**Step 3: Take the limit as** $N \to \infty$ .

$$\lim_{N \to \infty} \frac{4N - 1}{52N - 1} = \frac{4}{52} = \frac{1}{13}$$

$$\lim_{N \to \infty} P(\text{Win}) = \frac{1 - \frac{1}{13}}{2} = \frac{6}{13} \approx 0.4615$$

> **Key insight:** As $N \to \infty$ , the without-replacement game converges to the with-replacement game. This makes sense — removing one card from an infinite deck has negligible effect, so the draws become effectively independent.

**Convergence table:**

| $N$ (decks) | $P(\text{Tie})$ | $P(\text{Win})$ |
|---|---|---|
| 1 | $\frac{3}{51} = \frac{1}{17}$ | $\frac{8}{17} \approx 0.4706$ |
| 2 | $\frac{7}{103}$ | $\approx 0.4660$ |
| 6 | $\frac{23}{311}$ | $\approx 0.4630$ |
| $\infty$ | $\frac{1}{13}$ | $\frac{6}{13} \approx 0.4615$ |

More decks always **hurts** the player (monotonically decreasing win probability), because ties become more likely.

---

### Follow-Up 3: Information Advantage — Optimal Strategy

> If the dealer's card is drawn **face-up** before you decide whether to bet, what is your optimal strategy, and what is your expected profit assuming a \$1 bet?

#### Solution

**Optimal strategy:** Only bet when you have a positive expected value — i.e., when the probability that your (yet unseen) card beats the dealer's card exceeds $\frac{1}{2}$ .

**Step 1: Counting winning cards given the dealer shows rank** $r$ .

After seeing the dealer's card, there are 51 remaining cards. The cards that beat rank $r$ are all cards with rank strictly higher than $r$ .

If the dealer shows the $r$-th lowest rank (where Ace = 1, ..., King = 13):
- Cards that beat rank $r$ : $4 \times (13 - r) = 4(13 - r)$
- Cards that lose to rank $r$ : $4 \times (r - 1) = 4(r - 1)$
- Cards that tie rank $r$ : $3$

**Step 2: When to bet?**

You bet when $P(\text{Win} \mid \text{dealer shows } r) > \frac{1}{2}$ :

$$\frac{4(13 - r)}{51} > \frac{1}{2}$$

$$4(13 - r) > 25.5$$

$$13 - r > 6.375$$

$$r < 6.625$$

So you bet when $r \leq 6$ , i.e., when the dealer shows **Ace through 6**.

**Step 3: Expected profit.**

When the dealer shows rank $r$ and you bet \$1:

$$E[\text{Profit} \mid r] = \frac{4(13-r)}{51} \times (+1) + \left(1 - \frac{4(13-r)}{51}\right) \times (-1) = \frac{8(13-r) - 51}{51}$$

You only bet for $r = 1, 2, \ldots, 6$ . Each rank appears with probability $\frac{4}{52} = \frac{1}{13}$ :

$$E[\text{Profit}] = \frac{1}{13} \sum_{r=1}^{6} \frac{8(13-r) - 51}{51}$$

Computing the sum:

| $r$ | $8(13-r) - 51$ | Profit per dollar |
|---|---|---|
| 1 (Ace) | $96 - 51 = 45$ | $+45/51$ |
| 2 | $88 - 51 = 37$ | $+37/51$ |
| 3 | $80 - 51 = 29$ | $+29/51$ |
| 4 | $72 - 51 = 21$ | $+21/51$ |
| 5 | $64 - 51 = 13$ | $+13/51$ |
| 6 | $56 - 51 = 5$ | $+5/51$ |

$$\text{Sum} = \frac{45 + 37 + 29 + 21 + 13 + 5}{51} = \frac{150}{51} = \frac{50}{17}$$

$$E[\text{Profit}] = \frac{1}{13} \times \frac{50}{17} = \frac{50}{221} \approx \$0.226$$

> **With perfect information, your expected profit is about \$0.23 per \$1 bet.** The information advantage transforms a losing game (house edge) into a winning one.

---

### Follow-Up 4: Deck Without Face Cards

> Suppose the house still wins on ties, but the deck is modified so all face cards (J, Q, K) are removed. How does this impact your probability of winning?

#### Solution

**Modified deck:** Remove J, Q, K from each suit → $4 \times 3 = 12$ cards removed.

New deck has $52 - 12 = 40$ cards with 10 distinct ranks (Ace through 10), each appearing 4 times.

**Step 1: Apply the same symmetry framework.**

The game is still symmetric between you and the dealer. The only thing that changes is the tie probability.

**Step 2: Compute** $P(\text{Tie})$ .

After you draw your card, there are 39 remaining cards, with 3 matching your rank:

$$P(\text{Tie}) = \frac{3}{39} = \frac{1}{13}$$

**Step 3: Win probability.**

$$P(\text{Win}) = \frac{1 - \frac{1}{13}}{2} = \frac{6}{13} \approx 0.4615$$

**Comparison to standard deck:**

| Deck | Ranks | Cards | $P(\text{Tie})$ | $P(\text{Win})$ |
|---|---|---|---|---|
| Standard (52) | 13 | 52 | $\frac{1}{17}$ | $\frac{8}{17} \approx 0.4706$ |
| No face cards (40) | 10 | 40 | $\frac{1}{13}$ | $\frac{6}{13} \approx 0.4615$ |

> **Key insight:** Removing face cards **hurts the player**. The win probability drops because $P(\text{Tie})$ increases from $\frac{1}{17}$ to $\frac{1}{13}$ .

**Why?** With fewer ranks but the same number of suits per rank, ties become more likely. The general formula is:

$$P(\text{Tie}) = \frac{s - 1}{rs - 1}$$

where $r$ = number of ranks and $s$ = number of suits. As $r$ increases (more ranks), $P(\text{Tie})$ decreases and the player's odds improve. The extreme case is $r \to \infty$ (every card unique), where $P(\text{Tie}) \to 0$ and $P(\text{Win}) \to \frac{1}{2}$ .

---

### Follow-Up 5: Continuous Distribution — Fair Price of a Squared Payout

> If instead of cards, you and the dealer draw independent random variables from a Uniform(0,1) distribution, and ties are impossible, what is the fair price of a contract that pays the **square of your draw** if you win, and \$0 if you lose?

#### Solution

Let $X$ (your draw) and $Y$ (dealer's draw) be i.i.d. Uniform(0,1).

**You win iff** $X > Y$ . The payout is $X^2$ if you win, $0$ otherwise.

**Step 1: Set up the expectation.**

$$E[\text{Payout}] = E[X^2 \cdot \mathbf{1}_{X > Y}]$$

where $\mathbf{1}_{X > Y}$ is the indicator that you win.

**Step 2: Compute via double integral.**

Since $X$ and $Y$ are independent Uniform(0,1):

$$E[X^2 \cdot \mathbf{1}_{X>Y}] = \int_0^1 \int_0^x x^2 \, dy \, dx$$

The inner integral (over $y$ from 0 to $x$ ) is just $x$ :

$$= \int_0^1 x^2 \cdot x \, dx = \int_0^1 x^3 \, dx = \frac{x^4}{4}\Bigg|_0^1 = \frac{1}{4}$$

$$\boxed{E[\text{Payout}] = \frac{1}{4}}$$

**The fair price of the contract is** $\frac{1}{4}$ .

**Intuition check via decomposition:**

$$E[X^2 \cdot \mathbf{1}_{X>Y}] = E[X^2 \mid X > Y] \cdot P(X > Y) = E[X^2 \mid X > Y] \cdot \frac{1}{2}$$

So $E[X^2 \mid X > Y] = \frac{1}{2}$ .

This makes sense: conditioning on $X > Y$ biases $X$ toward higher values, inflating $E[X^2]$ from its unconditional value of $\frac{1}{3}$ up to $\frac{1}{2}$ .

**Bonus — general payout** $X^n$ :

$$E[X^n \cdot \mathbf{1}_{X>Y}] = \int_0^1 x^n \cdot x \, dx = \int_0^1 x^{n+1} \, dx = \frac{1}{n+2}$$

| Payout | $X^0 = 1$ | $X^1$ | $X^2$ | $X^3$ | $X^n$ |
|---|---|---|---|---|---|
| Fair price | $\frac{1}{2}$ | $\frac{1}{3}$ | $\frac{1}{4}$ | $\frac{1}{5}$ | $\frac{1}{n+2}$ |

> **Elegant pattern:** The fair price of a contract paying $X^n$ when you win is always $\frac{1}{n+2}$ . The $P(\text{Win}) = \frac{1}{2}$ factor from symmetry combines with the power-law integration to produce this clean result.
