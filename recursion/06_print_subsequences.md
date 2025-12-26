# 06. Printing All Subsequences
**LeetCode**: [78 - Subsets](https://leetcode.com/problems/subsets/) (Generalized approach for strings/arrays)  
**Difficulty**: Medium

## Problem Statement
A **subsequence** is a sequence derived from another sequence by deleting zero or more elements without changing the order of the remaining elements. Given an array or string, print all its possible subsequences.

## Examples
### Example 1
Input: `arr = [3, 1, 2]`  
Output: 
```
3 1 2 
3 1 
3 2 
3 
1 2 
1 
2 
{}
```
Explanation: All possible combinations maintaining the relative order.

## Constraints
- `1 <= arr.length <= 20` (Typical recursion limit for 2^N)
- `-100 <= arr[i] <= 100`

## Visual Explanation
### ASCII Diagram: The "Pick and No Pick" Decision Tree
For input `{3, 1}`:
```
              f(0, [])
             /        \
        Pick 3        No Pick 3
         /              \
     f(1, [3])        f(1, [])
       /     \         /     \
    Pick 1 No Pick 1 Pick 1 No Pick 1
     /         \       /        \
f(2, [3,1]) f(2, [3]) f(2, [1]) f(2, [])
```

## Approach & Intuition
The solution uses the **"Pick and No Pick"** recursive pattern. For every element at the current index, we have two choices:
1. **Include** the element in our current subsequence and move to the next index.
2. **Exclude** the element and move to the next index.
This generates a binary tree of depth `N`, where each leaf represents a unique subsequence.

## Algorithm Steps
1. Define a function `solve(index, currentVector)`.
2. **Base Case**: If `index == n`, the current recursive path is complete. Print the `currentVector` and return.
3. **Pick (Take)**: 
   - Add `arr[index]` to `currentVector`.
   - Recurse for `index + 1`.
4. **No Pick (Not Take)**: 
   - Remove `arr[index]` from `currentVector` (Backtracking step).
   - Recurse for `index + 1`.

## Dry Run (Step-by-Step Execution)
Input: `[3, 1]`, `n=2`
1. `f(0, [])`: Pick `3`. `ds = [3]`. Call `f(1, [3])`.
2. `f(1, [3])`: Pick `1`. `ds = [3, 1]`. Call `f(2, [3, 1])`.
3. `f(2, [3, 1])`: **Base Case**. Output `[3, 1]`. Return.
4. Back at `f(1, [3])`: No Pick `1`. `ds = [3]`. Call `f(2, [3])`.
5. `f(2, [3])`: **Base Case**. Output `[3]`. Return.
6. Back at `f(0, [3])`: Backtrack `3`. `ds = []`. Call `f(0, [])` -> No Pick `3`.
7. `f(0, [])`: No Pick `3`. `ds = []`. Call `f(1, [])`.
8. `f(1, [])`: Pick `1`. `ds = [1]`. Call `f(2, [1])`.
9. `f(2, [1])`: **Base Case**. Output `[1]`. Return.
10. `f(1, [])`: No Pick `1`. `ds = []`. Call `f(2, [])`.
11. `f(2, [])`: **Base Case**. Output `{}`. Return.

## C++ Solution
### Code with Detailed Comments
```cpp
#include <iostream>
#include <vector>
using namespace std;

class Subsequences {
public:
    /**
     * Recursive function to generate all subsequences.
     * @param ind: Current index in the original array
     * @param ds: Current subsequence being built
     * @param arr: The original input array
     * @param n: Total number of elements
     */
    void printSubsequences(int ind, vector<int> &ds, int arr[], int n) {
        // Base Case: Decisions made for all elements
        if (ind == n) {
            if (ds.size() == 0) {
                cout << "{}" << endl;
            } else {
                for (auto it : ds) cout << it << " ";
                cout << endl;
            }
            return;
        }

        // Choice 1: PICK the current element
        // Add current element to our subsequence and move to next
        ds.push_back(arr[ind]);
        printSubsequences(ind + 1, ds, arr, n);

        // Choice 2: NO PICK (Backtracking)
        // Remove the element to explore the branch where we don't include it
        ds.pop_back();
        printSubsequences(ind + 1, ds, arr, n);
    }
};

int main() {
    int arr[] = {3, 1, 2};
    int n = 3;
    vector<int> ds;
    
    Subsequences sol;
    sol.printSubsequences(0, ds, arr, n);
    
    return 0;
}
```

### Complexity Analysis
- **Time Complexity**: **O(2^N * N)**
    - 2^N total subsequences (nodes in the tree).
    - Printing each subsequence takes O(N) time.
- **Space Complexity**: **O(N)**
    - Maximum depth of recursion stack is N.
    - Temporary vector `ds` stores at most N elements.

## Edge Cases
- **Empty Array**: The algorithm should return/print `{}`.
- **Single Element**: Outputs the element and `{}`.
- **Duplicates in Array**: This approach will generate all mathematical subsequences; if "unique" sequences are needed, additional logic (sorting + skip) is required.

## Key Insights & Interview Tips
- **Subsequence vs Subarray**: Subsequences don't need to be contiguous, but they must maintain relative order.
- **Power Set**: The total number of subsequences is always 2^N.
- **Foundation**: This is the basis for solving 0/1 Knapsack, Combination Sum, and Subset Sum problems.
