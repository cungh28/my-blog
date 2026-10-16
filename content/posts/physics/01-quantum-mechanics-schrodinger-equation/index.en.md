+++
title = "Quantum Mechanics #1: The Schrödinger Equation and Wave-Particle Duality"
date = '2026-10-16T14:30:00+07:00'
draft = false
hideMeta = false
tags = ["physics", "quantum mechanics", "schrodinger equation", "wave function", "uncertainty principle"]
categories = ["Physics", "Science"]
math = true
slug = "quantum-mechanics-schrodinger-equation"
description = "Exploring the foundations of quantum mechanics through the Schrödinger wave equation, Heisenberg's uncertainty principle, and wave function collapse."
summary = "An in-depth overview of the time-dependent Schrödinger equation, Born's probabilistic interpretation of the wave function, and Heisenberg's uncertainty principle."
+++

In the early decades of the 20th century, classical physics founded by Isaac Newton and James Clerk Maxwell confronted insurmountable paradoxes: blackbody radiation, the photoelectric effect, and the stability of atomic structures. To resolve this foundational crisis, quantum mechanics emerged, fundamentally transforming our understanding of the physical fabric of reality.

At the core of non-relativistic quantum theory lies the Schrödinger equation — a partial differential equation dictating the deterministic time-evolution of a quantum state.

## 1. De Broglie's Hypothesis and Wave-Particle Duality

