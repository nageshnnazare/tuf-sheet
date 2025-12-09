# 17b. Rearrange Array Elements by Sign (Variation - Unequal Counts)

**Related to**: LeetCode #2149  
**Difficulty**: Medium

## Problem Statement

Given an integer array `nums` with **unequal** numbers of positive and negative integers, rearrange the elements such that:

1. Positive and negative numbers **alternate as much as possible**
2. The **relative order** of elements with the same sign is preserved
3. **Remaining elements** (from whichever sign has more) are placed at the **end in order**
4. The rearranged array **begins with a positive** integer (if any positive exists)

Return the modified array.

## Examples

### Example 1
```
Input: nums = [1, 2, -4, -5, 3, 4]
Output: [1, -4, 2, -5, 3, 4]
Explanation:
Positives: [1, 2, 3, 4] (4 elements)
Negatives: [-4, -5] (2 elements)
Alternate 2 pairs: [1, -4, 2, -5]
Append remaining positives: [3, 4]
Result: [1, -4, 2, -5, 3, 4]
```

### Example 2
```
Input: nums = [1, -2, -3, -4, -5, 2]
Output: [1, -2, 2, -3, -4, -5]
Explanation:
Positives: [1, 2] (2 elements)
Negatives: [-2, -3, -4, -5] (4 elements)
Alternate 2 pairs: [1, -2, 2, -3]
Append remaining negatives: [-4, -5]
Result: [1, -2, 2, -3, -4, -5]
```

### Example 3
```
Input: nums = [3, 1, 4, -2, -5]
Output: [3, -2, 1, -5, 4]
Explanation: 3 positives, 2 negatives. Alternate 2, append 1 positive.
```

## Constraints
- 1 ≤ nums.length ≤ 2 × 10^5
- -10^9 ≤ nums[i] ≤ 10^9
- nums[i] != 0

## Visual Explanation

### ASCII Diagram - Separate and Merge

```
Input: nums = [1, 2, -4, -5, 3, 4]

Step 1: Separate by sign
  Scan array: [1, 2, -4, -5, 3, 4]
              ↓  ↓   ↓   ↓  ↓  ↓
  Positives: [1, 2, 3, 4]  (count = 4)
  Negatives: [-4, -5]      (count = 2)

Step 2: Determine alternation length
  minCount = min(4, 2) = 2
  Can make 2 complete (positive, negative) pairs

Step 3: Alternate for minCount pairs
  Pair 0: pos[0], neg[0] → [1, -4]
  Pair 1: pos[1], neg[1] → [1, -4, 2, -5]
  
  After alternation: [1, -4, 2, -5]

Step 4: Append remaining elements
  Used indices: pos[0..1], neg[0..1]
  Remaining: pos[2..3] = [3, 4]
  
  Append in order: [1, -4, 2, -5, 3, 4]

Final: [1, -4, 2, -5, 3, 4]

Visualization:
  Positives: [1, 2, 3, 4]
              ↓  ↓  ↓  ↓
  Result:    [1,-4, 2,-5, 3, 4]
                 ↓     ↓
  Negatives:   [-4, -5]
               (all used, positives remaining)
```

### More Negatives Example

```
Input: nums = [1, -2, -3, -4, -5, 2]

Positives: [1, 2]           (count = 2)
Negatives: [-2, -3, -4, -5] (count = 4)

minCount = 2

Alternate 2 pairs:
  [1, -2, 2, -3]

Remaining negatives [2..3]:
  [-4, -5]

Final: [1, -2, 2, -3, -4, -5]
       ←alternating→  ←extra→
```

## Approach & Intuition

**Key Differences from Equal Counts Version:**
- Can only alternate until smaller group exhausts
- Must handle remainder from larger group
- Array length can be odd

**Algorithm:**
1. Separate positives and negatives (preserving order)
2. Find minimum count
3. Alternate for min(posCount, negCount) pairs
4. Append remaining from whichever group has more

**Why this works:**
- Maximizes alternation (as much as possible)
- Maintains relative order (scan left to right)
- Cleanly handles remainder at end

## Algorithm Steps

1. Create two arrays: `positives[]` and `negatives[]`
2. Scan nums and separate by sign
3. Calculate `minCount = min(positives.size(), negatives.size())`
4. For i from 0 to minCount-1:
   - Add positives[i] to result
   - Add negatives[i] to result
5. Append remaining from positives (if any)
6. Append remaining from negatives (if any)
7. Return result

## Dry Run (Step-by-Step Execution)

Input: `nums = [1, 2, -4, -5, 3, 4]`

