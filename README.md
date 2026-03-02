# ZK-BIOWN: A Trustless Biometric Authentication Architecture Using Cancelable Biometrics and Zero-Knowledge Proofs

## IEEE Conference Format

---

**Paper Title:**
# ZK-BIOWN: A Trustless Biometric Authentication Architecture Using Cancelable Biometrics and Zero-Knowledge Proofs

---

**Authors:**

| Author 1 | Author 2 |
|----------|----------|
| Pittaya Sutheerwut | Kittipol Horapong |
| Department of Computer Engineering | Department of Computer Engineering |
| Faculty of Engineering | Faculty of Engineering |
| Kasetsart University | Kasetsart University |
| Bangkok, Thailand | Bangkok, Thailand |
| pittaya.sut@ku.th | kittipol.ho@ku.ac.th |

---

## Abstract

Centralized biometric storage creates significant security risks since biometric data cannot be changed once compromised. This paper proposes ZK-BIOWN, a trustless biometric authentication system using cancelable biometrics and zero-knowledge proofs. The biometric template is stored nowhere while maintaining valid verification. The architecture 3combines BioHashing for cancelable biometric template generation with mathematically proven security properties, a three-party key distribution mechanism ensuring no single entity can reconstruct templates, and zero-knowledge proofs enabling verification without data exposure. We evaluate the system using the FaceScrub dataset with 466 subjects and 7,771 genuine pairs. Rather than proposing novel cryptographic primitives, our contribution lies in the system architecture that integrates existing building blocks into a practical browser-based solution requiring no specialized hardware.

---

## Keywords

System Architecture, Zero-Knowledge Proofs, Cancelable Biometrics, Privacy-Preserving Authentication, BioHashing

---

## I. Introduction

Biometric authentication is widely used in services requiring proof of identity. However, biometric templates are often stored in centralized databases, creating a significant single point of failure where users must trust service providers to securely store their data. Unlike passwords, biometrics represent a new form of digital key that cannot be changed once compromised.

Traditional biometric systems suffer from several critical limitations. First, they create a single point of failure where database breaches expose immutable biometric data permanently. Second, users lack control over their own biometric information. Third, organizations bear substantial liability for protecting sensitive biometric databases as systems scale.

This paper proposes ZK-BIOWN, a trustless biometric authentication architecture that addresses these challenges by combining established cancelable biometrics techniques with zero-knowledge proofs. We evaluate the system using the FaceScrub dataset [24] with 466 subjects and 2,632 quality-filtered images, generating 7,771 genuine pairs and 10,000 impostor pairs for statistical validation. Rather than proposing novel cryptographic primitives, our contribution lies in the system architecture that integrates:

- BioHashing [7] for cancelable template generation with proven security properties
- Three-party key distribution for distributed trust, addressing the key management vulnerability in traditional cancelable biometrics
- Zero-knowledge proofs for privacy-preserving verification without data exposure
- Browser-based implementation requiring no specialized hardware

### A. Contributions

The main contributions of this paper are:

1. **Store Nowhere Architecture:** A system design where biometric templates exist nowhere persistently, computed on-the-fly, never stored
2. **Three-Party Key Distribution:** A trust model where no single entity can reconstruct templates alone
3. **ZK-Native Integration:** Practical browser-based zero-knowledge proof generation using Poseidon Hash
4. **Liability Elimination Model:** Architecture that inherently satisfies GDPR/BIPA data minimization

### B. Paper Organization

The remainder of this paper is organized as follows. Section II presents related work. Section III describes the system architecture. Section IV provides security analysis. Section V presents experimental evaluation. Section VI describes implementation details. Section VII compares with existing systems. Section VIII discusses practical considerations. Section IX concludes the paper.

---

## II. Related Work

### A. Cancelable Biometrics

Patel et al. [1] surveyed cancelable biometric techniques, identifying key requirements: revocability, unlinkability, and non-invertibility. BioHashing [7] achieves these properties through random orthogonal projections seeded by user tokens, with mathematical proofs provided in [8][22]. However, BioHashing alone does not address key management. If the token is compromised alongside the template, the original biometric may be recoverable [1].

Fuzzy vault [9] and fuzzy commitment [10] schemes provide error tolerance but require helper data storage, creating additional attack surfaces. Pillai et al. [3] applied random projections to iris recognition with sparse representations, demonstrating robustness but not addressing zero-knowledge verification.

### B. Zero-Knowledge Biometric Authentication

