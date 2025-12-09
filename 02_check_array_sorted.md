# 2. Check if Array is Sorted

**LeetCode**: N/A (Foundational Concept)  
**Difficulty**: Easy

## Problem Statement

Given an array of integers, determine whether the array is sorted in non-decreasing (ascending) order. Return `true` if the array is sorted, otherwise return `false`.

## Examples

### Example 1
```
Input: arr = [1, 2, 3, 4, 5]
Output: true
Explanation: Each element is greater than or equal to the previous element.
```

### Example 2
```
Input: arr = [1, 3, 2, 4]
Output: false
Explanation: 3 > 2, so the array is not sorted.
```

### Example 3
```
Input: arr = [5, 5, 5, 5]
Output: true
Explanation: All elements are equal, which is considered sorted (non-decreasing).
```

### Example 4
```
Input: arr = []
Output: true
Explanation: An empty array is considered sorted.
```

## Constraints
- 0 ≤ arr.length ≤ 10^5
- -10^9 ≤ arr[i] ≤ 10^9

## Visual Explanation

### ASCII Diagram - Sorted vs Unsorted

```
SORTED ARRAY (Non-Decreasing):
Index:  0   1   2   3   4
Array: [1,  2,  3,  4,  5]
        ↓   ↓   ↓   ↓   ↓
Check:  ✓ 1≤2 ✓ 2≤3 ✓ 3≤4 ✓ 4≤5
        
All comparisons pass → SORTED


UNSORTED ARRAY:
Index:  0   1   2   3   4
Array: [1,  3,  2,  4,  5]
        ↓   ↓   ✗   ↓   ↓
Check:  ✓ 1≤3 ✗ 3≤2 (FALSE!)
                ↑
            Violation found!
            
Comparison fails → NOT SORTED


ARRAY WITH DUPLICATES (Still Sorted):
Index:  0   1   2   3   4
Array: [1,  2,  2,  3,  3]
        ↓   ↓   ↓   ↓   ↓
Check:  ✓ 1≤2 ✓ 2≤2 ✓ 2≤3 ✓ 3≤3
        
All comparisons pass → SORTED
```

## Approach & Intuition

The solution is straightforward:
- **Traverse the array from left to right**
- **Compare each element with the next element**
- If any element is greater than the next element, the array is not sorted
- If we complete the traversal without finding any violation, the array is sorted

**Why this works:**
- In a sorted array, every element must be ≤ the next element
- We only need to find one violation to conclude the array is not sorted
- We can stop early if we find a violation (optimization)

## Algorithm Steps

1. Handle edge cases: empty array or single element → return true
2. Iterate from index 0 to n-2 (where n is array length)
3. For each index i:
   - Compare arr[i] with arr[i+1]
   - If arr[i] > arr[i+1], return false (found a violation)
4. If loop completes without returning, return true (array is sorted)

## Dry Run (Step-by-Step Execution)

### Example 1: Sorted Array `[1, 2, 3, 4, 5]`

```
Initial State:
arr = [1, 2, 3, 4, 5]
n = 5

Iteration 0: i = 0
  Compare arr[0] with arr[1]
  1 ≤ 2 → TRUE
  Continue...

Iteration 1: i = 1
  Compare arr[1] with arr[2]
  2 ≤ 3 → TRUE
  Continue...

Iteration 2: i = 2
  Compare arr[2] with arr[3]
  3 ≤ 4 → TRUE
  Continue...

Iteration 3: i = 3
  Compare arr[3] with arr[4]
  4 ≤ 5 → TRUE
  Continue...

Loop completed without violations.
Result: true (Array is SORTED)
```

### Example 2: Unsorted Array `[1, 3, 2, 4]`

```
Initial State:
arr = [1, 3, 2, 4]
n = 4

Iteration 0: i = 0
  Compare arr[0] with arr[1]
  1 ≤ 3 → TRUE
  Continue...

Iteration 1: i = 1
  Compare arr[1] with arr[2]
  3 ≤ 2 → FALSE ✗
  Violation found!
  
Return false immediately (Array is NOT SORTED)
```

### Example 3: Array with Duplicates `[5, 5, 5, 5]`

```
Initial State:
arr = [5, 5, 5, 5]
n = 4

Iteration 0: i = 0
  Compare arr[0] with arr[1]
  5 ≤ 5 → TRUE (equal is allowed in non-decreasing)
  Continue...

Iteration 1: i = 1
  Compare arr[1] with arr[2]
  5 ≤ 5 → TRUE
  Continue...

Iteration 2: i = 2
  Compare arr[2] with arr[3]
  5 ≤ 5 → TRUE
  Continue...

Loop completed without violations.
Result: true (Array is SORTED - all equal)
```

## C++ Solution

### Code with Detailed Comments

