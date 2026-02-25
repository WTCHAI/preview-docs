# Similarity Scores

## Experiment: Scenario B - Same Key, Different Person

**Purpose:** Verify that the cancelable biometric system maintains discrimination between different persons even when using the same secret key.

**Configuration:**
- Same key used for all transformations
- Comparing different persons' templates
- Threshold: 79.7% (102/128 matches)

**Result:** FAR (False Accept Rate) = 0% across most configurations, proving the algorithm correctly rejects impostors.

---

## 1. 10_sample (Collected)

### Raw Embedding Similarity

| Model | Same Mean | Same Min | Same Max | Diff Mean | Diff Min | Diff Max |
|-------|-----------|----------|----------|-----------|----------|----------|
| face-api.js (128D) | 98.9% | 97.2% | 99.8% | 92.0% | 86.9% | 95.6% |
| FaceNet (128D) | 91.1% | 20.7% | 99.0% | 49.1% | 0.3% | 80.6% |
| FaceNet512 (512D) | 91.9% | 51.8% | 98.9% | 45.7% | 9.9% | 77.8% |
| ArcFace (512D) | 83.3% | 0.9% | 97.3% | 27.0% | -2.9% | 65.8% |

### SZQ Template Similarity

| Model | Same Mean | Same Min | Same Max | Diff Mean | Diff Min | Diff Max |
|-------|-----------|----------|----------|-----------|----------|----------|
| face-api.js (128D) | 88.5% | 80.5% | 96.1% | 69.2% | 57.0% | 82.0% |
| FaceNet (128D) | 83.9% | 52.3% | 96.9% | 58.6% | 39.1% | 78.9% |
| FaceNet512 (512D) | 86.8% | 68.8% | 99.2% | 68.5% | 53.9% | 82.8% |
| ArcFace (512D) | 82.2% | 61.7% | 95.3% | 62.6% | 46.9% | 78.1% |

### BioHashing Template Similarity

| Model | Same Mean | Same Min | Same Max | Diff Mean | Diff Min | Diff Max |
|-------|-----------|----------|----------|-----------|----------|----------|
| face-api.js (128D) | 95.1% | 91.4% | 99.2% | 86.8% | 78.9% | 93.0% |
| FaceNet (128D) | 87.7% | 57.8% | 96.1% | 67.5% | 46.9% | 85.2% |
| FaceNet512 (512D) | 89.0% | 70.3% | 97.7% | 67.8% | 50.0% | 82.8% |
| ArcFace (512D) | 82.8% | 49.2% | 96.1% | 58.8% | 39.8% | 75.8% |

---

## 2. Selfies (Axion) - Only 10 Sample Free

### Raw Embedding Similarity

| Model | Same Mean | Same Min | Same Max | Diff Mean | Diff Min | Diff Max |
|-------|-----------|----------|----------|-----------|----------|----------|
| FaceNet (128D) | 67.1% | 11.8% | 93.8% | 13.7% | -14.1% | 53.0% |
| FaceNet512 (512D) | 70.6% | -7.5% | 96.3% | 9.1% | -24.6% | 43.4% |
| ArcFace (512D) | 63.0% | 3.4% | 90.8% | 10.8% | -17.8% | 45.6% |

### SZQ Template Similarity

| Model | Same Mean | Same Min | Same Max | Diff Mean | Diff Min | Diff Max |
|-------|-----------|----------|----------|-----------|----------|----------|
| FaceNet (128D) | 67.4% | 49.2% | 82.8% | 51.6% | 39.8% | 64.8% |
| FaceNet512 (512D) | 77.1% | 61.7% | 94.5% | 63.5% | 50.8% | 75.0% |
| ArcFace (512D) | 73.0% | 62.5% | 85.9% | 63.2% | 53.1% | 73.4% |

### BioHashing Template Similarity

| Model | Same Mean | Same Min | Same Max | Diff Mean | Diff Min | Diff Max |
|-------|-----------|----------|----------|-----------|----------|----------|
| FaceNet (128D) | 74.7% | 56.3% | 89.1% | 54.6% | 42.2% | 68.0% |
| FaceNet512 (512D) | 77.5% | 48.4% | 92.2% | 52.2% | 39.1% | 64.8% |
| ArcFace (512D) | 72.1% | 37.5% | 85.2% | 52.9% | 40.6% | 64.8% |

---

## 3. FaceScrub

### Raw Embedding Similarity

| Model | Same Mean | Same Min | Same Max | Diff Mean | Diff Min | Diff Max |
|-------|-----------|----------|----------|-----------|----------|----------|
| face-api.js (128D) | 95.3% | 75.2% | 100.0% | 83.5% | 68.6% | 93.7% |
| FaceNet (128D) | 63.9% | -26.8% | 100.0% | 10.0% | -45.5% | 96.6% |
| FaceNet512 (512D) | 62.4% | -28.8% | 100.0% | 4.3% | -54.5% | 89.9% |
| ArcFace (512D) | 55.1% | -19.2% | 100.0% | 4.4% | -30.7% | 99.5% |

### SZQ Template Similarity

| Model | Same Mean | Same Min | Same Max | Diff Mean | Diff Min | Diff Max |
|-------|-----------|----------|----------|-----------|----------|----------|
| face-api.js (128D) | 76.1% | 46.9% | 100.0% | 58.9% | 43.0% | 78.1% |
| FaceNet (128D) | 64.8% | 39.8% | 100.0% | 51.8% | 36.7% | 71.9% |
| FaceNet512 (512D) | 73.2% | 52.3% | 100.0% | 62.1% | 47.7% | 78.1% |
| ArcFace (512D) | 71.2% | 52.3% | 100.0% | 62.4% | 49.2% | 78.1% |

### BioHashing Template Similarity

| Model | Same Mean | Same Min | Same Max | Diff Mean | Diff Min | Diff Max |
|-------|-----------|----------|----------|-----------|----------|----------|
| face-api.js (128D) | 89.6% | 75.8% | 100.0% | 79.4% | 64.8% | 90.6% |
| FaceNet (128D) | 73.0% | 39.1% | 100.0% | 53.3% | 35.2% | 75.0% |
| FaceNet512 (512D) | 72.0% | 35.2% | 100.0% | 51.4% | 33.6% | 71.9% |
| ArcFace (512D) | 69.4% | 39.1% | 100.0% | 51.5% | 35.9% | 71.1% |
