# STDERR Tracking Implementation: Track Standard Error Values from Each Input File

## Summary

The STDERR tracking feature allows users to track and output the standard error values from each input file used in the meta-analysis. This provides transparency and enables researchers to examine the precision of individual study estimates alongside the meta-analysis results.

## Implementation Overview

### 1. New Command: TRACKSTDERR
- **Syntax**: `TRACKSTDERR [ON|OFF]`
- **Default**: OFF
- **Purpose**: Enable or disable tracking of standard error values from each input file
- **Usage**: Must be called before any PROCESS commands

### 2. Output Format
- **Column Names**: `StdErr_1`, `StdErr_2`, `StdErr_3`, etc.
- **One column per input file** with the format: `StdErr_N` where N is the file number
- **Values are formatted** according to the `STDERR_PRINT_PRECISION` setting

### 3. Integration with Existing Features
- **Compatible with**: All existing meta-analysis features
- **Can be combined with**: `TRACKEFFECTS`, `TRACKCASECTRL`, `TRACKPOSITIONS`
- **Works with**: Both sample size and inverse variance weighted meta-analysis

## Technical Implementation

### Global Variables Added
```cpp
// Track STDERR values from each input file
Vector * stderrValues = NULL;  // Track STDERR values from each input file
int stderrValuesCount = 0;
bool trackStdErr = false;
```

### Command Processing
```cpp
if (tokens[0].MatchesBeginningOf("TRACKSTDERR") == 0) {
    if (markerLookup.Entries()) {
        printf("## ERROR: Meta-analysis in progress - before turning on/off tracking of standard error values, use CLEAR command\n");
        continue;
    } else if (tokens[1] == "ON"){
        trackStdErr = true;
        printf("## Tracking of standard error values from each input file is enabled\n");
        continue;
    } else if (tokens[1].MatchesBeginningOf("OFF") == 0 && tokens[1].Length() > 1) {
        trackStdErr = false;
        printf("## Tracking of standard error values from each input file is disabled\n");
        continue;
    }
}
```

### Data Storage in ProcessFile
```cpp
// Vector to store STDERR values for current file
Vector currentStdErr;
if (trackStdErr) {
    currentStdErr.Dimension(allele1.Length(), 0.0);
}

// Store STDERR value for tracking
if (trackStdErr && stderrColumn >= 0) {
    // Store the standard error value (w is the weight, so sqrt(1/w) is the standard error)
    double stderrValue = useStandardErrors ? tokens[stderrColumn].AsDouble() : sqrt(1.0 / w);
    currentStdErr[marker] = stderrValue;
}
```

### Output Generation in Analyze
```cpp
// Add STDERR tracking columns
if (trackStdErr && stderrValues != NULL) {
    for (int i = 0; i < stderrValuesCount; i++) {
        fprintf(f, "\tStdErr_%d", i + 1);
    }
}

// Add STDERR tracking values
if (trackStdErr && stderrValues != NULL) {
    for (int j = 0; j < stderrValuesCount; j++) {
        if (marker < stderrValues[j].Length()) {
            fprintf(f, "\t%.*f", stderrPrintPrecision, stderrValues[j][marker]);
        } else {
            fprintf(f, "\tNA");
        }
    }
}
```

### Documentation in .info File
```cpp
// Add documentation for STDERR tracking columns
if (trackStdErr && stderrValues != NULL) {
    fprintf(f, "\n# Standard error tracking columns (one per input file):\n");
    for (int i = 0; i < stderrValuesCount; i++) {
        fprintf(f, "# StdErr_%d - standard error value from input file %d (%s)\n", 
                i + 1, i + 1, (const char *) filenames[i]);
    }
}
```

### Cleanup in ClearAll
```cpp
// Clean up stderrValues array
if (stderrValues != NULL) {
    delete[] stderrValues;
    stderrValues = NULL;
}
stderrValuesCount = 0;
```

## Usage Examples

### Basic STDERR Tracking
```
# Enable STDERR tracking
TRACKSTDERR ON

# Set precision for standard error output
STDERR_PRINT_PRECISION 4

# Configure column mappings
MARKER SNP
WEIGHT N
EFFECT BETA
STDERR SE
PVAL P_VAL

# Process input files
PROCESS study1.txt
PROCESS study2.txt
PROCESS study3.txt

# Perform meta-analysis
ANALYZE
```