Groth16 [2] enabled practical ZK-SNARKs with constant-size proofs, making biometric ZKP systems feasible. ZABA [13] combined Pedersen commitments with cancelable biometrics for anonymous authentication, but lacks open-source implementation and browser support. Wu and Yu [14] proposed privacy-preserving biometric authentication using ZKPs but did not address distributed key management.

Worldcoin [19] deploys Groth16 for iris-based proof of personhood at scale, but requires proprietary Orb hardware for enrollment, limiting accessibility. Keyless [15] uses secure multi-party computation rather than true zero-knowledge proofs, distributing trust across MPC servers rather than eliminating it.

### C. Gap Analysis

Existing approaches share common limitations: (1) specialized hardware requirements [19], (2) centralized key storage vulnerable to single-point compromise [7][15], (3) closed-source implementations [13][15], or (4) MPC-based trust models rather than true zero-knowledge [15].

ZK-BIOWN addresses these gaps through three-party key distribution eliminating single points of failure, browser-based implementation requiring no specialized hardware, and open-source availability for security audit.

---

## III. System Architecture

### A. Design Principles

ZK-BIOWN is designed around four principles:

1. **Zero Storage:** No biometric data or templates stored server-side
2. **User-Controlled Revocation:** Users can invalidate templates by changing their key
3. **Distributed Trust:** No single party can reconstruct templates
4. **Client-Side Computation:** All sensitive operations occur on user's device

### B. System Overview

The architecture consists of three parties: User Device, Product Server, and ZTIZEN Platform. All sensitive processing occurs client-side in the browser:

1. Capture Face and Extract Embedding (128D/512D)
2. Combine Keys: K = SHA256(Kp || Kz || Ku)
3. Generate BioHashing Matrix from K
4. Project and Quantize to Template
5. Hash with Poseidon to Commitment
6. Generate ZK Proof

Verification accepts only ZK Proof and Public Commitment, verifying proof validity without biometric data revelation.

### C. Three-Party Key Distribution

The system distributes key material across three independent parties:

**TABLE I: THREE-PARTY KEY DISTRIBUTION**

| Party | Holds | Purpose |
|-------|-------|---------|
| Product | Product Key (Kp) | Service-specific binding |
| ZTIZEN | Platform Key (Kz) | Platform security |
| User | User Key (Ku) | User-controlled revocation |

The composite key is computed as:

K_composite = SHA256(Kp || Kz || Ku || version)    (1)

Security Properties:
- **Compromise Resistance:** Compromising any single party reveals nothing
- **Revocability:** User changes Ku resulting in completely new template
- **Service Isolation:** Different Kp per service yields unlinkable templates

### D. BioHashing Integration

We adopt the BioHashing algorithm [7] for cancelable template generation:

**Step 1: Matrix Generation** - Using K_composite as PRNG seed, generate Gaussian random matrix where R[i][j] follows (1/sqrt(m)) times N(0, 1).

**Step 2: Gram-Schmidt Orthogonalization** - Orthogonalize R to ensure distance preservation per the Johnson-Lindenstrauss lemma [6].

**Step 3: Projection** - Project biometric embedding x through orthogonal matrix: v = R_orthogonal times x.

**Step 4: Binarization** - Convert continuous projections to binary codes using threshold tau = 0 [8]: b_i = 1 if v_i > 0, else 0.

This produces a 128-bit binary template with maximum entropy.

### E. ZK Circuit Design

The ZK circuit proves knowledge of a biometric template matching a stored commitment without revealing the template.

**Circuit Inputs:**
- Private: Template T[128], User Key Ku
- Public: Enrollment Commitment C, Product Key Kp, Platform Key Kz

**Circuit Logic:**
1. Recompute composite key: K = SHA256(Kp || Kz || Ku)
2. Hash template: H = Poseidon(T)
3. Assert: H == C
4. Assert: MatchCount(T_enrollment, T_verification) >= threshold

**Why Poseidon Hash:** Approximately 300 constraints vs SHA-256's 25,000, native field arithmetic, proven security in ZK context [5].

### F. Enrollment and Verification Protocols

**Enrollment Protocol:**
1. User captures face yielding embedding x
2. Client receives Kp (product), Kz (platform)
3. User provides Ku (user key)
4. Client computes: K = SHA256(Kp || Kz || Ku)
5. Client generates: template T = BioHash(x, K)
6. Client computes: commitment C = Poseidon(T)
7. Server stores: only C (no template, no keys)

