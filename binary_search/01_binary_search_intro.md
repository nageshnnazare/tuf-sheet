# 01. Binary Search Introduction
**LeetCode**: [704 - Binary Search](https://leetcode.com/problems/binary-search/)  
**Difficulty**: Easy

## Problem Statement
Given an array of integers `nums` which is sorted in ascending order, and an integer `target`, write a function to search `target` in `nums`. If `target` exists, then return its index. Otherwise, return `-1`.

You must write an algorithm with `O(log n)` runtime complexity.

## Examples
### Example 1
Input: nums = [-1,0,3,5,9,12], target = 9
Output: 4
Explanation: 9 exists in nums and its index is 4

### Example 2
Input: nums = [-1,0,3,5,9,12], target = 2
Output: -1
Explanation: 2 does not exist in nums so return -1

## Constraints
- `1 <= nums.length <= 10^4`
- `-10^4 < nums[i], target < 10^4`
- All the integers in `nums` are **unique**.
- `nums` is sorted in ascending order.

## Visual Explanation
### ASCII Diagram
```
Target: 9
Sorted Array: [-1, 0, 3, 5, 9, 12]
Indices:       0  1  2  3  4  5

Pass 1:
low = 0, high = 5
mid = (0 + 5) / 2 = 2
nums[2] = 3
Since 3 < 9, search in the right half.
New low = mid + 1 = 3

Pass 2:
low = 3, high = 5
mid = (3 + 5) / 2 = 4
nums[4] = 9
Target found at index 4!
```

## Approach & Intuition
Binary Search is an efficient algorithm for finding an item from a sorted list of items. It works by repeatedly dividing in half the portion of the list that could contain the item, until you've narrowed down the possible locations to just one.

**Key Idea**: 
Compare the target element with the middle element of the array.
1. If the target matches the middle element, its position is returned.
2. If the target is less than the middle element, the search continues in the lower (left) half of the array.
3. If the target is greater than the middle element, the search continues in the upper (right) half of the array.

## Algorithm Steps
### Iterative Approach:
1. Initialize two pointers: `low = 0` and `high = n - 1`.
2. While `low <= high`:
    a. Calculate `mid = low + (high - low) / 2`.
    b. If `nums[mid] == target`, return `mid`.
    c. If `nums[mid] < target`, move `low` to `mid + 1`.
    d. If `nums[mid] > target`, move `high` to `mid - 1`.
3. If the loop ends without finding the target, return `-1`.

### Recursive Approach:
1. Define a function `binarySearch(nums, target, low, high)`.
2. Base Case: If `low > high`, return `-1`.
3. Calculate `mid = low + (high - low) / 2`.
4. If `nums[mid] == target`, return `mid`.
5. If `nums[mid] < target`, return `binarySearch(nums, target, mid + 1, high)`.
6. Else, return `binarySearch(nums, target, low, mid - 1)`.

## Dry Run (Step-by-Step Execution)
**Input**: `nums = [-1,0,3,5,9,12], target = 9`

**Iterative Step-by-Step**:
| Step | low | high | mid | nums[mid] | Comparison | Action |
|------|-----|------|-----|-----------|------------|--------|
| Initial | 0 | 5 | - | - | - | - |
| 1 | 0 | 5 | 2 | 3 | 3 < 9 | low = 3 |
| 2 | 3 | 5 | 4 | 9 | 9 == 9 | Return 4 |

## C++ Solution
### Code with Detailed Comments

```cpp
#include <vector>
using namespace std;

class Solution {
public:
    // Iterative Implementation
    int searchIterative(vector<int>& nums, int target) {
        int low = 0;
        int high = nums.size() - 1;
        
        while (low <= high) {
            // Calculate mid - using this formula to avoid potential overflow
            int mid = low + (high - low) / 2;
            
            if (nums[mid] == target) {
                return mid; // Target found
            }
            else if (nums[mid] < target) {
                low = mid + 1; // Discard left half
            }
            else {
                high = mid - 1; // Discard right half
            }
        }
        
        return -1; // Target not found
    }

    // Recursive Implementation Wrapper
    int searchRecursive(vector<int>& nums, int target) {
        return binarySearch(nums, target, 0, nums.size() - 1);
    }

private:
    int binarySearch(vector<int>& nums, int target, int low, int high) {
        // Base case: target not in range
        if (low > high) {
            return -1;
        }
        
        int mid = low + (high - low) / 2;
        
        if (nums[mid] == target) {
            return mid;
        }
        
        if (nums[mid] < target) {
            // Recursive call for right half
            return binarySearch(nums, target, mid + 1, high);
        } else {
            // Recursive call for left half
            return binarySearch(nums, target, low, mid - 1);
        }
    }
};
```

### Complexity Analysis
- **Time Complexity**: 
    - Iterative: O(log N) - We discard half of the search space in each step.
    - Recursive: O(log N) - Same logarithmic division.
- **Space Complexity**: 
    - Iterative: O(1) - Only a few extra variables for pointers.
    - Recursive: O(log N) - Due to the recursive call stack (auxiliary space).

## Edge Cases
- **Target at the beginning**: `nums = [1, 2, 3], target = 1` -> Should return 0.
- **Target at the end**: `nums = [1, 2, 3], target = 3` -> Should return 2.
- **Array size 1**: `nums = [5], target = 5` -> Should return 0.
- **Target not present**: `nums = [1, 2, 4], target = 3` -> Should return -1.
- **Target smaller than smallest element**: Should return -1.
- **Target larger than largest element**: Should return -1.

## Key Insights & Interview Tips
- **Overflow Avoidance**: Always use `mid = low + (high - low) / 2` instead of `(low + high) / 2` to prevent integer overflow when `low + high` exceeds the maximum value of `int`.
- **Pre‑condition**: Binary Search **only** works on sorted data.
- **Check Boundaries**: Ensure your `while` condition is `low <= high` and pointers are updated with `+1` or `-1` to avoid infinite loops.
- **Interview Tip**: Mention the recursive approach's space complexity due to the call stack; usually, interviewers prefer the iterative one for O(1) space.
