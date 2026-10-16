+++
title = "Group Testing #4: Decoding Algorithms for NAGT — COMP, SCOMP, and Definite Defectives (DD)"
date = '2026-10-16T10:15:00+07:00'
draft = true
tags = ["group-testing", "decoding", "comp", "scomp", "dd", "algorithms", "python"]
categories = ["Computer Science", "Group Testing"]
math = true
description = "Investigating classical decoding algorithms in Non-Adaptive Group Testing: COMP, Definite Defectives (DD), Sequential COMP (SCOMP), accompanied by Python implementations."
summary = "Recovering defective status vectors from Boolean measurements: negative test elimination with COMP, certifying confirmed defectives via DD, greedy Set Cover resolution with SCOMP, and complexity trade-offs."
+++

## 1. The Inverse Decoding Problem

Once the measurement matrix $\mathbf{M} \in \{0, 1\}^{t \times N}$ is synthesized and $t$ tests are performed in parallel, we observe the binary outcome vector:

$$
\mathbf{y} = (y_1, y_2, \dots, y_t)^T \in \{0, 1\}^t
$$

The decoding objective is:
> **Given matrix $\mathbf{M}$ and outcome vector $\mathbf{y}$, reconstruct the underlying status vector $\hat{\mathbf{x}} \in \{0, 1\}^N$ such that $\mathbf{M} \odot \hat{\mathbf{x}} = \mathbf{y}$ with maximum statistical accuracy and minimal computational complexity.**

This constitutes an inverse problem over Boolean algebra. Here we explore three fundamental decoding algorithms: **COMP**, **DD**, and **SCOMP**.

---

## 2. Combinatorial Orthogonal Matching Pursuit (COMP)

**COMP** is the simplest, fastest, and most pervasive baseline decoding algorithm in group testing.

### 2.1. Principle of Negative Test Elimination

Key observation:
> If test $i$ returns a negative outcome ($y_i = 0$), then **EVERY** item participating in test $i$ is guaranteed to be **CLEAN** ($x_j = 0$ for all $j$ where $M_{ij} = 1$).

Why? Because if even a single defective item were present in test $i$, the Boolean OR operation would have forced the outcome to $1$.

### 2.2. COMP Algorithm Procedure

1. **Initialization:** Presume all $N$ items are suspect candidates: $\hat{x}_j = 1$ for all $j \in \{1, \dots, N\}$.
2. **Scan Negative Tests:** For each test $i \in \{1, \dots, t\}$ where $y_i = 0$:
   - For all items $j$ with $M_{ij} = 1$, assign $\hat{x}_j = 0$ (definite elimination).
3. **Declaration:** Any item not eliminated in Step 2 is declared defective ($\hat{x}_j = 1$).