```cpp
#include <iostream>
#include <vector>
using namespace std;

class Solution {
public:
    // Function to check if array is sorted in non-decreasing order
    bool isSorted(vector<int>& arr) {
        int n = arr.size();
        
        // Edge case: Empty array or single element is always considered sorted
        if (n <= 1) {
            return true;
        }
        
        // Traverse the array and check each adjacent pair
        // We iterate from 0 to n-2 because we compare arr[i] with arr[i+1]
        // This ensures we don't go out of bounds
        for (int i = 0; i < n - 1; i++) {
            // Check if current element is greater than next element
            // In a sorted array, arr[i] should be <= arr[i+1]
            if (arr[i] > arr[i + 1]) {
                // Found a violation: current element is greater than next
                // This means array is NOT sorted
                return false;
            }
            // If arr[i] <= arr[i+1], continue to next pair
        }
        
        // If we've checked all adjacent pairs without finding violations
        // The array is sorted
        return true;
    }
    
    // Alternative: Check if sorted in strictly increasing order (no duplicates allowed)
    bool isStrictlySorted(vector<int>& arr) {
        int n = arr.size();
        
        // Edge case handling
        if (n <= 1) {
            return true;
        }
        
        // For strictly increasing, we use < instead of <=
        for (int i = 0; i < n - 1; i++) {
            // Current element must be strictly less than next element
            // Equal elements are NOT allowed in strictly increasing order
            if (arr[i] >= arr[i + 1]) {
                return false;
            }
        }
        
        return true;
    }
    
    // Check if array is sorted in descending order
    bool isSortedDescending(vector<int>& arr) {
        int n = arr.size();
        
        if (n <= 1) {
            return true;
        }
        
        // For descending order, each element should be >= next element
        for (int i = 0; i < n - 1; i++) {
            if (arr[i] < arr[i + 1]) {
                return false;
            }
        }
        
        return true;
    }
};

// Driver code for testing
int main() {
    Solution sol;
    
    // Test Case 1: Sorted array
    vector<int> arr1 = {1, 2, 3, 4, 5};
    cout << "Array: [1, 2, 3, 4, 5]" << endl;
    cout << "Is Sorted: " << (sol.isSorted(arr1) ? "true" : "false") << endl;
    cout << "Is Strictly Sorted: " << (sol.isStrictlySorted(arr1) ? "true" : "false") << endl << endl;
    
    // Test Case 2: Unsorted array
    vector<int> arr2 = {1, 3, 2, 4};
    cout << "Array: [1, 3, 2, 4]" << endl;
    cout << "Is Sorted: " << (sol.isSorted(arr2) ? "true" : "false") << endl << endl;
    
    // Test Case 3: Array with duplicates
    vector<int> arr3 = {5, 5, 5, 5};
    cout << "Array: [5, 5, 5, 5]" << endl;
    cout << "Is Sorted: " << (sol.isSorted(arr3) ? "true" : "false") << endl;
    cout << "Is Strictly Sorted: " << (sol.isStrictlySorted(arr3) ? "true" : "false") << endl << endl;
    
    // Test Case 4: Empty array
    vector<int> arr4 = {};
    cout << "Array: []" << endl;
    cout << "Is Sorted: " << (sol.isSorted(arr4) ? "true" : "false") << endl << endl;
    
    // Test Case 5: Single element
    vector<int> arr5 = {42};
    cout << "Array: [42]" << endl;
    cout << "Is Sorted: " << (sol.isSorted(arr5) ? "true" : "false") << endl << endl;
    
    // Test Case 6: Descending order
    vector<int> arr6 = {5, 4, 3, 2, 1};
    cout << "Array: [5, 4, 3, 2, 1]" << endl;
    cout << "Is Sorted Ascending: " << (sol.isSorted(arr6) ? "true" : "false") << endl;
    cout << "Is Sorted Descending: " << (sol.isSortedDescending(arr6) ? "true" : "false") << endl;
    
    return 0;
}
```

### Complexity Analysis

- **Time Complexity**: O(n) - We traverse the array once, where n is the number of elements. In the worst case (when array is sorted), we check all n-1 pairs. In the best case (when first pair violates), we return immediately in O(1).

- **Space Complexity**: O(1) - We only use a constant amount of extra space (variable `i` for the loop counter). No additional data structures are used.

## Edge Cases

1. **Empty array**: `[]` → Return true (vacuously true)
2. **Single element**: `[5]` → Return true (trivially sorted)
3. **Two elements sorted**: `[1, 2]` → Return true
4. **Two elements unsorted**: `[2, 1]` → Return false
5. **All elements equal**: `[7, 7, 7]` → Return true (non-decreasing)
6. **Large numbers**: `[INT_MIN, 0, INT_MAX]` → Return true
7. **Negative numbers**: `[-5, -3, -1, 0, 2]` → Return true
8. **Mix of positive and negative**: `[-10, -5, 0, 5, 10]` → Return true

## Key Insights & Interview Tips

1. **Clarify Requirements**: Ask the interviewer:
   - Should the array be non-decreasing (≤) or strictly increasing (<)?
   - How to handle duplicates?
   - What about descending order?

2. **Edge Case Handling**: Always consider:
   - Empty arrays - usually return true
   - Single element - always sorted
   - All equal elements

3. **Optimization**: Early termination when violation is found saves time in practice.

4. **Variation - Sorted with One Rotation**: A common follow-up is to check if array is sorted after one rotation (e.g., `[3, 4, 5, 1, 2]`). This requires finding at most one "break point".

5. **Related Problems**:
   - Check if array is sorted and rotated
   - Find the rotation point in a rotated sorted array
   - Merge two sorted arrays

6. **Built-in Alternative (C++)**: `std::is_sorted(arr.begin(), arr.end())` exists in `<algorithm>`, but implementing from scratch shows understanding.

7. **Interview Variations**:
   - "Is the array sorted in descending order?"
   - "Is the array sorted but potentially rotated?"
   - "Count how many elements are out of place"
   - "Find the minimum number of swaps to make array sorted"

8. **Common Mistakes**:
   - Using `arr[i] < arr[i+1]` instead of `arr[i] <= arr[i+1]` (this would reject valid arrays with duplicates)
   - Forgetting to handle edge cases (empty or single element)
   - Going out of bounds by iterating to `n` instead of `n-1`

9. **Testing Strategy**: Test with:
   - Normal sorted array
   - Unsorted array
   - Array with duplicates
   - Edge cases (empty, single element)
   - Reverse sorted array
