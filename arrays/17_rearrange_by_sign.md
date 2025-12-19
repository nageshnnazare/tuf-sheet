# 17. Rearrange Array Elements by Sign

**LeetCode**: [#2149 - Rearrange Array Elements by Sign](https://leetcode.com/problems/rearrange-array-elements-by-sign/)  
**Difficulty**: Medium

## Problem Statement

You are given a **0-indexed** integer array `nums` of **even** length consisting of an **equal** number of positive and negative integers.

You should rearrange the elements of `nums` such that the modified array follows the given conditions:

1. Every **consecutive pair** of integers have **opposite signs**.
2. For all integers with the same sign, the **order** in which they were present in `nums` is **preserved**.
3. The rearranged array begins with a **positive** integer.

Return the modified array after rearranging the elements to satisfy the aforementioned conditions.

## Examples

### Example 1
```
Input: nums = [3, 1, -2, -5, 2, -4]
Output: [3, -2, 1, -5, 2, -4]
Explanation:
Positive integers: [3, 1, 2]
Negative integers: [-2, -5, -4]
Rearranged: [3, -2, 1, -5, 2, -4]
            pos neg pos neg pos neg
```

### Example 2
```
Input: nums = [-1, 1]
Output: [1, -1]
Explanation: Start with positive, so [1, -1].
```

### Example 3
```
Input: nums = [1, 2, -3, -1, -2, 3]
Output: [1, -3, 2, -1, 3, -2]
```

## Constraints
- 2 ≤ nums.length ≤ 2 × 10^5
- nums.length is **even**
- 1 ≤ |nums[i]| ≤ 10^5
- nums consists of **equal** number of positive and negative integers

## Visual Explanation

### ASCII Diagram - Two-Pointer Filling

```
Input: nums = [3, 1, -2, -5, 2, -4]

Step 1: Identify positive and negative numbers
  Positives: [3, 1, 2]    (maintain order)
  Negatives: [-2, -5, -4] (maintain order)

Step 2: Create result array with alternating positions
  Result array indices:
  [0, 1, 2, 3, 4, 5]
   ↑  ↑  ↑  ↑  ↑  ↑
   P  N  P  N  P  N
   
  P = Positive positions (0, 2, 4)
  N = Negative positions (1, 3, 5)

Step 3: Fill positions
  Positive index = 0, Negative index = 1
  
  Place positives at even indices:
    result[0] = 3
    result[2] = 1
    result[4] = 2
    
  Place negatives at odd indices:
    result[1] = -2
    result[3] = -5
    result[5] = -4

Final: [3, -2, 1, -5, 2, -4]

Visual Process:
  Original: [3, 1, -2, -5, 2, -4]
             ↓  ↓   ↓   ↓  ↓   ↓
  Separate: [3, 1, 2] and [-2, -5, -4]
             ↓     ↓        ↓
  Interleave alternately:
  [3, -2, 1, -5, 2, -4]
   P   N  P   N  P   N
```

## Approach & Intuition

### Approach 1: Two-Pass with Separate Arrays ⭐

**Key Insight**: 
- Since we need to maintain relative order AND alternate signs
- Separate positive and negative numbers into two lists
- Merge them alternately into result array

**Algorithm**:
1. Collect all positive numbers (maintaining order)
2. Collect all negative numbers (maintaining order)
3. Place them alternately in result: pos, neg, pos, neg...

**Why it works**:
- Equal count of positive and negative guaranteed
- Relative order preserved when collecting
- Alternating placement ensures opposite signs requirement

### Approach 2: Single-Pass with Two Pointers (Optimal) ⭐⭐

**Key Insight**:
- Positive numbers go to even indices (0, 2, 4...)
- Negative numbers go to odd indices (1, 3, 5...)
- Can fill result array directly in single pass

**Algorithm**:
1. Create result array of same size
2. Use two pointers: posIndex = 0, negIndex = 1
3. For each element:
   - If positive: place at posIndex, increment posIndex by 2
   - If negative: place at negIndex, increment negIndex by 2
4. Return result

**Advantages**:
- Single pass through input
- Direct placement in result
- More efficient

## Algorithm Steps

**Optimal Two-Pointer Approach:**

1. Create result array of size n
2. Initialize posIndex = 0 (even positions for positives)
3. Initialize negIndex = 1 (odd positions for negatives)
4. For each num in nums:
   - If num > 0:
     - result[posIndex] = num
     - posIndex += 2
   - Else:
     - result[negIndex] = num
     - negIndex += 2
5. Return result

## Dry Run (Step-by-Step Execution)

Input: `nums = [3, 1, -2, -5, 2, -4]`

```
Initial State:
nums = [3, 1, -2, -5, 2, -4]
n = 6
result = [_, _, _, _, _, _]
posIndex = 0 (for positive numbers)
negIndex = 1 (for negative numbers)

Iteration 0: nums[0] = 3 (positive)
  3 > 0 → Place at even index
  result[posIndex] = result[0] = 3
  posIndex += 2 → posIndex = 2
  State: result = [3, _, _, _, _, _]

Iteration 1: nums[1] = 1 (positive)
  1 > 0 → Place at even index
  result[posIndex] = result[2] = 1
  posIndex += 2 → posIndex = 4
  State: result = [3, _, 1, _, _, _]

Iteration 2: nums[2] = -2 (negative)
  -2 < 0 → Place at odd index
  result[negIndex] = result[1] = -2
  negIndex += 2 → negIndex = 3
  State: result = [3, -2, 1, _, _, _]

Iteration 3: nums[3] = -5 (negative)
  -5 < 0 → Place at odd index
  result[negIndex] = result[3] = -5
  negIndex += 2 → negIndex = 5
  State: result = [3, -2, 1, -5, _, _]

Iteration 4: nums[4] = 2 (positive)
  2 > 0 → Place at even index
  result[posIndex] = result[4] = 2
  posIndex += 2 → posIndex = 6 (out of bounds, won't be used)
  State: result = [3, -2, 1, -5, 2, _]

Iteration 5: nums[5] = -4 (negative)
  -4 < 0 → Place at odd index
  result[negIndex] = result[5] = -4
  negIndex += 2 → negIndex = 7 (out of bounds, won't be used)
  State: result = [3, -2, 1, -5, 2, -4]

Final Result: [3, -2, 1, -5, 2, -4] ✓

Verification:
- Alternating signs: 3(+), -2(-), 1(+), -5(-), 2(+), -4(-) ✓
- Positive order preserved: 3, 1, 2 ✓
- Negative order preserved: -2, -5, -4 ✓
- Starts with positive: 3 ✓
```

### Trace of Index Movements

```
Position tracking:

posIndex: 0 → 2 → 4 → 6 (stops)
          ↓    ↓    ↓
          3    1    2

negIndex: 1 → 3 → 5 → 7 (stops)
          ↓    ↓    ↓
         -2   -5   -4

Result positions:
[0][1][2][3][4][5]
[3][-2][1][-5][2][-4]
 P  N  P  N   P  N
```

## C++ Solution

### Code with Detailed Comments

```cpp
#include <iostream>
#include <vector>
using namespace std;

class Solution {
public:
    // Optimal Approach: Single-pass with two pointers
    vector<int> rearrangeArray(vector<int>& nums) {
        int n = nums.size();
        
        // Create result array to store rearranged elements
        // We can't modify in-place because we need to maintain order
        vector<int> result(n);
        
        // Two pointers for placing elements:
        // posIndex: tracks next position for positive numbers (even indices: 0, 2, 4...)
        // negIndex: tracks next position for negative numbers (odd indices: 1, 3, 5...)
        int posIndex = 0;  // Start at index 0 (first positive position)
        int negIndex = 1;  // Start at index 1 (first negative position)
        
        // Single pass through the input array
        for (int i = 0; i < n; i++) {
            // Check if current number is positive or negative
            if (nums[i] > 0) {
                // Positive number: place at current posIndex
                // This ensures all positive numbers go to even positions
                result[posIndex] = nums[i];
                
                // Move to next even position
                // Increment by 2 to skip the negative position
                posIndex += 2;
            } 
            else {
                // Negative number: place at current negIndex
                // This ensures all negative numbers go to odd positions
                result[negIndex] = nums[i];
                
                // Move to next odd position
                // Increment by 2 to skip the positive position
                negIndex += 2;
            }
        }
        
        // Result array now has alternating positive and negative numbers
        // with relative order preserved
        return result;
    }
    
    // Alternative Approach: Two-pass with separate collections
    // Easier to understand but slightly less efficient
    vector<int> rearrangeArrayTwoPass(vector<int>& nums) {
        int n = nums.size();
        
        // Step 1: Collect all positive and negative numbers separately
        vector<int> positives;
        vector<int> negatives;
        
        for (int num : nums) {
            if (num > 0) {
                positives.push_back(num);
            } else {
                negatives.push_back(num);
            }
        }
        
        // Step 2: Merge alternatively into result
        vector<int> result(n);
        int posIdx = 0, negIdx = 0;
        
        for (int i = 0; i < n; i++) {
            if (i % 2 == 0) {
                // Even index: place positive
                result[i] = positives[posIdx++];
            } else {
                // Odd index: place negative
                result[i] = negatives[negIdx++];
            }
        }
        
        return result;
    }
};

// Driver code for testing
int main() {
    Solution sol;
    
    // Test Case 1
    vector<int> nums1 = {3, 1, -2, -5, 2, -4};
    cout << "Input: [3, 1, -2, -5, 2, -4]" << endl;
    vector<int> result1 = sol.rearrangeArray(nums1);
    cout << "Output: [";
    for (int i = 0; i < result1.size(); i++) {
        cout << result1[i];
        if (i < result1.size() - 1) cout << ", ";
    }
    cout << "]" << endl;
    
    // Verify constraints
    cout << "Verification:" << endl;
    bool alternating = true;
    for (int i = 0; i < result1.size() - 1; i++) {
        if ((result1[i] > 0 && result1[i+1] > 0) || 
            (result1[i] < 0 && result1[i+1] < 0)) {
            alternating = false;
            break;
        }
    }
    cout << "- Alternating signs: " << (alternating ? "Yes" : "No") << endl;
    cout << "- Starts with positive: " << (result1[0] > 0 ? "Yes" : "No") << endl << endl;
    
    // Test Case 2
    vector<int> nums2 = {-1, 1};
    cout << "Input: [-1, 1]" << endl;
    vector<int> result2 = sol.rearrangeArray(nums2);
    cout << "Output: [";
    for (int i = 0; i < result2.size(); i++) {
        cout << result2[i];
        if (i < result2.size() - 1) cout << ", ";
    }
    cout << "]" << endl << endl;
    
    // Test Case 3: Larger array
    vector<int> nums3 = {1, 2, -3, -1, -2, 3};
    cout << "Input: [1, 2, -3, -1, -2, 3]" << endl;
    vector<int> result3 = sol.rearrangeArray(nums3);
    cout << "Output: [";
    for (int i = 0; i < result3.size(); i++) {
        cout << result3[i];
        if (i < result3.size() - 1) cout << ", ";
    }
    cout << "]" << endl;
    
    return 0;
}
```

### Complexity Analysis

**Single-Pass Two-Pointer Approach:**
- **Time Complexity**: O(n) - Single pass through the array. Each element is visited once and placed directly in result.
- **Space Complexity**: O(n) - Result array of size n. If we don't count the output array (which is required), then O(1) extra space.

**Two-Pass Separate Collections:**
- **Time Complexity**: O(n) - First pass to collect, second pass to merge. Total: O(2n) = O(n).
- **Space Complexity**: O(n) - Two temporary arrays plus result array.

## Edge Cases

1. **Minimum size**: `[-1, 1]` → `[1, -1]`
2. **Equal distribution**: `[1, -2, 3, -4]` → `[1, -2, 3, -4]` (already arranged)
3. **Large numbers**: `[100000, -100000, 50000, -50000]` → Works correctly
4. **Multiple same values**: `[1, 1, -1, -1]` → `[1, -1, 1, -1]`
5. **Already alternating**: Still maintains relative order

## Key Insights & Interview Tips

1. **Why Not In-Place?**
   - Can't rearrange in-place while maintaining relative order
   - Would need complex swapping logic
   - O(n) space is acceptable and simplifies solution

2. **Index Pattern Recognition**:
   - **Even indices (0, 2, 4...)**: Always positive
   - **Odd indices (1, 3, 5...)**: Always negative
   - Increment by 2 to maintain pattern

3. **Guaranteed Constraints**:
   - Equal count of positive and negative
   - This means posIndex and negIndex will never go out of bounds before all elements are placed

4. **Order Preservation**:
   - By scanning left to right and placing in fixed positions
   - Relative order is automatically maintained

5. **Interview Variations**:
   - "What if unequal positive/negative counts?" → Place remaining at end
   - "Start with negative instead?" → Swap posIndex and negIndex initialization
   - "Is in-place possible?" → Very complex, not recommended

6. **Common Mistakes**:
   - Forgetting to increment by 2 (incrementing by 1 will place at wrong position)
   - Trying to modify in-place (loses relative order)
   - Not handling edge case of size 2

7. **Follow-up Questions**:
   - "Can you do it in O(1) space?" → Not while maintaining order
   - "What if array isn't guaranteed to have equal counts?" → Need to handle remainder
   - "Optimize for already-arranged arrays?" → Still need O(n) to verify

8. **Real-world Applications**:
   - Balancing alternating shifts (day/night workers)
   - Tournament bracket arrangement
   - Task scheduling with alternating priorities

9. **Related Problems**:
   - Sort array by parity (even/odd)
   - Wiggle sort (alternating greater/less than)
   - Separate array into two halves by criteria

10. **Pro Tip**: 
    - The two-pointer approach is elegant and efficient
    - Key insight: think about **where** elements should go, not **how** to move them
    - Direct placement beats swapping when order matters

11. **Comparison with Similar Problems**:
    ```
    | Problem          | Strategy          | In-place? |
    |------------------|-------------------|-----------|
    | Sort Colors      | Dutch Flag        | Yes       |
    | Move Zeros       | Two-pointer       | Yes       |
    | Rearrange Sign   | Index pattern     | No*       |
    
    * = Maintaining order prevents in-place modification
    ```

12. **Testing Checklist**:
    - Verify alternating pattern
    - Check relative order preservation
    - Confirm starts with positive
    - Test minimum size (n=2)
    - Test with duplicate values
