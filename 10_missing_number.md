# 10. Missing Number in Array

**LeetCode**: [#268 - Missing Number](https://leetcode.com/problems/missing-number/)  
**Difficulty**: Easy

## Problem Statement

Given an array `nums` containing `n` distinct numbers in the range `[0, n]`, find the one number that is missing from the array.

## Examples

### Example 1
```
Input: nums = [3, 0, 1]
Output: 2
Explanation: n = 3 since there are 3 numbers. Range is [0,3]. Missing number is 2.
```

### Example 2
```
Input: nums = [0, 1]
Output: 2
Explanation: n = 2, range is [0,2]. Missing number is 2.
```

### Example 3
```
Input: nums = [9,6,4,2,3,5,7,0,1]
Output: 8
Explanation: n = 9, range is [0,9]. Missing number is 8.
```

## Constraints
- n == nums.length
- 1 ≤ n ≤ 10^4
- 0 ≤ nums[i] ≤ n
- All numbers in nums are **unique**

## Visual Explanation

### ASCII Diagram - Three Approaches

```
Example: nums = [3, 0, 1], n = 3

Expected range: [0, 1, 2, 3]
Given array:    [3, 0, 1]
Missing:                2 ←

Approach 1: Sum Formula
  Expected sum: 0+1+2+3 = 6
  Actual sum:   3+0+1   = 4
  Missing = 6 - 4 = 2 ✓

Approach 2: XOR
  XOR all indices [0,1,2,3]: 0^1^2^3
  XOR all values [3,0,1]:    3^0^1
  Combined: 0^1^2^3^3^0^1 = 2 ✓
  (duplicates cancel, 2 remains)

Approach 3: Mark Array (modifies input)
  Create boolean array for [0,n]
  Mark present elements
  Find unmarked

Visual - Sum Method:
  Expected: [0, 1, 2, 3]
            ↓  ↓  X  ↓
  Actual:   [0, 1, _, 3]
            
  Sum of expected - Sum of actual = Missing
```

## Approach & Intuition

### Approach 1: Mathematical Formula ⭐

**Formula**: Sum of first n natural numbers = n × (n + 1) / 2

**Algorithm**:
1. Calculate expected sum: n × (n + 1) / 2
2. Calculate actual sum of array elements
3. Missing number = expected_sum - actual_sum

**Why it works**: The difference between what should be there and what is there gives the missing number.

### Approach 2: XOR Bit Manipulation ⭐

**Algorithm**:
1. XOR all numbers from 0 to n
2. XOR all numbers in the array
3. Duplicate numbers cancel out, missing remains

**Why it works**: `a ^ a = 0`, so all present numbers cancel out with their expected counterparts.

### Approach 3: Hash Set (O(n) space)

Use set to store all numbers, then iterate 0 to n to find missing.

## Algorithm Steps

**Sum Method:**
1. Calculate n = length of array
2. expected_sum = n × (n + 1) / 2
3. actual_sum = sum of all array elements
4. Return expected_sum - actual_sum

**XOR Method:**
1. Initialize result = n
2. For i from 0 to n-1:
   - result ^= i
   - result ^= nums[i]
3. Return result

## Dry Run (Step-by-Step Execution)

### Sum Method
Array: `nums = [3, 0, 1]`, n = 3

```
Initial State:
nums = [3, 0, 1]
n = 3 (array length)

Step 1: Calculate expected sum
  Expected sum = n × (n + 1) / 2
               = 3 × 4 / 2
               = 12 / 2
               = 6
  
  This is sum of [0, 1, 2, 3]

Step 2: Calculate actual sum
  Actual sum = 3 + 0 + 1 = 4

Step 3: Find missing
  Missing = expected_sum - actual_sum
          = 6 - 4
          = 2 ✓

Result: 2
```

### XOR Method
Array: `nums = [3, 0, 1]`, n = 3

```
Initial State:
nums = [3, 0, 1]
n = 3
result = n = 3

Iteration 0: i = 0
  result ^= i     → result = 3 ^ 0 = 3
  result ^= nums[i] → result = 3 ^ 3 = 0
  (3 from initial and 3 from array cancel out)

Iteration 1: i = 1
  result ^= i     → result = 0 ^ 1 = 1
  result ^= nums[i] → result = 1 ^ 0 = 1

Iteration 2: i = 2
  result ^= i     → result = 1 ^ 2 = 3
  result ^= nums[i] → result = 3 ^ 1 = 2

Final Result: 2 ✓

Explanation:
  XORed: 3 ^ (0^0) ^ (1^1) ^ 2
       =3 ^ 0 ^ 0 ^ 2
       = 3 ^ 2
  But wait, we also XORed 3 from nums:
  3 ^ 0^0 ^ 1^1 ^ 2 ^ 3
  = 0 ^ 0 ^ 2 = 2 ✓
```

## C++ Solution

### Code with Detailed Comments

```cpp
#include <iostream>
#include <vector>
#include <numeric> // for accumulate
using namespace std;

class Solution {
public:
    // Approach 1: Using sum formula (most intuitive)
    int missingNumber(vector<int>& nums) {
        int n = nums.size();
        
        // Calculate expected sum: sum of 0 to n
        // Formula: n × (n + 1) / 2
        // This is faster than looping to calculate sum
        int expectedSum = n * (n + 1) / 2;
        
        // Calculate actual sum of array elements
        int actualSum = 0;
        for (int num : nums) {
            actualSum += num;
        }
        
        // The missing number is the difference
        // Because: expected sum = actual sum + missing number
        // Therefore: missing number = expected sum - actual sum
        return expectedSum - actualSum;
    }
    
    // Approach 2: XOR method (bit manipulation)
    int missingNumberXOR(vector<int>& nums) {
        int n = nums.size();
        
        // Initialize result with n
        // We'll XOR with all indices [0 to n-1] and all array values
        int result = n;
        
        // XOR all indices and corresponding array values
        for (int i = 0; i < n; i++) {
            // XOR with index: this represents the expected numbers
            result ^= i;
            
            // XOR with array value: this represents actual numbers
            result ^= nums[i];
            
            // Numbers present in array will cancel out with their indices
            // Only the missing number's index will remain
        }
        
        return result;
    }
    
    // Approach 3: Using STL accumulate (concise sum method)
    int missingNumberSTL(vector<int>& nums) {
        int n = nums.size();
        int expectedSum = n * (n + 1) / 2;
        
        // accumulate sums all elements from begin() to end()
        // Third parameter 0 is the initial value
        int actualSum = accumulate(nums.begin(), nums.end(), 0);
        
        return expectedSum - actualSum;
    }
};

// Driver code
int main() {
    Solution sol;
    
    // Test Case 1
    vector<int> nums1 = {3, 0, 1};
    cout << "Array: [3, 0, 1]" << endl;
    cout << "Missing (Sum method): " << sol.missingNumber(nums1) << endl;
    cout << "Missing (XOR method): " << sol.missingNumberXOR(nums1) << endl << endl;
    
    // Test Case 2
    vector<int> nums2 = {0, 1};
    cout << "Array: [0, 1]" << endl;
    cout << "Missing (Sum method): " << sol.missingNumber(nums2) << endl << endl;
    
    // Test Case 3
    vector<int> nums3 = {9, 6, 4, 2, 3, 5, 7, 0, 1};
    cout << "Array: [9, 6, 4, 2, 3, 5, 7, 0, 1]" << endl;
    cout << "Missing (Sum method): " << sol.missingNumber(nums3) << endl << endl;
    
    // Test Case 4: Missing is 0
    vector<int> nums4 = {1, 2, 3};
    cout << "Array: [1, 2, 3]" << endl;
    cout << "Missing: " << sol.missingNumber(nums4) << " (missing is 0)" << endl << endl;
    
    // Test Case 5: Missing is n
    vector<int> nums5 = {0, 1, 2};
    cout << "Array: [0, 1, 2]" << endl;
    cout << "Missing: " << sol.missingNumber(nums5) << " (missing is n=3)" << endl;
    
    return 0;
}
```

### Complexity Analysis

#### Sum Method:
- **Time Complexity**: O(n) - Single pass to sum array elements
- **Space Complexity**: O(1) - Only using variables for sums

#### XOR Method:
- **Time Complexity**: O(n) - Single pass to XOR all elements
- **Space Complexity**: O(1) - Only using result variable

Both methods are equally efficient!

## Edge Cases

1. **Missing is 0**: `[1, 2, 3]` → `0`
2. **Missing is n**: `[0, 1, 2]` → `3`
3. **Single element [0]**: `[0]` → `1`
4. **Single element [1]**: `[1]` → `0`
5. **Two elements**: `[0,1]` → `2`
6. **Large n**: Works fine, watch for integer overflow in sum method

## Key Insights & Interview Tips

1. **Two Optimal Solutions**:
   - **Sum**: More intuitive, easier to explain
   - **XOR**: Shows bit manipulation knowledge

2. **Integer Overflow Risk**: 
   - Sum method might overflow for very large n
   - Use `long long` if necessary: `long long sum = (long long)n * (n + 1) / 2`
   - XOR method has no overflow risk

3. **Follow-up Questions**:
   - "What if two numbers are missing?" → Sum alone won't work, need sum and sum of squares
   - "What if range is [1, n] instead of [0, n]?" → Adjust formula slightly
   - "What if array is unsorted?" → Both methods still work!

4. **Why These Methods Work**:
   - **Sum**: Arithmetic - difference gives missing
   - **XOR**: Bit manipulation - duplicates cancel

5. **Common Mistakes**:
   - Forgetting that range includes n (it's [0, n], not [0, n-1])
   - Integer overflow in sum calculation
   - Off-by-one in loop bounds for XOR method

6. **Alternative (Not Optimal)**:
   - Sort and check gaps: O(n log n)
   - Use hash set: O(n) space
   - Boolean array: O(n) space

7. **Interview Variations**:
   - Find missing in [1, n] range
   - Find two missing numbers
   - Find missing in arithmetic sequence

8. **Real-world Applications**:
   - Detecting missing sequential IDs
   - Finding missing page numbers
   - Network packet loss detection

9. **Choice Between Methods**:
   - **Prefer Sum** if: Want clarity, overflow not a concern
   - **Prefer XOR** if: Want to show bit manipulation skills, avoid overflow

10. **Pro Tip**: Both answers are equally acceptable in interviews. Choose based on what you can explain better!

