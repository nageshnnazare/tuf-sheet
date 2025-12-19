# 9. Find Element That Appears Once

**LeetCode**: [#136 - Single Number](https://leetcode.com/problems/single-number/)  
**Difficulty**: Easy

## Problem Statement

Given a non-empty array of integers where every element appears **twice** except for one element which appears **once**, find that single element.

**Constraint**: Implement a solution with **O(n) time** and **O(1) space** complexity.

## Examples

### Example 1
```
Input: nums = [2, 2, 1]
Output: 1
Explanation: 2 appears twice, 1 appears once.
```

### Example 2
```
Input: nums = [4, 1, 2, 1, 2]
Output: 4
Explanation: 1 and 2 appear twice, 4 appears once.
```

### Example 3
```
Input: nums = [1]
Output: 1
Explanation: Single element in array.
```

### Example 4
```
Input: nums = [7, 3, 5, 3, 5]
Output: 7
```

## Constraints
- 1 ≤ nums.length ≤ 3 × 10^4
- -3 × 10^4 ≤ nums[i] ≤ 3 × 10^4
- Each element appears twice except for one

## Visual Explanation

### ASCII Diagram - XOR Magic

```
nums = [4, 1, 2, 1, 2]

XOR Properties:
1. a ^ a = 0    (any number XOR itself = 0)
2. a ^ 0 = a    (any number XOR 0 = itself)
3. XOR is commutative: a ^ b = b ^ a
4. XOR is associative: (a ^ b) ^ c = a ^ (b ^ c)

Process:
  result = 0

  Step 1: 0 ^ 4 = 4
    Binary: 000 ^ 100 = 100
    result = 4

  Step 2: 4 ^ 1 = 5
    Binary: 100 ^ 001 = 101
    result = 5

  Step 3: 5 ^ 2 = 7
    Binary: 101 ^ 010 = 111
    result = 7

  Step 4: 7 ^ 1 = 6
    Binary: 111 ^ 001 = 110
    result = 6

  Step 5: 6 ^ 2 = 4
    Binary: 110 ^ 010 = 100
    result = 4

  Final: 4 ← The element that appears once!

Why it works:
  4 ^ 1 ^ 2 ^ 1 ^ 2
= 4 ^ (1 ^ 1) ^ (2 ^ 2)  [rearrange using associative property]
= 4 ^ 0 ^ 0                [duplicate pairs cancel out]
= 4                        [single element remains]

Visual Cancellation:
  Elements:  4   1   2   1   2
             ↓   ↓___↓   ↓___↓
             ↓     cancel    pair
             ↓     
           Result = 4
```

### XOR Truth Table
```
a | b | a^b
--|---|----
0 | 0 |  0
0 | 1 |  1
1 | 0 |  1
1 | 1 |  0   ← Same bits cancel out
```

## Approach & Intuition

### Optimal Approach: XOR Bit Manipulation ⭐

**Key Insight**: XOR has special properties that make duplicates cancel out!

**Properties Used**:
1. `a ^ a = 0` - Any number XOR with itself equals zero
2. `a ^ 0 = a` - Any number XOR with zero equals itself
3. XOR is **commutative and associative** - order doesn't matter

**Algorithm**:
- XOR all elements together
- Duplicate pairs cancel out to 0
- Only the single element remains

### Alternative Approaches

**Approach 2: Hash Map**
- Count frequency of each element
- Find element with count 1
- Time: O(n), Space: O(n) ❌ (violates space requirement)

**Approach 3: Sorting**
- Sort array  
- Check consecutive pairs
- Time: O(n log n) ❌ (slower than required)

**Approach 4: Math**
- `2 * (sum of unique) - sum of all = single element`
- Time: O(n), Space: O(n) for set ❌

## Algorithm Steps

**XOR Method:**
1. Initialize result = 0
2. For each element in array:
   - result = result ^ element
3. Return result

## Dry Run (Step-by-Step Execution)

Array: `nums = [4, 1, 2, 1, 2]`

```
Initial State:
nums = [4, 1, 2, 1, 2]
result = 0

Iteration 0: nums[0] = 4
  result = 0 ^ 4
  Binary: 000 XOR 100 = 100
  result = 4 (decimal)

Iteration 1: nums[1] = 1
  result = 4 ^ 1
  Binary: 100 XOR 001 = 101
  result = 5 (decimal)

Iteration 2: nums[2] = 2
  result = 5 ^ 2
  Binary: 101 XOR 010 = 111
  result = 7 (decimal)

Iteration 3: nums[3] = 1 (duplicate of index 1)
  result = 7 ^ 1
  Binary: 111 XOR 001 = 110
  result = 6 (decimal)
  
  Note: The first 1 we XORed earlier (step 1) and this 1
  will eventually cancel out due to XOR properties

Iteration 4: nums[4] = 2 (duplicate of index 2)
  result = 6 ^ 2
  Binary: 110 XOR 010 = 100
  result = 4 (decimal)

Final Result: 4 ✓

Verification:
  All elements XORed: 4 ^ 1 ^ 2 ^ 1 ^ 2
  Rearranged:         4 ^ (1^1) ^ (2^2)
  Simplified:         4 ^ 0 ^ 0 = 4 ✓
```

### Detailed Binary Trace

```
Step-by-step in binary:

  0000  (result = 0)
^ 0100  (4)
------
  0100  (result = 4)

^ 0001  (1)
------
  0101  (result = 5)

^ 0010  (2)
------
  0111  (result = 7)

^ 0001  (1 again - will cancel previous 1)
------
  0110  (result = 6)

^ 0010  (2 again - will cancel previous 2)
------
  0100  (result = 4) ← Final answer!
```

## C++ Solution

### Code with Detailed Comments

```cpp
#include <iostream>
#include <vector>
using namespace std;

class Solution {
public:
    // Function to find the element that appears once
    // Uses XOR bit manipulation for O(1) space
    int singleNumber(vector<int>& nums) {
        // Initialize result to 0
        // This is our XOR accumulator
        // Starting with 0 because: a ^ 0 = a (identity property)
        int result = 0;
        
        // XOR all elements together
        // Due to XOR properties:
        // - Duplicate pairs will cancel out (a ^ a = 0)
        // - Only the unique element remains
        for (int i = 0; i < nums.size(); i++) {
            result = result ^ nums[i];
            
            // Alternative concise syntax: result ^= nums[i];
        }
        
        // After XORing all elements:
        // - All pairs have cancelled to 0
        // - result = 0 ^ single_element = single_element
        return result;
    }
    
    // Alternative: Using range-based for loop (more idiomatic C++)
    int singleNumberConcise(vector<int>& nums) {
        int result = 0;
        
        // Iterate through all elements
        for (int num : nums) {
            result ^= num; // XOR assignment operator
        }
        
        return result;
    }
    
    // For comparison: Hash Map approach (uses O(n) space)
    // This is NOT optimal for this problem but good to know
    int singleNumberHashMap(vector<int>& nums) {
        unordered_map<int, int> freq;
        
        // Count frequency of each element
        for (int num : nums) {
            freq[num]++;
        }
        
        // Find element with frequency 1
        for (auto& pair : freq) {
            if (pair.second == 1) {
                return pair.first;
            }
        }
        
        return -1; // Should never reach here given problem constraints
    }
};

// Driver code
int main() {
    Solution sol;
    
    // Test Case 1
    vector<int> nums1 = {2, 2, 1};
    cout << "Array: [2, 2, 1]" << endl;
    cout << "Single number: " << sol.singleNumber(nums1) << endl << endl;
    
    // Test Case 2
    vector<int> nums2 = {4, 1, 2, 1, 2};
    cout << "Array: [4, 1, 2, 1, 2]" << endl;
    cout << "Single number: " << sol.singleNumber(nums2) << endl << endl;
    
    // Test Case 3: Single element
    vector<int> nums3 = {1};
    cout << "Array: [1]" << endl;
    cout << "Single number: " << sol.singleNumber(nums3) << endl << endl;
    
    // Test Case 4: Negative numbers
    vector<int> nums4 = {-1, -1, -2};
    cout << "Array: [-1, -1, -2]" << endl;
    cout << "Single number: " << sol.singleNumber(nums4) << endl << endl;
    
    // Test Case 5: Larger array
    vector<int> nums5 = {7, 3, 5, 3, 5};
    cout << "Array: [7, 3, 5, 3, 5]" << endl;
    cout << "Single number: " << sol.singleNumber(nums5) << endl;
    
    return 0;
}
```

### Complexity Analysis

- **Time Complexity**: O(n) - We traverse the array once, XORing each element. XOR operation is O(1).

- **Space Complexity**: O(1) - Only using one integer variable `result`. No additional data structures needed regardless of input size.

## Edge Cases

1. **Single element**: `[5]` → `5`
2. **Two distinct elements, one pair**: `[1, 1, 2]` → `2`
3. **Negative numbers**: `[-1, -1, -2]` → `-2`
4. **Zero included**: `[0, 1, 0]` → `1`
5. **Large numbers**: `[30000, 30000, 15000]` → `15000`
6. **Many duplicates**: `[1,2,3,4,5,1,2,3,4]` → `5`

## Key Insights & Interview Tips

1. **XOR is the Magic**:
   - This problem is a **classic bit manipulation** problem
   - XOR's self-canceling property is perfect for finding unique element
   - Remember: `a ^ a ^ b = b`

2. **Why XOR Works**:
   ```
   Commutative: a ^ b = b ^ a
   Associative: (a ^ b) ^ c = a ^ (b ^ c)
   Identity: a ^ 0 = a
   Self-inverse: a ^ a = 0
   ```

3. **Bit Manipulation Pattern**: This XOR trick appears in many problems:
   - Find two non-repeating elements
   - Find missing number
   - Swap two numbers without temp variable

4. **Interview Red Flags**:
   - ❌ Using HashMap/Set (O(n) space)
   - ❌ Sorting first (O(n log n) time)
   - ❌ Not knowing XOR properties
   - ✅ Immediately recognizing XOR pattern

5. **Follow-up Questions**:
   - "What if **two** elements appear once?" → XOR all, then separate using any set bit
   - "What if every element appears **three times** except one?" → Use bit counting
   - "Can you do it without XOR?" → Yes, but not O(1) space

6. **Common Mistakes**:
   - Starting with result = nums[0] instead of 0
   - Not understanding why XOR works (just memorizing)
   - Trying to use XOR when elements repeat more than twice (won't work!)

7. **Related Problems**:
   - LeetCode #260: Single Number III (two unique elements)
   - LeetCode #137: Single Number II (elements appear 3 times)
   - LeetCode #268: Missing Number (similar XOR technique)

8. **Proof of Correctness**:
   ```
   Let array = [a, a, b, b, c]  where c appears once
   XOR all: a ^ a ^ b ^ b ^ c
   Rearrange: (a ^ a) ^ (b ^ b) ^ c
   Simplify: 0 ^ 0 ^ c = c ✓
   ```

9. **When NOT to Use XOR**:
   - Elements appear more than twice (need different approach)
   - Multiple elements appear once (basic XOR won't work)
   - Need to preserve original array (XOR doesn't modify, so this is fine)

10. **Interview Pro Tip**: If you see "find unique element where others appear **even number** of times", think XOR immediately!

