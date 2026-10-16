+++
title = "Group Testing #2: Adaptive Group Testing and Hwang's Algorithm"
date = '2026-10-16T10:05:00+07:00'
draft = true
tags = ["group-testing", "adaptive", "binary-splitting", "hwang", "information-theory", "algorithms"]
categories = ["Mathematics", "Group Testing"]
math = true
description = "Investigating information-theoretic lower bounds and classical adaptive group testing algorithms, highlighted by Hwang's Generalized Binary Splitting."
summary = "Analyzing lower bounds from an entropy perspective, binary splitting search for a single defective, and Hwang's Generalized Binary Splitting algorithm achieving asymptotically optimal O(d log(N/d))."
+++

## 1. What Is Adaptive Group Testing?

In our previous article, we examined Dorfman's two-stage procedure. That represents a basic adaptive mechanism: Stage 1 test results dictate which samples undergo individual testing in Stage 2.

If we allow multiple sequential rounds — observing the outcome of each test before determining the composition of the next — we enter the realm of **Fully Adaptive Group Testing**.

> **Central Question:** Endowed with real-time feedback, what is the absolute theoretical minimum number of tests required to identify $d$ defectives among $N$ items?

---

## 2. Information-Theoretic Lower Bound

Consider identifying a defective subset $D \subseteq \{1, 2, \dots, N\}$ containing exactly $d$ defectives among $N$ total items:
- The total number of candidate defective configurations is given by the binomial coefficient:
  $$|\Omega| = \binom{N}{d}$$

Each binary test outcome $y \in \{0, 1\}$ supplies at most $1$ bit of Shannon information ($\log_2 2 = 1$).

Consequently, any algorithm capable of distinguishing between all $\binom{N}{d}$ configurations must satisfy the decision tree bound:
$$
2^T \ge \binom{N}{d} \implies T \ge \left\lceil \log_2 \binom{N}{d} \right\rceil
$$

Employing Stirling's approximation or combinatorial bounds:
$$
\binom{N}{d} \ge \left( \frac{N}{d} \right)^d \implies T \ge d \log_2 \left( \frac{N}{d} \right)
$$

> **Theorem (Entropy Lower Bound):** Any group testing algorithm (whether adaptive or non-adaptive) requires at least:
> $$T \ge d \log_2 \left( \frac{N}{d} \right)$$
> tests in the worst case.

---

## 3. Foundational Case: $d = 1$ and Binary Splitting

Consider the special case where exactly $d = 1$ defective item is known to exist among $N$ candidates:

1. Partition the candidate set into two equal halves: $A$ and $B$ ($|A| = \lfloor N/2 \rfloor$, $|B| = \lceil N/2 \rceil$).
2. Test subset $A$:
   - If $A$ tests positive ($y = 1$): The defective item is contained in $A$; discard $B$.
   - If $A$ tests negative ($y = 0$): Since a defective item is guaranteed to exist, it must reside in $B$; discard $A$ (no test required for $B$!).
3. Recursively bisect the remaining candidate set until a single element remains.

Total tests consumed equals:
$$
T = \lceil \log_2 N \rceil
$$
This precisely matches the information-theoretic lower bound for $d = 1$.

---

## 4. Hwang's Generalized Binary Splitting Algorithm (GBSA - 1972)

When $d > 1$, naïve bisection loses efficiency because both halves may frequently test positive. In 1972, mathematician **Frank K. Hwang** introduced the **Generalized Binary Splitting Algorithm (GBSA)**, an asymptotically optimal algorithm that matches the information-theoretic lower bound.

### 4.1. Core Intuition Behind GBSA

Suppose that at a given iteration, $d'$ defectives remain to be uncovered within an active candidate pool of size $n'$.

Rather than bisecting the entire pool $n'$ (which would almost surely test positive when $n'$ is large), Hwang proposes extracting a subset $S$ whose size is a power of 2:
$$
|S| = 2^\alpha, \quad \text{where } \alpha = \left\lfloor \log_2 \left( \frac{n' - d' + 1}{d'} \right) \right\rfloor
$$

### 4.2. Algorithm Execution Steps

1. **Step 1 (Test Block $S$):** Test the entire group $S$.
   - **Case 1 (Negative - $y = 0$):** All $2^\alpha$ items in $S$ are certified clean. Discard $S$ while keeping the defective count $d'$ unchanged.
   - **Case 2 (Positive - $y = 1$):** Subset $S$ contains at least one defective. Proceed to localized binary search.
2. **Step 2 (Localized Binary Search):**
   - Execute binary bisection on block $S$ to isolate exactly one defective item.
   - Because $|S| = 2^\alpha$, this isolation requires exactly $\alpha$ tests.
   - Unresolved elements from $S$ are returned to the candidate pool.
3. **Update:** Decrement the remaining defectives $d' \leftarrow d' - 1$ and repeat until all $d$ defectives are uncovered.

### 4.3. Complexity Analysis of Hwang GBSA

Hwang proved that the total number of tests $T_{GBSA}$ never exceeds:

$$
T_{GBSA} \le d \log_2 \left( \frac{N}{d} \right) + O(d)
$$

Compared against the lower bound $d \log_2 (N/d)$, Hwang's algorithm diverges only by an additive linear term in $d$, making it asymptotically optimal!

---

## 5. Li's $s$-Stage Multi-Round Algorithm

Hwang's algorithm is strictly sequential: each measurement depends on earlier outcomes (decision tree depth reaches $O(d \log(N/d))$). In high-throughput laboratory robotics, waiting for hundreds of sequential iterations is impractical.

In 1962, C. H. Li proposed an $s$-stage compromise:
- Balances test count against the number of feedback rounds.
- Stage $1$: Screen large coarse pools of size $k_1$.
- Stage $2$: Positive pools are partitioned into finer pools of size $k_2 < k_1$.
- ...
- Stage $s$: Individual confirmation.

---

## 6. Evaluating Adaptive Group Testing

| Metric | Advantages | Limitations |
| :--- | :--- | :--- |
| **Test Count ($T$)** | Achieves optimal lower bound $O(d \log(N/d))$. | Cannot be reduced further. |
| **Turnaround Latency** | N/A | **Substantial**, requiring sequential waiting between rounds. |
| **Automation** | N/A | Incompatible with mass-parallel liquid-handling robotics. |

Due to sequential latency constraints, researchers posed the compelling question: **Can we design all tests a priori and execute them simultaneously in parallel?**

We address this in our next article on **Non-Adaptive Group Testing**.

---

## 7. References

1. **Hwang, F. K. (1972).** *A method for detecting all defective members in a population by group testing.* Journal of the American Statistical Association, 67(339), 605-608.
2. **Li, C. H. (1962).** *A sequential method for screening experimental variables.* Journal of the American Statistical Association, 57(298), 455-477.
3. **Du, D. Z., & Hwang, F. K. (1993).** *Combinatorial Group Testing and Its Applications.* World Scientific Publishing.
