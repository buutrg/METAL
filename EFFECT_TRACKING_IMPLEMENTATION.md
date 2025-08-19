# EFFECT Tracking Implementation in METAL

## Overview

This document describes the implementation of EFFECT tracking functionality in the METAL meta-analysis software. The feature allows users to track individual EFFECT values from each input file in the meta-analysis output.

## Features Added

### 1. New Command: TRACKEFFECTS
- **Syntax**: `TRACKEFFECTS [ON|OFF]`
- **Default**: OFF
- **Description**: Enables or disables tracking of EFFECT values from each input file

### 2. Output Format Changes
When EFFECT tracking is enabled, the output file includes additional columns:
- One column per input file with the format: `Effect_1`, `Effect_2`, `Effect_3`, etc.
- Each column contains the **harmonized** EFFECT values from the corresponding input file
- Values are formatted according to the `EFFECT_PRINT_PRECISION` setting
- **Harmonized values** means the effects have been flipped to ensure consistent allele reference across studies

### 3. Documentation
- Help text includes the new TRACKEFFECTS command
- Info file includes documentation for each EFFECT tracking column
- Each column is labeled with the source filename

## Implementation Details

### Global Variables Added
```cpp
Vector * effectValues = NULL;  // Track EFFECT values from each input file
int effectValuesCount = 0;
bool trackEffects = false;
```

### Key Functions Modified

1. **ProcessFile()**: 
   - Added collection of EFFECT values during file processing
   - Stores EFFECT values in a local Vector for each file
   - Handles log transformation if specified

2. **Analyze()**: 
   - Added EFFECT tracking columns to output header
   - Added EFFECT values to output data
   - Added documentation to info file

3. **ClearAll()**: 
   - Added cleanup for effectValues array

4. **CreateNewMarkerId()**: 
   - Added extension of currentEffects Vector for new markers

### Data Flow
1. User enables EFFECT tracking with `TRACKEFFECTS ON`
2. During file processing, EFFECT values are harmonized (flipped if needed) and stored
3. After each file is processed, harmonized EFFECT values are added to global array
4. During analysis, EFFECT tracking columns are added to output
5. Each row includes harmonized EFFECT values from all input files

## Usage Examples

### Basic Usage
```
TRACKEFFECTS ON
MARKER SNP
EFFECT BETA
PROCESS file1.txt
PROCESS file2.txt
ANALYZE
```

### Output Format
```
MarkerName  Allele1  Allele2  Weight  Zscore  P-value  Direction  Effect_1  Effect_2
rs123       A        T        1000    1.5     0.1      ++         0.05      0.03
rs456       C        G        1000    -0.8    0.4      -+         -0.02     0.01
```

**Note**: The EFFECT tracking columns show harmonized values that are consistent with the Direction column. For example:
- Direction `++` means both studies have positive effects (Effect_1 > 0, Effect_2 > 0)
- Direction `-+` means first study has negative effect, second has positive effect (Effect_1 < 0, Effect_2 > 0)

## Compatibility

- **Backward Compatible**: When EFFECT tracking is disabled (default), output format is identical to original
- **No Performance Impact**: EFFECT tracking only adds overhead when explicitly enabled
- **Memory Efficient**: EFFECT values are stored only when tracking is enabled

## Testing

The implementation has been tested with:
- Single file processing
- Multiple file processing  
- EFFECT tracking enabled/disabled
- Various input file formats
- Memory management and cleanup

## Files Modified

1. `metal/Main.cpp` - Main implementation
2. `metal/Main.h` - Header declarations (no changes needed)
3. `CMakeLists.txt` - Updated CMake version requirement

## Important Notes

### Harmonization
The EFFECT tracking columns show **harmonized** values, meaning:
- Effects are flipped to ensure consistent allele reference across studies
- The values are consistent with the Direction column in the output
- This allows direct comparison of effect directions across studies

### Consistency with Meta-analysis
- The EFFECT tracking values use the same harmonization as the meta-analysis
- This ensures the individual study effects are directly comparable to the combined effect
- Users can verify the contribution of each study to the overall meta-analysis result

## Future Enhancements

Potential improvements could include:
- Custom column naming for EFFECT tracking
- Filtering of EFFECT values
- Statistical summaries of EFFECT distributions
- Integration with heterogeneity analysis
- Option to show both original and harmonized EFFECT values
