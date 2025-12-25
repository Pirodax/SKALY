# Add Calculation Skill

## Description
Add a new financial calculation rule to SKALY with proper documentation and testing.

## Usage
```
/add-calculation [calculation-name]
```

## What This Skill Does

1. **Asks about the calculation** requirements
2. **Reviews existing calculations** in docs/04_CALCULATIONS_RULES.md
3. **Creates the calculation function** with proper TypeScript types
4. **Adds comprehensive tests**
5. **Updates documentation**
6. **Provides usage examples**

## Process

### Step 1: Gather Requirements
Ask the user:
- Calculation name and purpose
- Input parameters needed
- Expected output
- Formula or business logic
- Edge cases to handle
- Example values for testing

### Step 2: Review Related Calculations
Read `docs/04_CALCULATIONS_RULES.md` to:
- Check if similar calculation exists
- Follow naming conventions
- Understand related formulas
- Ensure consistency

### Step 3: Create Calculation Function

File location: `lib/calculations/[calculationName].ts`

Template:
```typescript
/**
 * [Calculation Name]
 *
 * @description [Detailed description of what this calculates]
 *
 * @param [param1] - [Description]
 * @param [param2] - [Description]
 * @returns [Description of return value]
 *
 * @formula
 * ```
 * [Mathematical formula]
 * ```
 *
 * @example
 * ```typescript
 * const result = calculate[Name]({
 *   param1: value1,
 *   param2: value2
 * })
 * // result: expectedValue
 * ```
 *
 * @throws {Error} If [condition that throws error]
 */
export function calculate[Name](
  params: [ParamType]
): [ReturnType] {
  // Input validation
  if ([validation condition]) {
    throw new Error('[Error message]')
  }

  // Calculation logic
  const result = [calculation]

  // Return
  return result
}

// Type definitions
interface [ParamType] {
  [field]: [type]
}
```

### Step 4: Create Tests

File location: `__tests__/calculations/[calculationName].test.ts`

Template:
```typescript
import { describe, it, expect } from '@jest/globals'
import { calculate[Name] } from '@/lib/calculations/[calculationName]'

describe('calculate[Name]', () => {
  it('should calculate correctly with typical values', () => {
    const result = calculate[Name]({
      // typical values
    })

    expect(result).toBe(expectedValue)
  })

  it('should handle edge case: [description]', () => {
    const result = calculate[Name]({
      // edge case values
    })

    expect(result).toBe(expectedValue)
  })

  it('should throw error for invalid input', () => {
    expect(() => {
      calculate[Name]({
        // invalid values
      })
    }).toThrow('[Expected error message]')
  })

  it('should handle zero values correctly', () => {
    const result = calculate[Name]({
      // zero values
    })

    expect(result).toBe(0)
  })

  it('should handle negative values [if applicable]', () => {
    const result = calculate[Name]({
      // negative values
    })

    expect(result).toBe(expectedValue)
  })
})
```

### Step 5: Update Documentation

Add to `docs/04_CALCULATIONS_RULES.md`:

```markdown
### [Calculation Number]. [Calculation Name]

**Purpose**: [Brief description]

**Formula:**
\`\`\`typescript
function calculate[Name](
  [params]
): [returnType] {
  // Step-by-step calculation
  const step1 = [formula]
  const step2 = [formula]

  return result
}
\`\`\`

**Parameters:**
| Parameter | Type | Description |
|-----------|------|-------------|
| [param1] | [type] | [description] |

**Returns:** [Description of return value]

**Example:**
\`\`\`
Input:
  [param1]: [value]
  [param2]: [value]

Calculation:
  [step1]: [value]
  [step2]: [value]

Output: [result]
\`\`\`

**Edge Cases:**
- [Edge case 1]: [How handled]
- [Edge case 2]: [How handled]

---
```

### Step 6: Integration Checklist

- [ ] Calculation function created with full documentation
- [ ] TypeScript types defined
- [ ] Input validation implemented
- [ ] Unit tests written (including edge cases)
- [ ] All tests passing
- [ ] Documentation updated in docs/04_CALCULATIONS_RULES.md
- [ ] Usage examples provided
- [ ] Integration point identified (where to use this calculation)
- [ ] Error handling implemented

## Common Calculation Patterns

### Financial Percentage Calculation
```typescript
const percentage = (value / total) * 100
```

### Provisions Calculation
```typescript
const provision = amount * (rate / 100)
```

### Net Calculation
```typescript
const net = gross - deductions
```

### Average Calculation
```typescript
const average = total / count
```

### Projection with Compound Interest
```typescript
const future = present * Math.pow(1 + rate / 100, periods)
```

## Best Practices

- Always validate inputs (positive numbers, non-null, etc.)
- Handle edge cases (zero, negative, null)
- Use descriptive variable names
- Add comprehensive comments
- Test with realistic values
- Document the formula clearly
- Round financial values appropriately (2 decimals)
- Never use `toFixed()` for calculations (only for display)
- Consider precision issues with floating point

## Example Usage

```
User: /add-calculation effective-tax-rate

Claude: I'll help you add a calculation for effective tax rate.

Questions:
1. Should this calculate the effective rate based on:
   - Total income and total tax paid? (most common)
   - Or marginal tax brackets?

2. What inputs are needed:
   - Gross income
   - Total tax paid
   - Or more detailed breakdown?

3. Any special cases to handle?
   - Negative income?
   - Zero tax?

[After gathering info, creates the function, tests, and documentation]

Created files:
✓ lib/calculations/calculateEffectiveTaxRate.ts
✓ __tests__/calculations/calculateEffectiveTaxRate.test.ts
✓ Updated docs/04_CALCULATIONS_RULES.md

The effective tax rate calculation is ready! Here's how to use it:

[Provides usage example and integration points]
```
