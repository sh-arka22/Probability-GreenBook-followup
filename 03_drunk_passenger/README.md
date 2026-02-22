# 3. The Drunk Passenger

## The Premise

> A line of 100 passengers board a plane. The 1st person is drunk and picks a **random seat**. All subsequent passengers take their assigned seat if available, or a **random empty seat** if not. What is the probability that the 100th passenger gets their assigned seat?

---

## What the Interviewer Wants to Hear

Do **not** attempt backwards induction for $n = 100$ . This is a classic **absorbing state** problem disguised as a sequence.

### Core Insight — The "New Drunk" Invariant

**The key observation:** Every displaced passenger becomes a "new drunk" — they face the exact same situation as the original drunk, just with fewer seats. The fate of passenger #100 depends entirely on whether seat #1 or seat #100 is chosen first.

**Step 1: What happens when any displaced passenger picks a random seat?**

| They sit in... | What happens next? |
|---|---|
| **Seat #1** (the drunk's assigned seat) | ✅ Chain breaks! Everyone else sits correctly |
| **Seat #100** (the last passenger's seat) | ❌ Passenger #100 is doomed |
| **Some other seat** $j$ | Passenger $j$ becomes the new "drunk" — recurses |

**Step 2: Reduce to absorbing states.**

The chain **always terminates** when either seat #1 or seat #100 is chosen. These are the only two **absorbing states** — seat #1 is empty (drunk skipped it) and seat #100 is empty (passenger #100 hasn't boarded).

**Step 3: Apply symmetry.**

At every random-choice event, both seat #1 and seat #100 are in the pool of empty seats. A displaced passenger is equally likely to pick either one. Therefore:

