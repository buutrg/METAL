# Original Direction Implementation: Direction Column Now Shows Original Directions

## Summary

The Direction column in the meta-analysis output has been modified to reflect the **original effect directions** from each input file, rather than the harmonized directions. This provides users with a clear view of the original effect patterns across studies before harmonization.

## Implementation Changes

### Code Modification
```cpp
// Store original effect value for direction calculation (before any transformations)
double originalEffectForDirection = z;

// ... later in the code ...

// Calculate direction based on original effect value (before harmonization)
// Use the original effect value stored earlier, before any transformations
direction[marker] = originalEffectForDirection == 0.0 ? '0' : (originalEffectForDirection > 0.0 ? '+' : '-');
```

### Key Changes
1. **Original Effect Storage**: Store the effect value immediately after reading from the input file, before any transformations
2. **Direction Calculation**: Use the original effect value for direction calculation instead of the harmonized value
3. **Timing**: Store the original effect before the `flip` operation and `FlipAlleles` function

## Verification Results

### Example: rs1004
**Original Data:**
```
Study 1: BETA = +0.20000 (positive)
Study 2: BETA = -0.18000 (negative) 
Study 3: BETA = +0.19000 (positive)
```

**Previous Output (Harmonized):**
```
Direction: --- (all negative after harmonization)
```

**New Output (Original):**
```
Direction: +-+ (positive, negative, positive - original directions)
```

### Example: rs2001
**Original Data:**
```
Study 1: BETA = +0.18000 (positive)
Study 2: BETA = -0.17000 (negative)
Study 3: BETA = +0.16000 (positive)
```

**New Output:**
```
Direction: +-+ (positive, negative, positive - original directions)
```

## Benefits of Original Direction Tracking

### 1. **Transparency**
- Users can see the original effect directions from each study
- Clear understanding of which studies had positive vs negative effects
- No confusion about harmonization effects

### 2. **Interpretation Clarity**
- **Direction +-+**: Study 1 positive, Study 2 negative, Study 3 positive (original)
- **Direction ---**: All studies had negative effects (original)
- **Direction +++**: All studies had positive effects (original)

### 3. **Research Insights**
- Identify studies with opposite effects before harmonization
- Understand effect direction heterogeneity across studies
- Better interpretation of meta-analysis results

### 4. **Data Quality Assessment**
- Detect potential issues with effect direction consistency
- Identify studies that may need closer examination
- Validate harmonization process

## Comparison: Original vs Harmonized Directions

### **Original Directions (New Implementation)**
- Shows effect directions as reported in each study
- Reflects the raw data before any harmonization
- Useful for understanding study-specific patterns

### **Harmonized Directions (Previous Implementation)**
- Shows effect directions after allele harmonization
- All effects refer to the same allele reference
- Useful for meta-analysis interpretation

## Technical Details

### **When Original Effect is Stored**
```cpp
// After reading effect from input file
z = eff;
w = 1.0 / (sd * sd);

// Store original effect value for direction calculation (before any transformations)
double originalEffectForDirection = z;
```

### **When Direction is Calculated**
```cpp
// Calculate direction based on original effect value (before harmonization)
direction[marker] = originalEffectForDirection == 0.0 ? '0' : (originalEffectForDirection > 0.0 ? '+' : '-');
```

### **Transformations Applied After Storage**
1. `flip` operation: `z *= -1;`
2. `FlipAlleles` function: Harmonizes alleles and effects
3. Other transformations as needed

## Output Examples

### **Mixed Effect Directions**
```
Marker  Direction  Effect_1   Effect_2   Effect_3
rs1004  +-+       -3.8906   -3.4316   -3.6153
rs2002  --+        2.9889    2.6083    3.1559
rs2005  +-+       -3.1747   -2.8269   -2.6606
rs1005  -++        2.8269    2.3615    3.0485
rs2001  +-+       -3.7190   -3.3896    3.2636
```

### **Interpretation**
- **rs1004**: Original effects were positive, negative, positive
- **rs2002**: Original effects were negative, negative, positive  
- **rs2005**: Original effects were positive, negative, positive
- **rs1005**: Original effects were negative, positive, positive
- **rs2001**: Original effects were positive, negative, positive

## Compatibility

### **Backward Compatibility**
- No changes to user interface or commands
- Same output format and column structure
- Only the Direction column interpretation has changed

### **Existing Features**
- All tracking features continue to work
- Harmonized effects are still tracked correctly
- Case/control tracking remains unchanged

## Conclusion

The Direction column now provides users with the **original effect directions** from each study, offering greater transparency and insight into the raw data before harmonization. This change:

1. **Improves Transparency**: Users can see original effect patterns
2. **Enhances Interpretation**: Better understanding of study-specific effects
3. **Maintains Functionality**: All existing features continue to work
4. **Supports Research**: Better data quality assessment and validation

The implementation successfully balances the need for harmonized meta-analysis with the transparency of original effect directions, providing researchers with comprehensive information for their analyses.
