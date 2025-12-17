# 33. Find Missing and Repeating Number
**LeetCode**: Related to [645 - Set Mismatch](https://leetcode.com/problems/set-mismatch/)  
**Difficulty**: Medium

## Problem Statement
Given an unsorted array of size `n` containing numbers from `1` to `n`, where one number appears twice and one number is missing, find the repeating number and the missing number.

## Examples

### Example 1
```
Input: arr = [3,1,2,5,3]
Output: Repeating = 3, Missing = 4
Explanation: 3 appears twice, 4 is missing
```

### Example 2
```
Input: arr = [2,2]
Output: Repeating = 2, Missing = 1
Explanation: 2 appears twice, 1 is missing
```

### Example 3
```
Input: arr = [1,3,3]
Output: Repeating = 3, Missing = 2
```

## Constraints
- `2 <= n <= 10^5`
- `1 <= arr[i] <= n`
- Exactly one number repeats exactly once
- Exactly one number is missing

## Visual Explanation

### ASCII Diagram
```
Array: [3, 1, 2, 5, 3]
Should be: [1, 2, 3, 4, 5]

Approach 1 - Mathematical:
Expected sum = 1+2+3+4+5 = 15
Actual sum = 3+1+2+5+3 = 14
Expected sum² = 1+4+9+16+25 = 55
Actual sum² = 9+1+4+25+9 = 48

Let: x = repeating, y = missing
Equation 1: actualSum - expectedSum = x - y = 14 - 15 = -1
Equation 2: actualSum² - expectedSum² = x² - y² = 48 - 55 = -7
            x² - y² = (x+y)(x-y)
            -7 = (x+y)(-1)
            x+y = 7

Solve: x-y = -1, x+y = 7
       2x = 6, x = 3 (repeating)
       y = 4 (missing)

Approach 2 - XOR:
XOR all array elements and all numbers 1 to n
Result = repeating ⊕ missing

Then use XOR properties to separate them
```

## Approach & Intuition

**Three Approaches**:

**Approach 1: Hash Map** - O(n) time, O(n) space
- Count frequency of each number
- Number with count 2 is repeating
- Number with count 0 is missing

**Approach 2: Mathematical (Sum & Sum of Squares)** - O(n) time, O(1) space
- Use two equations with two unknowns
- Equation 1: x - y = (actualSum - expectedSum)
- Equation 2: x² - y² = (actualSum² - expectedSum²)
- Solve to find x (repeating) and y (missing)
- **Risk**: Potential overflow with large numbers

**Approach 3: XOR Method** - O(n) time, O(1) space, No overflow
- XOR all array elements with numbers 1 to n
- Result is `repeating ⊕ missing`
- Find rightmost set bit in result
- Divide numbers into two groups based on this bit
- XOR each group separately to find repeating and missing

## Algorithm Steps

### Mathematical Approach (Simpler to code):

1. **Calculate sums**:
   - Sum of array elements (S)
   - Sum of 1 to n: `Sn = n*(n+1)/2`
   - Sum of squares of array elements (S2)
   - Sum of squares of 1 to n: `Sn2 = n*(n+1)*(2n+1)/6`

2. **Form equations**:
   - `S - Sn = repeating - missing` → Equation 1
   - `S2 - Sn2 = repeating² - missing²` → Equation 2

3. **Solve for repeating and missing**:
   - From Eq2: `(repeating - missing)(repeating + missing) = S2 - Sn2`
   - `repeating + missing = (S2 - Sn2) / (S - Sn)`
   - Add with Eq1 to get: `2*repeating = (S - Sn) + (repeating + missing)`
   - Subtract to get missing

### XOR Approach (Overflow-safe):

1. **XOR all elements**:
   - `xorAll = arr[0] ⊕ arr[1] ⊕ ... ⊕ arr[n-1] ⊕ 1 ⊕ 2 ⊕ ... ⊕ n`
   - Result: `xorAll = repeating ⊕ missing`

2. **Find rightmost set bit** in xorAll

3. **Partition into two groups**:
   - Group 1: Numbers with that bit set
   - Group 2: Numbers with that bit not set
   - Repeating and missing will be in different groups

4. **XOR each group** to find the two numbers

5. **Determine which is repeating**: Scan array once

## Dry Run (Step-by-Step Execution)

**Input**: `arr = [3,1,2,5,3]`, n = 5

### Mathematical Approach:

```
Step 1: Calculate sums
  S = 3 + 1 + 2 + 5 + 3 = 14
  Sn = 5*(5+1)/2 = 15
  S2 = 9 + 1 + 4 + 25 + 9 = 48
  Sn2 = 5*6*11/6 = 55

Step 2: Form equations
  S - Sn = 14 - 15 = -1         (repeating - missing)
  S2 - Sn2 = 48 - 55 = -7       (repeating² - missing²)

Step 3: Solve
  repeating - missing = -1       ... (1)
  (repeating - missing)(repeating + missing) = -7
  -1 * (repeating + missing) = -7
  repeating + missing = 7        ... (2)
  
  Adding (1) and (2):
  2 * repeating = 6
  repeating = 3 ✓
  
  missing = repeating - (-1) = 3 + 1 = 4 ✓
```

### XOR Approach:

```
Step 1: XOR all
  xorAll = 3⊕1⊕2⊕5⊕3 ⊕ 1⊕2⊕3⊕4⊕5
  
  Regroup: (3⊕3) ⊕ (1⊕1) ⊕ (2⊕2) ⊕ (5⊕5) ⊕ 4
          = 0 ⊕ 0 ⊕ 0 ⊕ 0 ⊕ 4 = 4
  
  Wait, this doesn't match formula. Let me recalculate:
  
  Actually: one 3 appears twice, so:
  xorAll = 3⊕1⊕2⊕5⊕3 ⊕ 1⊕2⊕3⊕4⊕5
         = 3 ⊕ (3⊕3) ⊕ (1⊕1) ⊕ (2⊕2) ⊕ (5⊕5) ⊕ 4
         = 3 ⊕ 4 = 7   (binary: 111)
  
  This is repeating ⊕ missing = 3 ⊕ 4

Step 2: Find rightmost set bit
  xorAll = 7 = 111 (binary)
  rightmost set bit = 1 (LSB)
  Bit position 0 is set

Step 3 & 4: Partition and XOR
  Group with bit 0 set: 1,3,5,3 (from array), 1,3,5 (from 1..n)
  Group with bit 0 not set: 2 (from array), 2,4 (from 1..n)
  
  xor1 = (1⊕3⊕5⊕3 ⊕ 1⊕3⊕5) = 3
  xor2 = (2 ⊕ 2⊕4) = 4
  
Step 5: Determine which is which
  Check array: 3 appears in array → repeating
  4 doesn't appear → missing
```

**Result**: Repeating = 3, Missing = 4 ✓

## C++ Solution

### Mathematical Approach (Simpler)

```cpp
class Solution {
public:
    vector<int> findMissingRepeating(vector<int>& arr) {
        int n = arr.size();
        
        // Step 1: Calculate actual sum and sum of squares
        long long actualSum = 0;        // Sum of array elements
        long long actualSumSq = 0;      // Sum of squares of array elements
        
        for (int num : arr) {
            actualSum += num;
            actualSumSq += (long long)num * num;
        }
        
        // Step 2: Calculate expected sum and sum of squares for 1 to n
        // Formula: Sum of first n natural numbers = n*(n+1)/2
        long long expectedSum = (long long)n * (n + 1) / 2;
        
        // Formula: Sum of squares = n*(n+1)*(2n+1)/6
        long long expectedSumSq = (long long)n * (n + 1) * (2 * n + 1) / 6;
        
        // Step 3: Form two equations
        // Let repeating = x, missing = y
        // Equation 1: x - y = actualSum - expectedSum
        long long diff = actualSum - expectedSum;  // x - y
        
        // Equation 2: x² - y² = actualSumSq - expectedSumSq
        // Which can be written as: (x-y)(x+y) = actualSumSq - expectedSumSq
        long long diffSq = actualSumSq - expectedSumSq;  // x² - y²
        
        // Step 4: Solve for x and y
        // From Eq2: (x-y)(x+y) = diffSq
        // We know (x-y) = diff, so:
        // x + y = diffSq / diff
        long long sum = diffSq / diff;  // x + y
        
        // Now we have:
        // x - y = diff
        // x + y = sum
        // Adding: 2x = diff + sum
        long long repeating = (diff + sum) / 2;
        
        // Subtracting: 2y = sum - diff
        long long missing = (sum - diff) / 2;
        
        return {(int)repeating, (int)missing};
    }
};
```

### XOR Approach (Overflow-safe)

```cpp
class Solution {
public:
    vector<int> findMissingRepeating(vector<int>& arr) {
        int n = arr.size();
        
        // Step 1: XOR all array elements and numbers 1 to n
        // Result will be: repeating ⊕ missing
        int xorAll = 0;
        
        // XOR all array elements
        for (int num : arr) {
            xorAll ^= num;
        }
        
        // XOR all numbers from 1 to n
        for (int i = 1; i <= n; i++) {
            xorAll ^= i;
        }
        
        // Now xorAll = repeating ⊕ missing
        
        // Step 2: Find rightmost set bit in xorAll
        // This bit is different in repeating and missing numbers
        // We use it to partition all numbers into two groups
        int rightmostSetBit = xorAll & (-xorAll);
        
        // Step 3: Partition and XOR each group
        int bucket1 = 0, bucket2 = 0;
        
        // Partition array elements
        for (int num : arr) {
            if (num & rightmostSetBit) {
                // This bit is set in num
                bucket1 ^= num;
            } else {
                // This bit is not set in num
                bucket2 ^= num;
            }
        }
        
        // Partition numbers 1 to n
        for (int i = 1; i <= n; i++) {
            if (i & rightmostSetBit) {
                bucket1 ^= i;
            } else {
                bucket2 ^= i;
            }
        }
        
        // Now bucket1 and bucket2 contain our two numbers
        // But we don't know which is repeating and which is missing
        
        // Step 4: Determine which is repeating by checking array
        int repeating, missing;
        
        // Check if bucket1 appears in array
        bool found = false;
        for (int num : arr) {
            if (num == bucket1) {
                found = true;
                break;
            }
        }
        
        if (found) {
            repeating = bucket1;
            missing = bucket2;
        } else {
            repeating = bucket2;
            missing = bucket1;
        }
        
        return {repeating, missing};
    }
};
```

### Complexity Analysis
- **Time Complexity**: O(n) - Single or multiple passes through array
- **Space Complexity**: O(1) - Only using constant extra space

**Mathematical Approach**:
- Time: O(n) - One pass to calculate sums
- Space: O(1)
- Risk: Overflow for large n (use long long)

**XOR Approach**:
- Time: O(n) - Multiple passes but still linear
- Space: O(1)
- Advantage: No overflow risk

## Edge Cases
- **n = 2**: `[2,2]` → Repeating=2, Missing=1
- **Repeating at start**: `[1,1,3]` → Works correctly
- **Missing at start**: `[2,3,2]` → Works correctly
- **Large n**: Both methods handle, but XOR safer for overflow
- **Consecutive numbers**: `[1,2,2,4]` → Repeating=2, Missing=3

## Key Insights & Interview Tips

**Important Observations**:
- Three distinct approaches with different trade-offs
- Mathematical approach simpler to code but overflow risk
- XOR approach more complex but completely overflow-safe
- Hash map approach simplest but uses extra space
- Problem has exactly one solution (one missing, one repeating)

**Common Mistakes to Avoid**:
- Integer overflow in mathematical approach (use long long)
- Wrong formulas for sum and sum of squares
- In XOR approach, forgetting to determine which number is repeating
- Not handling the case where repeating number appears in specific positions
- Dividing before checking for zero (though guaranteed not to happen here)

**What Interviewers Look For**:
- Knowledge of multiple approaches
- Understanding of XOR properties
- Awareness of overflow issues
- Ability to use mathematical relationships
- Clean implementation with proper type casting

**Follow-up Questions**:
- Q: "What if there are multiple missing/repeating numbers?"
- A: More complex - might need different approach like cyclic sort or marking

- Q: "Can you do it without modifying the array?"
- A: Yes, all three approaches shown don't modify array

- Q: "Which approach is best?"
- A: XOR for production (no overflow), Mathematical for interviews (simpler to explain)

- Q: "What if numbers are not from 1 to n?"
- A: Need to adjust formulas or use different approach entirely

**Related Problems**:
- LeetCode 645: Set Mismatch
- LeetCode 268: Missing Number
- LeetCode 287: Find the Duplicate Number
- LeetCode 448: Find All Numbers Disappeared in an Array

**XOR Properties Recap**:
```
a ⊕ a = 0              (self-cancellation)
a ⊕ 0 = a              (identity)
a ⊕ b = b ⊕ a          (commutative)
(a ⊕ b) ⊕ c = a ⊕ (b ⊕ c)  (associative)
rightmost set bit: x & (-x) or x & ~(x-1)
```
