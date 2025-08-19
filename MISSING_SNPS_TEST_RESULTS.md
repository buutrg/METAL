# Missing SNPs Test Results

## Test Configuration

**Test File**: `test_missing_snps.txt`

**Features Tested**:
- ✅ **TRACKEFFECTS ON**: Track individual effect values from each study
- ✅ **TRACKSTDERR ON**: Track individual standard error values from each study
- ✅ **HETEROGENEITY ON**: Enable heterogeneity testing
- ✅ **Missing SNPs**: Test how METAL handles SNPs present in some studies but not others

**Input Files**:
1. `toy_examples/study1_nucleotide_alleles.txt` (10 markers) - Complete dataset
2. `toy_examples/study2_missing_snps.txt` (8 markers) - Missing rs1003 and rs2001
3. `toy_examples/study3_nucleotide_alleles.txt` (10 markers) - Complete dataset

**Missing SNPs in Study2**:
- **rs1003**: Present in studies 1 and 3, missing from study 2
- **rs2001**: Present in studies 1 and 3, missing from study 2

## Test Results

### ✅ **Success Indicators**
- **All files processed successfully**: Study1 (10), Study2 (8), Study3 (10)
- **Meta-analysis completed**: 10 markers total (all SNPs from all studies)
- **Missing SNPs handled correctly**: Missing values properly tracked
- **Heterogeneity testing enabled**: Second pass analysis completed
- **Tracking columns generated**: All tracking features working with missing data

### 📊 **Output Summary**

**Output File**: `METAANALYSIS1.TBL`

**Columns Generated**:
1. `MarkerName` - SNP identifier
2. `Allele1`, `Allele2` - Harmonized alleles
3. `Weight` - Combined sample size (adjusted for missing studies)
4. `Zscore` - Combined Z-statistic
5. `P-value` - Meta-analysis p-value
6. `Direction` - Effect direction summary (with '?' for missing studies)
7. `Effect_1`, `Effect_2`, `Effect_3` - Individual harmonized effect values
8. `StdErr_1`, `StdErr_2`, `StdErr_3` - Individual standard error values
9. `HetISq`, `HetChiSq`, `HetDf`, `HetPVal` - Heterogeneity statistics

### 🔍 **Missing SNPs Analysis**

#### **rs2001 (Missing from Study2)**
- **Effect values**: 3.719016, **0.000000**, -3.263616
- **Standard errors**: 0.022942, **0.000000**, 0.022942
- **Direction**: +?- (positive, missing, negative)
- **Weight**: 3800.00 (reduced from 5900 due to missing study)
- **P-value**: 0.7474 (less significant due to reduced sample size)
- **Heterogeneity**: I² = 95.9%, Chi² = 24.379, P = 7.914e-07 (high heterogeneity)

#### **rs1003 (Missing from Study2)**
- **Effect values**: -3.540084, **0.000000**, -3.719016
- **Standard errors**: 0.021320, **0.000000**, 0.022361
- **Direction**: -?- (negative, missing, negative)
- **Weight**: 4200.00 (reduced from 5800 due to missing study)
- **P-value**: 2.921e-07 (still highly significant)
- **Heterogeneity**: I² = 0.0%, Chi² = 0.062, P = 0.8036 (no significant heterogeneity)

### 📈 **Key Observations**

**1. Missing Value Handling**:
- ✅ **Effect tracking**: Missing studies show `0.000000` in effect columns
- ✅ **Standard error tracking**: Missing studies show `0.000000` in stderr columns
- ✅ **Direction tracking**: Missing studies show `?` in direction string
- ✅ **Weight adjustment**: Total weight reduced for SNPs missing from some studies

**2. Statistical Impact**:
- **Reduced power**: Missing studies reduce total sample size and statistical power
- **Heterogeneity effects**: Missing studies can affect heterogeneity calculations
- **P-value changes**: Less significant results due to reduced sample size

**3. Direction String Interpretation**:
- **+++**: All three studies present with consistent direction
- **+?-**: Study1 positive, Study2 missing, Study3 negative
- **-?-**: Study1 negative, Study2 missing, Study3 negative

**4. Heterogeneity Analysis**:
- **rs2001**: High heterogeneity (95.9%) due to missing study and inconsistent directions
- **rs1003**: No heterogeneity (0.0%) despite missing study, consistent negative effects

### ✅ **Feature Integration Verification**

**1. Effect + Standard Error Tracking with Missing Data**:
- Both features work correctly with missing SNPs
- Missing values properly represented as `0.000000`
- No crashes or errors during processing

**2. Heterogeneity Integration with Missing Data**:
- Heterogeneity testing works with missing studies
- Degrees of freedom adjusted appropriately (HetDf = 1 for SNPs missing from one study)
- Statistical calculations remain valid

**3. Direction Tracking with Missing Data**:
- Direction string properly indicates missing studies with `?`
- Maintains readability and interpretability

### 🎯 **Test Conclusions**

**✅ Missing SNPs Handled Correctly**:
1. **No data loss**: All SNPs from all studies included in final output
2. **Proper missing value representation**: `0.000000` for missing effects and standard errors
3. **Clear direction indication**: `?` character shows missing studies
4. **Statistical integrity**: Weight and heterogeneity calculations adjusted appropriately
5. **No crashes**: METAL processes missing data gracefully

**✅ Tracking Features Robust**:
- Effect tracking works with missing data
- Standard error tracking works with missing data
- Heterogeneity testing works with missing data
- All features maintain data integrity

**✅ Practical Implications**:
- Researchers can easily identify which studies contributed to each SNP
- Missing data doesn't break the meta-analysis pipeline
- Statistical power is appropriately reduced for SNPs with missing data
- Heterogeneity analysis remains meaningful even with missing studies

This test confirms that METAL's tracking features are robust and handle missing SNPs appropriately, providing researchers with clear information about data availability across studies while maintaining statistical integrity.
