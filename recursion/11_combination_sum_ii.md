# 11. Combination Sum II
**LeetCode**: [40 - Combination Sum II](https://leetcode.com/problems/combination-sum-ii/)  
**Difficulty**: Medium

## Problem Statement
Given a collection of candidate numbers (`candidates`) and a target number (`target`), find all unique combinations in `candidates` where the candidate numbers sum to `target`.

Each number in `candidates` may only be used **once** in the combination. The solution set must not contain duplicate combinations. Note that the input array can contain duplicates (e.g., `[1, 1, 2]`), and our goal is to use those '1's appropriately without generating the same result twice.

## Examples
### Example 1
Input: `candidates = [10, 1, 2, 7, 6, 1, 5]`, `target = 8`  
Output: `[[1, 1, 6], [1, 2, 5], [1, 7], [2, 6]]`  
Explanation: We found `[1, 1, 6]` because the input had two `1`s. We didn't produce `[1, 7]` twice.

## Constraints
- `1 <= candidates.length <= 100`
- `1 <= candidates[i] <= 50`
- `1 <= target <= 30`

## Visual Explanation
### ASCII Diagram: The "Branch-per-Unique-Element" Pattern
To avoid duplicates, we don't use "Pick/No Pick". Instead, we use a loop where each iteration starts a branch for a *unique* element choice at that position.

Sorted Input: `[1, 1, 2, 5]`
```
Level 0: (Start at index 0)
 |
 |-- Try 1 (index 0): [1] -> recurse to Level 1
 |
 |-- Try 1 (index 1): SKIP! (arr[1] == arr[0] and i > start_index)
 |
 |-- Try 2 (index 2): [2] -> recurse to Level 1
```

## Approach & Intuition
Standard backtracking often results in duplicate combinations when the input has duplicate numbers. Filtering with a `set` is slow ($O(2^N \cdot N \log S)$). 
The **Optimal Approach** is to generate only unique combinations during construction:
1. **Sort** the input: This groups duplicates together.
2. **Recursive Loop**: At each level of the recursion, we decide which element will fill the *next* position in our current combination.
3. **Horizontal Duplicate Skipping**: If we have a choice between two identical numbers *at the same position*, we only pick the first one and skip the others.
   - Condition: `if (i > ind && arr[i] == arr[i-1]) continue;`
4. **Vertical Inclusion**: We can still pick identical numbers at *different* positions (e.g., picking one '1' at level 0 and another '1' at level 1).

## Algorithm Steps
1. Sort `candidates`.
2. Define `solve(index, target, currentDS)`.
3. **Base Case**: If `target == 0`, add `currentDS` to results. Return.
4. **Iterative Search**:
   - Loop `i` from `index` to `N-1`:
     - **Skip Duplicates**: `if (i > index && arr[i] == arr[i-1]) continue;`.
     - **Pruning**: `if (arr[i] > target) break;` (No need to check later elements as they are larger).
     - **Choice**: Push `arr[i]` to `currentDS`.
     - **Recurse**: `solve(i + 1, target - arr[i], currentDS)`. (Move to `i+1` because each element is used once).
     - **Backtrack**: Pop from `currentDS`.

## Dry Run (Step-by-Step Execution)
Input: `[1, 1, 2]`, `target = 2`
1. **`solve(0, 2, [])`**:
   - `i = 0`: `arr[0]=1`. `target=1`. Call `solve(1, 1, [1])`.
     - **`solve(1, 1, [1])`**:
       - `i = 1`: `arr[1]=1`. `target=0`. Call `solve(2, 0, [1,1])`.
         - **`solve(2, 0)`**: Match found! Add `[1, 1]`.
       - `i = 2`: `arr[2]=2`. `2 > 1`. Break loop.
   - `i = 1`: `arr[1]=1`. Since `1 > 0` and `arr[1] == arr[0]`, **SKIP**. (Avoids another `[1, 1]` or `[1, ...]` path).
   - `i = 2`: `arr[2]=2`. `target=0`. Call `solve(3, 0, [2])`.
     - **`solve(3, 0)`**: Match found! Add `[2]`.

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
     * Recursive function to find unique combinations.
     * Uses a loop-based recursion to handle duplicates efficiently.
     */
    void findCombinations(int ind, int target, vector<int> &arr, vector<vector<int>> &ans, vector<int> &ds) {
        // Base Case: We have reached our target sum
        if (target == 0) {
            ans.push_back(ds);
            return;
        }

        // We try to fill the 'current' position in our DS with every possible unique candidate
        for (int i = ind; i < arr.size(); i++) {
            
            // DUPLICATE SKIP LOGIC:
            // i > ind ensures we don't skip the first occurrence of a number at this level.
            // arr[i] == arr[i-1] means we've already explored this number's possibilities 
            // for the current position in a previous iteration of this loop.
            if (i > ind && arr[i] == arr[i - 1]) continue;

            // EARLY PRUNING:
            // Since the array is sorted, if the current element is greater than the target,
            // all subsequent elements will also be too large.
            if (arr[i] > target) break;

            // 1. PLACE
            ds.push_back(arr[i]);
            
            // 2. RECURSE: Move to next index (i+1) since elements are used once
            findCombinations(i + 1, target - arr[i], arr, ans, ds);
            
            // 3. BACKTRACK: Remove the element to try others for the same position
            ds.pop_back();
        }
    }

    vector<vector<int>> combinationSum2(vector<int>& candidates, int target) {
        // Sorting is mandatory for both duplicate logic and pruning
        sort(candidates.begin(), candidates.end());
        vector<vector<int>> ans;
        vector<int> ds;
        findCombinations(0, target, candidates, ans, ds);
        return ans;
    }
};
```

### Complexity Analysis
- **Time Complexity**: **O(2^N * K)** - Where N is candidates length and K is combination length.
- **Space Complexity**: **O(K * X)** where X is number of combinations (to store results) + **O(N)** for the recursion stack.

## Edge Cases
- **Target exceeding total sum**: Handled by breaking the loop.
- **Large number of duplicates**: Handled by the skipping logic.

## Key Insights & Interview Tips
- **Index Management**: Notice the difference from Combination Sum I. Here we pass `i + 1` to move forward, whereas in I we passed `i` to allow repetition.
- **Horizontal vs Vertical**: This is the best way to explain duplicates. We skip duplicates "horizontally" (in the same loop) but allow them "vertically" (in the next recursion call).
- **Interview Tip**: Emphasize that sorting + backtracking is almost always better than recursion + `set` for uniqueness problems in interviews.
