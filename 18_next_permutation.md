# 18. Next Permutation

**LeetCode**: [#31 - Next Permutation](https://leetcode.com/problems/next-permutation/)  
**Difficulty**: Medium

## Problem Statement

A **permutation** of an array of integers is an arrangement of its members into a sequence or linear order.

- For example, for `arr = [1,2,3]`, the following are all permutations: `[1,2,3], [1,3,2], [2,1,3], [2,3,1], [3,1,2], [3,2,1]`.

The **next permutation** of an array of integers is the next lexicographically greater permutation of its integer. More formally, if all the permutations of the array are sorted in one container according to their lexicographical order, then the **next permutation** of that array is the permutation that follows it in the sorted container.

If such arrangement is not possible, the array must be rearranged as the lowest possible order (i.e., sorted in ascending order).

The replacement must be **in-place** and use only constant extra memory.

## Examples

### Example 1
```
Input: nums = [1, 2, 3]
Output: [1, 3, 2]
Explanation: [1,3,2] is the next permutation of [1,2,3].
```

### Example 2
```
Input: nums = [3, 2, 1]
Output: [1, 2, 3]
Explanation: [3,2,1] is the largest permutation. Next is smallest: [1,2,3].
```

### Example 3
```
Input: nums = [1, 1, 5]
Output: [1, 5, 1]
```

### Example 4
```
Input: nums = [1, 3, 2]
Output: [2, 1, 3]
```

## Constraints
- 1 ≤ nums.length ≤ 100
- 0 ≤ nums[i] ≤ 100

## Visual Explanation

### ASCII Diagram - Finding Next Permutation

```
Example: nums = [1, 3, 5, 4, 2]
Goal: Find next lexicographically greater permutation

Step 1: Find the "break point" (rightmost ascending pair)
  Scan from right to left:
  
  [1, 3, 5, 4, 2]
        ↑  ↑  ↑
        5>4>2 (descending, skip)
     ↑  ↑
     3<5 (found break point!)
     
  Index i = 1 (nums[1] = 3)
  Everything after i is descending: [5,4,2]

Step 2: Find element just larger than nums[i] from right
  [1, 3, 5, 4, 2]
     ↑     ↑
     3  <  4 (first element > 3 from right)
     
  Index j = 3 (nums[3] = 4)

Step 3: Swap nums[i] and nums[j]
  [1, 3, 5, 4, 2]
     ↑     ↑
     swap
  [1, 4, 5, 3, 2]

Step 4: Reverse everything after i
  [1, 4, | 5, 3, 2]
          ← reverse →
  [1, 4, | 2, 3, 5]

Result: [1, 4, 2, 3, 5] ✓

Verification:
  Original: 1 3 5 4 2
  Next:     1 4 2 3 5 (lexicographically next!)

Visual Pattern:
  [..., smaller, LARGER larger larger]
             ↑        ↑
        break point   all descending after
```

### Why This Works

```
Lexicographic ordering (for [1,2,3]):
  [1, 2, 3]  ← start
  [1, 3, 2]  ← next (swap 2,3)
  [2, 1, 3]  ← next (reset and increment first)
  [2, 3, 1]
  [3, 1, 2]
  [3, 2, 1]  ← largest
  [1, 2, 3]  ← wrap around to smallest

Key Insight:
  - To get "next" permutation, find rightmost position 
    where we can make a small increase
  - Everything after that position should be minimized
    (sorted ascending) for "next" to be as small as possible
```

## Approach & Intuition

**Single-Pass Algorithm** (Optimal):

**Key Observations**:
1. **Break Point**: The rightmost position `i` where `nums[i] < nums[i+1]`
   - Everything after `i` is in descending order
   - To get next permutation, we must change something at or before `i`

2. **Small Increase**: Find the smallest number greater than `nums[i]` from the right
   - This ensures minimal increase
   - Swap it with `nums[i]`

3. **Minimize Suffix**: Reverse everything after `i`
   - Changes descending order to ascending
   - Makes the suffix as small as possible

**Edge Case**: If no break point exists (entire array descending), it's the largest permutation → return smallest (sorted ascending).

## Algorithm Steps

1. **Find break point** (from right):
   - For i from n-2 down to 0:
     - If nums[i] < nums[i+1]: break point found
   - If no break point: reverse entire array and return
   
2. **Find swap element**:
   - For j from n-1 down to i:
     - If nums[j] > nums[i]: found element to swap
     
3. **Swap**: 
   - Swap nums[i] and nums[j]
   
4. **Reverse suffix**:
   - Reverse array from i+1 to end

## Dry Run (Step-by-Step Execution)

Input: `nums = [1, 3, 5, 4, 2]`

```
Initial State:
nums = [1, 3, 5, 4, 2]
n = 5

Step 1: Find Break Point (rightmost i where nums[i] < nums[i+1])
  
  i = 3: nums[3]=4, nums[4]=2
    4 < 2? NO (4 > 2)
    Continue...
    
  i = 2: nums[2]=5, nums[3]=4
    5 < 4? NO (5 > 4)
    Continue...
    
  i = 1: nums[1]=3, nums[2]=5
    3 < 5? YES! Break point found!
    i = 1
    
  After break point: [5, 4, 2] is in descending order ✓

Step 2: Find element just larger than nums[i]=3 from right
  
  j = 4: nums[4]=2
    2 > 3? NO
    
  j = 3: nums[3]=4
    4 > 3? YES! Found!
    j = 3

Step 3: Swap nums[i] and nums[j]
  Before: [1, 3, 5, 4, 2]
              ↑     ↑
          nums[1] nums[3]
          
  Swap: nums[1] ↔ nums[3]
  
  After: [1, 4, 5, 3, 2]

Step 4: Reverse suffix from i+1 to end
  i+1 = 2, end = 4
  
  Reverse [5, 3, 2]:
    [1, 4, | 5, 3, 2]
            ← reverse →
    [1, 4, | 2, 3, 5]

Final Result: [1, 4, 2, 3, 5] ✓

Verification:
  Original: 13542
  Next:     14235 (lexicographically greater and smallest such) ✓
```

### Edge Case: Largest Permutation

```
Input: nums = [3, 2, 1]

Step 1: Find break point
  i = 1: nums[1]=2, nums[2]=1
    2 < 1? NO
    
  i = 0: nums[0]=3, nums[1]=2
    3 < 2? NO
    
  i = -1: No break point found!
  
  This means entire array is descending → largest permutation

Step 2-4: Skip (no break point)

Step 5: Reverse entire array
  [3, 2, 1] → [1, 2, 3]

Result: [1, 2, 3] (smallest permutation) ✓
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
    void nextPermutation(vector<int>& nums) {
        int n = nums.size();
        
        // Step 1: Find the break point (rightmost i where nums[i] < nums[i+1])
        // This is the position where we can make a change to get next permutation
        int breakPoint = -1;  // Initialize to -1 (no break point found)
        
        // Scan from right to left (from n-2 because we compare with i+1)
        for (int i = n - 2; i >= 0; i--) {
            // Check if current element is smaller than next
            // This means we found a position where order increases
            if (nums[i] < nums[i + 1]) {
                breakPoint = i;
                break;  // Found the rightmost break point
            }
        }
        
        // Edge case: If no break point found, array is in descending order
        // This means it's the largest permutation
        // Next permutation is the smallest (sorted ascending)
        if (breakPoint == -1) {
            // Reverse entire array to get ascending order
            reverse(nums.begin(), nums.end());
            return;
        }
        
        // Step 2: Find the element just larger than nums[breakPoint] from the right
        // We need the smallest element that is larger than nums[breakPoint]
        // to ensure we get the "next" (not a larger) permutation
        for (int j = n - 1; j > breakPoint; j--) {
            // Find first element from right that is greater than nums[breakPoint]
            // Since elements after breakPoint are in descending order,
            // first element > nums[breakPoint] is the smallest such element
            if (nums[j] > nums[breakPoint]) {
                // Step 3: Swap nums[breakPoint] with nums[j]
                // This makes a minimal increase at position breakPoint
                swap(nums[breakPoint], nums[j]);
                break;
            }
        }
        
        // Step 4: Reverse the suffix (everything after breakPoint)
        // After swap, suffix is still in descending order
        // Reverse to make it ascending (smallest possible arrangement)
        // This ensures we get the immediate next permutation
        reverse(nums.begin() + breakPoint + 1, nums.end());
        
        // Array now contains the next lexicographical permutation!
    }
    
    // Helper function to print array (for testing)
    void printArray(vector<int>& nums) {
        cout << "[";
        for (int i = 0; i < nums.size(); i++) {
            cout << nums[i];
            if (i < nums.size() - 1) cout << ", ";
        }
        cout << "]";
    }
};

// Driver code for testing
int main() {
    Solution sol;
    
    // Test Case 1: Normal case
    vector<int> nums1 = {1, 2, 3};
    cout << "Original: ";
    sol.printArray(nums1);
    sol.nextPermutation(nums1);
    cout << " → Next: ";
    sol.printArray(nums1);
    cout << endl;
    
    // Test Case 2: Largest permutation
    vector<int> nums2 = {3, 2, 1};
    cout << "Original: ";
    sol.printArray(nums2);
    sol.nextPermutation(nums2);
    cout << " → Next: ";
    sol.printArray(nums2);
    cout << " (wraps to smallest)" << endl;
    
    // Test Case 3: With duplicates
    vector<int> nums3 = {1, 1, 5};
    cout << "Original: ";
    sol.printArray(nums3);
    sol.nextPermutation(nums3);
    cout << " → Next: ";
    sol.printArray(nums3);
    cout << endl;
    
    // Test Case 4: Descending suffix
    vector<int> nums4 = {1, 3, 5, 4, 2};
    cout << "Original: ";
    sol.printArray(nums4);
    sol.nextPermutation(nums4);
    cout << " → Next: ";
    sol.printArray(nums4);
    cout << endl;
    
    // Test Case 5: Single element
    vector<int> nums5 = {1};
    cout << "Original: ";
    sol.printArray(nums5);
    sol.nextPermutation(nums5);
    cout << " → Next: ";
    sol.printArray(nums5);
    cout << " (no change)" << endl;
    
    // Test Case 6: Two elements
    vector<int> nums6 = {1, 2};
    cout << "Original: ";
    sol.printArray(nums6);
    sol.nextPermutation(nums6);
    cout << " → Next: ";
    sol.printArray(nums6);
    cout << endl;
    
    return 0;
}
```

### Complexity Analysis

- **Time Complexity**: O(n) - Three linear scans in worst case:
  1. Finding break point: O(n)
  2. Finding swap element: O(n)
  3. Reversing suffix: O(n)
  Total: O(n)

- **Space Complexity**: O(1) - Only using a few variables (breakPoint, loop counters). All operations are in-place. No additional data structures.

## Edge Cases

1. **Single element**: `[1]` → `[1]` (no change)
2. **Two elements ascending**: `[1, 2]` → `[2, 1]`
3. **Two elements descending**: `[2, 1]` → `[1, 2]`
4. **All same elements**: `[1, 1, 1]` → `[1, 1, 1]` (no change)
5. **Largest permutation**: `[3, 2, 1]` → `[1, 2, 3]`
6. **With duplicates**: `[1, 5, 1]` → `[5, 1, 1]`
7. **Already sorted**: `[1, 2, 3]` → `[1, 3, 2]`

## Key Insights & Interview Tips

1. **Lexicographic Order**:
   - Think of numbers as words in a dictionary
   - "Next" means next word that would appear in dictionary
   - We want the smallest increase possible

2. **Break Point is Key**:
   - Rightmost position where we can increase
   - Everything after is descending (can't be rearranged to be larger)
   - Must modify at break point or before

3. **Why Reverse Suffix?**:
   - After swap, suffix is still descending
   - We want smallest possible suffix
   - Reversing descending → ascending (smallest)

4. **Three Steps Pattern**:
   ```
   1. Find where to change (break point)
   2. What to change it to (minimal increase)
   3. Minimize rest (reverse suffix)
   ```

5. **Common Mistakes**:
   - Not handling largest permutation case (no break point)
   - Sorting instead of reversing (sorting is O(n log n))
   - Forgetting to break after finding swap element
   - Wrong loop bounds

6. **Interview Variations**:
   - "Find previous permutation?" → Similar logic, reverse conditions
   - "Find kth permutation?" → Different approach (factorial number system)
   - "Generate all permutations?" → Backtracking

7. **Related Problems**:
   - LeetCode #46: Permutations (backtracking)
   - LeetCode #47: Permutations II (with duplicates)
   - LeetCode #60: Permutation Sequence (kth permutation)

8. **Optimization Note**:
   - Could binary search for swap element (since suffix is sorted)
   - But O(log n) vs O(n) doesn't matter when overall is O(n)
   - Linear search is simpler and sufficient

9. **Why In-Place is Required**:
   - Generating all permutations would be O(n!)
   - Finding position would be expensive
   - In-place algorithm is elegant and efficient

10. **Testing Strategy**:
    - Test with smallest (ascending) array
    - Test with largest (descending) array
    - Test middle cases
    - Test with duplicates
    - Verify lexicographic ordering manually

11. **Real-World Applications**:
    - Combinatorial generation
    - Testing all configurations
    - Brute-force search optimization
    - Password cracking (ordered attempts)

12. **Pro Tip for Interviews**:
    - Start by explaining lexicographic order with simple example [1,2,3]
    - Draw the break point concept
    - Explain why we reverse (not sort) the suffix
    - Mention the edge case upfront (largest permutation)

