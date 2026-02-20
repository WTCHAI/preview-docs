# BioHashing Peer Review Guide

> **Purpose**: This document explains the mathematical foundations of BioHashing for professor peer review. It answers WHY each step matters, not just WHAT the steps are.

---

## Table of Contents

1. [Quick Reference: Papers to Read](#1-quick-reference-papers-to-read)
2. [Why Gram-Schmidt Orthogonalization Matters](#2-why-gram-schmidt-orthogonalization-matters)
3. [Why 50/50 Bit Distribution Matters](#3-why-5050-bit-distribution-matters)
4. [Why Gaussian Distribution for Random Matrix](#4-why-gaussian-distribution-for-random-matrix)
5. [Complete Algorithm with Paper References](#5-complete-algorithm-with-paper-references)
6. [Real Numbers Verification](#6-real-numbers-verification)
7. [Your ZK-BIOWN Contributions](#7-your-zk-biown-contributions)

---

## 1. Quick Reference: Papers to Read

### Primary Papers

| Paper | Citation | DOI | Key Content |
|-------|----------|-----|-------------|
| **[PAPER-1]** | Teoh, Ngo, Goh (2004) "BioHashing: Two factor authentication featuring fingerprint data and tokenised random number" | `10.1016/j.patcog.2004.05.024` | Algorithm steps, Gram-Schmidt |
| **[PAPER-2]** | Teoh, Ngo, Goh (2006) "Random Multispace Quantization as an Analytic Mechanism for BioHashing" | `10.1109/TPAMI.2006.250` | Mathematical proofs, Gaussian distribution |

### Where to Find Key Equations

| Concept | Paper | Section | Page |
|---------|-------|---------|------|
| Algorithm steps 1-4 | Paper-1 | Section 3 | 2247-2248 |
| Gram-Schmidt process | Paper-1 | Section 3, Step 2 | 2247 |
| Gaussian distribution r_ij ~ N(0, 1/m) | Paper-2 | Section III-B, Eq.(8) | 1895 |
| Orthonormality R^T R = I | Paper-2 | Section III-A, Theorem 1 | 1894 |
| Threshold τ = 0 justification | Paper-2 | Section III-C | 1896 |
| Cancelability proof | Paper-2 | Section IV-B | 1898 |
| 50% bit distribution | Paper-2 | Section III-C | 1896 |

---

## 2. Why Gram-Schmidt Orthogonalization Matters

### The Problem Without Orthogonalization

Imagine you're slicing a pizza to categorize toppings. If you make two cuts at similar angles (say 10° and 15°), you're essentially checking the same region twice. This is **redundant information**.

```
WITHOUT ORTHOGONALIZATION (Correlated Vectors):
══════════════════════════════════════════════

                y
                ↑
                │    r₁ (45°)
                │   ↗
                │  ↗
                │ ↗  r₂ (30°)
                │↗ ↗
    ────────────┼────────────→ x

    Problem: r₁ and r₂ point in SIMILAR directions

    If biometric projects positively onto r₁,
    it will ALSO project positively onto r₂!

    Result: Bit 1 and Bit 2 are CORRELATED
            If Bit 1 = 1, Bit 2 is likely = 1 too

    This means: REDUNDANT INFORMATION
                Less entropy (information content)
                Worse discrimination between users
```

### The Solution With Orthogonalization

```
WITH GRAM-SCHMIDT (Orthogonal Vectors):
═══════════════════════════════════════

                y
                ↑
                │    b₁ (45°)
                │   ↗
                │  /
                │ /
                │/
    ────────────┼────────────→ x
                │\
                │ \
                │  \
                │   ↘
                │    b₂ (-45°)

    Solution: b₁ and b₂ are PERPENDICULAR (90°)

    Projection onto b₁ tells you NOTHING about
    projection onto b₂!

    Result: Bit 1 and Bit 2 are INDEPENDENT
            Each bit carries UNIQUE information

    This means: MAXIMUM INFORMATION
                Maximum entropy
                Best discrimination between users
```

### Mathematical Proof: Why Independence Matters

**From [PAPER-2] Section III-A, Theorem 1:**

> "If R is orthonormal, then R^T R = I (identity matrix)"

This has three critical implications:

#### Implication 1: Distance Preservation (Johnson-Lindenstrauss)

```
If R^T R = I, then:

    ||Rx - Ry||² = (Rx - Ry)^T (Rx - Ry)
                 = (x - y)^T R^T R (x - y)
                 = (x - y)^T I (x - y)
                 = ||x - y||²

Translation: The distance between two points is PRESERVED after projection!

Why this matters:
- Similar faces (small ||x - y||) → Similar hashes
- Different faces (large ||x - y||) → Different hashes
- This is the FOUNDATION of biometric matching
```

#### Implication 2: Bit Independence (Maximum Entropy)

```
Orthogonal projection vectors mean:
    ⟨bᵢ, bⱼ⟩ = 0  for all i ≠ j

The projection onto bᵢ is:
    pᵢ = ⟨x, bᵢ⟩

Since bᵢ and bⱼ are orthogonal:
    Knowing pᵢ tells you NOTHING about pⱼ

Therefore:
    Bit i and Bit j are STATISTICALLY INDEPENDENT

Information theory says:
    H(independent bits) = n bits  (maximum)
    H(correlated bits) < n bits   (information loss)

For a 128-bit hash:
    With independence: 2^128 possible hashes
    With correlation:  Much fewer unique hashes
```

#### Implication 3: Clean Decision Boundaries

```
Think of each orthogonal vector as a "slicer" that divides the space:

WITHOUT ORTHOGONALITY:                WITH ORTHOGONALITY:
═══════════════════════               ═════════════════════

    ╲ ╲                                   │
     ╲ ╲  Slices overlap!                 │  Clean vertical slice
      ╲ ╲                                 │
       ╲ ╲                            ────┼────  Clean horizontal slice
        ╲ ╲                               │
                                          │

    Some regions checked                  Every region checked
    multiple times,                       exactly once
    some regions missed!

This affects False Accept Rate (FAR) and False Reject Rate (FRR):
- Overlapping slices → Poor separation → Higher errors
- Clean slices → Maximum separation → Lower errors
```

### Paper Quote: Why Gram-Schmidt is Essential

**From [PAPER-1] Section 3, Step 2:**

> "Apply the Gram-Schmidt process on {rᵢ} to transform them into orthonormal basis {bᵢ}"

**From [PAPER-2] Section III-A:**

> "The random basis r must be orthonormalized via Gram-Schmidt to satisfy R^T R = I (identity matrix). This ensures that pairwise distances between the original biometric features are preserved in the hashed domain."

---

## 3. Why 50/50 Bit Distribution Matters

### The Concept: Maximum Entropy

**Entropy** is a measure of information content or unpredictability.

```
ENTROPY FORMULA (Shannon):
══════════════════════════

    H = -Σ p(x) log₂ p(x)

For a single bit with probability p of being 1:

    H = -p log₂(p) - (1-p) log₂(1-p)

Let's calculate for different distributions:

┌──────────────────┬───────────┬───────────────────────────────┐
│ Distribution     │ Entropy   │ Meaning                       │
├──────────────────┼───────────┼───────────────────────────────┤
│ 50% 0s, 50% 1s   │ 1.00 bit  │ MAXIMUM - perfectly random    │
│ 60% 0s, 40% 1s   │ 0.97 bit  │ Slightly biased               │
│ 70% 0s, 30% 1s   │ 0.88 bit  │ Noticeably biased             │
│ 90% 0s, 10% 1s   │ 0.47 bit  │ Highly biased - predictable   │
│ 100% 0s, 0% 1s   │ 0.00 bit  │ No information at all         │
└──────────────────┴───────────┴───────────────────────────────┘
```

### Why Maximum Entropy is Critical for Security

```
SCENARIO: 128-bit BioHash

WITH 50/50 DISTRIBUTION (Maximum Entropy):
══════════════════════════════════════════
    Possible combinations = 2^128
                          ≈ 3.4 × 10^38 unique hashes

    Brute force attack: Need to try ~2^127 hashes on average
    This is COMPUTATIONALLY INFEASIBLE


WITH 90/10 DISTRIBUTION (Low Entropy):
══════════════════════════════════════
    Effective entropy ≈ 0.47 × 128 = 60 bits

    Possible "likely" combinations ≈ 2^60
                                   ≈ 1.15 × 10^18

    Brute force attack: Much easier!
    Attacker can focus on likely patterns


CONCRETE EXAMPLE:
═════════════════
    If 90% of bits are 0:
    - The hash "000...000" is very likely
    - The hash "111...111" is almost impossible
    - Attacker knows to try 0-heavy patterns first

    If 50% of bits are 0:
    - All 2^128 patterns are equally likely
    - No pattern gives attacker an advantage
```

### Why Threshold τ = 0 Gives 50/50

**From [PAPER-2] Section III-C:**

> "The threshold τ is established at zero according to experimental data. Setting τ = 0 ensures approximately 50% bits are 0 and 50% are 1 (maximum entropy)."

**Mathematical Reason:**

```
When using Gaussian random projection:

1. Each element of projection matrix ~ N(0, 1/m)

2. Biometric x is typically normalized (||x|| = 1)

3. Projection p = ⟨x, b⟩ where b is orthonormal row

4. By Central Limit Theorem:
   p is approximately normally distributed

5. For orthonormal b with Gaussian elements:
   E[p] ≈ 0  (mean is zero)

6. Therefore:
   P(p > 0) ≈ 50%
   P(p ≤ 0) ≈ 50%

VISUAL:
═══════
                    τ = 0
                      ↓
           ┌─────────┬─────────┐
           │         │         │
           │  ≈50%   │  ≈50%   │
           │  (0s)   │  (1s)   │
           │         │         │
    ───────┴─────────┴─────────┴───────
         -0.3      0.0       0.3
              Projection values

The Gaussian distribution is SYMMETRIC around 0.
Setting threshold at 0 naturally splits it 50/50.
```

### What Happens If Threshold ≠ 0?

```
IF THRESHOLD τ = 0.1 (shifted right):
═════════════════════════════════════

                    τ = 0.1
                      ↓
           ┌───────────┬───────┐
           │           │       │
           │   ~60%    │ ~40%  │
           │   (0s)    │ (1s)  │
           │           │       │
    ───────┴───────────┴───────┴───────
         -0.3        0.1      0.3

Result: More 0s than 1s → Lower entropy → Weaker security

This is why the paper specifically recommends τ = 0!
```

### Real Execution Verification

From our script execution:

```
Bit distribution from 128D BioHash:
- 0s: 69 (53.9%)
- 1s: 59 (46.1%)

This is close to 50/50, confirming:
✅ Gaussian projection works as expected
✅ τ = 0 is appropriate threshold
✅ Maximum entropy achieved
```

---

## 4. Why Gaussian Distribution for Random Matrix

### Evolution: Uniform (2004) → Gaussian (2006)

**[PAPER-1] 2004** originally used Uniform distribution U[-1, 1].

**[PAPER-2] 2006** refined this to Gaussian N(0, 1/m).

Why the change?

### Reason 1: Better Orthogonality in High Dimensions

```
UNIFORM DISTRIBUTION:
═════════════════════
    Elements distributed evenly in [-1, 1]

    In high dimensions:
    - Dot products between random vectors
    - Have higher variance
    - Gram-Schmidt needs more "correction"


GAUSSIAN DISTRIBUTION:
══════════════════════
    Elements from N(0, 1/m)

    In high dimensions:
    - Random Gaussian vectors are "almost orthogonal"
    - Dot product variance = 1/m → 0 as m increases
    - Gram-Schmidt needs minimal correction

    Mathematical property:
    For r₁, r₂ ~ N(0, 1/m)^n independently:

    E[⟨r₁, r₂⟩] = 0
    Var[⟨r₁, r₂⟩] = n/m² → 0 as m,n → ∞
```

### Reason 2: Johnson-Lindenstrauss Lemma

**From [PAPER-2] Section III-B:**

> "Using Gaussian random vectors allows the system to satisfy the Johnson-Lindenstrauss Lemma."

```
JOHNSON-LINDENSTRAUSS LEMMA:
════════════════════════════

For any set of n points in R^d, there exists a mapping
to R^k (where k << d) such that:

    (1-ε)||u-v||² ≤ ||f(u)-f(v)||² ≤ (1+ε)||u-v||²

Translation:
- You can project high-dimensional data to lower dimensions
- Distances are preserved within ε error
- Gaussian random projection achieves this!

Why this matters for BioHashing:
- Face embeddings are 128-512 dimensions
- We project to 128 bits
- Gaussian ensures similar faces stay similar
- Different faces stay different
```

### Reason 3: Zero-Centered Projections

```
GAUSSIAN PROJECTION RESULT:
═══════════════════════════

Input: x ∈ R^n (biometric feature, normalized)
Matrix: R where R[i][j] ~ N(0, 1/m)
Output: y = Rx

Each projection yᵢ = ⟨rᵢ, x⟩ is a sum of products:
    yᵢ = Σⱼ rᵢⱼ × xⱼ

By Central Limit Theorem:
    yᵢ ~ approximately N(0, σ²)

Key property: E[yᵢ] = 0 (zero mean)

This means:
    τ = 0 is the NATURAL threshold
    Projections are centered at 0
    50/50 split happens automatically
```

### Paper Quote on Gaussian

**From [PAPER-2] Section III-B, Equation (8):**

> "rᵢⱼ ~ N(0, 1/m) independently"

**Interpretation:**
- Each matrix element is drawn from Gaussian distribution
- Mean = 0
- Variance = 1/m (where m = output dimension)
- The 1/m scaling ensures proper normalization

---

## 5. Complete Algorithm with Paper References

### Step-by-Step Algorithm

```
╔═══════════════════════════════════════════════════════════════════════════╗
║                     BIOHASHING ALGORITHM                                   ║
╠═══════════════════════════════════════════════════════════════════════════╣
║                                                                           ║
║  INPUTS:                                                                  ║
║  ───────                                                                  ║
║  • ω ∈ R^n : Biometric feature vector (e.g., 128D face embedding)        ║
║  • T : User token (seed for PRNG)                                        ║
║                                                                           ║
║  [PAPER-1] Section 3: "Let Γ = {ω, T} denote the biometric input"        ║
║                                                                           ║
╠═══════════════════════════════════════════════════════════════════════════╣
║                                                                           ║
║  STEP 1: Generate Random Vectors                                          ║
║  ────────────────────────────────                                         ║
║  Using T as seed, generate m random vectors {r₁, r₂, ..., rₘ}            ║
║  Each rᵢ ∈ R^n                                                           ║
║                                                                           ║
║  [PAPER-1] Section 3, Step 1:                                            ║
║    "Generate a set of n pseudo-random vectors {rᵢ} using the             ║
║     tokenised random number as a seed"                                   ║
║                                                                           ║
║  [PAPER-2] Section III-B, Equation (8):                                  ║
║    "rᵢⱼ ~ N(0, 1/m) independently"                                       ║
║                                                                           ║
╠═══════════════════════════════════════════════════════════════════════════╣
║                                                                           ║
║  STEP 2: Gram-Schmidt Orthogonalization                                   ║
║  ──────────────────────────────────────                                   ║
║  Transform {rᵢ} into orthonormal basis {bᵢ}                              ║
║                                                                           ║
║  Algorithm:                                                               ║
║    b₁ = r₁ / ||r₁||                                                      ║
║    For i = 2 to m:                                                       ║
║      vᵢ = rᵢ - Σⱼ₌₁^(i-1) proj_bⱼ(rᵢ)                                   ║
║      bᵢ = vᵢ / ||vᵢ||                                                    ║
║                                                                           ║
║  [PAPER-1] Section 3, Step 2:                                            ║
║    "Apply the Gram-Schmidt process on {rᵢ} to transform them             ║
║     into orthonormal basis {bᵢ}"                                         ║
║                                                                           ║
║  [PAPER-2] Section III-A, Theorem 1:                                     ║
║    "If R is orthonormal, then R^T R = I"                                 ║
║                                                                           ║
╠═══════════════════════════════════════════════════════════════════════════╣
║                                                                           ║
║  STEP 3: Inner Product (Projection)                                       ║
║  ──────────────────────────────────                                       ║
║  Compute projections: pᵢ = ⟨ω, bᵢ⟩ for i = 1, ..., m                     ║
║                                                                           ║
║  [PAPER-1] Section 3, Step 3:                                            ║
║    "Perform iterated inner product operations:                           ║
║     pᵢ = ⟨ω, bᵢ⟩ for i = 1, 2, ..., m"                                   ║
║                                                                           ║
║  [PAPER-2] Section II-A, Equation (3):                                   ║
║    "y = Rx where R is the projection matrix"                             ║
║                                                                           ║
╠═══════════════════════════════════════════════════════════════════════════╣
║                                                                           ║
║  STEP 4: Binary Discretization                                            ║
║  ─────────────────────────────                                            ║
║  Binarize using threshold τ:                                             ║
║    cᵢ = 1 if pᵢ > τ                                                      ║
║    cᵢ = 0 if pᵢ ≤ τ                                                      ║
║                                                                           ║
║  [PAPER-1] Section 3, Step 4:                                            ║
║    "Binarize the inner products:                                         ║
║     bᵢ = 0 if pᵢ ≤ τ, bᵢ = 1 if pᵢ > τ"                                 ║
║                                                                           ║
║  [PAPER-2] Section III-C:                                                ║
║    "The threshold τ is established at zero"                              ║
║    "Setting τ = 0 ensures ~50% bits are 0 and 50% are 1"                 ║
║                                                                           ║
╠═══════════════════════════════════════════════════════════════════════════╣
║                                                                           ║
║  OUTPUT:                                                                  ║
║  ───────                                                                  ║
║  BioHash c = [c₁, c₂, ..., cₘ] ∈ {0,1}^m                                 ║
║                                                                           ║
╚═══════════════════════════════════════════════════════════════════════════╝
```

### Visual Pipeline

```
┌─────────────┐     ┌─────────────┐
│  Biometric  │     │    Token    │
│   ω ∈ R^n   │     │      T      │
└──────┬──────┘     └──────┬──────┘
       │                   │
       │                   ▼
       │           ┌─────────────────┐
       │           │  PRNG(seed=T)   │
       │           │  Generate {rᵢ}  │
       │           └────────┬────────┘
       │                    │
       │                    ▼
       │           ┌─────────────────┐
       │           │  Gram-Schmidt   │
       │           │  {rᵢ} → {bᵢ}   │
       │           │  (orthonormal)  │
       │           └────────┬────────┘
       │                    │
       ▼                    ▼
┌─────────────────────────────────────┐
│         Inner Products              │
│    pᵢ = ⟨ω, bᵢ⟩  for all i         │
└──────────────────┬──────────────────┘
                   │
                   ▼
┌─────────────────────────────────────┐
│      Binary Discretization          │
│    cᵢ = 1 if pᵢ > 0, else 0        │
└──────────────────┬──────────────────┘
                   │
                   ▼
          ┌───────────────┐
          │   BioHash c   │
          │  ∈ {0,1}^m    │
          └───────────────┘
```

---

## 6. Real Numbers Verification

### Example 1: Simple 2D Case

```
INPUTS:
═══════
  Biometric ω = [3, 4]
  Token T = "User123"

  Raw random vectors (from PRNG):
    r₁ = [1, 1]
    r₂ = [2, 0]


STEP 2: GRAM-SCHMIDT
════════════════════

Processing r₁:
  ||r₁|| = √(1² + 1²) = √2 = 1.4142
  b₁ = [1/1.4142, 1/1.4142] = [0.7071, 0.7071]

Processing r₂:
  proj = r₂ · b₁ = (2 × 0.7071) + (0 × 0.7071) = 1.4142
  v₂ = r₂ - proj × b₁
     = [2, 0] - 1.4142 × [0.7071, 0.7071]
     = [2, 0] - [1, 1]
     = [1, -1]
  ||v₂|| = √(1² + (-1)²) = 1.4142
  b₂ = [1/1.4142, -1/1.4142] = [0.7071, -0.7071]

VERIFICATION:
  b₁ · b₂ = (0.7071 × 0.7071) + (0.7071 × -0.7071)
          = 0.5 - 0.5 = 0  ✅ ORTHOGONAL!


STEP 3: PROJECTIONS
═══════════════════
  p₁ = ω · b₁ = (3 × 0.7071) + (4 × 0.7071)
              = 2.1213 + 2.8284 = 4.9497

  p₂ = ω · b₂ = (3 × 0.7071) + (4 × -0.7071)
              = 2.1213 - 2.8284 = -0.7071


STEP 4: BINARIZATION (τ = 0)
════════════════════════════
  c₁ = (4.9497 > 0) → 1
  c₂ = (-0.7071 > 0) → 0

FINAL BIOHASH = [1, 0]
```

### Example 2: Real 128D Case

```
CONFIGURATION:
══════════════
  Input dimension: 128 (face embedding)
  Output dimension: 128 (BioHash bits)
  Token: "USER_TOKEN_ABC123"


FACE EMBEDDING (first 10 of 128 dimensions):
════════════════════════════════════════════
  [-0.0678, 0.1337, -0.0043, -0.0343, 0.0511,
   -0.1303, -0.0654, 0.0295, -0.0435, -0.0791...]

  ||x|| = 1.0000 (unit normalized)


GAUSSIAN RANDOM MATRIX:
═══════════════════════
  Size: 128 × 128 = 16,384 elements
  Scale factor: 1/√128 = 0.0884

  BEFORE Gram-Schmidt:
    R[0] · R[1] = -0.084859  ❌ Not orthogonal!

  AFTER Gram-Schmidt:
    b[0] · b[1] = 0.0000000  ✅ Orthogonal!


PROJECTIONS (first 10 of 128):
══════════════════════════════
  [-0.0412, 0.0747, -0.0928, 0.1473, 0.0224,
   -0.0399, -0.0188, -0.0559, -0.0871, 0.0061...]

  Statistics:
    Mean: -0.0026 (≈ 0, as expected)
    Std Dev: 0.0884


BINARIZATION (τ = 0):
═════════════════════
  BioHash (first 64 bits):
  0101100001110010001001011000110000110100010000110010110000010111

  Distribution:
    0s: 69 (53.9%)
    1s: 59 (46.1%)

  ✅ Close to 50/50 (maximum entropy)


CANCELABILITY TEST:
═══════════════════
  Same biometric + Different token = 51.6% match

  ✅ Confirms statistical independence of different tokens
```

---

## 7. Your ZK-BIOWN Contributions

### What's From BioHashing (Cite Papers)

| Component | Source | Citation |
|-----------|--------|----------|
| Gaussian random matrix | Teoh et al. 2006 | [PAPER-2] Section III-B, Eq.(8) |
| Gram-Schmidt orthogonalization | Teoh et al. 2004 | [PAPER-1] Section 3, Step 2 |
| Binary threshold τ = 0 | Teoh et al. 2006 | [PAPER-2] Section III-C |
| Cancelability via token | Teoh et al. 2004 | [PAPER-1] Section 2 |

### What's Your Novel Contribution

| Component | Description | Why It's Novel |
|-----------|-------------|----------------|
| **Three-party key derivation** | SHA-256(productKey \|\| ztizenKey \|\| userKey) | No single party can reconstruct the projection matrix |
| **SZQ Quantization** | 9-level σ-based bins instead of binary | Preserves more discriminative information |
| **Self-normalizing** | Uses session statistics, no stored enrollment data | Eliminates helper data vulnerability |
| **ZK Integration** | Poseidon Hash for efficient ZK circuits | ~300 constraints vs SHA-256's ~25,000 |
| **Browser-native** | WASM-based proof generation | First browser-native ZK biometric system |

### Pipeline Comparison

```
STANDARD BIOHASHING (Teoh 2006):
════════════════════════════════
  Token → Gaussian Matrix → Gram-Schmidt → Project → Binary (τ=0)
                                                         ↓
                                                    {0, 1}^m


YOUR ZK-BIOWN:
══════════════
  3 Keys → SHA-256 → Gaussian Matrix → Gram-Schmidt → Project → SZQ
     ↓                                                            ↓
  (Novel)                                                    {0..8}^m
                                                                  ↓
                                                          Poseidon Hash
                                                                  ↓
                                                           ZK Proof
```

### How to Explain to Professor

> "Our system builds upon the mathematical foundation of BioHashing (Teoh et al. 2004, 2006), specifically using Gaussian random projection with Gram-Schmidt orthogonalization. However, we make three novel contributions:
>
> 1. **Three-party key distribution** ensures no single entity (product owner, ZTIZEN platform, or user) can reconstruct the cancelable template alone.
>
> 2. **Self-normalizing Z-score Quantization (SZQ)** replaces binary discretization with 9-level quantization, preserving more discriminative information while remaining deterministic for ZK circuits.
>
> 3. **Browser-native ZK integration** using Poseidon Hash enables privacy-preserving biometric verification entirely in the browser, without exposing raw biometrics to any server."

---

## Summary: Key Points for Professor Meeting

### 1. Why Gram-Schmidt?
- Ensures statistical independence of each bit
- Preserves distances (similar faces → similar hashes)
- Maximizes discrimination between genuine/imposter

### 2. Why 50/50 Distribution?
- Maximum entropy = maximum security
- τ = 0 naturally achieves this with Gaussian projection
- Prevents attackers from exploiting biased patterns

### 3. Why Gaussian Distribution?
- Better orthogonality properties than Uniform
- Satisfies Johnson-Lindenstrauss lemma
- Zero-centered projections → τ = 0 is optimal

### 4. Your Contribution vs Prior Work
- BioHashing foundation: Cite Teoh 2004, 2006
- Novel: Three-party keys, SZQ, ZK integration

---

## Appendix: Running the Verification Script

```bash
# Navigate to project directory
cd ZTIZEN/vite-demo

# Run the verification script
npx tsx experiments/biohashing-verification.ts
```

This will output:
1. 2D example with exact calculations
2. 128D example with real numbers
3. Cancelability demonstration
4. Paper reference summary

---

*Document created for professor peer review of ZK-BIOWN paper.*
*Last updated: 2024*
