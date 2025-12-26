# 10. Combination Sum
**LeetCode**: [39 - Combination Sum](https://leetcode.com/problems/combination-sum/)  
**Difficulty**: Medium

## Problem Statement
Given an array of **distinct** integers `candidates` and a `target` integer, return a list of all unique combinations where the chosen numbers sum to `target`. 

The same number may be chosen from `candidates` an **unlimited number of times**.

## Examples
### Example 1
Input: `candidates = [2, 3, 6, 7]`, `target = 7`  
Output: `[[2, 2, 3], [7]]`  
Explanation: 2 and 3 can be combined as (2+2+3)=7. 7 itself is a combination.

### Example 2
Input: `candidates = [2, 3, 5]`, `target = 8`  
Output: `[[2, 2, 2, 2], [2, 3, 3], [3, 5]]`

## Constraints
- `1 <= candidates.length <= 30`
- `2 <= candidates[i] <= 40`
- `All elements of candidates are distinct.`
- `1 <= target <= 40`

## Visual Explanation
### ASCII Diagram: Infinite Supply Pattern
```
                f(0, 7, [])
               /           \
        Pick 2: f(0, 5, [2])  No Pick 2: f(1, 7, [])
           /         \              /         \
    Pick 2: f(0, 3, [2,2]) ...    Pick 3: f(1, 4, [3]) ...
       /        \
Pick 2: f(0, 1, [2,2,2]) (Sum exceeds target? X)
```

## Approach & Intuition
This is a variation of the recursive "Pick and No Pick" pattern. Because an element can be reused:
- **Pick**: If `arr[index] <= target`, include the element and **stay at the same index**.
- **No Pick**: Move to the next index (`index + 1`) without including the element.

This "stay-at-index" logic enables the unlimited supply of each candidate.

## Algorithm Steps
1. Define `solve(index, target, currentDS)`.
2. **Base Case**: If `index == n`:
   - If `target == 0`, add `currentDS` to global `ans`.
   - Return.
3. **Recursive Decisions**:
   - **Pick**: If `candidates[index] <= target`:
     - Push `candidates[index]` into `currentDS`.
     - Recurse with `solve(index, target - candidates[index], currentDS)`.
     - Backtrack by popping from `currentDS`.
   - **No Pick**:
     - Recurse with `solve(index + 1, target, currentDS)`.

## Dry Run (Step-by-Step Execution)
Input: `candidates = [2, 3]`, `target = 4`
1. `f(0, 4, [])`: Pick 2. Target=2. Call `f(0, 2, [2])`.
2. `f(0, 2, [2])`: Pick 2. Target=0. Call `f(0, 0, [2, 2])`.
3. `f(0, 0, [2, 2])`: Since target is 0, we can't recursive pick (target - arr[0] is negative). Move to No Pick.
4. `f(1, 0, [2, 2])`: Recursive No Pick will eventually reach index=n with target=0. **Result += [2, 2]**.
5. Back at `f(0, 2, [2])`: No Pick 2. Call `f(1, 2, [2])`.
6. `f(1, 2, [2])`: Pick 3? No (3 > 2).
7. `f(1, 2, [2])`: No Pick 3. Call `f(2, 2, [2])`. Base Case: Target=2 != 0.
8. Back at `f(0, 4, [])`: No Pick 2. Call `f(1, 4, [])`.
9. `f(1, 4, [])`: Pick 3. Target=1. Call `f(1, 1, [3])`.
10. `f(1, 1, [3])`: Pick 3? No (3 > 1).
11. `f(1, 1, [3])`: No Pick 3. Base case reached index 2. Target=1. Return.
12. Final Result: `[[2, 2]]`. (Wait, 4 itself isn't in candidates, 2+2=4).

## C++ Solution
### Code with Detailed Comments
```cpp
#include <iostream>
#include <vector>
using namespace std;

class Solution {
public:
    void findCombinations(int ind, int target, vector<int> &arr, vector<vector<int>> &ans, vector<int> &ds) {
        // Base Case: All candidates considered
        if (ind == arr.size()) {
            if (target == 0) {
                ans.push_back(ds);
            }
            return;
        }

        // Logic: PICK current element
        // Requirement: Element must not exceed remaining target
        if (arr[ind] <= target) {
            ds.push_back(arr[ind]);
            // Stay at index 'ind' to allow multiple picks of the same element
            findCombinations(ind, target - arr[ind], arr, ans, ds);
            // Mandatory Backtrack
            ds.pop_back();
        }

        // Logic: MOVE TO NEXT element (No Pick)
        findCombinations(ind + 1, target, arr, ans, ds);
    }

    vector<vector<int>> combinationSum(vector<int>& candidates, int target) {
        vector<vector<int>> ans;
        vector<int> ds;
        findCombinations(0, target, candidates, ans, ds);
        return ans;
    }
};

int main() {
    Solution sol;
    vector<int> c = {2, 3, 6, 7};
    auto res = sol.combinationSum(c, 7);
    return 0;
}
```

### Complexity Analysis
- **Time Complexity**: **O(2^T * K)** - Where T is target value. In worst case (candidates=[1]), depth is target. K is average length of combination.
- **Space Complexity**: **O(T)** - Max depth of recursion stack (target/min_element).

## Edge Cases
- **No Solution**: If no combination sums to target, returns empty list.
- **Target Smaller than Minimum Candidate**: No Pick logic handles this correctly.

## Key Insights & Interview Tips
- **Unlimited Supply**: The only difference from basic subsequences is **not incrementing index on Pick**.
- **Pruning**: If the `candidates` array is sorted, we can break the pick logic early if `arr[ind] > target`.
- **Interview Tip**: Mention that this problem generates a tree whose height depends on the `target` value, not just the array size `N`.