```
Step 1: Separate by sign
  Initial: nums = [1, 2, -4, -5, 3, 4]
  positives = []
  negatives = []
  
  i=0: 1 > 0 → positives = [1]
  i=1: 2 > 0 → positives = [1, 2]
  i=2: -4 < 0 → negatives = [-4]
  i=3: -5 < 0 → negatives = [-4, -5]
  i=4: 3 > 0 → positives = [1, 2, 3]
  i=5: 4 > 0 → positives = [1, 2, 3, 4]
  
  Final:
    positives = [1, 2, 3, 4], size = 4
    negatives = [-4, -5], size = 2

Step 2: Calculate min count
  minCount = min(4, 2) = 2

Step 3: Alternate for minCount pairs
  result = []
  
  i=0:
    result.push_back(positives[0]) → [1]
    result.push_back(negatives[0]) → [1, -4]
    
  i=1:
    result.push_back(positives[1]) → [1, -4, 2]
    result.push_back(negatives[1]) → [1, -4, 2, -5]
  
  After alternation: result = [1, -4, 2, -5]

Step 4: Append remaining positives
  Already used: positives[0..1]
  Remaining: positives[2..3] = [3, 4]
  
  i=2: result.push_back(3) → [1, -4, 2, -5, 3]
  i=3: result.push_back(4) → [1, -4, 2, -5, 3, 4]

Step 5: Append remaining negatives
  Already used: negatives[0..1]
  Remaining: none
  Skip this step

Final Result: [1, -4, 2, -5, 3, 4] ✓
```

### Example 2 Dry Run

Input: `nums = [1, -2, -3, -4, -5, 2]`

```
Separate:
  positives = [1, 2], size = 2
  negatives = [-2, -3, -4, -5], size = 4

minCount = min(2, 4) = 2

Alternate 2 pairs:
  result = [1, -2, 2, -3]

Append remaining positives:
  None (all used)

Append remaining negatives:
  negatives[2..3] = [-4, -5]
  result = [1, -2, 2, -3, -4, -5]

Final: [1, -2, 2, -3, -4, -5] ✓
```

## C++ Solution

### Code with Detailed Comments

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;

class Solution {
public:
    // Variation: Unequal counts of positive and negative
    vector<int> rearrangeArray(vector<int>& nums) {
        // Step 1: Separate positive and negative numbers
        // This preserves the relative order of each type
        vector<int> positives;
        vector<int> negatives;
        
        for (int num : nums) {
            if (num > 0) {
                positives.push_back(num);
            } else {
                negatives.push_back(num);
            }
        }
        
        // Result array to build our answer
        vector<int> result;
        
        // Step 2: Calculate how many complete pairs we can make
        // A pair consists of one positive and one negative
        int minCount = min(positives.size(), negatives.size());
        
        // Step 3: Alternate elements for minCount pairs
        // This ensures maximum alternation
        for (int i = 0; i < minCount; i++) {
            // Add positive first (problem requires starting with positive)
            result.push_back(positives[i]);
            // Add corresponding negative
            result.push_back(negatives[i]);
        }
        
        // Step 4: Append remaining positive elements (if any)
        // If positives.size() > minCount, we have extras
        for (int i = minCount; i < positives.size(); i++) {
            result.push_back(positives[i]);
        }
        
        // Step 5: Append remaining negative elements (if any)
        // If negatives.size() > minCount, we have extras
        for (int i = minCount; i < negatives.size(); i++) {
            result.push_back(negatives[i]);
        }
        
        return result;
    }
    
    // Alternative: One-loop approach (slightly more complex logic)
    vector<int> rearrangeArrayOneLoop(vector<int>& nums) {
        vector<int> positives, negatives;
        
        // Separate
        for (int num : nums) {
            if (num > 0) positives.push_back(num);
            else negatives.push_back(num);
        }
        
        vector<int> result;
        int posIdx = 0, negIdx = 0;
        
        // Alternate while both have elements
        while (posIdx < positives.size() && negIdx < negatives.size()) {
            result.push_back(positives[posIdx++]);
            result.push_back(negatives[negIdx++]);
        }
        
        // Append remaining (only one of these loops will execute)
        while (posIdx < positives.size()) {
            result.push_back(positives[posIdx++]);
        }
        while (negIdx < negatives.size()) {
            result.push_back(negatives[negIdx++]);
        }
        
        return result;
    }
};

