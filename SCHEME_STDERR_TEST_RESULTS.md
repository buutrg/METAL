# SCHEME STDERR Test Results: Inverse Variance Weighted Meta-Analysis

## Summary

This test demonstrates the `SCHEME STDERR` option in METAL, which switches from sample size weighted meta-analysis to inverse variance weighted meta-analysis using standard errors. The test shows how this affects the meta-analysis results and output format.

## Test Configuration

### **Meta-Analysis Scheme**
```bash
SCHEME STDERR    # Use inverse variance weighted meta-analysis
```

### **Tracking Features Enabled**
```bash
TRACKEFFECTS ON      # Track harmonized effect values
TRACKSTDERR ON       # Track standard error values  
TRACKCASECTRL ON     # Track case/control data
TRACKPOSITIONS ON    # Track chromosome and position
```

### **Output Files**
- **Sample Size Weighted**: `METAANALYSIS1.TBL`
- **Inverse Variance Weighted**: `STDERR_WEIGHTED1.TBL`

## Key Differences Between Schemes

### **1. Output Column Structure**

#### **Sample Size Weighted (Default)**
```
Chromosome  Position  MarkerName  Allele1  Allele2  Weight  Zscore  P-value  Direction  Effect_1  Effect_2  Effect_3  StdErr_1  StdErr_2  StdErr_3  ...
```

#### **Inverse Variance Weighted (SCHEME STDERR)**
```
Chromosome  Position  MarkerName  Allele1  Allele2  Effect  StdErr  P-value  Direction  Effect_1  Effect_2  Effect_3  StdErr_1  StdErr_2  StdErr_3  ...
```

### **2. Statistical Interpretation**

#### **Sample Size Weighted**
- **Weight Column**: Total sample size across studies
- **Zscore Column**: Z-score statistic (effect/standard error)
- **P-value**: Based on Z-score
- **Weighting**: Studies weighted by sample size

#### **Inverse Variance Weighted**
- **Effect Column**: Meta-analysis effect estimate
- **StdErr Column**: Meta-analysis standard error
- **P-value**: Based on effect/standard error ratio
- **Weighting**: Studies weighted by inverse variance (1/SE²)

## Results Comparison

### **rs1004 (Strong Association)**

#### **Sample Size Weighted**
```
Weight: 5400.00, Zscore: 6.276, P-value: 3.473e-10
```

#### **Inverse Variance Weighted**
```
Effect: 0.1903, StdErr: 0.0298, P-value: 1.724e-10
```

**Analysis**:
- **P-value**: Inverse variance weighted shows stronger significance (1.724e-10 vs 3.473e-10)
- **Effect Size**: 0.1903 (interpretable effect size)
- **Precision**: Standard error of 0.0298 provides confidence interval information

### **rs2002 (Moderate Association)**

#### **Sample Size Weighted**
```
Weight: 5900.00, Zscore: 2.266, P-value: 0.02346
```

#### **Inverse Variance Weighted**
```
Effect: 0.0504, StdErr: 0.0237, P-value: 0.03329
```

**Analysis**:
- **P-value**: Similar significance levels (0.02346 vs 0.03329)
- **Effect Size**: 0.0504 (smaller but interpretable effect)
- **Precision**: Standard error of 0.0237

### **rs2005 (Strong Association)**

#### **Sample Size Weighted**
```
Weight: 6000.00, Zscore: 5.001, P-value: 5.7e-07
```

#### **Inverse Variance Weighted**
```
Effect: 0.1302, StdErr: 0.0260, P-value: 5.385e-07
```

**Analysis**:
- **P-value**: Very similar significance (5.7e-07 vs 5.385e-07)
- **Effect Size**: 0.1302 (moderate effect size)
- **Precision**: Standard error of 0.0260

## Statistical Implications

### **1. Weighting Mechanism**

