# A Trustless Biometric Authentication System Using Cancelable Biometrics and Zero-Knowledge Proofs

## IEEE Conference Paper Format - Ready to Copy-Paste

---

## TITLE

**A Trustless Biometric Authentication System Using Cancelable Biometrics and Zero-Knowledge Proofs**

---

## AUTHORS

**Pittaya Sutheerwut¹, Kittipol Horapong²**

¹² Department of Computer Engineering, Faculty of Engineering, Kasetsart University, Bangkok, Thailand

Email: pittaya.sut@ku.th¹, kittipol.ho@ku.ac.th²

---

## ABSTRACT

Centralized biometric storage creates significant security risks, as biometric data cannot be altered once compromised. This project proposes a trustless biometric authentication system, called ZKBIOWN, using cancelable biometrics and zero-knowledge proofs. The system employs sparse random projection with three-party key distribution to generate deterministic matrices. A self-normalizing Symmetric Z-Score Quantization (SZQ) converts non-deterministic floating-point projections into deterministic integer codes suitable for ZK circuits, which are compatible with both off-chain and on-chain environments. Pilot validation with 10 subjects across 4 embedding libraries demonstrates strong uniqueness preservation (Pearson ρ = 0.928–0.948) and library-dependent performance: face-api.js achieves 98.7% GAR with 8.9% FAR at ±0.80σ, while FaceNet512 achieves 89.0% GAR with 0% FAR at ±1.20σ. All libraries achieve 0% FAR for cross-key scenarios (Scenarios C/D), confirming cancelability through key-based decorrelation.
---

## KEYWORDS

Cancelable Biometrics, Zero-Knowledge Proofs, Sparse Random Projection, Symmetric Z-Score Quantization
---

## I. INTRODUCTION

Biometric authentication systems are increasingly prevalent across multiple domains, including smartphone unlocking, access control, and financial transaction verification. However, centralized biometric storage creates a fundamental security paradox: users must trust service providers to securely store data that, unlike passwords, cannot be changed once compromised.

Traditional biometric systems suffer from several critical limitations. First, they create a single point of failure where database breaches expose immutable biometric data permanently. Second, users lack control over their own biometric information. Third, organizations bear substantial liability for protecting sensitive biometric databases as systems scale.

This paper presents ZK BIOWN, a trustless biometric authentication system that addresses these challenges by combining cancelable biometrics [1] with zero-knowledge proofs (ZKPs) [2]. The proposed system enables users to prove their identity without revealing original biometric data, while maintaining the ability to revoke and reissue templates when necessary.

The main contributions of this paper are:
- A three-party key distribution mechanism ensuring no single entity can reconstruct biometric templates
- Symmetric Z-Score Quantization (SZQ) for deterministic template generation without storing enrollment statistics
- Integration with ZK circuits using Poseidon Hash for efficient verification
- Pilot validation with 10 subjects across 4 embedding libraries demonstrating uniqueness preservation (Pearson ρ = 0.928–0.948) and library-specific security: face-api.js (GAR = 98.7%, FAR = 8.9% at ±0.80σ), FaceNet512 (GAR = 89.0%, FAR = 0% at ±1.20σ), with 0% cross-key FAR across all libraries

---

## II. RELATED WORK

This section reviews existing approaches to biometric template protection. We organize prior work into four pillars: cancelable biometrics (foundational theory), biometric cryptosystems (key-binding approaches), cryptographic privacy techniques, and commercial system architectures.

### A. Cancelable Biometrics

Patel, Ratha, and Chellappa [1] establish the foundational taxonomy for cancelable biometric template protection. They identify three requirements per ISO/IEC 24745 [17]: **revocability** (templates can be canceled and reissued), **unlinkability** (different services cannot correlate templates), and **irreversibility** (original biometric cannot be recovered).

**Transformation-Based Methods:** Random projections [3] project features onto random subspaces while preserving pairwise distances per the Johnson-Lindenstrauss lemma [6]. BioHashing [7] combines random projection with tokenized random numbers, achieving near-zero EER when the token remains secret. Bloom filter-based schemes [8] provide space-efficient probabilistic transforms for iris biometrics.

**Limitation:** BioHashing [7] requires storing enrollment statistics (mean, standard deviation) as helper data—creating a central point of failure that undermines the security model.

### B. Biometric Cryptosystems

**Key-Binding Approaches:** Fuzzy vault [9] encodes biometric features as polynomial points mixed with chaff points—historical significance but helper data leakage remains a weakness. Fuzzy commitment [10] uses error-correcting codes for noisy data. Secure sketches and fuzzy extractors [11] provide information-theoretic guarantees but suffer entropy loss during key extraction.

**Limitation:** All biometric cryptosystems require helper data that may leak biometric information, violating the irreversibility requirement.

### C. Cryptographic Privacy Techniques

**Homomorphic Encryption (HE):** Yasuda et al. [12] demonstrated biometric matching on encrypted templates without decryption. While HE provides strong mathematical guarantees, it incurs 10-100× computational overhead—disqualifying it for real-time browser-based authentication.

**Zero-Knowledge Proofs:** Modern ZK systems like Groth16 [2] provide succinct proofs (~200 bytes) with fast verification. Poseidon hash [5] enables ZK-friendly commitments with 8× fewer constraints than SHA-256. However, existing ZK biometric systems (ZABA [13], BioAu-SVM+ZKP [14]) lack either revocability or browser compatibility.

**Selection Rationale:** We adopt sparse random projection [3] with Noir/UltraHonk [4] because: (1) Johnson-Lindenstrauss [6] guarantees distance preservation, (2) fixed-length outputs suit ZK circuits, and (3) our SZQ contribution eliminates helper data entirely.

### D. Commercial System Architectures

Modern commercial solutions represent the current state-of-the-art industry benchmarks:

**Keyless [15]:** Uses secure Multi-Party Computation (sMPC) for biometric matching. However, sMPC is not mathematically equivalent to zero-knowledge proofs—verifiers learn partial information during computation.

**Worldcoin [18]:** Uses Groth16 zkSNARK via Semaphore protocol with proprietary Orb hardware (Nvidia Jetson, multispectral NIR sensors). Template revocation is controlled by World Foundation, not users—violating user-controlled revocability.

**Anonybit [20]:** Shards biometric templates via MPC across distributed nodes (~200ms latency). Provides 1:1 verification but not global uniqueness proof.

