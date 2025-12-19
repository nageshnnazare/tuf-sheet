# 3. Remove Duplicates from Sorted Array (In-Place)

**LeetCode**: [#26 - Remove Duplicates from Sorted Array](https://leetcode.com/problems/remove-duplicates-from-sorted-array/)  
**Difficulty**: Easy

## Problem Statement

Given a sorted array `nums`, remove the duplicates in-place such that each unique element appears only once. The relative order of elements should be kept the same. Return the number of unique elements.

**Important**: You must modify the array in-place with O(1) extra space. The first k elements of the array should contain the unique elements in the order they were present.

## Examples

### Example 1
```
Input: nums = [1, 1, 2]
Output: 2, nums = [1, 2, _]
Explanation: Function should return k = 2, with first two elements being 1 and 2.
```

### Example 2
```
Input: nums = [0, 0, 1, 1, 1, 2, 2, 3, 3, 4]
Output: 5, nums = [0, 1, 2, 3, 4, _, _, _, _, _]
Explanation: Function returns k = 5, with first five elements being unique values.
```

### Example 3
```
Input: nums = [1, 2, 3]
Output: 3, nums = [1, 2, 3]
Explanation: No duplicates, all elements are unique.
```

## Constraints
- 1 ≤ nums.length ≤ 3 × 10^4
- -100 ≤ nums[i] ≤ 100
- nums is sorted in non-decreasing order

## Visual Explanation

### ASCII Diagram - Two Pointer Technique

```
Initial Array: [0, 0, 1, 1, 1, 2, 2, 3, 3, 4]
                ↑
                i (slow pointer - position for next unique element)
                j (fast pointer - scanning for unique elements)

Step 0: Initialize
  i = 0 (points to first element, which is always unique)
  j = 1 (starts scanning from second element)
  
  [0, 0, 1, 1, 1, 2, 2, 3, 3, 4]
   ↑  ↑
   i  j

Step 1: j = 1
  nums[j] = 0 (duplicate of nums[i] = 0)
  No action needed, j++
  
  [0, 0, 1, 1, 1, 2, 2, 3, 3, 4]
   ↑     ↑
   i     j

Step 2: j = 2
  nums[j] = 1 (different from nums[i] = 0)
  Found new unique element!
  i++ → i = 1
  nums[i] = nums[j] → nums[1] = 1
  
  [0, 1, 1, 1, 1, 2, 2, 3, 3, 4]
      ↑  ↑
      i  j

Step 3: j = 3
  nums[j] = 1 (duplicate of nums[i] = 1)
  No action, j++
  
  [0, 1, 1, 1, 1, 2, 2, 3, 3, 4]
      ↑     ↑
      i     j

Step 4: j = 4
  nums[j] = 1 (still duplicate)
  No action, j++
  
  [0, 1, 1, 1, 1, 2, 2, 3, 3, 4]
      ↑        ↑
      i        j

Step 5: j = 5
  nums[j] = 2 (different from nums[i] = 1)
  Found new unique!
  i++ → i = 2
  nums[i] = nums[j] → nums[2] = 2
  
  [0, 1, 2, 1, 1, 2, 2, 3, 3, 4]
         ↑        ↑
         i        j

Step 6-7: Skip duplicates of 2

Step 8: j = 7
  nums[j] = 3 (new unique)
  i++ → i = 3
  nums[i] = nums[j] → nums[3] = 3
  
  [0, 1, 2, 3, 1, 2, 2, 3, 3, 4]
            ↑              ↑
            i              j

Step 9: j = 8
  nums[j] = 3 (duplicate)
  Skip
  
Step 10: j = 9
  nums[j] = 4 (new unique)
  i++ → i = 4
  nums[i] = nums[j] → nums[4] = 4
  
  [0, 1, 2, 3, 4, 2, 2, 3, 3, 4]
               ↑              ↑
               i              j

Final: Return i + 1 = 5
  First 5 elements are unique: [0, 1, 2, 3, 4]
```

## Approach & Intuition

Since the array is **already sorted**, all duplicates are adjacent. This is the key insight!

**Two-Pointer Technique:**
1. **Slow pointer (i)**: Points to the position where the next unique element should be placed
2. **Fast pointer (j)**: Scans through the array to find unique elements

**Why this works:**
- Start with i = 0 (first element is always unique)
- Scan with j from index 1
- When we find nums[j] != nums[i], we've found a new unique element
- Increment i and copy nums[j] to nums[i]
- All unique elements end up in positions 0 to i

## Algorithm Steps

1. Handle edge case: if array is empty, return 0
2. Initialize i = 0 (slow pointer for unique elements)
3. For j from 1 to n-1 (fast pointer):
   - If nums[j] != nums[i]:
     - Increment i
     - Copy nums[j] to nums[i]
4. Return i + 1 (count of unique elements)

## Dry Run (Step-by-Step Execution)

Let's trace with: `nums = [0, 0, 1, 1, 1, 2, 2, 3, 3, 4]`

```
Initial State:
nums = [0, 0, 1, 1, 1, 2, 2, 3, 3, 4]
n = 10
i = 0 (slow pointer)

Iteration 1: j = 1
  nums[j] = 0, nums[i] = 0
  0 == 0 → Duplicate, skip
  State: i = 0, nums = [0, 0, 1, 1, 1, 2, 2, 3, 3, 4]

Iteration 2: j = 2
  nums[j] = 1, nums[i] = 0
  1 != 0 → New unique element!
  i++ → i = 1
  nums[i] = nums[j] → nums[1] = 1
  State: i = 1, nums = [0, 1, 1, 1, 1, 2, 2, 3, 3, 4]

Iteration 3: j = 3
  nums[j] = 1, nums[i] = 1
  1 == 1 → Duplicate, skip
  State: i = 1, nums = [0, 1, 1, 1, 1, 2, 2, 3, 3, 4]

Iteration 4: j = 4
  nums[j] = 1, nums[i] = 1
  1 == 1 → Duplicate, skip
  State: i = 1, nums = [0, 1, 1, 1, 1, 2, 2, 3, 3, 4]

Iteration 5: j = 5
  nums[j] = 2, nums[i] = 1
  2 != 1 → New unique element!
  i++ → i = 2
  nums[i] = nums[j] → nums[2] = 2
  State: i = 2, nums = [0, 1, 2, 1, 1, 2, 2, 3, 3, 4]

Iteration 6: j = 6
  nums[j] = 2, nums[i] = 2
  2 == 2 → Duplicate, skip
  State: i = 2, nums = [0, 1, 2, 1, 1, 2, 2, 3, 3, 4]

Iteration 7: j = 7
  nums[j] = 3, nums[i] = 2
  3 != 2 → New unique element!
  i++ → i = 3
  nums[i] = nums[j] → nums[3] = 3
  State: i = 3, nums = [0, 1, 2, 3, 1, 2, 2, 3, 3, 4]

Iteration 8: j = 8
  nums[j] = 3, nums[i] = 3
  3 == 3 → Duplicate, skip
  State: i = 3, nums = [0, 1, 2, 3, 1, 2, 2, 3, 3, 4]

Iteration 9: j = 9
  nums[j] = 4, nums[i] = 3
  4 != 3 → New unique element!
  i++ → i = 4
  nums[i] = nums[j] → nums[4] = 4
  State: i = 4, nums = [0, 1, 2, 3, 4, 2, 2, 3, 3, 4]

Loop ends.
Return: i + 1 = 4 + 1 = 5

Final Array: [0, 1, 2, 3, 4, _, _, _, _, _]
                ← 5 unique elements →
```

## C++ Solution

### Code with Detailed Comments

```cpp
#include <iostream>
#include <vector>
using namespace std;

class Solution {
public:
    // Function to remove duplicates from sorted array in-place
    int removeDuplicates(vector<int>& nums) {
        int n = nums.size();
        
        // Edge case: If array is empty, no elements to process
        if (n == 0) {
            return 0;
        }
        
        // Edge case: If array has only one element, it's already unique
        if (n == 1) {
            return 1;
        }
        
        // Initialize slow pointer 'i'
        // i points to the last position of unique elements
        // First element is always unique, so start at index 0
        int i = 0;
        
        // Fast pointer 'j' scans through the array from index 1
        // We compare nums[j] with nums[i] to detect unique elements
        for (int j = 1; j < n; j++) {
            // If current element is different from the last unique element
            // It means we've found a new unique element
            if (nums[j] != nums[i]) {
                // Move slow pointer forward to make space for new unique element
                i++;
                
                // Copy the new unique element to position i
                // This overwrites the duplicate value that was there
                nums[i] = nums[j];
                
                // Note: We don't need to do anything when nums[j] == nums[i]
                // because those are duplicates we want to skip
            }
        }
        
        // i is the index of last unique element
        // So count of unique elements is i + 1 (since indexing starts at 0)
        return i + 1;
    }
};

// Driver code for testing
int main() {
    Solution sol;
    
    // Test Case 1
    vector<int> nums1 = {1, 1, 2};
    int k1 = sol.removeDuplicates(nums1);
    cout << "Test 1 - Input: [1, 1, 2]" << endl;
    cout << "Unique count: " << k1 << endl;
    cout << "Result array: [";
    for (int i = 0; i < k1; i++) {
        cout << nums1[i];
        if (i < k1 - 1) cout << ", ";
    }
    cout << "]" << endl << endl;
    
    // Test Case 2
    vector<int> nums2 = {0, 0, 1, 1, 1, 2, 2, 3, 3, 4};
    int k2 = sol.removeDuplicates(nums2);
    cout << "Test 2 - Input: [0, 0, 1, 1, 1, 2, 2, 3, 3, 4]" << endl;
    cout << "Unique count: " << k2 << endl;
    cout << "Result array: [";
    for (int i = 0; i < k2; i++) {
        cout << nums2[i];
        if (i < k2 - 1) cout << ", ";
    }
    cout << "]" << endl << endl;
    
    // Test Case 3: No duplicates
    vector<int> nums3 = {1, 2, 3};
    int k3 = sol.removeDuplicates(nums3);
    cout << "Test 3 - Input: [1, 2, 3]" << endl;
    cout << "Unique count: " << k3 << endl;
    cout << "Result array: [";
    for (int i = 0; i < k3; i++) {
        cout << nums3[i];
        if (i < k3 - 1) cout << ", ";
    }
    cout << "]" << endl << endl;
    
    // Test Case 4: All same elements
    vector<int> nums4 = {5, 5, 5, 5, 5};
    int k4 = sol.removeDuplicates(nums4);
    cout << "Test 4 - Input: [5, 5, 5, 5, 5]" << endl;
    cout << "Unique count: " << k4 << endl;
    cout << "Result array: [";
    for (int i = 0; i < k4; i++) {
        cout << nums4[i];
        if (i < k4 - 1) cout << ", ";
    }
    cout << "]" << endl;
    
    return 0;
}
```

### Complexity Analysis

- **Time Complexity**: O(n) - We traverse the array exactly once with the fast pointer `j`. Each element is visited once, and each operation inside the loop is O(1).

- **Space Complexity**: O(1) - We only use two integer variables (`i` and `j`) regardless of input size. The modification is done in-place without any additional data structures.

## Edge Cases

1. **Empty array**: `[]` → Return 0
2. **Single element**: `[5]` → Return 1
3. **No duplicates**: `[1, 2, 3, 4]` → Return 4 (arr unchanged)
4. **All duplicates**: `[7, 7, 7, 7]` → Return 1, arr = `[7, ...]`
5. **Two unique elements with duplicates**: `[1, 1, 1, 2, 2, 2]` → Return 2, arr = `[1, 2, ...]`
6. **Negative numbers**: `[-3, -3, -1, -1, 0, 0, 1]` → Works correctly

## Key Insights & Interview Tips

1. **Why Two Pointers Work Here**: The array is sorted, so duplicates are adjacent. This allows us to use two pointers efficiently.

2. **In-Place Modification**: We're not creating a new array. We're rearranging the existing array so unique elements are at the front.

3. **Order Preservation**: The relative order of unique elements is maintained because we process from left to right.

4. **What Happens to Duplicates**: They remain in the array after index `i`, but we don't care about them. Only first k elements matter.

5. **Variation - Allow K Duplicates**: A follow-up question might ask "remove duplicates such that each unique element appears at most twice". The logic changes slightly:
   ```cpp
   if (i < 1 || nums[j] != nums[i-1]) {
       nums[i++] = nums[j];
   }
   ```

6. **Related Problems**:
   - LeetCode #80: Remove Duplicates from Sorted Array II (allow at most 2)
   - Remove duplicates from unsorted array (requires different approach - hash set)

7. **Interview Follow-ups**:
   - "What if array is not sorted?" → Use HashSet, O(n) space
   - "What if we want to preserve all elements?" → Return new array
   - "Can you do it without two pointers?" → Yes, but less efficient

8. **Common Mistakes**:
   - Starting both pointers at 0 (first element is always unique!)
   - Incrementing i before checking condition
   - Returning i instead of i+1
   - Not handling empty array edge case

9. **Optimization Note**: The if condition `nums[j] != nums[i]` naturally handles the case where array has no duplicates (i and j move together).

10. **Testing Checklist**:
    - Array with duplicates
    - Array without duplicates
    - All elements same
    - Single/empty array
    - Mix of positive/negative numbers