In algebraic terms:
$$
\hat{x}_j = 1 \iff \mathbf{m}_j \subseteq \mathbf{y}
$$
(meaning item $j$'s signature column is completely covered by the positive test indices).

### 2.3. Theoretical Properties of COMP

- **Zero False Negatives:** COMP never misclassifies a true defective as clean ($x_j = 1 \implies \hat{x}_j = 1$).
- **Susceptible to False Positives:** A clean item might participate exclusively in tests rendered positive by other defectives, causing COMP to misclassify it as defective.
- **Time Complexity:** Exceptionally fast, demanding only $O(t \cdot N)$ bitwise operations.

---

## 3. Definite Defectives (DD) Algorithm

To curb false positive rates inherent in COMP, the **Definite Defectives (DD)** algorithm adds a deductive inference layer.

### 3.1. Core Intuition

After COMP eliminates all certifiably clean items, inspect each positive test $i$ ($y_i = 1$):
- The set of remaining candidate defectives in test $i$ is:
  $$C_i = \{j : M_{ij} = 1 \text{ and } \hat{x}_j^{(COMP)} = 1\}$$
- If $|C_i| = 1$ (exactly one suspect candidate $j^* \in C_i$ remains):
  - Because test $i$ is positive, it must contain at least one defective.
  - Since $j^*$ is the sole candidate remaining in $C_i$, we deduce: **$j^*$ is definitively defective (Definite Defective)!**

### 3.2. DD Algorithm Procedure

1. Run COMP to identify the candidate set $S_{cand} = \{j : \hat{x}_j^{(COMP)} = 1\}$.
2. Initialize confirmed defectives: $S_{DD} = \emptyset$.
3. For each test $i$ where $y_i = 1$:
   - Evaluate active candidates: $C_i = \{j \in S_{cand} : M_{ij} = 1\}$.
   - If $|C_i| = 1$, append the single item $j^* \in C_i$ to $S_{DD}$.
4. Declare items in $S_{DD}$ defective; mark unconfirmed candidates in $S_{cand} \setminus S_{DD}$ as ambiguous or apply thresholding.

---

## 4. Sequential COMP (SCOMP) Algorithm

The **Sequential COMP (SCOMP)** algorithm pairs DD deduction with a greedy heuristic for the classic **Set Cover Problem**.

1. **Step 1:** Run COMP to eliminate clean items, obtaining candidate pool $S_{cand}$.
2. **Step 2:** Run DD to extract confirmed defectives $S_{DD}$. Mark positive tests already accounted for by $S_{DD}$ as satisfied.
3. **Step 3 (Greedy Coverage):**
   - While unexplained positive tests remain:
   - Identify the candidate in $S_{cand} \setminus S_{DD}$ whose signature explains the greatest number of currently unsatisfied positive tests.
   - Add this candidate to the defective set and mark its tests as satisfied. Repeat until all positive outcomes are fully explained.

SCOMP exhibits empirical recovery performance closely approaching information-theoretic limits, sharply suppressing both false positives and false negatives.

---

## 5. Comparative Overview of Decoding Algorithms

| Algorithm | Computational Complexity | False Negatives | False Positives | Practical Utility |
| :--- | :--- | :--- | :--- | :--- |
| **COMP** | $O(t \cdot N)$ | $0\%$ (Guaranteed zero) | Occasional when $t$ is small | Extremely fast, hardware-friendly |
| **DD** | $O(t \cdot N)$ | Small omission rate | Near zero (exact for $S_{DD}$) | Fast, conservative |
| **SCOMP** | $O(t \cdot N + d \cdot t \cdot |S_{cand}|)$ | Very low | Very low | Superb balance of fidelity and speed |
| **Combinatorial Optimal (Exhaustive)** | $O\left(t \cdot \binom{N}{d}\right)$ | $0\%$ | Minimal | Intractable for large $N, d$ (NP-hard) |

---

## 6. Python Implementation

The following self-contained script illustrates COMP and DD execution:

```python
import numpy as np

def generate_group_testing_data(n=1000, d=5, t=100, seed=42):
    np.random.seed(seed)
    # Generate ground-truth status vector with d defective items
    x_true = np.zeros(n, dtype=int)
    defective_indices = np.random.choice(n, size=d, replace=False)
    x_true[defective_indices] = 1
    
    # Random Bernoulli measurement matrix with density p = 1 / (d + 1)
    p = 1.0 / (d + 1)
    M = (np.random.rand(t, n) < p).astype(int)
    
    # Compute measurement vector y via Boolean OR
    y = np.clip(M @ x_true, 0, 1)
    
    return M, y, x_true, defective_indices

def decode_comp(M, y):
    t, n = M.shape
    x_hat = np.ones(n, dtype=int)
    
    # Eliminate any candidate participating in negative tests (y_i = 0)
    for i in range(t):
        if y[i] == 0:
            clean_indices = np.where(M[i, :] == 1)[0]
            x_hat[clean_indices] = 0
            
    return x_hat

def decode_dd(M, y, x_comp):
    t, n = M.shape
    x_dd = np.zeros(n, dtype=int)
    candidates = np.where(x_comp == 1)[0]
    
    for i in range(t):
        if y[i] == 1:
            # Filter active candidates in test i
            active_in_test = [j for j in candidates if M[i, j] == 1]
            if len(active_in_test) == 1:
                x_dd[active_in_test[0]] = 1
                
    return x_dd

if __name__ == "__main__":
    M, y, x_true, true_defectives = generate_group_testing_data(n=500, d=5, t=60)
    
    x_comp = decode_comp(M, y)
    x_dd = decode_dd(M, y, x_comp)
    
    print(f"True Defective Indices: {sorted(true_defectives)}")
    print(f"COMP Recovered:         {sorted(np.where(x_comp == 1)[0])}")
    print(f"DD Certified:           {sorted(np.where(x_dd == 1)[0])}")
```

---

## 7. References

1. **Aldridge, M., Baldassini, L., & Johnson, O. (2014).** *Group testing algorithms: bounds and simulations.* IEEE Transactions on Information Theory, 60(6), 3671-3687.
2. **Chan, C. L., Cai, S., & Zheng, H. (2011).** *Non-adaptive group testing with definite defectives.* IEEE Transactions on Information Theory.
3. **Aldridge, M. (2017).** *The capacity of noiseless and noisy non-adaptive group testing.* IEEE Transactions on Information Theory.