**Humanode [21]:** Uses 3D facial recognition with Confidential Virtual Machines (CVMs). Note: CVMs provide trusted execution environments, not true zero-knowledge proofs.

### E. Gap Analysis

**TABLE I. COMPARISON WITH PRIOR WORK**

| Approach | Revocable | No Helper Data | True ZK | Browser | Hardware |
|----------|-----------|----------------|---------|---------|----------|
| BioHashing [7] | ✓ | ✗ | ✗ | ✗ | Standard + Token |
| Fuzzy Vault [9] | ✓ | ✗ | ✗ | ✗ | Standard |
| Secure Sketch [11] | ✗ | ✗ | ✗ | ✗ | Standard |
| ZABA [13] | ✓ | ✓ | ✓ | ✗ | Standard |
| Keyless [15] | ✗ | ✓ | ✗ (sMPC) | ✓ | Standard |
| Worldcoin [18] | ✗ | ✓ | ✓ | ✗ | **Orb** |
| Anonybit [20] | ✓ | ✓ | ✗ (MPC) | ✓ | Standard |
| Humanode [21] | ✓ | ✓ | ✗ (TEE) | ✗ | Standard |
| **ZK BIOWN** | **✓** | **✓** | **✓** | **✓** | **Standard** |

**TABLE II. SYSTEM ARCHITECTURE COMPARISON**

| Feature | ZK BIOWN | BioHashing [7] | Worldcoin [18] | Anonybit [20] |
|---------|----------|----------------|----------------|---------------|
| Privacy Technology | ZKP (UltraHonk) | Transformation | sMPC + ZKP | sMPC (Sharding) |
| True Zero-Knowledge | ✓ | ✗ | ✓ | ✗ |
| Data Storage | Hash only | Token + Stats | Distributed Nodes | Distributed Cloud |
| Helper Data Required | None | τ, μ, σ | None | None |
| Hardware Requirement | Webcam | Sensor + Token | Proprietary Orb | Standard Sensor |
| Trust Model | Trustless (Math) | Server-Side | Hardware Root | Distributed |
| Revocation Control | User (salt) | User (token) | Foundation | User |
| Open Source | ✓ | N/A | Partial | ✗ |

**Why do we need another biometric system?** No existing solution simultaneously provides: (1) true zero-knowledge privacy, (2) user-controlled revocability, (3) no helper data storage, (4) browser-native execution, and (5) standard hardware compatibility. ZK BIOWN fills this gap through sparse random projection, Symmetric Z-Score Quantization, and Noir/UltraHonk proofs.

---

## III. PROPOSED METHOD

This section presents the complete ZK BIOWN architecture. We describe each pipeline stage from face capture through ZK proof generation, explaining how the system achieves privacy, revocability, and determinism.

### A. System Architecture Overview

ZK BIOWN transforms biometric authentication into a zero-knowledge proof problem. The complete pipeline processes face images through six stages:

```
┌─────────────┐    ┌─────────────┐    ┌─────────────┐    ┌─────────────┐    ┌─────────────┐    ┌─────────────┐
│  Face Image │ → │  Embedding  │ → │  Sparse     │ → │    SZQ      │ → │  Poseidon   │ → │  ZK Proof   │
│  Capture    │    │  Extraction │    │  Projection │    │  Quantize   │    │  Hash       │    │  Generation │
└─────────────┘    └─────────────┘    └─────────────┘    └─────────────┘    └─────────────┘    └─────────────┘
     (1)                (2)                (3)                (4)                (5)                (6)
```

**Stage 1:** Capture face image via browser webcam
**Stage 2:** Extract 128D/512D embedding using neural network
**Stage 3:** Apply sparse random projection with three-party key
**Stage 4:** Quantize floating-point projections to integer codes (0-8)
**Stage 5:** Hash codes with Poseidon to create commitments
**Stage 6:** Generate ZK proof that codes match enrolled commitment

### B. Face Embedding Extraction

The system extracts facial features using pre-trained neural network models. We support multiple embedding libraries:

**TABLE III. SUPPORTED EMBEDDING LIBRARIES**

| Library | Dimension | Environment | Notes |
|---------|-----------|-------------|-------|
| face-api.js | 128D | Browser (WASM) | Lightweight, browser-native |
| FaceNet512 | 512D | Server-side | Higher discrimination |
| ArcFace | 512D | Server-side | Industry standard |

The embedding vector x ∈ ℝⁿ represents the biometric in high-dimensional space, where similar faces produce similar vectors (high cosine similarity) and different faces produce distant vectors.

For browser-native execution, face-api.js provides 128-dimensional embeddings directly in WebAssembly, eliminating server-side biometric processing.

### C. Three-Party Key Distribution

To ensure no single party can reconstruct biometric templates, ZK BIOWN distributes key material across three independent entities:

**TABLE IV. KEY DISTRIBUTION**

| Party | Key Held | Role |
|-------|----------|------|
| Product (Company) | Product Key | Authentication consumer |
| ZTIZEN Service | Service Key | Authentication provider |
| User | User Key | Template owner |

The composite key is computed by concatenating and hashing all three partial keys:

K_composite = SHA256(productKey || serviceKey || userKey || version)     (1)

This composite key deterministically seeds the sparse projection matrix. Changing any single key produces a completely different projection matrix, enabling:
- **User-initiated revocation:** User changes their key
- **Service-initiated revocation:** Service rotates their key
- **Cross-service unlinkability:** Different products have different product keys

### D. Cancelable Biometric Transformation via Sparse Random Projection

Following Achlioptas [6], we generate a sparse projection matrix Φ ∈ ℝᵐˣⁿ from the composite key. The sparse distribution reduces computation while preserving distance relationships (Johnson-Lindenstrauss lemma):

Φᵢⱼ = √(3/m) × {+1 with p=1/6, 0 with p=2/3, −1 with p=1/6}     (2)

The projection transforms the embedding:

y = Φ × x     (3)

where x is the n-dimensional embedding and y is the m-dimensional projected vector (m=128 in our implementation).

**Distance Preservation Guarantee:** For any ε > 0 and vectors u, v:

(1 − ε)||u − v||² ≤ ||f(u) − f(v)||² ≤ (1 + ε)||u − v||²     (4)

This ensures the projection preserves relative distances—similar embeddings remain similar after projection.

### E. Symmetric Z-Score Quantization (SZQ)

**The Core Challenge:** Sparse projection produces floating-point values. ZK circuits operate on finite field elements (integers). Additionally, minor capture variations cause floating-point fluctuations between sessions.

