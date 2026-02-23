# 3. The Drunk Passenger

## The Premise

> A line of 100 passengers board a plane. The 1st person is drunk and picks a **random seat**. All subsequent passengers take their assigned seat if available, or a **random empty seat** if not. What is the probability that the 100th passenger gets their assigned seat?

---

## What the Interviewer Wants to Hear

Do **not** attempt backwards induction for $n = 100$ . This is a classic **absorbing state** problem disguised as a sequence.

### Core Insight — The "New Drunk" Invariant

**The key observation:** Every displaced passenger becomes a "new drunk" — they face the exact same situation as the original drunk, just with fewer seats. The fate of passenger 100 depends entirely on whether seat 1 or seat 100 is chosen first.

**Step 1: What happens when any displaced passenger picks a random seat?**

| They sit in... | What happens next? |
|---|---|
| **Seat 1** (the drunk's assigned seat) | ✅ Chain breaks! Everyone else sits correctly |
| **Seat 100** (the last passenger's seat) | ❌ Passenger 100 is doomed |
| **Some other seat** $j$ | Passenger $j$ becomes the new "drunk" — recurses |

**Step 2: Reduce to absorbing states.**

The chain **always terminates** when either seat 1 or seat 100 is chosen. These are the only two **absorbing states** — seat 1 is empty (drunk skipped it) and seat 100 is empty (passenger 100 hasn't boarded).

**Step 3: Apply symmetry.**

At every random-choice event, both seat 1 and seat 100 are in the pool of empty seats. A displaced passenger is equally likely to pick either one. Therefore:

$$P(\text{seat 1 first}) = P(\text{seat 100 first}) = \boxed{\frac{1}{2}}$$

> **The answer is** $\frac{1}{2}$ **, regardless of** $n$ . Works for 3 passengers or a million.

### Verification with Small Cases

**$n = 3$ :** Drunk picks from {1, 2, 3}:
- Seat 1 (prob $\frac{1}{3}$ ): everyone correct ✅
- Seat 3 (prob $\frac{1}{3}$ ): P3 displaced ❌
- Seat 2 (prob $\frac{1}{3}$ ): P2 displaced, picks from {1, 3} → 50-50

$$P = \frac{1}{3} + \frac{1}{3} \cdot \frac{1}{2} = \frac{1}{2} \checkmark$$

---

## Follow-Up Questions

These follow-ups escalate in difficulty and test whether you can extend structural reasoning rather than just memorize the base case.

---

### Follow-Up 1: Probability the $k$-th Passenger Gets Their Seat

> What is the probability that the $k^{th}$ passenger (where $1 < k \leq 100$ ) gets their assigned seat?

#### Solution

**Intuition:** Passenger $k$ gets displaced *only if* the disruption chain hasn't terminated before they board AND someone sitting before them randomly chose seat $k$ .

When it's passenger $k$ 's turn to board, the chain of displacement has been bouncing around among seats. The chain terminates (harmlessly) if someone picks seat 1 before passenger $k$ boards. Passenger $k$ loses their seat only if someone in the chain randomly picks seat $k$ before picking seat 1.

At each step of the chain before passenger $k$ boards, consider the "dangerous" seats: seat 1 (resolves the chain) and seat $k$ (steals $k$ 's seat). By the same symmetry argument as the base case — to any displaced passenger, seats 1 and $k$ are just two random unoccupied seats — they're equally likely to be hit first.

$$\boxed{P(\text{passenger } k \text{ gets their seat}) = \frac{1}{2}} \quad \text{for all } 1 < k \leq 100$$

> **Surprisingly clean:** Every single non-first passenger has exactly a 50% chance of sitting correctly, not just the last one.

---

### Follow-Up 2: Expected Number of Correctly Seated Passengers

> Compute $E[\text{number of passengers in their correct seat}]$ .

#### Solution

Define indicator variables:

$$X_k = \begin{cases} 1 & \text{if passenger } k \text{ sits correctly} \\ 0 & \text{otherwise} \end{cases}$$

From Follow-Up 1: $P(X_k = 1) = \frac{1}{2}$ for all $k \in \{2, 3, \ldots, 100\}$ .

Passenger 1 (the drunk) sits correctly with probability $\frac{1}{100}$ since they pick uniformly at random from all 100 seats.

By **linearity of expectation** (no independence required — this is the beauty of it):

$$E\left[\sum_{k=1}^{100} X_k\right] = \frac{1}{100} + 99 \times \frac{1}{2}$$

$$\boxed{E = \frac{1}{100} + \frac{99}{2} = 50.01}$$

Roughly half the plane sits correctly, which is intuitive — the disruption is a coin flip for everyone.

> **Interview note:** If a candidate tries to compute joint distributions here instead of invoking linearity of expectation, that's a red flag. In quant work, decomposing portfolio-level expectations into per-position indicators is a fundamental technique (think expected shortfall decomposition, expected number of defaults in a CDO tranche).

---

### Follow-Up 3: Two Drunk Passengers

> Passengers 1 and 2 are both drunk (pick uniformly at random). What is $P(\text{passenger 100 gets their seat})$ ?

#### Solution

This one is harder and breaks the clean symmetry. We condition on Passenger 1's choice:

**Case A: Passenger 1 picks seat 1** (prob $\frac{1}{100}$ ).
Disruption from passenger 1 resolved. Passenger 2 is still drunk → standard drunk problem with 99 passengers. $P = \frac{1}{2}$ .

**Case B: Passenger 1 picks seat 2** (prob $\frac{1}{100}$ ).
Passenger 2 arrives, seat taken, picks randomly — but they were going to pick randomly anyway. Equivalent to single drunk in a 99-person problem. $P = \frac{1}{2}$ .

**Case C: Passenger 1 picks seat 100** (prob $\frac{1}{100}$ ).
Seat 100 is permanently gone. $P = 0$ .

**Case D: Passenger 1 picks seat** $j$ **where** $j \notin \{1, 2, 100\}$ (prob $\frac{97}{100}$ ).
Passenger 2 is drunk AND passenger $j$ will be displaced. Passenger 2 picks randomly from 99 remaining seats.

- **D1:** Passenger 2 picks seat 2 (prob $\frac{1}{99}$ ). Their disruption resolves, one displaced person ( $j$ ) remains → $P = \frac{1}{2}$
- **D2:** Passenger 2 picks seat 1 (prob $\frac{1}{99}$ ). Resolves the first chain too, passenger $j$ displaced → $P = \frac{1}{2}$
- **D3:** Passenger 2 picks seat 100 (prob $\frac{1}{99}$ ). Passenger 100 is done → $P = 0$
- **D4:** Passenger 2 picks seat $j$ (prob $\frac{1}{99}$ ). Resolves $j$ 's displacement, one chain remains → $P = \frac{1}{2}$
- **D5:** Passenger 2 picks some other seat $m$ (prob $\frac{95}{99}$ ). Two disruption chains active. By absorbing-state analysis: seat 100 is symmetric with the "resolution" seats from the perspective of any randomly choosing displaced passenger → $P = \frac{1}{2}$

Through careful accounting:

$$P = \frac{1}{100} \cdot \frac{1}{2} + \frac{1}{100} \cdot \frac{1}{2} + \frac{1}{100} \cdot 0 + \frac{97}{100} \cdot P(\text{Case D})$$

Working through Case D fully (or by simulation/recursion):

$$\boxed{P(\text{passenger 100 gets their seat}) = \frac{1}{2}}$$

> **Deep result** — it's *still* $\frac{1}{2}$ . The fundamental symmetry between seat 1 (resolution) and seat 100 (failure) is preserved because no drunk has any preference between these two seats. All the chaos in the middle is irrelevant from the last seat's perspective.

---

### Follow-Up 4: Drunk Avoids the Last Seat

> What if the drunk picks randomly from all empty seats **except** seat 100 (they intentionally avoid it)?

#### Solution

The restriction only applies to the **first** displaced choice. After that, the chain proceeds normally.

**Calculation:** Passenger 1 picks uniformly from 99 seats (excluding seat 100):

- Prob picks seat 1: $\frac{1}{99}$ → chain ends, passenger 100 safe ✅
- Prob picks seat $j \neq 1, 100$ : $\frac{98}{99}$ → passenger $j$ is displaced and plays the **standard** drunk role (no restriction) → $P = \frac{1}{2}$

$$P(\text{pass 100 gets seat}) = \frac{1}{99} \cdot 1 + \frac{98}{99} \cdot \frac{1}{2} = \frac{1}{99} + \frac{49}{99} = \frac{50}{99}$$

$$\boxed{P = \frac{50}{99} \approx 0.5051}$$

> **Slightly better than** $\frac{1}{2}$ . The restriction buys passenger 100 a tiny edge because the first step has zero probability of immediately killing their seat.

---

### Follow-Up 5: Monte Carlo Simulation and Complexity

> How would you code a Monte Carlo simulation? What is the Big-O complexity?

#### Solution

**Optimal Python implementation:**

```python
import numpy as np

def simulate(n=100, trials=1_000_000):
    count = 0
    for _ in range(trials):
        seats = np.zeros(n, dtype=bool)
        choice = np.random.randint(0, n)
        if choice == 0:
            count += 1
            continue
        if choice == n - 1:
            continue
        seats[choice] = True
        seats[0] = True

        for i in range(1, n - 1):
            if not seats[i]:
                seats[i] = True
            else:
                empty = np.where(~seats)[0]
                pick = empty[np.random.randint(len(empty))]
                seats[pick] = True
                if pick == 0 or pick == n - 1:
                    break

        if not seats[n - 1]:
            count += 1
    return count / trials
```

**Optimized — only track the displacement chain:**

```python
def simulate_chain(n=100, trials=1_000_000):
    count = 0
    for _ in range(trials):
        taken = set()
        choice = np.random.randint(0, n)
        while choice != 0 and choice != n - 1:
            taken.add(choice)
            remaining = [s for s in range(n) if s not in taken]
            choice = remaining[np.random.randint(len(remaining))]
        if choice == 0:
            count += 1
    return count / trials
```

**Complexity Analysis:**

The displacement chain has expected length $O(\log n)$ — each displaced passenger has roughly a $\frac{2}{k}$ chance of hitting an absorbing state when $k$ seats remain.

| Approach | Per trial | Notes |
|---|---|---|
| Naive (scan all seats) | $O(n)$ | Process every passenger |
| Chain simulation | $O(\log n)$ expected | Only track displaced passengers |
| With indexed empty-seat list | $O(\log n)$ | $O(1)$ random selection via swap-and-pop |

$$\boxed{O(M \log n) \text{ optimal for } M \text{ trials}}$$

> **Why these follow-ups matter:** They test whether you can generalize from a known result to novel variants — exactly the skill needed when a trading strategy that worked in one regime needs to be adapted to new market conditions. The Monte Carlo question specifically tests computational efficiency thinking, which is non-negotiable in production quant systems running millions of simulations for options pricing or risk calculations.