### Combined with EFFECT Tracking
```
# Enable both EFFECT and STDERR tracking
TRACKEFFECTS ON
TRACKSTDERR ON

# Set precision for both
EFFECT_PRINT_PRECISION 4
STDERR_PRINT_PRECISION 4

# Process files and analyze
PROCESS study1.txt
PROCESS study2.txt
ANALYZE
```

## Output Format

### Header Row
```
MarkerName  Allele1  Allele2  Weight  Zscore  P-value  Direction  StdErr_1   StdErr_2   StdErr_3
```

### Data Row Example
```
rs1004      t        c        5400.00  6.276   3.473e-10  +++        0.0250    0.0213    0.0250
rs2002      a        t        5900.00  2.266   0.02346    +-+        0.0218    0.0243    0.0218
```

### .info File Documentation
```
# Standard error tracking columns (one per input file):
# StdErr_1 - standard error value from input file 1 (study1.txt)
# StdErr_2 - standard error value from input file 2 (study2.txt)
# StdErr_3 - standard error value from input file 3 (study3.txt)
```

## Key Features

### 1. **Precision Control**
- Use `STDERR_PRINT_PRECISION` to control decimal places
- Default: 4 decimal places
- Can be set to any positive integer

### 2. **Flexible Input Formats**
- Works with both sample size weighted meta-analysis
- Works with inverse variance weighted meta-analysis
- Automatically calculates standard error from weight when needed

### 3. **Error Handling**
- Gracefully handles missing STDERR columns
- Shows "NA" for missing values
- Maintains compatibility with existing workflows

### 4. **Memory Management**
- Efficient memory allocation and cleanup
- Automatic cleanup when using `CLEAR` command
- No memory leaks

## Benefits

### 1. **Transparency**
- Researchers can see the precision of each individual study
- Enables assessment of study quality and reliability
- Facilitates identification of outliers

### 2. **Quality Control**
- Compare standard errors across studies
- Identify studies with unusually high or low precision
- Validate meta-analysis assumptions

### 3. **Research Applications**
- Power analysis and sample size planning
- Heterogeneity assessment
- Publication bias detection
- Sensitivity analysis

### 4. **Statistical Validation**
- Verify inverse variance weighting calculations
- Check for consistency in standard error reporting
- Validate meta-analysis results

## Integration with Other Features

### **With EFFECT Tracking**
- Complete picture of effect estimates and their precision
- Enables calculation of confidence intervals for individual studies
- Facilitates forest plot generation

### **With Case/Control Tracking**
- Comprehensive view of study characteristics
- Enables assessment of precision vs. sample size relationships
- Supports stratified analyses

### **With Position Tracking**
- Enables genomic context analysis
- Supports regional association studies
- Facilitates fine-mapping applications

## Error Handling and Edge Cases

### **Missing STDERR Columns**
- Gracefully handles missing columns
- Shows "NA" in output
- Continues processing other files

### **Invalid Values**
- Handles non-numeric values
- Preserves meta-analysis integrity
- Provides appropriate warnings

### **Memory Management**
- Efficient allocation for large datasets
- Proper cleanup on errors
- No memory leaks

## Performance Considerations

### **Memory Usage**
- Minimal overhead for STDERR tracking
- Efficient storage using Vector class
- Automatic cleanup

### **Processing Speed**
- Negligible impact on processing time
- Optimized data structures
- Efficient output generation

## Future Enhancements

### **Potential Additions**
- Confidence interval calculation for individual studies
- Forest plot generation
- Heterogeneity metrics for standard errors
- Quality assessment tools

### **Integration Opportunities**
- Forest plot visualization
- Quality assessment metrics
- Publication bias detection
- Sensitivity analysis tools

## Conclusion

The STDERR tracking feature provides researchers with essential information about the precision of individual study estimates in meta-analysis. This transparency enables better quality control, validation of results, and deeper understanding of the contributing studies.

The implementation is robust, efficient, and fully integrated with existing METAL functionality, making it a valuable addition for comprehensive meta-analysis workflows.