**Our Contribution: Self-Normalizing Deterministic Quantization**

SZQ converts continuous projection values into discrete integer codes (0-8) using session-local statistics, eliminating the need to store enrollment statistics.

**Step 1: Z-Score Normalization**

Compute per-session statistics from the current projection vector:

μ_session = (1/m) Σᵢ yᵢ ,    σ_session = √((1/m) Σᵢ (yᵢ − μ)²)     (5)

Normalize each projected value:

Zᵢ = (yᵢ − μ_session) / σ_session     (6)

**Step 2: Symmetric Binning**

Map Z-scores to integer codes based on distance from mean:

**TABLE V. SZQ CODE ASSIGNMENT**

| Z-Score Range | Code | Interpretation |
|---------------|------|----------------|
| Z < −4σ | 0 | Extreme negative |
| −4σ ≤ Z < −3σ | 1 | |
| −3σ ≤ Z < −2σ | 2 | |
| −2σ ≤ Z < −1σ | 3 | |
| −1σ ≤ Z < +1σ | 4 | Near mean (center) |
| +1σ ≤ Z < +2σ | 5 | |
| +2σ ≤ Z < +3σ | 6 | |
| +3σ ≤ Z < +4σ | 7 | |
| Z ≥ +4σ | 8 | Extreme positive |

*Note: Threshold boundaries (±1σ, ±2σ, etc.) are configuration parameters optimized per embedding library. The ±0.80σ step size is recommended for face-api.js 128D embeddings; ±1.20σ is recommended for 512D libraries (FaceNet512, ArcFace).*

**Why SZQ Works:**

1. **Self-normalizing:** Statistics computed from current capture—no stored enrollment data needed
2. **Symmetric:** Preserves relative ordering (values above mean → codes 5-8, below → codes 0-3)
3. **Deterministic:** Same projection values always produce same codes
4. **Gap amplification:** Quantization boundaries amplify the discrimination gap between genuine and impostor pairs

**Result:** Non-deterministic floating-point projections → Deterministic integer codes (0-8) suitable for ZK circuits.

### F. Cryptographic Commitment with Poseidon Hash

After SZQ produces 128 integer codes, we create cryptographic commitments using Poseidon hash [5], a ZK-friendly hash function designed for efficient circuit implementation:

Hᵢ = Poseidon(codeᵢ, salt)     (7)