**Verification Protocol:**
1. User captures face yielding embedding x'
2. Client retrieves Kp, Kz; user provides Ku
3. Client computes: T' = BioHash(x', K)
4. Client generates: ZK proof pi that T' matches stored C
5. Server verifies: pi (learns nothing about T' or x')
6. Result: Accept if proof valid, Reject otherwise

---

## IV. Security Analysis

### A. Inherited Security Properties

By adopting BioHashing [7], ZK-BIOWN inherits mathematically proven properties:

**Theorem 1 (Cancelability) [7]:** When key K changes to K', the resulting templates T and T' are statistically independent.

**Theorem 2 (Non-Invertibility) [7]:** Given template T and key K, recovering original biometric x is computationally infeasible due to dimensionality reduction and quantization.

**Theorem 3 (Unlinkability) [7]:** Templates generated with different keys cannot be linked to the same individual.

### B. ZK-BIOWN Additional Properties

**Property 1 (Zero-Knowledge Privacy):** The verifier learns nothing beyond the validity of the authentication claim, following from the zero-knowledge property of UltraHonk [23].

**Property 2 (Three-Party Security):** Compromising fewer than all three key holders reveals no information about the biometric template.

### C. Attack Resistance

**TABLE II: ATTACK RESISTANCE ANALYSIS**

| Attack | Traditional | BioHashing | ZK-BIOWN |
|--------|-------------|------------|----------|
| Database breach | Full compromise | Template only | Commitment only |
| Replay attack | Vulnerable | Vulnerable | Proof has unique challenge |
| Cross-service linking | Possible | Prevented | Prevented + ZK privacy |
| Template inversion | Risk | Proven infeasible | N/A (no template stored) |
| Man-in-middle | Server sees biometric | Server sees template | Server sees nothing |

---

## V. Experimental Evaluation

### A. Dataset Selection and Preparation

We evaluate our implementation using the FaceScrub dataset [24], a real-world face recognition benchmark comprising 530 subjects with approximately 107,000 face images collected from internet sources under uncontrolled conditions.

**TABLE III: QUALITY FILTERING CRITERIA**

| Filter | Threshold | Purpose |
|--------|-----------|---------|
| Detection Confidence | >= 95% | High-quality face detection |
| Face Area Ratio | 10-90% | Proper face framing |
| Blur Score | >= 100 | Sharp, focused images |
| Face Count | Exactly 1 | No group photos |
| Yaw Angle | <= 20 degrees | Frontal pose only |
| Pitch Angle | <= 25 degrees | No extreme head tilt |

**TABLE IV: DATASET AFTER QUALITY FILTERING**

| Metric | Original | After Filtering | Retention |
|--------|----------|-----------------|-----------|
| Subjects | 530 | 466 | 88% |
| Images | 107,000 | 2,632 | 2.5% |
| Genuine Pairs | - | 7,771 | - |
| Impostor Pairs | - | 10,000 | - |

### B. Four-Scenario Validation Framework

We validate the system using the standard four-scenario framework for cancelable biometrics:

**TABLE V: FOUR-SCENARIO VALIDATION FRAMEWORK**

| Scenario | Description | Tests | Expected |
|----------|-------------|-------|----------|
| A | Same Person + Same Key | Verifiability | High match |
| B | Different Person + Same Key | Uniqueness | Low match |
| C | Same Person + Different Key | Cancelability | No correlation |
| D | Different Person + Different Key | Unlinkability | No correlation |

**TABLE VI: BIOHASHING RESULTS (128-BIT OUTPUT)**

| Model | Scenario A | Scenario B | Gap | Scenario C | Scenario D |
|-------|------------|------------|-----|------------|------------|
| face-api.js | 91.4% | 83.2% | 8.2% | ~50% | ~50% |
| FaceNet | 73.0% | 52.5% | 20.5% | ~50% | ~50% |
| FaceNet512 | 72.3% | 51.4% | 20.9% | ~50% | ~50% |
| ArcFace | 69.4% | 51.3% | 18.1% | ~50% | ~50% |

### C. Statistical Validity

Our evaluation exceeds the statistical requirements of the original BioHashing papers: 466 subjects vs. 100-150 in [7][8], 5,436-7,771 genuine pairs (statistically significant), and 10,000 impostor pairs (sufficient for FAR estimation).

---

## VI. Implementation

### A. Technology Stack

**TABLE VII: IMPLEMENTATION COMPONENTS**

| Component | Technology | Purpose |
|-----------|------------|---------|
| Face Embedding | face-api.js (128D) | Browser-based face recognition |
| Random Projection | BioHashing [7] | Cancelable template generation |
| ZK Circuit | Noir [4] | Circuit definition |
| Proof System | UltraHonk | Proof generation/verification |
| Hash Function | Poseidon [5] | ZK-friendly commitment |
| Frontend | React + TypeScript | User interface |