// Driver code for testing
int main() {
    Solution sol;
    
    // Test Case 1: More positives
    vector<int> nums1 = {1, 2, -4, -5, 3, 4};
    cout << "Input: [1, 2, -4, -5, 3, 4]" << endl;
    cout << "Positives: 4, Negatives: 2" << endl;
    vector<int> result1 = sol.rearrangeArray(nums1);
    cout << "Output: [";
    for (int i = 0; i < result1.size(); i++) {
        cout << result1[i];
        if (i < result1.size() - 1) cout << ", ";
    }
    cout << "]" << endl << endl;
    
    // Test Case 2: More negatives
    vector<int> nums2 = {1, -2, -3, -4, -5, 2};
    cout << "Input: [1, -2, -3, -4, -5, 2]" << endl;
    cout << "Positives: 2, Negatives: 4" << endl;
    vector<int> result2 = sol.rearrangeArray(nums2);
    cout << "Output: [";
    for (int i = 0; i < result2.size(); i++) {
        cout << result2[i];
        if (i < result2.size() - 1) cout << ", ";
    }
    cout << "]" << endl << endl;
    
    // Test Case 3: Odd total
    vector<int> nums3 = {3, 1, 4, -2, -5};
    cout << "Input: [3, 1, 4, -2, -5]" << endl;
    cout << "Positives: 3, Negatives: 2" << endl;
    vector<int> result3 = sol.rearrangeArray(nums3);
    cout << "Output: [";
    for (int i = 0; i < result3.size(); i++) {
        cout << result3[i];
        if (i < result3.size() - 1) cout << ", ";
    }
    cout << "]" << endl << endl;
    
    // Test Case 4: All positive
    vector<int> nums4 = {1, 2, 3, 4};
    cout << "Input: [1, 2, 3, 4]" << endl;
    cout << "Positives: 4, Negatives: 0" << endl;
    vector<int> result4 = sol.rearrangeArray(nums4);
    cout << "Output: [";
    for (int i = 0; i < result4.size(); i++) {
        cout << result4[i];
        if (i < result4.size() - 1) cout << ", ";
    }
    cout << "]" << endl;
    
    return 0;
}
```

### Complexity Analysis

- **Time Complexity**: O(n) 
  - O(n) to separate into two arrays
  - O(min(p, n)) to alternate (where p = positives, n = negatives)
  - O(|p-n|) to append remainder
  - Total: O(n)

- **Space Complexity**: O(n)
  - Two temporary arrays for separation
  - Result array
  - Total: O(n)

## Edge Cases

1. **All positive**: `[1, 2, 3, 4]` → `[1, 2, 3, 4]` (no alternation possible)
2. **All negative**: `[-1, -2, -3]` → `[-1, -2, -3]` (no alternation possible)
3. **One of each**: `[5, -3]` → `[5, -3]`
4. **Highly unbalanced**: `[1, -2, -3, -4, -5, -6, -7]` → `[1, -2, -3, -4, -5, -6, -7]`
5. **Single element**: `[5]` → `[5]`

## Comparison with Original Problem

| Aspect | Equal Counts (Original) | Unequal Counts (Variation) |
|--------|-------------------------|----------------------------|
| **Input constraint** | Equal pos & neg | Any distribution |
| **Array length** | Always even | Can be odd or even |
| **Alternation** | Complete | Partial (until smaller exhausts) |
| **Remainder handling** | No remainder | Append in order |
| **Complexity** | O(n) time, O(n) space | O(n) time, O(n) space |
| **Index pattern** | Fixed positions | Dynamic based on counts |

## Key Insights & Interview Tips

1. **Simpler Than Equal Counts**:
   - Don't need to track even/odd indices
   - Just separate and merge
   - More straightforward logic

2. **Remainder Handling**:
   - The key difference is appending extras
   - Must maintain order of extras (don't sort them!)

3. **When to Use Each Approach**:
   - **Equal counts**: Use index pattern (more efficient)
   - **Unequal counts**: Use separation (simpler logic)

4. **Common Mistakes**:
   - Forgetting to preserve order of remaining elements
   - Not handling all-positive or all-negative cases
   - Trying to use the index pattern approach (won't work here)

5. **Interview Follow-ups**:
   - "Can you do it without extra space?" → Very difficult, order constraint makes it hard
   - "What if we start with negative?" → Change alternation order in step 3
   - "What if we want remainder at beginning?" → Insert instead of append

6. **Real-World Applications**:
   - Task scheduling with preferences
   - Balancing workload between teams
   - Round-robin with variable team sizes

7. **Pro Tip**: This variation is actually easier than the equal counts version because you don't need the index pattern trick!

