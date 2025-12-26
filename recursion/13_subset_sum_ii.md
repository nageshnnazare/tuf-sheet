# 13. Subset Sum II
**LeetCode**: [90 - Subsets II](https://leetcode.com/problems/subsets-ii/)  
**Difficulty**: Medium

## Problem Statement
Given an integer array `nums` that may contain **duplicates**, return all possible subsets. The solution set must not contain duplicate subsets. Return the solution in any order. This is the subset version of the unique combination problem.

## Examples
### Example 1
Input: `nums = [1, 2, 2]`  
Output: `[[], [1], [1, 2], [1, 2, 2], [2], [2, 2]]`  
Explanation: We avoid duplicate `[1, 2]` paths by skipping identical elements at the same recursion depth.

### Example 2
Input: `nums = [0]`  
Output: `[[], [0]]`

## Constraints
- `1 <= nums.length <= 10`
- `-10 <= nums[i] <= 10`

## Visual Explanation
### ASCII Diagram: Horizontal vs Vertical Uniqueness
We sort the array `[1, 2, 2]` and pick elements using a loop.

```
Level 0: [] (The empty subset is the root)
 |
 |--(i=0) Pick 1: [1] -> Vertical: recurse for f(1, [1])
 |    |
 |    |-- (i=1) Pick 2: [1, 2] -> Vertical: recurse for f(2, [1, 2])
 |    |    |
 |    |    |-- (i=2) Pick 2: [1, 2, 2] -> f(3) Base Case
 |    |
 |    |-- (i=2) Pick 2: SKIP! (Horizontal: arr[2]==arr[1] and i>start_index)
 |
 |--(i=1) Pick 2: [2] -> Vertical: recurse for f(2, [2])
 |    |
 |    |-- (i=2) Pick 2: [2, 2] -> f(3)
 |
 |--(i=2) Pick 2: SKIP! (Horizontal: arr[2]==arr[1] and i>start_index)
```

## Approach & Intuition
The core problem is that picking the first '2' and then ignoring the second '2' creates the same subsets as ignoring the first '2' and picking the second one.

- **Sorting**: Mandatory. It groups duplicate values together so we can detect them easily.
- **Node-Collection**: Unlike many recursion problems that only collect results at leaf nodes (base cases), Subset Sum II collects the current state at **every node** in the recursion tree.
- **Horizontal Duplicate Skipping**: In the loop `for(i = ind to N-1)`, if we see `arr[i] == arr[i-1]`, it means we already started a branch with this same value at this same depth. We skip it ("Horizontal" skipping).
- **Vertical Inclusion**: We pass `i + 1` to the next call, which allows picking identical values if they are at different indices in the sorted array ("Vertical" inclusion).

## Algorithm Steps
1. **Sort** `nums`.
2. Define `solve(index, currentDS)`.
3. **Collect**: Immediately add `currentDS` to the global answer vector.
4. **Iterative Step**:
   - Loop `i` from `index` to `N-1`:
     - **Unique Filter**: `if (i > index && nums[i] == nums[i-1]) continue;`.
     - **Pick**: Push `nums[i]` to `currentDS`.
     - **Recurse**: `solve(i + 1, currentDS)`.
     - **Backtrack**: Pop from `currentDS`.

## Dry Run (Step-by-Step Execution)
Input: `nums = [1, 2, 2]`
1. **`solve(0, [])`**: `ans = [ [] ]`.
   - `i = 0`: Pick `1`. Call `solve(1, [1])`.
     - **`solve(1, [1])`**: `ans = [ [], [1] ]`.
       - `i = 1`: Pick `2`. Call `solve(2, [1, 2])`.
         - **`solve(2, [1, 2])`**: `ans = [ [], [1], [1, 2] ]`.
           - `i = 2`: Pick `2`. Call `solve(3, [1, 2, 2])`.
             - **`solve(3, [1, 2, 2])`**: `ans = [..., [1, 2, 2]]`. Returns.
       - `i = 2`: `nums[2]==nums[1]` and `2 > 1`. **SKIP**.
   - `i = 1`: Pick `2`. Call `solve(2, [2])`.
     - **`solve(2, [2])`**: `ans = [..., [2]]`.
       - `i = 2`: Pick `2`. Call `solve(3, [2, 2])`.
         - **`solve(3, [2, 2])`**: `ans = [..., [2, 2]]`. Returns.
   - `i = 2`: `nums[2]==nums[1]` and `2 > 0`. **SKIP**.

## C++ Solution
### Code with Detailed Comments
```cpp
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;

class Solution {
public:
    /**
     * Recursive function to generate all unique subsets.
     * Note how the current combination is added at the start of every call.
     */
    void findUniqueSubsets(int ind, vector<int> &nums, vector<int> &ds, vector<vector<int>> &ans) {
        // Collect current subset (node) into results
        ans.push_back(ds);

        for (int i = ind; i < nums.size(); i++) {
            // DUPLICATE HANDLING:
            // Skip the choice if it's identical to the previous choice at THIS level.
            // i > ind ensures we don't skip the very first element we pick for this depth.
            if (i > ind && nums[i] == nums[i - 1]) continue;

            // Decision: Include this number
            ds.push_back(nums[i]);
            
            // Recurse: Move to next index for the subsequent positions
            findUniqueSubsets(i + 1, nums, ds, ans);
            
            // Backtrack: Remove choice to explore other alternatives in the loop
            ds.pop_back();
        }
    }

    vector<vector<int>> subsetsWithDup(vector<int>& nums) {
        // Mandatory sort to group duplicates together
        sort(nums.begin(), nums.end());
        vector<vector<int>> ans;
        vector<int> ds;
        findUniqueSubsets(0, nums, ds, ans);
        return ans;
    }
};

int main() {
    Solution sol;
    vector<int> nums = {1, 2, 2};
    auto result = sol.subsetsWithDup(nums);
    return 0;
}
```

### Complexity Analysis
- **Time Complexity**: **O(2^N * N)**
    - There are $2^N$ possible subsets.
    - Each subset is copied into the result vector, taking $O(N)$ time.
- **Space Complexity**: **O(2^N * k)** - To store results, plus $O(N)$ for the recursion stack depth.

## Edge Cases
- **All Elements Identical**: Correctly generates subsets of length 0 to N.
- **Empty Array**: Returns `[[]]`.

## Key Insights & Interview Tips
- **The "Power Set" vs "Subsets"**: These terms are often used interchangeably in coding problems.
- **Logic Sibling**: This problem's logic is identical to **Combination Sum II**. In Combination Sum, you check for `target == 0`. In Subsets, you collect everything.
- **Interview Tip**: If an interviewer asks you to do this *without* sorting, explain that you would need a hash set of vectors or frequencies, but it would be significantly less efficient.