In 1924, Louis de Broglie formulated a revolutionary hypothesis: if electromagnetic waves possess particle-like characteristics (Einstein's photons), matter particles such as electrons must conversely exhibit wave-like behaviors.

The De Broglie wavelength $\lambda$ is inversely proportional to particle momentum $p$:

$$\lambda = \frac{h}{p} = \frac{2\pi\hbar}{p}$$

where $h$ denotes Planck's constant and $\hbar = \frac{h}{2\pi}$ represents the reduced Planck constant.

A direct consequence of this duality is that the state of a microscopic particle cannot be described by classical coordinates $\mathbf{r}(t)$ and velocity $\mathbf{v}(t)$. Instead, it is represented by a complex wave function $\Psi(\mathbf{r}, t)$.

## 2. The Time-Dependent Schrödinger Equation

In 1926, Erwin Schrödinger established the wave equation governing the dynamics of $\Psi(\mathbf{r}, t)$:

$$i\hbar \frac{\partial}{\partial t}\Psi(\mathbf{r}, t) = \hat{H}\Psi(\mathbf{r}, t)$$

where:
- $i = \sqrt{-1}$ is the imaginary unit.
- $\hat{H}$ is the Hamiltonian operator, corresponding to the total energy operator of the physical system.

For a particle of mass $m$ subject to a potential field $V(\mathbf{r}, t)$, the Hamiltonian operator takes the form:

$$\hat{H} = -\frac{\hbar^2}{2m}\nabla^2 + V(\mathbf{r}, t)$$

with $\nabla^2 = \frac{\partial^2}{\partial x^2} + \frac{\partial^2}{\partial y^2} + \frac{\partial^2}{\partial z^2}$ representing the Laplacian operator. In one spatial dimension, the Schrödinger equation explicitly reads:

$$i\hbar \frac{\partial \Psi(x, t)}{\partial t} = -\frac{\hbar^2}{2m}\frac{\partial^2 \Psi(x, t)}{\partial x^2} + V(x, t)\Psi(x, t)$$

This equation is linear, first-order in time, and second-order in space. Linearity guarantees the quantum superposition principle: if $\Psi_1$ and $\Psi_2$ are valid solutions, any linear combination $c_1\Psi_1 + c_2\Psi_2$ represents an admissible quantum state.

## 3. Born's Probabilistic Interpretation

Because $\Psi(x, t)$ is a complex-valued quantity, it cannot be measured directly. The physical interpretation of the wave function was clarified by Max Born through the Born rule:

The probability density of locating the particle at position $x$ at time $t$ is proportional to the square modulus of the wave function:

$$\rho(x, t) = |\Psi(x, t)|^2 = \Psi^*(x, t)\Psi(x, t)$$

The probability of finding the particle within an interval $[a, b]$ at time $t$ is obtained by integration:

$$P(a \le x \le b) = \int_a^b |\Psi(x, t)|^2 dx$$

Because the particle must exist somewhere in physical space, the wave function satisfies the normalization condition:

$$\int_{-\infty}^{\infty} |\Psi(x, t)|^2 dx = 1$$

The conservation of probability theorem dictates that if a wave function is normalized at $t = 0$, the Schrödinger equation preserves this normalization across all future times $t > 0$, guaranteed by the self-adjoint (Hermitian) nature of the Hamiltonian operator $\hat{H}^\dagger = \hat{H}$.

## 4. Stationary States and the Time-Independent Equation

When the potential energy $V(x)$ is time-independent, the Schrödinger equation can be solved using separation of variables:

$$\Psi(x, t) = \psi(x) \phi(t)$$

Substituting this product into the wave equation yields a separation constant $E$ (the energy eigenvalue):

$$\frac{i\hbar}{\phi(t)}\frac{d\phi(t)}{dt} = \frac{1}{\psi(x)}\left[ -\frac{\hbar^2}{2m}\frac{d^2\psi(x)}{dx^2} + V(x)\psi(x) \right] = E$$

The temporal component yields a harmonic phase factor:

$$\phi(t) = e^{-iEt/\hbar}$$

The spatial component produces the **Time-Independent Schrödinger Equation**:

$$\hat{H}\psi(x) = E\psi(x)$$

Explicitly:

$$-\frac{\hbar^2}{2m}\frac{d^2\psi(x)}{dx^2} + V(x)\psi(x) = E\psi(x)$$

This represents an eigenvalue problem: energy eigenvalues $E_n$ assume discrete values (quantization) whenever boundary conditions confine the particle within a localized potential (such as infinite potential wells, quantum harmonic oscillators, or hydrogen atoms).

In stationary states, the probability density remains completely static over time:

$$|\Psi(x, t)|^2 = |\psi(x) e^{-iEt/\hbar}|^2 = |\psi(x)|^2$$

## 5. Heisenberg's Uncertainty Principle

A rigorous mathematical consequence of wave mechanics is the Heisenberg Uncertainty Principle (1927). For any two non-commuting Hermitian operators $\hat{A}$ and $\hat{B}$, the standard deviations of measurement outcomes satisfy the Robertson-Schrödinger relation:

$$\sigma_A \sigma_B \ge \frac{1}{2}|\langle [\hat{A}, \hat{B}] \rangle|$$

For the position operator $\hat{x} = x$ and momentum operator $\hat{p} = -i\hbar \frac{\partial}{\partial x}$, the fundamental canonical commutation relation is:

$$[\hat{x}, \hat{p}] = \hat{x}\hat{p} - \hat{p}\hat{x} = i\hbar$$

yielding the canonical position-momentum uncertainty relation:

$$\sigma_x \sigma_p \ge \frac{\hbar}{2}$$

This demonstrates that uncertainty is not a deficiency of experimental measurement apparatus, but an intrinsic, structural feature of quantum reality. A quantum entity cannot possess simultaneously well-defined position and momentum values.

## 6. Summary and Outlook

The Schrödinger equation provided the mathematical bedrock of quantum mechanics, inaugurating rapid advancements across solid-state physics, quantum chemistry, semiconductor technology, and quantum information processing.

In our next article, we transition to a statistical paradigm to explore how microscopic quantum particles collectively give rise to macroscopic thermodynamics in **[Article 2: Statistical Mechanics: Micro to Macro: Entropy and the Arrow of Time](../statistical-mechanics-entropy-arrow-of-time/)**.
