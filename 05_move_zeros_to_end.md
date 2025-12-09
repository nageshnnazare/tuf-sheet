# 5. Move All Zeros to End

**LeetCode**: [#283 - Move Zeroes](https://leetcode.com/problems/move-zeroes/)  
**Difficulty**: Easy

## Problem Statement

Given an array of integers, move all zeros to the end of the array while maintaining the relative order of all non-zero elements. The operation must be done **in-place** without making a copy of the array.

## Examples

### Example 1
```
Input: nums = [0, 1, 0, 3, 12]
Output: [1, 3, 12, 0, 0]
Explanation: All non-zero elements (1, 3, 12) maintain their order, zeros moved to end.
```

### Example 2
```
Input: nums = [0, 0, 1]
Output: [1, 0, 0]
Explanation: Single non-zero element moved to front, zeros at end.
```

### Example 3
```
Input: nums = [1, 2, 3]
Output: [1, 2, 3]
Explanation: No zeros, array remains unchanged.
```

### Example 4
```
Input: nums = [0]
Output: [0]
Explanation: Single zero, no change needed.
```

## Constraints
- 1 ≤ nums.length ≤ 10^4
- -2^31 ≤ nums[i] ≤ 2^31 - 1

## Visual Explanation

### ASCII Diagram - Two Pointer Technique

```
Initial Array: [0, 1, 0, 3, 12]
                ↑
                i (position where next non-zero should be placed)
                j (scanning for non-zero elements)

Step 0: Initialize
  i = 0 (first position for non-zero elements)
  j will scan from 0 to n-1

Step 1: j = 0
  nums[j] = 0 (zero, skip it)
  [0, 1, 0, 3, 12]
   ↑
   i,j

Step 2: j = 1
  nums[j] = 1 (non-zero!)
  Place it at position i
  nums[i] = nums[j] → nums[0] = 1
  i++ → i = 1
  
  [1, 1, 0, 3, 12]
      ↑
      i

Step 3: j = 2
  nums[j] = 0 (zero, skip it)
  [1, 1, 0, 3, 12]
      ↑
      i

Step 4: j = 3
  nums[j] = 3 (non-zero!)
  Place it at position i
  nums[i] = nums[j] → nums[1] = 3
  i++ → i = 2
  
  [1, 3, 0, 3, 12]
         ↑
         i

Step 5: j = 4
  nums[j] = 12 (non-zero!)
  Place it at position i
  nums[i] = nums[j] → nums[2] = 12
  i++ → i = 3
  
  [1, 3, 12, 3, 12]
            ↑
            i

Step 6: Fill remaining positions with zeros
  From i = 3 to end (n-1 = 4):
  nums[3] = 0
  nums[4] = 0
  
  [1, 3, 12, 0, 0]

Final Result: [1, 3, 12, 0, 0] ✓

Visualization of Process:
  Original:  [0, 1, 0, 3, 12]
                    non-zero →
             [_, 1, _, 3, 12]  Collect non-zeros
             [1, 3, 12, _, _]  Move to front
             [1, 3, 12, 0, 0]  Fill rest with zeros
```

## Approach & Intuition

### Approach 1: Two-Pointer (Optimal) ⭐

**Key Insight**: Use pointer `i` to track where the next non-zero element should go.

**Algorithm**:
1. Maintain pointer `i = 0` (position for next non-zero)
2. Scan array with pointer `j`
3. When `nums[j] != 0`, place it at `nums[i]` and increment `i`
4. After scan, fill positions from `i` to end with zeros

**Why this works**:
- We're essentially collecting all non-zero elements in order
- Pointer `i` always points to next available position for non-zero
- All positions from `i` to end will be zeros

### Approach 2: Snowball Method (Alternative O(1) space)

Track size of "snowball" of zeros and swap when non-zero is found.

### Approach 3: Count Zeros (Uses similar logic)

Count zeros, shift non-zeros left, fill end with counted zeros.

## Algorithm Steps

**Optimal Two-Pointer Method:**

1. Initialize `i = 0` (tracks position for next non-zero element)
2. For `j` from 0 to n-1:
   - If `nums[j] != 0`:
     - Place `nums[j]` at position `i`
     - Increment `i`
3. Fill array from index `i` to n-1 with zeros

## Dry Run (Step-by-Step Execution)

Array: `nums = [0, 1, 0, 3, 12]`, n = 5

```
Initial State:
nums = [0, 1, 0, 3, 12]
n = 5
i = 0 (position for next non-zero)

Phase 1: Collect Non-Zero Elements

Iteration 0: j = 0
  nums[j] = 0
  0 == 0 → Zero, skip
  i remains 0
  State: i = 0, nums = [0, 1, 0, 3, 12]

Iteration 1: j = 1
  nums[j] = 1
  1 != 0 → Non-zero found!
  nums[i] = nums[j] → nums[0] = 1
  i++ → i = 1
  State: i = 1, nums = [1, 1, 0, 3, 12]

Iteration 2: j = 2
  nums[j] = 0
  0 == 0 → Zero, skip
  i remains 1
  State: i = 1, nums = [1, 1, 0, 3, 12]

Iteration 3: j = 3
  nums[j] = 3
  3 != 0 → Non-zero found!
  nums[i] = nums[j] → nums[1] = 3
  i++ → i = 2
  State: i = 2, nums = [1, 3, 0, 3, 12]

Iteration 4: j = 4
  nums[j] = 12
  12 != 0 → Non-zero found!
  nums[i] = nums[j] → nums[2] = 12
  i++ → i = 3
  State: i = 3, nums = [1, 3, 12, 3, 12]

Phase 2: Fill Remaining with Zeros

Current state: i = 3, n = 5
Need to fill from i to n-1 with zeros

Fill Loop:
  k = 3: nums[3] = 0 → [1, 3, 12, 0, 12]
  k = 4: nums[4] = 0 → [1, 3, 12, 0, 0]

Final Result: [1, 3, 12, 0, 0] ✓
```

### Another Example: `[0, 0, 1]`

```
Initial: nums = [0, 0, 1], i = 0

j = 0: nums[0] = 0 → skip, i = 0
j = 1: nums[1] = 0 → skip, i = 0
j = 2: nums[2] = 1 → nums[0] = 1, i = 1
  Array becomes: [1, 0, 1]

Fill from i=1 to n-1=2:
  nums[1] = 0 → [1, 0, 1]
  nums[2] = 0 → [1, 0, 0]

Result: [1, 0, 0] ✓
```

## C++ Solution

### Code with Detailed Comments

```cpp
#include <iostream>
#include <vector>
using namespace std;

class Solution {
public:
    // Function to move all zeros to end while maintaining order of non-zeros
    void moveZeroes(vector<int>& nums) {
        int n = nums.size();
        
        // Edge case: Empty or single element array
        if (n <= 1) {
            return;
        }
        
        // Pointer 'i' tracks the position where next non-zero element should go
        // Initially at index 0 (start of array)
        int i = 0;
        
        // Phase 1: Move all non-zero elements to the front
        // Pointer 'j' scans through entire array
        for (int j = 0; j < n; j++) {
            // If current element is non-zero
            if (nums[j] != 0) {
                // Place it at position i
                // This collects all non-zero elements in order at front of array
                nums[i] = nums[j];
                
                // Move i forward to next position
                // i always points to the next available spot for non-zero element
                i++;
            }
            // If nums[j] == 0, we simply skip it
            // i doesn't move, so zeros get overwritten in next iterations
        }
        
        // Phase 2: Fill remaining positions with zeros
        // After above loop, i points to first position that needs to be zero
        // All positions from i to n-1 should be filled with zeros
        for (int k = i; k < n; k++) {
            nums[k] = 0;
        }
        
        // Array is now: [all non-zeros in order, all zeros]
    }
    
    // Alternative: Optimal one-pass solution with swapping
    void moveZeroesOnePass(vector<int>& nums) {
        int n = nums.size();
        if (n <= 1) return;
        
        int i = 0; // Position for next non-zero
        
        // Single pass: swap non-zero elements with position i
        for (int j = 0; j < n; j++) {
            if (nums[j] != 0) {
                // Swap current non-zero with position i
                // This maintains zeros after i
                swap(nums[i], nums[j]);
                i++;
            }
        }
        // No need for second loop!
        // Zeros are automatically at the end due to swapping
    }
};

// Driver code for testing
int main() {
    Solution sol;
    
    // Test Case 1: Mixed zeros and non-zeros
    vector<int> nums1 = {0, 1, 0, 3, 12};
    cout << "Original: [0, 1, 0, 3, 12]" << endl;
    sol.moveZeroes(nums1);
    cout << "After moving zeros: [";
    for (int i = 0; i < nums1.size(); i++) {
        cout << nums1[i];
        if (i < nums1.size() - 1) cout << ", ";
    }
    cout << "]" << endl << endl;
    
    // Test Case 2: Zeros at beginning
    vector<int> nums2 = {0, 0, 1};
    cout << "Original: [0, 0, 1]" << endl;
    sol.moveZeroes(nums2);
    cout << "After moving zeros: [";
    for (int i = 0; i < nums2.size(); i++) {
        cout << nums2[i];
        if (i < nums2.size() - 1) cout << ", ";
    }
    cout << "]" << endl << endl;
    
    // Test Case 3: No zeros
    vector<int> nums3 = {1, 2, 3};
    cout << "Original: [1, 2, 3]" << endl;
    sol.moveZeroes(nums3);
    cout << "After moving zeros: [";
    for (int i = 0; i < nums3.size(); i++) {
        cout << nums3[i];
        if (i < nums3.size() - 1) cout << ", ";
    }
    cout << "]" << endl << endl;
    
    // Test Case 4: All zeros
    vector<int> nums4 = {0, 0, 0};
    cout << "Original: [0, 0, 0]" << endl;
    sol.moveZeroes(nums4);
    cout << "After moving zeros: [";
    for (int i = 0; i < nums4.size(); i++) {
        cout << nums4[i];
        if (i < nums4.size() - 1) cout << ", ";
    }
    cout << "]" << endl << endl;
    
    // Test Case 5: Using one-pass method
    vector<int> nums5 = {0, 1, 0, 3, 12};
    cout << "One-pass method on [0, 1, 0, 3, 12]:" << endl;
    sol.moveZeroesOnePass(nums5);
    cout << "Result: [";
    for (int i = 0; i < nums5.size(); i++) {
        cout << nums5[i];
        if (i < nums5.size() - 1) cout << ", ";
    }
    cout << "]" << endl;
    
    return 0;
}
```

### Complexity Analysis

- **Time Complexity**: O(n) - We traverse the array twice: once to collect non-zeros (O(n)) and once to fill zeros (O(n)). Total: O(2n) = O(n). The one-pass version does it in single traversal but still O(n).

- **Space Complexity**: O(1) - Only using a few integer variables (i, j, k). No additional arrays or data structures. True in-place modification.

## Edge Cases

1. **Empty array**: `[]` → Return as is
2. **Single zero**: `[0]` → No change
3. **Single non-zero**: `[5]` → No change
4. **All zeros**: `[0, 0, 0]` → Remains same
5. **No zeros**: `[1, 2, 3, 4]` → Remains same (i reaches end, no filling needed)
6. **Zeros at start**: `[0, 0, 1, 2]` → `[1, 2, 0, 0]`
7. **Zeros at end**: `[1, 2, 0, 0]` → `[1, 2, 0, 0]` (already correct)
8. **Negative numbers**: `[0, -1, 0, -3]` → `[-1, -3, 0, 0]`

## Key Insights & Interview Tips

1. **Why Two-Pointer Works**:
   - 'i' tracks "write" position for non-zeros
   - 'j' tracks "read" position scanning array
   - Non-zeros naturally collect at front in order

2. **One-Pass vs Two-Pass**:
   - Two-pass: Easier to understand, explicit zero filling
   - One-pass with swap: More elegant, automatic zero placement
   - Both are O(n) time and O(1) space

3. **Order Preservation**:
   - Crucial requirement: relative order of non-zeros must be maintained
   - Our solution guarantees this by processing left to right

4. **Comparison with Partitioning**:
   - This is similar to partition step in QuickSort
   - But here we maintain relative order (stable partition)

5. **Related Problems**:
   - Move all negative numbers to left
   - Partition array (Dutch National Flag problem)
   - Segregate 0s and 1s

6. **Interview Follow-ups**:
   - "Can you do it in one pass?" → Yes, with swapping method
   - "What if you need to move zeros to the front?" → Same logic, fill from 0 to i-1
   - "Count minimum operations needed?" → Different problem (counting swaps)

7. **Common Mistakes**:
   - Using extra array (violates O(1) space requirement)
   - Not preserving order of non-zeros
   - Forgetting to fill remaining positions with zeros (in two-pass method)
   - Off-by-one errors in loop bounds

8. **Optimization Notes**:
   - If i == j in swap method, we're swapping element with itself (harmless but unnecessary)
   - Can add: `if (i != j) swap(nums[i], nums[j]);`

9. **Testing Checklist**:
   - Array with zeros scattered
   - All zeros
   - No zeros
   - Zeros only at beginning/end
   - Single element
   - Negative numbers mixed in

10. **Alternative Thinking**:
    - Think of it as "collecting treasures (non-zeros)" and "pushing  garbage (zeros) aside"
    - Or "stable partition" where pivot is 0
