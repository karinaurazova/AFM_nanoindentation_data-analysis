# AFM Nanoindentation Data Analysis

A comprehensive Python script for processing and analyzing force-distance curves obtained by Atomic Force Microscopy (AFM) nanoindentation of biological samples (cells, tissues).

RUSSIAN VERSION

## Table of Contents
1. [Description](#description)
2. [Features](#features)
3. [Requirements](#requirements)
4. [Quick Start](#quick-start)
5. [Detailed Description](#detailed-description)
6. [Input Data Format](#input-data-format)
7. [Results](#results)
8. [Parameter Configuration](#parameter-configuration)
9. [Usage Examples](#usage-examples)
10. [Troubleshooting](#troubleshooting)
11. [References](#references)

## Description

This Python script implements a complete pipeline for processing nanoindentation data, including:
- Data preprocessing (smoothing, baseline correction)
- Automatic contact point detection
- Indentation depth calculation
- Elastic model fitting (Hertz, Sneddon)
- Viscoelastic property estimation
- Batch processing of multiple measurements

## Features

### Core Functions:
- Automated AFM curve processing
- Data smoothing using Savitzky-Golay filter
- Linear baseline drift correction
- Contact point detection via threshold method
- Support for two indentation models:
  - **Spherical Hertz model** (for spherical indenters)
  - **Conical Sneddon model** (for pyramidal indenters)
- Young's modulus calculation with error estimation
- Viscoelastic analysis through hysteresis

### Additional Capabilities:
- Automatic result visualization (6 plots per curve)
- Single file and batch folder processing
- Statistical analysis of measurement series
- CSV export of results
- Synthetic data generation for testing

## Requirements

### Required Libraries:
```bash
numpy >= 1.19.0
pandas >= 1.3.0
matplotlib >= 3.3.0
scipy >= 1.7.0
```

### Installation:
```bash
pip install numpy pandas matplotlib scipy
```

## Quick Start

### Step 1: Clone and Run
```python
# Simply run all cells in Jupyter Notebook in order
# The script will automatically:
# 1. Import required libraries
# 2. Define all processing functions
# 3. Generate test synthetic data
# 4. Analyze and display results
```

### Step 2: Test with Synthetic Data
After running all cells, you will see:
- 6 plots with processing results
- Calculated Young's modulus
- Viscoelastic property estimates
- Comparison of different models

## Detailed Description

### Script Structure:

| Cell | Purpose | Key Functions |
|------|---------|---------------|
| **1** | Library imports | - |
| **2** | Function definitions | `smooth_force()`, `correct_baseline()`, `find_contact_point()`, `calculate_indentation()`, `hertz_spherical()`, `fit_hertz_model()` |
| **3** | Synthetic data generation | `generate_synthetic_curve()` |
| **4** | Single curve processing | `process_single_curve()` |
| **5** | User file processing | `process_user_file()` |
| **6** | Batch processing | `process_folder()` |
| **7** | Additional utilities | `export_processed_curve()`, `batch_analysis_config()` |

### Processing Algorithm:
1. **Smoothing** → Savitzky-Golay filter for noise reduction
2. **Baseline correction** → Linear fit of tail portion
3. **Contact point detection** → Threshold method (5σ of noise)
4. **Indentation calculation** → δ = -(z - z₀) - F/k
5. **Model fitting** → Nonlinear least squares fitting
6. **Viscoelastic analysis** → Hysteresis area calculation

## Input Data Format

### Supported Formats:
- Delimited text files (`.txt`, `.csv`, `.dat`)
- Columns with numerical data

### Expected Data Structure:
```
# Example data file
Displacement_μm    Force_nN
0.000            0.001
0.001            0.005
0.002            0.012
...
```

### Loading Configuration:
```python
# Parameters for load_data()
z_column=0     # Column index for displacement (Z)
f_column=1     # Column index for force (F)
skiprows=0     # Number of header rows to skip
```

## Results

### For each curve, the following is calculated:
| Parameter | Symbol | Units | Description |
|-----------|--------|-------|-------------|
| **E** | Young's Modulus | Pa | Material stiffness |
| **G_loss** | Loss Modulus | Pa | Viscous component |
| **δ_max** | Max. Indentation | m | Maximum penetration depth |
| **z_contact** | Contact Point | m | First contact position |
| **R²** | Coefficient of Determination | - | Model fit quality |
| **A_hyst** | Hysteresis Area | - | Energy dissipation per cycle |

### Output Files:
- `indentation_results.csv` - Summary table for all files
- Processed curve export (optional)

## Parameter Configuration

### Key Analysis Parameters:

```python
config = {
    'k_cantilever': 0.1,      # Cantilever spring constant [N/m]
    'R': 2.5e-6,              # Indenter radius [m] (for spherical model)
    'alpha': 18,              # Cone angle [°] (for conical model)
    'model_type': 'spherical',# 'spherical' or 'conical'
    'nu': 0.5,                # Poisson's ratio (0.5 for cells)
    'smoothing_window': 21,   # Smoothing window (odd number)
    'contact_threshold': 5    # Contact threshold (in σ of noise)
}
```

### Parameter Selection Guidelines:
1. **Cantilever spring constant** - Should be pre-calibrated
2. **Indenter radius** - Provided by manufacturer
3. **Poisson's ratio** - 0.5 is standard for most cells
4. **Model type** - Depends on indenter geometry

## Usage Examples

### Example 1: Single File Processing
```python
# Specify your file path
results = process_user_file(
    filepath="data/curve_01.txt",
    k_cantilever=0.1,      # N/m
    R=2.5e-6,              # m
    z_column=0,            # displacement column
    f_column=1,            # force column
    skiprows=1             # skip header
)
```

### Example 2: Batch Processing
```python
# Process all files in a folder
df_results = process_folder(
    folder_path="data/experiment_1/",
    file_pattern="*.txt",      # all text files
    k_cantilever=0.1,
    R=2.5e-6,
    save_results=True         # export to CSV
)
```

### Example 3: Export Processed Data
```python
# After processing, save the curve
export_processed_curve(
    results, 
    output_file="results/processed_curve.csv"
)
```

## Implementation Details

### Physical Models:
1. **Hertz Model (Spherical)**:
   ```
   F = (4/3) * (E/(1-ν²)) * √R * δ^(3/2)
   ```

2. **Sneddon Model (Conical)**:
   ```
   F = (2/π) * (E/(1-ν²)) * tan(α) * δ²
   ```

### Analysis Methods:
- **Smoothing**: Savitzky-Golay filter (preserves derivatives)
- **Contact detection**: Statistical threshold method
- **Curve fitting**: Least squares (Levenberg-Marquardt algorithm)
- **Error estimation**: Covariance matrix of fit

## Troubleshooting

| Issue | Possible Cause | Solution |
|-------|----------------|----------|
| Poor fit quality | Wrong model | Check indenter geometry |
| Negative Young's modulus | Incorrect contact point | Increase contact threshold |
| Large result scatter | Noisy data | Increase smoothing window |
| No hysteresis | Too elastic sample | Check indentation rate |

## Result Interpretation

### Young's Modulus (E):
- **10²-10³ Pa**: Very soft cells (embryonic)
- **10³-10⁴ Pa**: Typical mammalian cells
- **10⁴-10⁵ Pa**: Stiff cells (osteoblasts, fibroblasts)

### Loss Factor (tan δ = G''/G'):
- **< 0.1**: Predominantly elastic behavior
- **0.1-0.5**: Viscoelastic behavior
- **> 0.5**: Predominantly viscous behavior

## References

1. Hertz, H. (1882). "On the contact of elastic solids"
2. Sneddon, I.N. (1965). "The relation between load and penetration"

## License

This script is distributed under the MIT License. You are free to use, modify, and distribute it with attribution.

## Contact and Support

If you encounter issues or have questions:
1. Verify your data format
2. Confirm cantilever parameters
3. Test with synthetic data first

For biological samples, it is recommended to perform at least 50 measurements on different cells for statistically significant results.
e-mail: karina_urazova@icloud.com

---
*Script developed for analyzing mechanical properties of living cells using AFM nanoindentation.*
