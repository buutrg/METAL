# METAL

[![Build Status](https://travis-ci.org/statgen/METAL.svg?branch=master)](https://travis-ci.org/statgen/METAL)

METAL is a tool for the meta-analysis of genome-wide association studies. More information about METAL is available at https://genome.sph.umich.edu/wiki/METAL.

## Requirements

- CMake cross-platform make system

## Compilation

Download and unarchive latest stable version of METAL source code or clone this GitHub repository to obtain development version.
Then execute the following commands inside the directory with METAL source code:

```
mkdir build && cd build
cmake -DCMAKE_BUILD_TYPE=Release ..
make
make test
make install
```

The compiled `metal` executable will be installed into `build/bin` directory.

## New features

- 2024-12-19
  - **Comprehensive Tracking Features**: Implemented a suite of tracking commands to preserve individual study data in meta-analysis results:
    - `TRACKEFFECTS [ON|OFF]`: Tracks individual effect values from each input file as `EFFECT_1`, `EFFECT_2`, etc. columns. Stores harmonized effect values (after allele flipping).
    - `TRACKSTDERR [ON|OFF]`: Tracks individual standard error values from each input file as `StdErr_1`, `StdErr_2`, etc. columns. Automatically calculates standard errors from weights when STDERR column is not present.
    - `TRACKCASECTRL [ON|OFF]`: Tracks case/control allele frequencies and sample sizes as `AF_case_1`, `AF_ctrl_1`, `N_case_1`, `N_ctrl_1`, etc. columns. Stores harmonized frequencies (after allele flipping).
    - `TRACKPOSITIONS [ON|OFF]`: Tracks chromosome and position information from input files. Requires `CHROMOSOMELABEL` and `POSITIONLABEL` commands to specify column headers.
  - **Enhanced Direction Column**: The `Direction` column now reflects harmonized effect directions (after allele flipping) rather than original directions.
  - **Flexible Column Label Mapping**: Added commands to specify custom column headers:
    - `AFCASELABEL [label]`: Specify case allele frequency column header (default: `AF_case`)
    - `AFCTRLLABEL [label]`: Specify control allele frequency column header (default: `AF_ctrl`)
    - `NCASELABEL [label]`: Specify case sample size column header (default: `N_case`)
    - `NCTRLLABEL [label]`: Specify control sample size column header (default: `N_ctrl`)
    - `CHROMOSOMELABEL [label]`: Specify chromosome column header (default: `CHROMOSOME`)
    - `POSITIONLABEL [label]`: Specify position column header (default: `POSITION`)
  - **Comprehensive Documentation**: Added extensive documentation files covering all new features, implementation details, usage examples, and test results.

- 2020-05-05
  - Implemented new options `EFFECT_PRINT_PRECISION` and `STDERR_PRINT_PRECISION` to control print precision of `Effect` and `StdErr` output columns, correspondingly. For example, setting `EFFECT_PRINT_PRECISION 12` will force METAL to print 12 digits after the decimal point in the `Effect` column. The default is to print 4 digits after the decimal point for both `Effect` and `StdErr`. Increasing precision is useful when you plan to use `Effect` and `StdErr` values computed by METAL in other downstream analyses.

- 2018-08-28
  - Implemented new option `TRACKPOSITIONS`. If enabled, METAL checks if chromosome and position of a variant match across studies. The `Chromosome` and `Position` fields are propagated to the meta-analysis result file (useful when generating Manhattan or LocusZoom plots).

- 2017-12-21
  - Correction for sample overlap in sample size weighted meta-analysis (developed by Sebanti Sengupta). First, METAL estimates the number of individuals that are common among two or more studies based on Z-statistics from each study. Then, METAL adjusts for sample overlap when calculating overall Z-statistics by correcting the weights with the estimated number of individuals in common. 

    To enable correction for sample overlap in your sample size weighted meta-analysis, use `OVERLAP ON` command (valid only with `SCHEME SAMPLESIZE`) before any `PROCESS` commands. By default, METAL uses Z-statistics <1 for esimating the number of individuals that are common among studies. To change this threshold, use `ZCUTOFF [number]` command. The `N` column in the result file will store sample size corrected for overlap between studies.

    More information on this specific method can be found on our wiki: https://genome.sph.umich.edu/wiki/METAL_Documentation#Sample_Overlap_Correction

## Usage Examples

### Basic Tracking Setup
```
# Enable all tracking features
TRACKEFFECTS ON
TRACKSTDERR ON
TRACKCASECTRL ON
TRACKPOSITIONS ON

# Specify custom column labels if needed
CHROMOSOMELABEL CHR
POSITIONLABEL POS
AFCASELABEL AF_case
AFCTRLLABEL AF_ctrl
NCASELABEL N_case
NCTRLLABEL N_ctrl

# Process input files
PROCESS study1.txt
PROCESS study2.txt
PROCESS study3.txt

# Analyze with heterogeneity testing
HETEROGENEITY ON
ANALYZE
```

### Output Format
When tracking is enabled, the output file will include additional columns:
- `EFFECT_1`, `EFFECT_2`, `EFFECT_3`: Individual effect values from each study
- `StdErr_1`, `StdErr_2`, `StdErr_3`: Individual standard errors from each study
- `AF_case_1`, `AF_ctrl_1`, `N_case_1`, `N_ctrl_1`: Case/control data from each study
- `Chromosome`, `Position`: Genomic coordinates (when TRACKPOSITIONS is enabled)

## Documentation

Complete documentation is available at https://genome.sph.umich.edu/wiki/METAL_Documentation.

### New Documentation Files
- `TRACKING_FEATURES_IMPLEMENTATION.md`: Comprehensive guide to all tracking features
- `STDERR_TRACKING_IMPLEMENTATION.md`: Detailed STDERR tracking implementation
- `ALL_TRACKING_FEATURES_TEST.md`: Test results for all tracking features combined
- `SCHEME_STDERR_TEST_RESULTS.md`: Comparison of different meta-analysis schemes
- `ALL_TRACKING_HETEROGENEITY_TEST_RESULTS.md`: Heterogeneity testing with tracking
- `HARMONIZED_DIRECTION_IMPLEMENTATION.md`: Direction column harmonization details
