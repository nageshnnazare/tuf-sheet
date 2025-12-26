# 12. Subset Sum I
**Problem**: [Subset Sums](https://www.geeksforgeeks.org/problems/subset-sums2234/1)  
**Difficulty**: Easy

## Problem Statement
Given a list of `N` integers, return the sums of all possible subsets in increasing order.

## Examples
### Example 1
Input: `arr = [2, 3]`  
Output: `[0, 2, 3, 5]`  
Explanation: Possible subsets are `{}`, `{2}`, `{3}`, `{2, 3}`. Their sums are 0, 2, 3, 5 respectively.

## Constraints
- `1 <= N <= 15`
- `0 <= arr[i] <= 10^4`

## Visual Explanation
### ASCII Diagram: Power Set Sum Tree
```
              f(0, 0)
             /       \
      Pick 2: f(1, 2)  No Pick 2: f(1, 0)
         /      \         /       \
  Pick 3:f(2,5) No Pick 3:f(2,2) Pick 3:f(2,3) No Pick 3:f(2,0)
```

## Approach & Intuition
Generating all possible subset sums is equivalent to calculating the sum for every element in the power set. Since each element has two choices (included or not included), we use the **"Pick and No Pick"** recursive pattern:
- **State**: `f(index, currentSum)`
- **Base Case**: When `index == N`, add the `currentSum` to a global/result list.

## Algorithm Steps
1. Initialize an empty list `ans`.
2. Define `calculateSums(index, currentSum, arr)`.
3. **Base Case**: If `index == N`, push `currentSum` to `ans` and return.
4. **Recursive Step**:
   - **Pick**: `calculateSums(index + 1, currentSum + arr[index], arr)`.
   - **No Pick**: `calculateSums(index + 1, currentSum, arr)`.
5. **Final Step**: After recursion completes, **sort** `ans` in ascending order.

## Dry Run (Step-by-Step Execution)
Input: `arr = [3, 1]`, `N = 2`
1. `f(0, 0)`:
   - Pick `arr[0]=3`: Call `f(1, 3)`.
     - `f(1, 3)` -> Pick `arr[1]=1`: Call `f(2, 4)`. **Sum 4 stored**.
     - `f(1, 3)` -> No Pick `1`: Call `f(2, 3)`. **Sum 3 stored**.
   - No Pick `3`: Call `f(1, 0)`.
     - `f(1, 0)` -> Pick `1`: Call `f(2, 1)`. **Sum 1 stored**.
     - `f(1, 0)` -> No Pick `1`: Call `f(2, 0)`. **Sum 0 stored**.
2. Sorted Result: `[0, 1, 3, 4]`.

## C++ Solution
### Code with Detailed Comments
```cpp
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;

class Solution {
public:
    void findSubsetSums(int ind, int sum, vector<int>& arr, int n, vector<int>& sumSubset) {
        // Base case: All elements processed
        if (ind == n) {
            sumSubset.push_back(sum);
            return;
        }

        // Choice 1: PICK the element
        // Add current element value to running sum
        findSubsetSums(ind + 1, sum + arr[ind], arr, n, sumSubset);

        // Choice 2: NO PICK the element
        // Carry forward the same sum without adding current element
        findSubsetSums(ind + 1, sum, arr, n, sumSubset);
    }

    vector<int> subsetSums(vector<int> arr, int n) {
        vector<int> sumSubset;
        findSubsetSums(0, 0, arr, n, sumSubset);
        // Sort as required by the problem statement
        sort(sumSubset.begin(), sumSubset.end());
        return sumSubset;
    }
};

int main() {
    Solution sol;
    vector<int> arr = {4, 5};
    vector<int> res = sol.subsetSums(arr, 2);
    return 0;
}
```

### Complexity Analysis
- **Time Complexity**: **O(2^N + 2^N log(2^N))** - 2^N calls for subset generation, followed by sorting the results.
- **Space Complexity**: **O(2^N)** - To store all possible subset sums in the result list.

## Edge Cases
- **Empty Array**: Result: `[0]`.
- **Large Values**: Sum can exceed `int` if constraints are larger (not in this case).

## Key Insights & Interview Tips
- **Number of Subsets**: Always 2^N.
- **Bit Manipulation**: Mention that subsets can also be generated iteratively using bits (0 to 2^N - 1).
- **Interview Tip**: Emphasize that the order of "Pick" and "No Pick" doesn't change the set of sums, but changes the generation order.