### B. Performance Measurements

**TABLE VIII: MEASURED PERFORMANCE (Browser, M1 MacBook)**

| Operation | Time | Notes |
|-----------|------|-------|
| Face Detection + Embedding | 200ms | face-api.js WASM |
| BioHashing | <15ms | Matrix multiplication |
| Poseidon Hashing | 50ms | 128 field elements |
| Proof Generation | 15s | Client-side, WASM |
| Proof Verification | 4s | Cryptographic verification |

**TABLE IX: PROOF CHARACTERISTICS**

| Metric | Value |
|--------|-------|
| Proof Size | 15.88 KB |
| Public Inputs | 257 fields |
| Circuit Constraints | ~45,000 |
| On-chain Gas | ~400,000 |

### C. Limitations

1. **Proof Generation Time:** 15s is acceptable for high-security scenarios but may impact UX for frequent authentication
2. **No Liveness Detection:** Current implementation does not include anti-spoofing
3. **Browser Dependency:** Requires modern browser with WebAssembly support

---

## VII. Comparison with Existing Systems

### A. Academic Systems

**TABLE X: COMPARISON WITH ACADEMIC APPROACHES**

| Feature | BioHashing | Fuzzy Vault | ZABA | ZK-BIOWN |
|---------|------------|-------------|------|----------|
| Cancelability | Yes | Yes | Yes | Yes |
| No Helper Data | No | No | Yes | Yes |
| ZK Privacy | No | No | Yes | Yes |
| Three-Party Trust | No | No | No | Yes |
| Open Source | N/A | N/A | No | Yes |
| Browser-Based | N/A | N/A | No | Yes |

### B. Commercial Systems

**TABLE XI: COMPARISON WITH COMMERCIAL SOLUTIONS**

| Feature | Keyless | Worldcoin | ZK-BIOWN |
|---------|---------|-----------|----------|
| Technology | sMPC | Groth16 | UltraHonk |
| True Zero-Knowledge | No | Yes | Yes |
| User Revocation | Unknown | No | Yes |
| Special Hardware | No | Yes (Orb) | No |
| Open Source | No | Partial | Yes |
| Browser-Native | Yes | No | Yes |

---

## VIII. Discussion

### A. System Architecture Contribution

This is a system engineering paper, not an algorithm improvement paper. The distinction is critical:

**What We Use (Cite):**
- BioHashing algorithm [7][8]
- Poseidon Hash [5]
- UltraHonk [23]

**What We Contribute (Novel):**
- Three-party key distribution architecture
- Store Nowhere system design
- Browser-native ZK biometric verification

### B. Practical Considerations

**Deployment Scenarios:**
- High-Security: Financial transactions where 15s proof time is acceptable
- Enterprise: Access control with moderate security requirements
- Consumer: May require optimization for faster proof generation

**Regulatory Compliance:**
The zero storage architecture provides inherent compliance advantages with GDPR Article 25 (data minimization), BIPA (no retention), and CCPA (cannot share data that does not exist).

### C. Limitations and Future Work

1. Liveness detection integration required
2. WebGPU acceleration could reduce proof time
3. Multi-biometric fusion for higher accuracy
4. Large-scale evaluation on standard benchmarks

---

## IX. Conclusion

This paper presented ZK-BIOWN, a trustless biometric authentication architecture that combines BioHashing for cancelable template generation with zero-knowledge proofs for privacy-preserving verification.

Key Contributions:
1. Three-party key distribution eliminating single points of failure
2. Integration of proven BioHashing with ZK circuits
3. Complete browser-based implementation requiring no specialized hardware
4. Open-source reference implementation

Inherited Properties (from BioHashing [7]):
- Cancelability: Templates revocable via key change
- Unlinkability: Cross-service tracking prevented
- Non-invertibility: Original biometric cannot be recovered

Added Properties (from ZK-BIOWN architecture):
- Zero-knowledge privacy: Verifier learns nothing
- Distributed trust: No single party compromise
- On-chain verifiability: Smart contract integration possible

The architecture demonstrates that practical privacy-preserving biometric authentication is achievable using existing cryptographic building blocks, without requiring novel primitives or specialized hardware.

---

## References

[1] V. M. Patel, N. K. Ratha, and R. Chellappa, "Cancelable biometrics: A review," *IEEE Signal Processing Magazine*, vol. 32, no. 5, pp. 54-65, 2015.