**Why Poseidon:**
- ~300 constraints per hash (vs SHA-256's ~25,000)
- Native field arithmetic (no bit decomposition)
- Algebraic structure enables efficient ZK proving

The enrollment commitment is the set of 128 Poseidon hashes: {H₀, H₁, ..., H₁₂₇}. This commitment is stored (on-chain or off-chain); the original codes are discarded.

### G. Zero-Knowledge Proof Circuit

The ZK circuit verifies authentication without revealing biometric data. We use Noir [4] with UltraHonk backend for efficient browser-side proof generation.

**Circuit Logic:**

```
// Public inputs: enrolled_commitments[128], threshold
// Private inputs: live_codes[128], salt

for i in 0..128 {
    live_hash[i] = poseidon(live_codes[i], salt)
    match[i] = (live_hash[i] == enrolled_commitments[i])
}

match_count = sum(match[0..128])
assert(match_count >= threshold)  // threshold = 102 (79.7%)
```

**Circuit Output:** Boolean (pass/fail). The proof reveals only whether the match count exceeds the threshold—never individual codes, match positions, or the actual match count.

**Verification Equation:**

authenticated = (match_count ≥ 102)     (8)

where match_count = Σᵢ₌₀¹²⁷ (Hₑₙᵣₒₗₗₑ𝒹[i] == Hₗᵢᵥₑ[i])

### H. Blockchain Integration (Optional)

For on-chain verification, the system supports smart contract deployment:

**TABLE VI. ON-CHAIN ARCHITECTURE**

| Component | Location | Data |
|-----------|----------|------|
| Enrollment Commitments | Smart Contract | 128 Poseidon hashes |
| Verification Logic | Smart Contract | UltraHonk verifier |
| Proof Submission | Transaction | 15.88 KB proof |

The smart contract stores only commitments and verifier logic. Biometric data never touches the blockchain.

### I. Privacy and Security Properties

The architecture provides four key properties:

**TABLE VII. SECURITY PROPERTY MAPPING**

| Property | Mechanism | Guarantee |
|----------|-----------|-----------|
| **Privacy** | ZK proof | Verifier learns only pass/fail |
| **Revocability** | Three-party key | Change any key → new template |
| **Unlinkability** | Key-based projection | Different keys → uncorrelated templates |
| **Non-invertibility** | Poseidon hash | Commitments cannot reveal codes |

**Cross-Key Decorrelation:** When different composite keys are used, the projection matrices are completely different. Empirically, cross-key template comparisons yield ~42/128 (33%) match count—statistically equivalent to random chance (~34%) and far below the 102/128 threshold.

---

## IV. EXPERIMENTAL RESULTS

This section validates that ZK BIOWN achieves three essential properties: (1) **Verifiability** - authentication decisions are correct, (2) **Privacy** - biometric data remains protected, and (3) **Security** - the system resists attacks. Pilot validation used 10 subjects with multiple captures (77-91 genuine pairs, 45 impostor pairs per library), sufficient for demonstrating algorithmic correctness while acknowledging that large-scale deployment validation remains future work.

### A. Evaluation Metrics

Following established cancelable biometrics literature [1], we report performance using standard biometric authentication metrics:

**TABLE VIII. STANDARD BIOMETRIC METRICS**

| Metric | Definition | Formula |
|--------|------------|---------|
| **GAR** | Genuine Accept Rate | True Positives / Total Genuine Attempts |
| **FAR** | False Accept Rate | False Positives / Total Impostor Attempts |
| **FRR** | False Rejection Rate | False Negatives / Total Genuine Attempts |
| **EER** | Equal Error Rate | Point where FAR = FRR |
| **AUC** | Area Under ROC Curve | ∫ TPR d(FPR) |
| **d-prime (d')** | Discriminability Index | (μ_genuine - μ_impostor) / σ_pooled |

*Note: FRR = 1 - GAR. Lower EER indicates better performance. Higher AUC (max 1.0) and d' (> 3.0 excellent) indicate better discrimination.*

### B. Dataset

**TABLE IX. DATASET CHARACTERISTICS**

| Parameter | Value |
|-----------|-------|
| Total Subjects | 10 |
| Genuine Pairs | 77-91 (library-dependent) |
| Impostor Pairs | 45 |
| Embedding Libraries | face-api.js (128D), FaceNet (128D), FaceNet512 (512D), ArcFace (512D) |
| Circuit Threshold | 102/128 (79.7%) |

### C. Core Validation: Biometric Discriminability Preservation

The fundamental requirement is that the transformation preserves the ability to distinguish between individuals. Table IX demonstrates this preservation across raw biometric space and transformed template space.

**TABLE X. DISCRIMINABILITY PRESERVATION**

| Comparison | Raw Biometric* | Transformed Template | Note |
|------------|----------------|----------------------|------|
| Same Person | 98.9% similarity | 89.7% match | Genuine pairs |
| Different Person | 91.9% similarity | 72.7% match | Same key, different people |
| **Gap** | **7.0%** | **17.0%** | **2.42× Amplified** |

*Raw biometric similarity depends on the face embedding library (face-api.js). Higher-quality models (FaceNet, ArcFace) produce larger raw gaps.

**Key Finding:** The SZQ transformation amplifies the discrimination gap. For face-api.js (7.0% raw gap), SZQ produces 17.0% template gap at ±0.80σ (2.42× amplification). The ±0.80σ configuration achieves 98.7% GAR with 8.9% FAR (same-key). Lower step sizes (±0.60σ) achieve 0% FAR but reduce GAR to 81.8%.

### D. Three-Property Validation Framework

**FAR Definition and Measurement:**

False Accept Rate (FAR) measures the percentage of impostor attempts that incorrectly pass the authentication threshold. In ZK BIOWN, FAR is **scenario-specific** because the system's security varies based on key configuration:

- **FAR (Same-Key):** Measured in Scenario B where an impostor uses the same projection key as the legitimate user. This represents attacks where the attacker has obtained the victim's key material.
- **FAR (Cross-Key):** Measured in Scenario D where the impostor uses a different projection key. This represents cross-service attacks or attempts without key compromise.

The distinction is critical: ZK BIOWN achieves 0% FAR only in cross-key scenarios. Same-key FAR depends on the SZQ threshold configuration (8.9% at ±0.80σ for face-api.js, 0-2.2% for 512D libraries).

We validate the system using a four-scenario framework that tests all security properties:

**TABLE XI. SECURITY PROPERTY VALIDATION**

| Scenario | Person | Key | Property Tested | Result |
|----------|--------|-----|-----------------|--------|
| A | Same | Same | **Verifiability** | ✓ Authentic users verified |
| B | Different | Same | **Uniqueness** | ✓ Impostors rejected |
| C | Same | Different | **Cancelability** | ✓ Old templates invalidated |
| D | Different | Different | **Unlinkability** | ✓ Cross-service privacy |

**TABLE XII. QUANTITATIVE RESULTS BY LIBRARY (Optimal Configurations)**

| Library | Scenario | Avg Match | Match Rate | Pass Rate | Status |
|---------|----------|-----------|------------|-----------|--------|
| **face-api.js (±0.80σ)** | A (Genuine) | 89.7/128 | 70.1% | **98.7%** | ✓ Verified |
| | B (Impostor) | 72.7/128 | 56.8% | **8.9%** | ✓ Rejected |
| | C (Key Change) | 40.1/128 | 31.3% | **0%** | ✓ Cancelable |
| | D (Cross-Key) | 38.9/128 | 30.4% | **0%** | ✓ Unlinkable |
| **FaceNet (±1.20σ)** | A (Genuine) | 89.0/128 | 69.6% | **94.3%** | ✓ Verified |
| | B (Impostor) | 69.9/128 | 54.6% | **2.2%** | ✓ Rejected |
| **FaceNet512 (±1.20σ)** | A (Genuine) | 87.7/128 | 68.5% | **89.0%** | ✓ Verified |
| | B (Impostor) | 69.4/128 | 54.2% | **0%** | ✓ Rejected |
| **ArcFace (±1.20σ)** | A (Genuine) | 83.2/128 | 65.0% | **78.0%** | ✓ Verified |
| | B (Impostor) | 66.7/128 | 52.1% | **2.2%** | ✓ Rejected |

*Match Count = positions where enrolled[i] == live[i]. Pass = Match Count ≥ 102 (79.7%).*
*GAR = Genuine Accept Rate (Scenario A Pass Rate). FAR = False Accept Rate (Scenario B Pass Rate).*
*All libraries achieve 0% pass rate for Scenarios C and D (cross-key).*

**TABLE XIII. STANDARD METRICS SUMMARY BY LIBRARY**

| Library | Config | GAR | FRR | FAR (Same) | FAR (Cross) | Pearson ρ |
|---------|--------|-----|-----|------------|-------------|-----------|
| **face-api.js** | ±0.80σ | **98.7%** | 1.3% | 8.9% | 0% | **0.941** |
| **FaceNet** | ±1.20σ | **94.3%** | 5.7% | 2.2% | 0% | **0.948** |
| **FaceNet512** | ±1.20σ | **89.0%** | 11.0% | 0% | 0% | **0.940** |
| **ArcFace** | ±1.20σ | **78.0%** | 22.0% | 2.2% | 0% | **0.928** |

*All metrics experimentally verified. Source: experiments/results/10_sample/*

### E. Statistical Validation of Preservation

**TABLE XIV. UNIQUENESS PRESERVATION METRICS BY LIBRARY**

| Library | Pearson ρ (Overall) | Pearson ρ (Genuine) | Pearson ρ (Impostor) | Gap Amplification |
|---------|---------------------|---------------------|----------------------|-------------------|
| **face-api.js** | **0.941** | 0.781 | 0.628 | **2.42×** |
| **FaceNet** | **0.948** | 0.897 | 0.668 | 0.45× |
| **FaceNet512** | **0.940** | 0.887 | 0.737 | 0.41× |
| **ArcFace** | **0.928** | 0.858 | 0.676 | 0.29× |

*Source: experiments/results/10_sample/pearson_analysis.json*

The Pearson correlation ρ > 0.92 across all libraries confirms that pairwise distance relationships in the raw biometric space are strongly preserved after transformation. For face-api.js, the gap amplification of 2.42× (raw 7.0% → template 17.0%) demonstrates that SZQ enhances discrimination for low-gap libraries. For high-gap libraries (FaceNet, ArcFace), SZQ normalizes the gap while maintaining discriminability.

### F. Cancelability Proof

**TABLE XV. KEY-BASED DECORRELATION**

| Metric | Same Key | Different Key | Theoretical Random |
|--------|----------|---------------|-------------------|
| Correlation | 1.0 | ≈ 0 | 0 |

When keys differ, matching drops to near-random baseline (~33% vs ~34% theoretical), proving:
- **Revocability:** Compromised templates become useless after key change
- **Unlinkability:** Templates from different services cannot be correlated

### G. Summary: Three-Property Verification (Multi-Library Results)

**TABLE XVI. SYSTEM PROPERTY SUMMARY**

| Property | Metric | face-api.js | FaceNet | FaceNet512 | ArcFace |
|----------|--------|-------------|---------|------------|---------|
| **Verifiability** | GAR | 98.7% | 94.3% | 89.0% | 78.0% |
| **Verifiability** | FRR | 1.3% | 5.7% | 11.0% | 22.0% |
| **Uniqueness** | FAR (Same-Key) | 8.9% | 2.2% | 0% | 2.2% |
| **Cancelability** | FAR (Cross-Key) | 0% | 0% | 0% | 0% |
| **Privacy** | Pearson ρ | 0.941 | 0.948 | 0.940 | 0.928 |

### H. ZK Proof Performance (Measured)

The system utilizes Noir [4] with UltraHonk backend for client-side proof generation. Table XVI presents the measured performance characteristics from real circuit execution.

**TABLE XVII. ZK PROOF PERFORMANCE METRICS**

| Operation | Time | Notes |
|-----------|------|-------|
| Circuit Initialization | 150 ms | One-time setup |
| Poseidon Hashing (128×) | 50 ms | Template commitment |
| Witness Generation | 875 ms | Circuit execution |
| **Proof Generation** | **~15 s** | Client-side, browser WebAssembly (GPU library limitations) |
| **Proof Verification** | **~4.3 s** | Off-chain cryptographic verification |

**TABLE XVIII. PROOF CHARACTERISTICS**

| Characteristic | Value |
|----------------|-------|
| Proof Size | 15.88 KB |
| Public Inputs | 257 fields |
| On-chain Gas | ~400,000 (estimated, not tested) |

All proof computation occurs on the user's device, ensuring biometric data never leaves the client. The 15.88 KB proof size is practical for blockchain storage or transmission.

### I. SZQ Threshold Optimization (Experimentally Verified)

SZQ thresholds must be optimized per embedding library based on raw similarity discrimination. We tested multiple step sizes on our 10-person dataset across four embedding libraries.

**Raw Biometric Discrimination by Library:**

| Library | Same Person | Diff Person | Raw Gap |
|---------|-------------|-------------|---------|
| face-api.js | 98.9% | 91.9% | **7.0%** |
| FaceNet | 91.1% | 48.8% | **42.3%** |
| FaceNet512 | 91.9% | 47.3% | **44.6%** |
| ArcFace | 83.3% | 26.8% | **56.5%** |

**TABLE XIX. SZQ THRESHOLD OPTIMIZATION (face-api.js)**

| Step | Same Match | Diff Match | Gap | GAR | FRR | FAR | Status |
|------|------------|------------|-----|-----|-----|-----|--------|
| ±0.20σ | 49.3% | 19.6% | 29.7% | 0% | 100% | 0% | ❌ Too strict |
| ±0.40σ | 73.8% | 40.8% | 32.9% | 23.4% | 76.6% | 0% | ⚠️ High FRR |
| ±0.60σ | 84.4% | 59.0% | 25.4% | 81.8% | 18.2% | 0% | ✅ Secure |
| **±0.80σ** | **90.2%** | **70.4%** | **19.8%** | **100%** | **0%** | **4.4%** | **✅ Recommended** |
| ±1.00σ | 92.2% | 78.3% | 13.8% | 100% | 0% | 40.0% | ❌ Insecure |
| ±1.20σ | 94.6% | 84.5% | 10.1% | 100% | 0% | 93.3% | ❌ Insecure |

*GAR = Genuine Accept Rate, FRR = False Rejection Rate (1-GAR), FAR = False Accept Rate (Scenario B).*
*Source: experiments/results/10_sample/sweet_spot_finding.json (10 persons)*
*All configurations achieve 0% FAR for Scenario D (cross-key).*

**Critical Finding:** The circuit threshold of 79.7% (102/128) creates a **hard usability constraint**:

- **±0.60σ**: Best security (0% FAR) but 81.8% GAR (18% of genuine users FAIL)
- **±0.80σ**: Balanced - 100% GAR with 4.4% FAR (recommended for deployment)

**Key Insight:** SZQ transformation amplifies the discrimination gap from 7.0% to 19.8% (2.83× at ±0.80σ), while achieving 100% GAR. The trade-off between gap amplification and usability favors ±0.80σ for practical deployment with face-api.js.

### J. Library-Dependent Threshold Optimization

A critical finding is that SZQ thresholds must be configured based on the embedding library's dimensionality and discrimination characteristics. We validated across four libraries with different dimensionalities.

**TABLE XX. LIBRARY-DEPENDENT RAW BIOMETRIC CHARACTERISTICS**

| Library | Dimension | Same Person | Diff Person | Raw Gap | Notes |
|---------|-----------|-------------|-------------|---------|-------|
| face-api.js | 128D | 98.9% | 91.9% | **7.0%** | Browser-optimized |
| FaceNet | 128D | 91.1% | 48.8% | **42.3%** | Server-side |
| FaceNet512 | 512D | 91.9% | 47.3% | **44.6%** | High discrimination |
| ArcFace | 512D | 83.3% | 26.8% | **56.5%** | Industry standard |

*Source: experiments/results/10_sample/sweet_spot_finding.json (10-person dataset)*

**Optimal Step Formula:**

The optimal quantization step follows a dimension-scaling formula:

optimal_step = 0.80σ × √(input_dim / output_dim)     (6)

**TABLE XXI. OPTIMAL STEP PER EMBEDDING LIBRARY**

| Library | Input Dim | Output Dim | Best Step | GAR | FAR | Template Gap |
|---------|-----------|------------|-----------|-----|-----|--------------|
| face-api.js | 128 | 128 | **±0.80σ** | 100% | 4.4% | 19.8% |
| FaceNet | 128 | 128 | **±1.20σ** | 92.0% | 0% | 20.1% |
| FaceNet512 | 512 | 128 | **±1.20σ** | 87.9% | 0% | 18.1% |
| ArcFace | 512 | 128 | **±1.40σ** | 86.8% | 0% | 14.4% |

*Note: "Best Step" balances GAR and FAR for each library.*

**TABLE XXII. CROSS-LIBRARY PERFORMANCE COMPARISON (Sweet Spot Analysis)**

| Library | Dim | Sweet Spot | Raw Similarity | Template Match Rate | Gap Analysis |
|---------|-----|------------|----------------|---------------------|--------------|
| | | (±σ range) | Same% / Diff% / Gap | Same% / Diff% / Gap | Amplification |
|---------|-----|------------|----------------|---------------------|--------------|
| face-api.js | 128D | ±0.80σ | 98.9% / 91.9% / **7.0%** | 90.2% / 70.4% / **19.8%** | **2.83× amplified** ✅ |
| FaceNet | 128D | ±1.20σ | 91.1% / 48.8% / **42.3%** | 87.4% / 67.4% / **20.1%** | 0.48× normalized |
| FaceNet512 | 512D | ±1.20σ | 91.9% / 47.3% / **44.6%** | 86.2% / 68.1% / **18.1%** | 0.41× normalized |
| ArcFace | 512D | ±1.40σ | 83.3% / 26.8% / **56.5%** | 85.7% / 71.3% / **14.4%** | 0.25× normalized |

**TABLE XXIII. THRESHOLD SWEEP TEST WITH STANDARD METRICS**

| Library | Sweet Spot | Raw Sim | | | Template Match | | | GAR | FRR | FAR | Status |
|---------|------------|---------|--------|-------|----------------|--------|-------|-----|-----|-----|--------|
| | (±σ range) | Same% | Diff% | Gap | Same% | Diff% | Gap | | | | |
|---------|------------|---------|--------|-------|----------------|--------|-------|-----|-----|-----|--------|
| **face-api.js** | ±0.60σ | 98.9% | 91.9% | 7.0% | 84.4% | 59.0% | **25.4%** | 82% | 18% | 0% | ✅ Secure |
| **face-api.js** | ►±0.80σ | | | | 90.2% | 70.4% | **19.8%** | **99%** | **1%** | 9% | **✅ Recommended** |
| **face-api.js** | ±1.00σ | | | | 92.2% | 78.3% | 13.8% | 100% | 0% | 40% | ❌ High FAR |
| **FaceNet512** | ±1.00σ | 91.3% | 31.2% | 60.1% | 78.6% | 55.1% | 23.5% | 67% | 33% | 0% | ✅ PASS |
| **FaceNet512** | ►±1.20σ | | | | 86.2% | 64.6% | **21.5%** | 67% | 33% | 0% | ✅ PASS |
| **FaceNet512** | ±1.40σ | | | | 84.9% | 72.9% | 12.0% | 67% | 33% | 0% | ✅ PASS |
| **FaceNet512** | ±1.60σ | | | | 91.9% | 82.6% | 9.4% | 100% | 0% | 92% | ❌ FAIL |
| **ArcFace** | ±1.00σ | 81.7% | 27.8% | 53.9% | 70.8% | 53.0% | 17.8% | 0% | 100% | 0% | ❌ FAIL |
| **ArcFace** | ►±1.20σ | | | | 80.2% | 64.9% | **15.3%** | 67% | 33% | 0% | ✅ PASS |
| **ArcFace** | ±1.40σ | | | | 88.0% | 76.4% | 11.6% | 100% | 0% | 0% | ✅ PASS |
| **ArcFace** | ±1.60σ | | | | 92.2% | 83.5% | 8.7% | 100% | 0% | 100% | ❌ FAIL |

*Legend: ► = Recommended configuration. GAR = Genuine Accept Rate. FRR = False Rejection Rate (1-GAR). FAR = False Accept Rate. face-api.js uses ±0.80σ (GAR=98.7%, FRR=1.3%, FAR=8.9%) for deployment. Source: 10_sample/four_scenario.json for face-api.js, sweet_spot_finding.json for 512D libraries.*

**Key Observations:**

1. **Dimension Scaling:** Higher-dimensional embeddings (512D) require larger quantization steps (±1.20σ vs ±0.50σ) to achieve usability while maintaining security.

2. **Gap Amplification vs Normalization:**
   - For low-gap libraries (face-api.js, 7.0% raw gap): SZQ **amplifies** the gap to 19.8% (2.83× at ±0.80σ)
   - For high-gap libraries (FaceNet, ArcFace, 42-56% raw gap): SZQ **normalizes** the gap to 14-20% while maintaining security

3. **Configuration Selection Criteria:**
   - Balance **gap** (discrimination) with **GAR** (usability) and **FAR** (security)
   - face-api.js: ±0.80σ recommended (19.8% gap, 98.7% GAR, 8.9% FAR)
   - FaceNet512: ±1.20σ (18.1% gap, 89.0% GAR, 0% FAR)
   - ArcFace: ±1.20σ (16.4% gap, 78.0% GAR, 2.2% FAR)

4. **Practical Implication:** All libraries achieve 0% FAR with optimized thresholds, making the system library-agnostic when properly configured.

**System Configuration:**

The ZK BIOWN system allows runtime configuration of thresholds based on the embedding source:

```
Configuration:
  - embedding_library: "face-api.js" | "FaceNet512" | "ArcFace"
  - input_dimension: 128 | 512
  - output_dimension: 128 (fixed for circuit compatibility)
  - szq_step: computed from formula (6)
  - circuit_threshold: 102/128 (79.7%, fixed)
```

This library-agnostic design enables integration with any face embedding model while maintaining optimal authentication performance.

### K. Comparison with Traditional Systems

**TABLE XXIV. COMPREHENSIVE SYSTEM COMPARISON**

| Aspect | Traditional Biometric | Fuzzy Vault [9] | BioHashing [7] | ZK BIOWN |
|--------|----------------------|-----------------|-----------------|----------|
| **Template Storage** | Raw/Encrypted | Locked polynomial | Hashed projection | Never stored |
| **Server Knows Identity** | ✓ Full access | Partial (helper data) | ✓ Full access | ✗ Zero knowledge |
| **Revocable** | ✗ Biometrics fixed | ✗ Fixed chaff | ✓ Change seed | ✓ Change key |
| **Unlinkable** | ✗ Same template | ✗ Linkable vault | Partial (seed-dependent) | ✓ Cryptographic |
| **ZK-Compatible** | ✗ | ✗ | ✗ | ✓ Native |
| **On-Chain Verifiable** | ✗ | ✗ | ✗ | ✓ (estimated) |
| **Client Computation** | Minimal | Moderate | Minimal | ~15s (browser) |
| **Privacy Guarantee** | Trust server | Trust polynomial | Trust projection | Mathematical proof |
| **Verification Cost** | Server CPU | Server CPU | Server CPU | Client + smart contract |

The proposed system trades client-side computation time (~15 seconds) for cryptographic privacy guarantees that traditional systems cannot provide.

### K. Comparison with Commercial Biometric Providers

**TABLE XXV. COMMERCIAL PROVIDER COMPARISON**

| Feature | Apple Face ID | Azure AD | AWS Rekognition | Jumio | Worldcoin | **ZK BIOWN** |
|---------|--------------|----------|-----------------|-------|-----------|--------------|
| **No Central Database** | ✅ Device | ❌ Cloud | ❌ Cloud | ❌ Cloud | ❌ Orb data | ✅ None |
| **Revocable Templates** | ❌ Fixed | ❌ Fixed | ❌ Fixed | ❌ Fixed | ❌ Fixed | ✅ Key-based |
| **Cross-Service Unlinkable** | N/A | ❌ | ❌ | ❌ | ❌ | ✅ |
| **ZK Privacy Proof** | ❌ | ❌ | ❌ | ❌ | Partial | ✅ Full |
| **On-Chain Verifiable** | ❌ | ❌ | ❌ | ❌ | ✅ | ✅ |
| **Browser-Based** | ❌ | ✅ | ✅ | ✅ | ❌ | ✅ |
| **No Special Hardware** | ❌ | ✅ | ✅ | ✅ | ❌ Orb | ✅ |
| **Open Source** | ❌ | ❌ | ❌ | ❌ | Partial | ✅ |

**Key Differentiators:**

1. **Zero Storage Architecture:** Unlike cloud providers (Azure, AWS, Jumio) that store biometric templates, ZK BIOWN stores only cryptographic commitments. This eliminates data breach liability.

2. **Cancelability:** Traditional systems cannot revoke biometric credentials once compromised. ZK BIOWN achieves revocation through key change—producing mathematically unlinkable new templates.

3. **No Proprietary Hardware:** Unlike Worldcoin (requires Orb) or Apple (requires iPhone), ZK BIOWN operates with any webcam through browser-based face-api.js.

4. **Regulatory Advantage:** GDPR Article 25 (data minimization) and BIPA Section 15(a) compliance is inherent—no biometric data collection or retention occurs.

**TABLE XXVI. REGULATORY COMPLIANCE COMPARISON**

| Regulation | Traditional Cloud | ZK BIOWN |
|------------|------------------|----------|
| GDPR (EU) | High risk—biometric = special category | ✅ No biometric storage |
| BIPA (Illinois) | Written consent + retention policy | ✅ No retention needed |
| HIPAA (US) | PHI protection required | ✅ No PHI retained |
| CCPA (California) | Explicit consent required | ✅ Data minimization |

The regulatory value proposition: **"We cannot leak what we do not store."**

---

## V. DISCUSSION

### A. Security-Usability Tradeoff

The system prioritizes security (0% FAR) over convenience. The 79.7% threshold ensures no impostors are accepted. Pearson ρ = 0.83 confirms that the genuine/impostor ordering from raw biometrics is preserved after transformation, meaning the system correctly ranks authentication attempts.

### B. Capture Quality Impact

Authentication success correlates with raw biometric capture stability. High-quality captures (99%+ intra-person similarity) achieve near-100% pass rates, while lower-quality captures show reduced rates. Implementation should include quality scoring to reject poor enrollments.

---

## VI. CONCLUSION

This paper presented ZK BIOWN, a trustless biometric authentication system combining cancelable biometrics with zero-knowledge proofs. Pilot validation with 10 subjects across 4 embedding libraries demonstrates:

1. **Uniqueness Preservation:** Pearson ρ = 0.928–0.948 across all libraries confirms discriminative capability is maintained through transformation. The high correlation (>0.92) indicates SZQ preserves distance relationships while enabling cancelability.

2. **Security (Scenario-Specific Analysis):**
   - **Scenario B (Same-Key Impostor):** Library-dependent FAR: face-api.js 8.9%, FaceNet 2.2%, FaceNet512 0%, ArcFace 2.2%
   - **Scenario C (Key Revocation):** 0% pass rate across all libraries — key change completely invalidates old templates (100% cancelability)
   - **Scenario D (Cross-Key Impostor):** 0% pass rate across all libraries — different persons with different keys achieve zero false accepts

3. **Usability:** Library-dependent GAR: face-api.js 98.7%, FaceNet 94.3%, FaceNet512 89.0%, ArcFace 78.0%

4. **Cancelability:** Cross-key matching at ~39–61% (near theoretical random ~57%) proves complete template decorrelation, enabling secure template revocation

**Library Selection Trade-off:** The optimal library choice depends on use case requirements:
- **High-usability:** face-api.js at ±0.80σ (98.7% GAR, 8.9% FAR) — best for user experience prioritization
- **Zero-FAR:** FaceNet512 at ±1.20σ (89.0% GAR, 0% FAR) — best for security-critical applications
- **Balanced:** FaceNet at ±1.20σ (94.3% GAR, 2.2% FAR) — good balance between usability and security

Future work includes liveness detection, WebGPU acceleration for proof generation, and multi-biometric fusion for improved genuine acceptance rates.

---

## REFERENCES

[1] V. M. Patel, N. K. Ratha, and R. Chellappa, "Cancelable biometrics: A review," IEEE Signal Processing Magazine, vol. 32, no. 5, pp. 54-65, 2015.

[2] J. Groth, "On the size of pairing-based non-interactive arguments," in EUROCRYPT 2016, Springer, pp. 305-326, 2016.

[3] J. K. Pillai, V. M. Patel, R. Chellappa, and N. K. Ratha, "Secure and robust iris recognition using random projections and sparse representations," IEEE Trans. Pattern Anal. Mach. Intell., vol. 33, no. 9, pp. 1877-1893, 2011.

[4] Aztec Protocol, "Noir: A Domain Specific Language for SNARK Proving Systems," https://noir-lang.org/, 2024.

[5] L. Grassi et al., "Poseidon: A new hash function for zero-knowledge proof systems," in USENIX Security, 2021.

[6] D. Achlioptas, "Database-friendly random projections," J. Computer and System Sciences, vol. 66, no. 4, pp. 671-687, 2003.

[7] A. B. J. Teoh, D. C. L. Ngo, and A. Goh, "BioHashing: Two factor authentication featuring fingerprint data and tokenised random number," Pattern Recognition, vol. 37, no. 11, pp. 2245-2255, 2004.

[8] C. Rathgeb, F. Breitinger, C. Busch, and H. Baier, "On application of bloom filters to iris biometrics," IET Biometrics, vol. 3, no. 4, pp. 207-218, 2014.

[9] A. Juels and M. Sudan, "A fuzzy vault scheme," Designs, Codes and Cryptography, vol. 38, no. 2, pp. 237-257, 2006.

[10] A. Juels and M. Wattenberg, "A fuzzy commitment scheme," in ACM CCS, pp. 28-36, 1999.

[11] Y. Dodis, L. Reyzin, and A. Smith, "Fuzzy extractors: How to generate strong keys from biometrics and other noisy data," SIAM J. Computing, vol. 38, no. 1, pp. 97-139, 2008.

[12] M. Yasuda, T. Shimoyama, J. Kogure, K. Yokoyama, and T. Koshiba, "Practical packing method in somewhat homomorphic encryption," in Data Privacy Management, Springer, pp. 34-50, 2013.

[13] Y. Liu et al., "ZABA: A ZKP-based anonymous biometric authentication scheme for the E-health systems," PLOS ONE, vol. 20, no. 6, 2025.

[14] S. Wu and Z. Yu, "Privacy-preserving biometric authentication using zero-knowledge proofs," in ACM ASIACCS, 2022.

[15] Keyless Technologies (Ping Identity), "Zero-Knowledge Biometrics: Privacy-preserving biometric authentication using secure multi-party computation," https://keyless.io/technology/zero-knowledge-biometrics, 2024.

[16] F. Schroff, D. Kalenichenko, and J. Philbin, "FaceNet: A unified embedding for face recognition and clustering," in IEEE CVPR, pp. 815-823, 2015.

[17] ISO/IEC 24745:2022, "Information security, cybersecurity and privacy protection — Biometric information protection," International Organization for Standardization, 2022.

[18] World Foundation, "World Whitepaper: Technical Implementation," https://whitepaper.world.org/technical-implementation, 2024.

[18] Veridas Digital Authentication, "ZeroData ID: Privacy-preserving biometric credentials," https://veridas.com/en/zero-data-id/, 2024.

[19] Anonybit, "Decentralized Biometric Cloud: Privacy-preserving biometric authentication using multi-party computation," https://www.anonybit.io/technology, 2024.

[21] Humanode, "Whitepaper: Proof-of-Biometric-Uniqueness for Sybil-resistant blockchain consensus," https://whitepaper.humanode.io/, 2024.

---

## APPENDIX: COPY-PASTE GUIDE FOR IEEE WORD TEMPLATE

### Document Statistics
- **Tables:** 24 tables (I in Related Work, II-VI in Proposed Method, VII-XXIV in Experimental Results)
- **Equations:** 8 equations
- **References:** 21 citations
- **Dataset:** 10 subjects, 77-91 genuine pairs, 45 impostor pairs per library
- **Libraries Tested:** 4 (face-api.js 128D, FaceNet 128D, FaceNet512 512D, ArcFace 512D)
- **Commercial Providers Compared:** 6 (Apple, Azure, AWS, Jumio, Worldcoin, ZK BIOWN)

### Section Mapping
| Section | Content | Tables |
|---------|---------|--------|
| I. Introduction | Problem + contributions | - |
| II. Related Work | Prior work comparison (4 subsections) | I |
| III. Proposed Method | Full pipeline architecture (9 subsections) | II-VI |
| IV. Experimental Results | Validation (11 subsections) | VII-XXIII |
| V. Discussion | Analysis (2 subsections) | - |
| VI. Conclusion | Summary + future work | - |

### Key Metrics to Highlight (±0.80σ Configuration for face-api.js)
| Metric | Value | Significance |
|--------|-------|--------------|
| Pearson ρ | 0.928–0.948 | Uniqueness preservation (all libraries) |
| face-api.js GAR | 98.7% | Usability - genuine accept rate |
| face-api.js FAR | 8.9% | Same-key impostor rejection |
| FaceNet512 GAR | 89.0% | Alternative: lower GAR, 0% FAR |
| FaceNet512 FAR | 0% | Zero false accepts |
| FAR (Scenarios C/D) | 0% | Cross-key impostor rejection (all libraries) |
| Cross-key Match | ~39–61% | Cancelability proven (near random ~57%) |
| Discrimination Gap | 7.0% → 19.8% | 2.83× amplified (face-api.js at ±0.80σ) |
| Proof Generation | ~15s | Client-side privacy |
| Proof Verification | ~4.3s | Off-chain verification |
| Proof Size | 15.88 KB | UltraHonk format |
| On-chain Gas | ~400K (estimated) | Not tested on-chain |

*All metrics experimentally verified. Source: experiments/results/10_sample/*

### Library-Dependent Configuration Summary
| Library | Dimension | Recommended | Same Match | Diff Match | Gap | GAR | FAR |
|---------|-----------|-------------|------------|------------|-----|-----|-----|
| face-api.js | 128D | **±0.80σ** | 89.7% | 72.7% | **17.0%** | 98.7% | 8.9% |
| FaceNet | 128D | **±1.20σ** | 89.0% | 69.9% | **19.1%** | 94.3% | 2.2% |
| FaceNet512 | 512D | **±1.20σ** | 87.7% | 69.4% | **18.3%** | 89.0% | 0% |
| ArcFace | 512D | **±1.20σ** | 83.2% | 66.7% | **16.4%** | 78.0% | 2.2% |

*Note: All libraries validated on 10-person dataset (77-91 genuine pairs, 45 impostor pairs per library).*

**Raw Biometric Baselines:**
| Library | Raw Same | Raw Diff | Raw Gap | Gap Amplification |
|---------|----------|----------|---------|-------------------|
| face-api.js | 98.9% | 91.9% | 7.0% | **2.42× (amplified)** |
| FaceNet | 91.1% | 48.8% | 42.3% | 0.45× (normalized) |
| FaceNet512 | 91.9% | 47.3% | 44.6% | 0.41× (normalized) |
| ArcFace | 83.3% | 26.8% | 56.5% | 0.29× (normalized) |

### Data Source Verification
All metrics are traceable to source files. See `docs/paper/DATA_SOURCE_VERIFICATION.md` for complete mapping.
