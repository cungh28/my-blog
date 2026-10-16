+++
title = "Group Testing #5: Noisy Group Testing and Probabilistic Recovery"
date = '2026-10-16T10:20:00+07:00'
draft = true
tags = ["group-testing", "noisy", "probability", "belief-propagation", "channel-capacity", "information-theory"]
categories = ["Mathematics", "Information Theory"]
math = true
description = "Analyzing group testing under noisy observations (false positives and false negatives), channel noise models, and robust decoding algorithms."
summary = "Modeling real-world errors in PCR and biosciences: binary symmetric channels (BSC), Z-channels, error-tolerant disjunct matrices, Noisy COMP, Linear Programming, and Belief Propagation."
+++

## 1. When Theory Meets Reality: The Emergence of Noise

In previous discussions, we operated under idealized premises:
- If a pool contains zero defective specimens $\rightarrow$ The test yields $0$ (100% certainty).
- If a pool contains at least one defective specimen $\rightarrow$ The test yields $1$ (100% certainty).

In wet-lab environments (such as molecular RT-qPCR testing):
1. **Cross-contamination:** Aerosolized viral particles drifting into adjacent wells produce **False Positives**.
2. **Dilution effects & reagent variations:** When excessive samples are co-mingled into a single reaction vessel, viral concentrations drop below the instrument's Limit of Detection (LoD), generating **False Negatives**.

A single measurement inversion from **$1 \to 0$ (false negative)** causes classic COMP to immediately discard a truly infected patient!

Formulating a rigorous theory of **Noisy Group Testing** is therefore indispensable for real-world deployment.

---

## 2. Mathematical Noise Channel Models

Let $u_i = \bigvee_{j=1}^N M_{ij} x_j \in \{0, 1\}$ represent the noiseless Boolean test measurement for pool $i$. The actual noisy observation is $y_i \in \{0, 1\}$.

We model the correspondence between $u_i$ and $y_i$ via a transition channel $P(y_i \mid u_i)$:

### 2.1. Binary Symmetric Channel (BSC)

Symmetric bit-flip noise characterized by error crossover probability $q \in [0, 0.5)$:
$$
P(y_i = 1 \mid u_i = 0) = q \quad \text{(False Positive)}
$$
$$
P(y_i = 0 \mid u_i = 1) = q \quad \text{(False Negative)}
$$

### 2.2. Z-Channel (Asymmetric Noise)

In biochemical assays, false positive and false negative error rates are often starkly asymmetric:
$$
P(y_i = 1 \mid u_i = 0) = q_0 \quad (\text{typically minute, } \approx 0)
$$
$$
P(y_i = 0 \mid u_i = 1) = q_1 \quad (\text{driven by dilution, } q_1 > 0)
$$

---

## 3. Error-Tolerant Disjunct Matrices

To withstand $e$ arbitrary adversarial measurement corruptions, incidence matrix $\mathbf{M}$ must incorporate minimum Hamming distance separation:

> **Definition ($(d, e)$-disjunct):** A matrix $\mathbf{M}$ is $(d, e)$-disjunct if for every subset $S \subseteq \{1, \dots, N\}$ with $|S| \le d$ and every $k \notin S$, column $\mathbf{m}_k$ possesses at least $e + 1$ support positions uncovered by the Boolean sum of columns in $S$:
> $$
> \left| \text{supp}(\mathbf{m}_k) \setminus \bigcup_{j \in S} \text{supp}(\mathbf{m}_j) \right| \ge e + 1
> $$

Under this structural guarantee, even if up to $e$ measurements are inverted by channel noise, column $\mathbf{m}_k$ retains at least one uncorrupted witness row ensuring unambiguous classification.

The measurement scaling required to combat adversarial errors scales as:
$$
t = O((d^2 + d \cdot e) \log N)
$$

---

## 4. Noise-Resilient Decoding Algorithms

### 4.1. Noisy COMP (Threshold-Based COMP)

Rather than immediately discarding a candidate upon encountering a single negative test, Noisy COMP tallies contradictions:
- For each item $j$, compute the discrepancy count:
  $$v_j = \sum_{i: M_{ij} = 1} \mathbb{I}(y_i = 0)$$
- **Decision Rule:** If $v_j \le \tau$ (where $\tau$ is a tuned error tolerance threshold), declare item $j$ defective ($\hat{x}_j = 1$). If discrepancies exceed $\tau$, classify item $j$ as clean.

### 4.2. Linear Programming (LP) Decoding

Relax the combinatorial integer program into a continuous linear program:
$$
\min_{\mathbf{x} \in [0, 1]^N, \mathbf{\xi} \in \mathbb{R}^t} \sum_{j=1}^N x_j + \lambda \sum_{i=1}^t \xi_i
$$
subject to linear constraints modeling channel observation penalties. This framework links group testing to linear programming decoding of error-correcting codes.

### 4.3. Belief Propagation (BP) on Factor Graphs

Formulate the decoding task over a bipartite Factor Graph:
- Variable nodes representing candidates $x_1, \dots, x_N$.
- Check / Factor nodes representing measurements $y_1, \dots, y_t$.

Posterior log-likelihood messages iteratively circulate across edges:
$$
\mu_{j \to i}(x_j) \longleftrightarrow \mu_{i \to j}(x_j)
$$

When graph topologies are sparse (Sparse Factor Graphs), Belief Propagation achieves near-capacity recovery performance in $O(t \log t)$ time.

---

## 5. Channel Capacity of Noisy Group Testing

Scarlett & Cevher (2016) and Aldridge (2019) characterized the exact information-theoretic capacity for noisy non-adaptive group testing:

$$
C = 1 - H_2(q)
$$
where $H_2(q) = -q \log_2 q - (1-q) \log_2(1-q)$ is the binary entropy function.

The fundamental lower bound on test measurements under a BSC with noise rate $q$ is:
$$
T \ge \frac{d \log_2(N/d)}{1 - H_2(q)}
$$

This equation embodies mathematical elegance: **Channel noise degrades testing capacity by precisely the entropy rate of the noise, mirroring Claude Shannon's Channel Coding Theorem!**

---

## 6. Summary

Incorporating noise transforms Group Testing from pure combinatorics into a crossroads uniting Information Theory, Probability Theory, and Graphical Models.

In our concluding article, we survey the overarching landscape: the relationship between Group Testing and **Compressed Sensing**, alongside modern deployments in epidemiological screening and Big Data indexing.

---

## 7. References

1. **Scarlett, J., & Cevher, V. (2016).** *Phase transitions in group testing.* In Proceedings of the 2016 Annual ACM-SIAM Symposium on Discrete Algorithms (SODA).
2. **Aldridge, M. (2019).** *Individual testing is optimal for nonadaptive group testing under very high noise.* IEEE Transactions on Information Theory, 65(4), 2107-2114.
3. **Cheraghchi, M. (2013).** *Noise-resilient group testing: Limits and constructions.* IEEE Transactions on Information Theory, 59(9), 5760-5776.
