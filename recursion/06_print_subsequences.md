# 06. Printing All Subsequences
**Source**: [Striver's Recursion Series - L6](https://youtu.be/AxNNVECce8c)  
**Difficulty**: Medium (Foundational for DP)

## Overview
A **subsequence** is a sequence derived from another sequence by deleting zero or more elements without changing the order of the remaining elements. This is different from a **subarray**, which must be contiguous.

Generated using the **"Pick and No Pick"** (or Take and Not Take) pattern, which is the foundation for solving most subsequence, combination, and backtracking problems.

---

## 1. Subsequence vs Subarray
For an array `[3, 1, 2]`:
- **Subarrays**: `[3]`, `[1]`, `[2]`, `[3, 1]`, `[1, 2]`, `[3, 1, 2]` (Must be contiguous)
- **Subsequences**: `[3]`, `[1]`, `[2]`, `[3, 1]`, `[1, 2]`, `[3, 2]`, `[3, 1, 2]`, `[]` (Can be non-contiguous, but order matters)

**Total Subsequences**: 2^N (where N is the size of the array).

---

## 2. The "Pick and No Pick" Pattern
For every element at index `i`, we have two choices:
1. **Pick (Take)**: Add the element to our current subsequence and move to `i + 1`.
2. **No Pick (Not Take)**: Don't add the element and move to `i + 1`.

### Visual Explanation: Recursion Tree
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

---

## 3. Algorithm Steps
1. Define a function `printS(index, currentVector)`.
2. **Base Case**: If `index == n`:
   - Print the `currentVector`.
   - Return.
3. **Pick Condition**:
   - Push `arr[index]` into `currentVector`.
   - Recursively call `printS(index + 1, currentVector)`.
4. **No Pick Condition**:
   - Pop `arr[index]` out of `currentVector` (Backtracking step).
   - Recursively call `printS(index + 1, currentVector)`.

---

## C++ Implementation
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
     * @param ds: Current subsequence being built (Data Structure)
     * @param arr: The original input array
     * @param n: Total number of elements
     */
    void printSubsequences(int ind, vector<int> &ds, int arr[], int n) {
        // Base Case: We have made a decision for every element
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
        ds.push_back(arr[ind]);
        printSubsequences(ind + 1, ds, arr, n);

        // Choice 2: NO PICK (Backtracking)
        // We must remove the element we picked to explore the "Not Take" path
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

## Complexity Analysis
- **Time Complexity**: **O(2^N * N)**
    - There are 2^N subsequences (leaf nodes in the tree).
    - For each leaf, we iterate through the list of size at most N to print it.
- **Space Complexity**: **O(N)**
    - The maximum depth of the recursion stack is N.
    - The size of the vector `ds` is at most N.

## Key Insights & Interview Tips
- **Empty Subsequence**: Always remember that an empty set `{}` is a valid subsequence.
- **Order of Picking**: If you "No Pick" first and then "Pick", the order of output will change but the subsequences will be the same.
- **Power Set**: This recursive approach is one way to generate the Power Set. Another way is using **Bit Manipulation**.
- **Important for DP**: This "Pick/No Pick" pattern is the foundation for the **0/1 Knapsack** problem and many other dynamic programming problems.
- **Interview Tip**: If an interviewer asks to generate all subsets or subsequences, this is the standard O(2^N) solution. Mention Bit Manipulation as an alternative for a non-recursive approach.