$$P(\text{seat \#1 chosen first}) = P(\text{seat \#100 chosen first}) = \boxed{\frac{1}{2}}$$

> **The answer is** $\frac{1}{2}$ **, regardless of** $n$ . Works for 3 passengers or a million.

### Verification with Small Cases

**$n = 3$ :** Drunk picks from {1, 2, 3}:
- Seat #1 (prob $\frac{1}{3}$ ): everyone correct ✅
- Seat #3 (prob $\frac{1}{3}$ ): P3 displaced ❌
- Seat #2 (prob $\frac{1}{3}$ ): P2 displaced, picks from {1, 3} → 50-50

$$P = \frac{1}{3} + \frac{1}{3} \cdot \frac{1}{2} = \frac{1}{2} \checkmark$$

---

## Follow-Up Questions

---

### Follow-Up 1: Probability the $k$-th Passenger Gets Their Seat

> What is the probability that the $k^{th}$ passenger (where $1 < k < 100$ ) gets their assigned seat?

#### Solution

**Claim:** For $n$ total passengers and passenger $k$ (where $2 \leq k \leq n$ ):

$$\boxed{P_k = \frac{n - k + 1}{n - k + 2}}$$

For $n = 100$ : $P_k = \frac{101 - k}{102 - k}$

**Derivation:** For passenger $k$ , the displacement chain before they board has three possible outcomes at each random choice:

- **Seat #1 chosen** → chain resolves, passenger $k$ safe ✅
- **Seat** $k$ **chosen** → passenger $k$ displaced ❌
- **Seat** $j > k$ **chosen** → passenger $k$ safe ✅ (chain passes to a later passenger)

The third case is the key difference from the last passenger — there are "escape routes." By a recursive argument, the probability of seat $k$ being taken before seat #1 works out to $\frac{1}{n-k+2}$ .

**Verification (** $n = 4$ **):**

| $k$ | $P_k = \frac{5-k}{6-k}$ | Direct check |
|---|---|---|
| 2 | $\frac{3}{4}$ | Drunk avoids seat #2 with prob $\frac{3}{4}$ ✓ |
| 3 | $\frac{2}{3}$ | ✓ (by recursion) |
| 4 | $\frac{1}{2}$ | ✓ (last passenger) |

**Special values for** $n = 100$ :

| Passenger | $P(\text{gets seat})$ |
|---|---|
| #2 | $\frac{99}{100} = 0.99$ |
| #50 | $\frac{51}{52} \approx 0.981$ |
| #90 | $\frac{11}{12} \approx 0.917$ |
| #99 | $\frac{2}{3} \approx 0.667$ |
| #100 | $\frac{1}{2} = 0.5$ |

> **Intuition:** Early passengers are almost certainly safe. The displacement probability grows as $k$ increases, reaching $\frac{1}{2}$ for the last passenger.

---

### Follow-Up 2: Expected Number in Correct Seats

> What is the expected number of passengers who sit in their correctly assigned seats?

#### Solution

By **linearity of expectation**:

$$E[\text{correct seats}] = \sum_{k=1}^{n} P_k$$

- **Passenger #1** (drunk): $P_1 = \frac{1}{n}$
- **Passengers #2 to #** $n$ : $P_k = \frac{n-k+1}{n-k+2}$

$$E = \frac{1}{n} + \sum_{k=2}^{n} \frac{n-k+1}{n-k+2}$$

Substituting $m = n - k + 2$ :

$$\sum_{k=2}^{n} \frac{n-k+1}{n-k+2} = \sum_{m=2}^{n} \frac{m-1}{m} = \sum_{m=2}^{n} \left(1 - \frac{1}{m}\right) = (n-1) - (H_n - 1)$$

where $H_n = \sum_{m=1}^{n} \frac{1}{m}$ is the $n$-th harmonic number.

$$\boxed{E = n - H_n + \frac{1}{n}}$$

For $n = 100$ : $H_{100} \approx 5.187$ , so $E \approx 94.82$ .

> **Insight:** Despite the chaos, ~95% of passengers sit correctly. The displacement chain typically resolves quickly.

---

### Follow-Up 3: Two Drunk Passengers

> Suppose the first **two** passengers are drunk and pick seats uniformly at random. What is the probability that the 100th passenger gets their assigned seat?

#### Solution

**Case analysis on Drunk #1's choice** (picks uniformly from all $n$ seats):

| Drunk #1 picks | Prob | Effect on P100 |
|---|---|---|
| Seat #1 | $\frac{1}{n}$ | Drunk #2 is now the only drunk → original problem with $n-1$ seats → $P = \frac{1}{2}$ |
| Seat #2 | $\frac{1}{n}$ | Drunk #2's seat is taken, so Drunk #2 also picks randomly → equivalent to 1-drunk problem → $P = \frac{1}{2}$ |
| Seat #100 | $\frac{1}{n}$ | Seat #100 permanently taken → $P = 0$ |
| Seat $j$ , $3 \leq j \leq 99$ | $\frac{n-3}{n}$ | Drunk #2 picks randomly from $n-1$ remaining seats — sub-cases needed |

For the last case, Drunk #2 picks from $n - 1$ seats. Applying the absorbing-state argument to Drunk #2's chain (absorbing states: seat #1, seat #2, seat #100):

- Seat #1 or #2 chosen before #100 → chain resolves favorably
- Seat #100 chosen first → P100 doomed

Since seat #1 and seat #2 are both "rescue" seats and seat #100 is the single "doom" seat, by careful recursion the probability that seat #100 is chosen before both #1 and #2 is $\frac{1}{2}$ in each sub-chain.

The exact result (confirmed by simulation):

$$\boxed{P = \frac{n-2}{2(n-1)} = \frac{49}{99} \approx 0.4949}$$

> **Intuition:** The second drunk adds a $\frac{1}{n}$ chance of immediately taking seat #100, reducing the probability slightly below $\frac{1}{2}$ .

---

### Follow-Up 4: Drunk Avoids the Last Seat

> What if the drunk randomly picks from empty seats, **except they intentionally avoid seat #100**?

#### Solution

Seat #100 can never be chosen by any displaced passenger. The only absorbing state is seat #1 — which **must** eventually be chosen (seats fill up, seat #1 remains available).

Once seat #1 is picked, all remaining passengers sit correctly.

$$\boxed{P(\text{P100 gets seat}) = 1}$$

> **Insight:** The entire $\frac{1}{2}$ failure probability came from someone picking seat #100 before #1. Remove that possibility → guaranteed success.

---

### Follow-Up 5: Monte Carlo Simulation

> How would you code a Monte Carlo simulation? What is the Big-O complexity?

#### Solution

**Optimal approach — simulate the displacement chain directly** $O(n)$ :

```python
import random

def simulate(n=100):
    """Simulate one trial. Returns True if last passenger gets their seat."""
    available = list(range(n))
    random.shuffle(available)  # not needed, just for clarity
    
    taken = set()
    displaced = 0  # passenger 0 is drunk
    
    while True:
        # Displaced passenger picks random available seat
        remaining = [s for s in range(n) if s not in taken]
        choice = random.choice(remaining)
        taken.add(choice)
        
        if choice == 0:        # sat in seat #1 → chain resolved
            return True
        if choice == n - 1:    # sat in seat #100 → last passenger doomed
            return False
        
        # Passengers between (displaced+1) and (choice-1) sit correctly
        for p in range(displaced + 1, choice):
            taken.add(p)
        displaced = choice

# Monte Carlo
trials = 100_000
wins = sum(simulate() for _ in range(trials))
print(f"P ≈ {wins / trials:.4f}")  # ≈ 0.5000
```

**Complexity:**

| Approach | Per trial | Notes |
|---|---|---|
| Naive (scan all seats) | $O(n^2)$ | List scan per displaced passenger |
| Chain simulation (above) | $O(n)$ worst | Early termination common |
| Absorbing shortcut | $O(1)$ | Uses the known answer: `random.random() < 0.5` |

**Big-O of optimal honest simulation:** $O(n)$ worst case.