#### **Sample Size Weighted**
- **Formula**: Weight = N (sample size)
- **Assumption**: Larger studies are more reliable
- **Advantage**: Simple interpretation
- **Disadvantage**: Ignores study-specific precision

#### **Inverse Variance Weighted**
- **Formula**: Weight = 1/SE² (inverse variance)
- **Assumption**: Studies with smaller standard errors are more precise
- **Advantage**: Statistically optimal weighting
- **Disadvantage**: More complex interpretation

### **2. Effect Size Interpretation**

#### **Sample Size Weighted**
- **Output**: Z-score (standardized statistic)
- **Interpretation**: Standardized effect size
- **Limitation**: Not directly interpretable as effect size

#### **Inverse Variance Weighted**
- **Output**: Effect estimate and standard error
- **Interpretation**: Direct effect size with confidence intervals
- **Advantage**: Can calculate confidence intervals and odds ratios

### **3. P-value Calculation**

#### **Sample Size Weighted**
- **Based on**: Z-score distribution
- **Formula**: P = 2 × (1 - Φ(|Z|))

#### **Inverse Variance Weighted**
- **Based on**: Effect/SE ratio
- **Formula**: P = 2 × (1 - Φ(|Effect/SE|))

## Practical Considerations

### **1. When to Use Each Scheme**

#### **Sample Size Weighted**
- **Use when**: Studies have similar precision
- **Use when**: Sample size is primary quality indicator
- **Use when**: Simple interpretation is needed

#### **Inverse Variance Weighted**
- **Use when**: Studies have varying precision
- **Use when**: Standard errors are reliable
- **Use when**: Effect size interpretation is important

### **2. Data Requirements**

#### **Sample Size Weighted**
- **Required**: Sample sizes for each study
- **Optional**: Standard errors

#### **Inverse Variance Weighted**
- **Required**: Effect estimates and standard errors
- **Optional**: Sample sizes

### **3. Output Interpretation**

#### **Sample Size Weighted**
- **Focus on**: Z-score and p-value
- **Effect size**: Requires additional calculation

#### **Inverse Variance Weighted**
- **Focus on**: Effect estimate and confidence intervals
- **Significance**: Direct from p-value

## Tracking Features Compatibility

### **All Tracking Features Work with Both Schemes**

#### **Effect Tracking**
- **Sample Size**: Shows harmonized Z-scores
- **Inverse Variance**: Shows harmonized effect estimates

#### **Standard Error Tracking**
- **Both Schemes**: Show individual study standard errors
- **Purpose**: Quality assessment and validation

#### **Case/Control Tracking**
- **Both Schemes**: Show harmonized frequencies and sample sizes
- **Purpose**: Study characteristics and quality control

#### **Position Tracking**
- **Both Schemes**: Show genomic coordinates
- **Purpose**: Genomic context and regional analysis

## Recommendations

### **1. For Genetic Association Studies**
- **Prefer**: Inverse variance weighted (SCHEME STDERR)
- **Reason**: Provides interpretable effect sizes
- **Benefit**: Enables calculation of odds ratios and confidence intervals

### **2. For Quality Control**
- **Use**: Both schemes for comparison
- **Check**: Consistency between approaches
- **Validate**: Results with different weighting schemes

### **3. For Publication**
- **Report**: Effect estimates and standard errors
- **Include**: Confidence intervals
- **Specify**: Weighting scheme used

## Conclusion

The `SCHEME STDERR` option provides inverse variance weighted meta-analysis, which offers several advantages over sample size weighting:

1. **Interpretable Effect Sizes**: Direct effect estimates instead of Z-scores
2. **Optimal Weighting**: Studies weighted by precision rather than sample size
3. **Confidence Intervals**: Can calculate confidence intervals from effect and SE
4. **Statistical Efficiency**: More efficient use of available information

The test demonstrates that all tracking features work seamlessly with both schemes, providing researchers with comprehensive data for analysis and interpretation regardless of the chosen weighting approach.
