# Down-and-Out-Barrier-Call-Pricing-Greeks-Monte-Carlo-Implied-Volatility
Python implementation of a down-and-out European call: closed-form pricing, barrier sensitivities, finite-difference Delta, Monte Carlo with discretisation-bias analysis, and implied volatility by bisection.

## Overview

This project prices and analyses a down-and-out European call option in Python. It is Report 7 of the A.Y. 2025-2026 coursework. The closed-form price is compared with Monte Carlo simulation, the Greeks are studied around the barrier, and implied volatility is extracted by bisection.

### Parameters

| Parameter | Symbol | Value |
|---|---|---|
| Spot | S₀ | 100 |
| Strike | K | 100 |
| Barrier (down) | H | 90 |
| Maturity | T | 1 year |
| Risk-free rate | r | 3% |
| Volatility | σ | 20% |
| Rebate | | 0 |

### Closed-form pricing

The pricer implements the Haug barrier-option formula, built from six auxiliary building blocks. Flags select knock-in or knock-out, barrier above or below the spot, and call or put. The down-and-out call is cheaper than the vanilla call because the option dies if the barrier is touched. An in-out parity check (down-and-in + down-and-out = vanilla) shows a small residual, which points to an inconsistency in one of the building blocks.

| Quantity | Value |
|---|---|
| Down-and-out call | 7.8717 |
| Down-and-in call | 1.4539 |
| Vanilla call (BSM) | 9.4134 |
| In + Out | 9.3256 |
| Parity residual | 0.0878 |

### Sensitivities and near-barrier behaviour

The barrier level is varied from 50 to 99. For low barriers the price converges to the vanilla call, and as H approaches the spot it collapses towards zero. The down-and-in price is the mirror image. Delta is computed by central finite differences (ε = 0.01) and compared with the vanilla Delta. It jumps discontinuously at the barrier, which makes delta-hedging near H unstable and expensive.

| Check | Result |
|---|---|
| Price at H = 0.1 (vs. vanilla 9.4134) | 9.3256 |
| Price at H = 99.9 | 1.6921 |
| Price at S₀ = 88 (below barrier) | 0.000000 |
| Price at S₀ = 90.01 (just above barrier) | 0.897181 |
| Delta just above H = 90 | ~0.63 |
| Vanilla Delta at S₀ = 90 | ~0.40 |

### Monte Carlo simulation

Paths are simulated with exact log-normal GBM steps and the barrier is checked on the discrete grid through the path minimum. Because crossings between monitoring dates are missed, the estimate is biased upwards. Convergence studies show that the bias disappears only as the time grid gets finer, while more paths reduce only the noise.

| Method | Price |
|---|---|
| Closed form (Haug) | 7.8717 |
| Monte Carlo (m = 100, n = 100,000) | 8.3769 |

| m (steps), n = 20,000 | MC price |
|---|---|
| 10 | ~8.77 |
| 50 | ~8.37 |
| 100 | ~8.04 |
| 250 | ~7.92 |
| 500 | ~7.89 |
| 1,000 | ~7.93 |
| 2,500 | ~7.83 |

### Implied volatility

A bisection solver on [0.01, 1.50] (tolerance 10⁻⁵, at most 200 iterations), implemented for both the barrier and the vanilla option, recovers the true σ. A convergence plot shows the geometric decay of the error.

| Quantity | Barrier | Vanilla |
|---|---|---|
| Market price | 7.8717 | 9.4134 |
| Implied volatility | 0.2000 | 0.2000 |
| Iterations | 20 | 20 |
| Difference vs. true σ | 0.0000 | 0.0000 |

**Skills demonstrated:** exotic option pricing, numerical Greeks, Monte Carlo and discretisation bias, root-finding, Python (NumPy, Matplotlib).