[2] J. Groth, "On the size of pairing-based non-interactive arguments," in *EUROCRYPT 2016*, Springer, pp. 305-326, 2016.

[3] J. K. Pillai, V. M. Patel, R. Chellappa, and N. K. Ratha, "Secure and robust iris recognition using random projections and sparse representations," *IEEE Trans. Pattern Anal. Mach. Intell.*, vol. 33, no. 9, pp. 1877-1893, 2011.

[4] Aztec Protocol, "Noir: A Domain Specific Language for SNARK Proving Systems," https://noir-lang.org/, 2024.

[5] L. Grassi *et al.*, "Poseidon: A new hash function for zero-knowledge proof systems," in *USENIX Security*, 2021.

[6] D. Achlioptas, "Database-friendly random projections," *J. Computer and System Sciences*, vol. 66, no. 4, pp. 671-687, 2003.

[7] A. B. J. Teoh, D. C. L. Ngo, and A. Goh, "BioHashing: Two factor authentication featuring fingerprint data and tokenised random number," *Pattern Recognition*, vol. 37, no. 11, pp. 2245-2255, 2004.

[8] A. B. J. Teoh, A. Goh, and D. C. L. Ngo, "Random Multispace Quantization as an Analytic Mechanism for BioHashing of Biometric and Random Identity Inputs," *IEEE Trans. Pattern Anal. Mach. Intell.*, vol. 28, no. 12, pp. 1892-1901, 2006.

[9] A. Juels and M. Sudan, "A fuzzy vault scheme," *Designs, Codes and Cryptography*, vol. 38, no. 2, pp. 237-257, 2006.

[10] A. Juels and M. Wattenberg, "A fuzzy commitment scheme," in *ACM CCS*, pp. 28-36, 1999.

[11] Y. Dodis, L. Reyzin, and A. Smith, "Fuzzy extractors: How to generate strong keys from biometrics and other noisy data," *SIAM J. Computing*, vol. 38, no. 1, pp. 97-139, 2008.

[12] ISO/IEC 24745:2022, "Information security, cybersecurity and privacy protection — Biometric information protection," International Organization for Standardization, 2022.

[13] E. Hanzlik and K. Kluczniak, "Zero-knowledge anonymous biometric authentication," in *IEEE WIFS*, pp. 1-6, 2019.

[14] S. Wu and Z. Yu, "Privacy-preserving biometric authentication using zero-knowledge proofs," in *ACM ASIACCS*, 2022.

[15] Keyless Technologies (Ping Identity), "Zero-Knowledge Biometrics," https://keyless.io/technology/zero-knowledge-biometrics, 2024.

[16] F. Schroff, D. Kalenichenko, and J. Philbin, "FaceNet: A unified embedding for face recognition and clustering," in *IEEE CVPR*, pp. 815-823, 2015.

[17] J. Deng, J. Guo, N. Xue, and S. Zafeiriou, "ArcFace: Additive angular margin loss for deep face recognition," in *IEEE CVPR*, pp. 4690-4699, 2019.

[18] World Foundation, "World Whitepaper: Technical Implementation," https://whitepaper.world.org/technical-implementation, 2024.

[19] World Foundation, "Worldcoin: Proof of Personhood," https://worldcoin.org/, 2024.

[20] W. Johnson and J. Lindenstrauss, "Extensions of Lipschitz mappings into a Hilbert space," *Contemporary Mathematics*, vol. 26, pp. 189-206, 1984.

[21] Y. Liu *et al.*, "ZABA: A ZKP-based anonymous biometric authentication scheme for the E-health systems," *PLOS ONE*, vol. 20, no. 6, 2025.

[22] A. B. J. Teoh and C. T. Yuang, "Cancelable biometrics realization with multispace random projections," *IEEE Trans. Syst., Man, Cybern. B*, vol. 37, no. 5, pp. 1096-1106, 2007.

[23] Aztec Protocol, "PLONK: Permutations over Lagrange-bases for Oecumenical Noninteractive arguments of Knowledge," https://eprint.iacr.org/2019/953, 2019.

[24] H. Ng and S. Winkler, "A data-driven approach to cleaning large face datasets," in *IEEE International Conference on Image Processing (ICIP)*, pp. 343-347, 2014.

---

## Document Information

- **Paper Type:** System Architecture Paper
- **Format:** IEEE Conference
- **Tables:** 11 (I-XI)
- **References:** 24
- **Word Count:** ~4,000
- **Core Contribution:** Store Nowhere biometric authentication architecture
