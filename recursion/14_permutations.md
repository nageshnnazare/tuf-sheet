# 14. Print All Permutations
**LeetCode**: [46 - Permutations](https://leetcode.com/problems/permutations/)  
**Difficulty**: Medium

## Problem Statement
Given an array `nums` of distinct integers, return all the possible **permutations**. You can return the answer in any order. A permutation is a rearrangement of elements such that every element is used exactly once in every configuration.

## Examples
### Example 1
Input: `nums = [1, 2, 3]`  
Output: `[[1, 2, 3], [1, 3, 2], [2, 1, 3], [2, 3, 1], [3, 1, 2], [3, 2, 1]]`  
Explanation: For $N=3$, there are $3! = 6$ possible permutations.

### Example 2
Input: `nums = [0, 1]`  
Output: `[[0, 1], [1, 0]]`

## Constraints
- `1 <= nums.length <= 6`
- `-10 <= nums[i] <= 10`
- `All integers in nums are unique.`

## Visual Explanation
### ASCII Diagram: Swapping and Fixing
We solve this by **fixing one element at a time** at each index.

```
Input: [A, B, C]

Level 0: Fix index 0
- Swap(0,0) -> [A] [B, C] -> recurse to Index 1
- Swap(0,1) -> [B] [A, C] -> recurse to Index 1
- Swap(0,2) -> [C] [B, A] -> recurse to Index 1

Inside Level 0 - Branch 2 (Swap 0,1):
Level 1: Fix index 1 in [B, A, C]
- Swap(1,1) -> [B, A] [C] -> Index 2: [B, A, C] (Match!)
- Swap(1,2) -> [B, C] [A] -> Index 2: [B, C, A] (Match!)
```

## Approach & Intuition
There are two primary backtracking patterns for permutations:

1. **Approach 1: Frequency Array (Intuitive)**
   - Maintain a global "picked" array. 
   - In each recursive step, loop through all elements. If an element isn't picked, pick it, mark it, and move to the next position.
   - **Insight**: This follows a "fill in the blanks" intuition. 

2. **Approach 2: Swapping (Space Optimized)**
   - Instead of tracking "which are left?", we just swap the current index with every subsequent index.
   - The elements to the left of `index` are "fixed", and we permute the elements to the right.
   - **Backtracking**: After recursing, we swap back to restore the original state for the next parallel choice at that same level.

## Algorithm Steps (Swapping Approach)
1. Define `solve(index, nums)`.
2. **Base Case**: If `index == nums.length`, we've fixed every position. Add the current state of `nums` to our answer.
3. **Iterative Step**:
   - For `i` from `index` to `nums.size() - 1`:
     - **Decision**: Swap `arr[index]` with `arr[i]`. This "fixes" `arr[i]` into the current `index` position.
     - **Explore**: Call `solve(index + 1, nums)`.
     - **Backtrack**: Swap `arr[index]` with `arr[i]` again. This resets the array to what it was before this specific branch, allowing the next iteration of the loop to try a different element at the same `index`.

## Dry Run (Step-by-Step Execution)
Trace for `[1, 2, 3]`:
1. `f(0)`:
   - `i = 0`: Swap(0,0) -> `[1, 2, 3]`. Call `f(1)`.
     - `f(1)`: `i = 1`: Swap(1,1) -> `[1, 2, 3]`. Base case `f(2)` returns `[1,2,3]`.
     - `f(1)`: `i = 2`: Swap(1,2) -> `[1, 3, 2]`. Base case `f(2)` returns `[1,3,2]`.
   - `i = 1`: Swap(0,1) -> `[2, 1, 3]`. Call `f(1)`.
     - `f(1)`: `i = 1`: Swap(1,1) -> `[2, 1, 3]`. Base case returns `[2,1,3]`.
     - `f(1)`: `i = 2`: Swap(1,2) -> `[2, 3, 1]`. Base case returns `[2,3,1]`.
   - `i = 2`: Swap(0,2) -> `[3, 2, 1]`. Call `f(1)`.
     - ... (yields `[3,2,1]` and `[3,1,2]`)

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
     * Swapping-based Recursion: Permutes 'nums' in-place.
     * @param index: The current position in the array we are fixing.
     * @param nums: Input array (passed by reference to modify in-place).
     * @param ans: Global result accumulator.
     */
    void findPermutations(int index, vector<int> &nums, vector<vector<int>> &ans) {
        // Base Case: Every position has been assigned an element
        if (index == nums.size()) {
            ans.push_back(nums);
            return;
        }

        for (int i = index; i < nums.size(); i++) {
            // STEP 1: FIX the element at index 'i' to current position 'index'
            swap(nums[index], nums[i]);
            
            // STEP 2: RECURSE to handle the next indices (index + 1 onwards)
            findPermutations(index + 1, nums, ans);
            
            // STEP 3: BACKTRACK - Restore the original positions
            // This is critical so that the next iteration of the loop 
            // starts with the same initial array state.
            swap(nums[index], nums[i]);
        }
    }

    vector<vector<int>> permute(vector<int>& nums) {
        vector<vector<int>> ans;
        findPermutations(0, nums, ans);
        return ans;
    }
};

int main() {
    Solution sol;
    vector<int> nums = {1, 2, 3};
    auto result = sol.permute(nums);
    
    // Output check
    for(auto p : result) {
        cout << "[ ";
        for(int x : p) cout << x << " ";
        cout << "]" << endl;
    }
    return 0;
}
```

### Complexity Analysis
- **Time Complexity**: **O(N! * N)**
    - There are exactly $N!$ permutations.
    - Each final configuration takes $O(N)$ to copy into the `ans` vector.
- **Space Complexity**: **O(1)** auxiliary space.
    - We don't use a frequency map or any extra list to store the permutation (besides the input `nums`).
    - The recursion stack takes $O(N)$ space.

## Edge Cases
- **Duplicate Elements**: This algorithm will treat identical numbers as distinct (e.g., `[1, 1, 2]` will have some identical permutations). To fix this, you would need to use a frequency map or sort the input and check `nums[i] == nums[i-1]`.
- **N = 1**: Correctly returns `[[nums[0]]]`.

## Key Insights & Interview Tips
- **Stability**: Notice that the Swap approach destroys the relative order of elements during the process.
- **Lexicographical Order**: If the interviewer asks for permutations in a specific order, you should use the **Frequency Array Approach** and sort the input first.
- **Small Constraints**: Always notice when $N$ is small (like 6-10). This is a strong hint that an $O(N!)$ backtracking or bitmask DP solution is expected.
- **Interview Tip**: Mention that `std::next_permutation` in C++ provides a built-in way to get the next lexicographical permutation, but be ready to implement the recursive version manually to prove your understanding of backtracking.
