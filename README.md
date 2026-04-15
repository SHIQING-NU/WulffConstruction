# Wulff Construction

This repository contains the data analysis workflow used for Wulff construction from STM images.

## Contents
- `WulffConstruction.ipynb`  
  Main analysis notebook containing the full workflow: image processing, contour extraction, γ(θ) calculation, averaging, and Wulff reconstruction.

### Folders
- `stm_images/`  
  Raw STM images used for island analysis.

- `marked_images/`  
  STM images with extracted island contours overlaid for visual verification.

- `gamma_csv/`  
  Per-island CSV files containing the extracted orientation-dependent anisotropy γ(θ).

- `gamma_png/`  
  Polar plots of γ(θ) for individual islands.

### Data files
- `gamma_mean.csv`  
  Ensemble-averaged γ(θ) and corresponding standard deviation.

- `gamma_ratio_results.csv`  
  Computed γA/γB ratios for all islands.

- `island_area.csv`  
  Island area dataset used for statistical analysis.

### Figures
- `gamma_theta.png`  
  Polar plot showing averaged γ(θ), uncertainty band, and reconstructed Wulff shape.

- `gamma_ratio_histogram.png`  
  Histogram of γA/γB ratios across all islands.

- `ratio_area.png`  
  Scatter plot of γA/γB versus island area, used to evaluate finite-size effects.

- `wulff_full_cartesian.png`  
  Cartesian visualization of the Wulff construction, including γ(θ), supporting lines, and reconstructed crystal shape.

### Movies
- `island_shapes.mp4`  
  Animation showing the collection of extracted island shapes.

## Workflow
### 1. Single-island contour extraction and γ(θ) analysis
For each STM image, the largest segmented island is identified and its boundary contour is extracted. The contour is shifted to its centroid and used to calculate the support function \(h(\theta)\) on a uniform angular grid. The normalized function
\[
\gamma(\theta)=h(\theta)/\langle h(\theta)\rangle
\]
is taken as the relative step-energy anisotropy for that island. To suppress pixel-scale noise, the angular dependence is smoothed by Fourier filtering with a limited number of retained harmonics.

For each image, the script saves:
- a contour-marked image,
- a polar plot of \(\gamma(\theta)\),
- a CSV file containing the extracted \(\gamma(\theta)\) values.

### 2. Ensemble-averaged anisotropy and Wulff reconstruction
Per-island `γ(θ)` data exported as CSV files are collected from the `gamma_csv/` folder. Only files containing valid `Theta` and `Gamma` columns on a common angular grid are included in the analysis. The data are stacked into an array of shape `(n_islands, n_theta)` and used to compute:
- the mean anisotropy function \(\langle \gamma(\theta) \rangle\),
- the standard deviation \(\sigma(\theta)\).

The averaged results are exported to `gamma_mean.csv`.

The equilibrium Wulff shape is reconstructed from the averaged support function using
\[
r(\phi) = \min_{\theta} \frac{\gamma(\theta)}{\cos(\phi-\theta)},
\]
with the constraint \(\cos(\phi-\theta) > 0\). The reconstructed shape is normalized and plotted together with the averaged \(\gamma(\theta)\) and its uncertainty band. The resulting polar figure is saved as `gamma_theta.png`.
### 3. γA/γB calculation
γ(θ) is evaluated at A-type (30°, 150°, 270°) and B-type (90°, 210°, 330°) directions.  
For each island, γA/γB is computed and saved to `gamma_ratio_results.csv`.  
A histogram is plotted to analyze the distribution of the ratio.

### 4. Size dependence analysis
The dependence of γA/γB on island area is examined by combining `gamma_ratio_results.csv` and `island_area.csv`. Outliers are removed using a Z-score filter (|Z| < 3).

A linear regression and non-parametric tests (Spearman and Kendall) are performed. The results show no significant correlation (p ≫ 0.05), indicating that γA/γB is independent of island size.

## Requirements
See `requirements.txt`

## Author
Qing Shi
