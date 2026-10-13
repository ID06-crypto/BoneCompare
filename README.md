# ICP Automation

Automated pipeline for comparing 3D point clouds of bones.

## Overview

This project automates the comparison of 3D bone models stored as `.stl` files. Beyond looping over and parallelizing comparisons across many samples, the main problem it addresses is **alignment**. The orientation and scale of each bone are unknown, so we first bring the two bones into rough alignment and then use ICP (Iterative Closest Point) for the final fine adjustment.

Throughout this document, each bone is treated as a matrix in which every row is a 3D vector: one of the points (vertices of the triangles) that make up the bone's surface.

## Alignment Pipeline

### 1. Center the bones
Compute the mean vector of each bone and subtract it from every point, so that each bone's center lies at the origin.

### 2. Reflect one bone
Reflect one bone across an axis so both bones share the same orientation. In principle, one bone is a left and the other is a right, so one must be mirrored.

> **Open question:** Confirm whether the input data has already been mirrored before this step.

### 3. Normalize scale
Divide every point in each bone by the length of that bone's longest vector (its farthest point from the origin). After this step, the longest vector in each bone has a length of exactly 1.

### 4. Align principal axes (PCA)
Use principal component analysis to find each bone's first principal component (PC1), the direction that accounts for the most variation in the bone's shape. Rotate both bones so that their PC1s lie on the same axis, which should also bring PC2, PC3, etc. into alignment.

### 5. Generate candidate orientations
Make several copies of one bone with additional transformations, including one flip and a few small rotations. This guards against cases where PCA produces a poor or inverted alignment.

### 6. Fine alignment with ICP
For each candidate, run CloudCompare's fine registration (ICP) tool against the other bone and record the resulting RMS error.

### 7. Select the best fit
Keep the candidate with the lowest RMS as the final alignment and comparison score.

## Automation

The pipeline is designed to run in batch:

1. Load two bones, or more typically a folder of bones.
2. Run the alignment pipeline for each pair, in parallel where possible.
3. Write the resulting RMS values to an output file (e.g., CSV or spreadsheet) for downstream analysis.

## Output

A table with one row per comparison, for example:

| bone_a | bone_b | best_candidate | rms |
|--------|--------|----------------|-----|
| ...    | ...    | ...            | ... |
