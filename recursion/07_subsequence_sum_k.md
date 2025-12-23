# 07. Subsequences with Sum K
**Source**: [Striver's Recursion Series - L7](https://youtu.be/eQCSKWMAat8)  
**Difficulty**: Medium

## Overview
This problem is a variation of the standard subsequence generation. Instead of printing all subsequences, we only print those where the sum of elements equals a target value **K**. 

This introduces the concept of **Conditional Recursion**, where we maintain a running sum and evaluate it at the base case.

---

## 1. The Approach
We use the standard **"Pick and No Pick"** pattern but with an additional variable `s` to keep track of the current sum of elements in our subsequence.

### Visual Explanation: The Filter
```
Input: [1, 2, 1], K = 2

Subsequences:
[1, 2, 1] -> Sum = 4 (X)
[1, 2]    -> Sum = 3 (X)
[1, 1]    -> Sum = 2 (MATCH!)
[2, 1]    -> Sum = 3 (X)
[1]       -> Sum = 1 (X)
[2]       -> Sum = 2 (MATCH!)
[]        -> Sum = 0 (X)
```

---

## 2. Algorithm Steps
1. Define `solve(index, currentSubsequence, currentSum)`.
2. **Base Case**: If `index == n`:
   - If `currentSum == K`, print `currentSubsequence`.
   - Return.
3. **Pick**:
   - Add `arr[index]` to `currentSubsequence`.
   - Update `currentSum += arr[index]`.
   - Recursive call: `solve(index + 1, currentSubsequence, currentSum)`.
4. **No Pick (Backtrack)**:
   - Remove `arr[index]` from `currentSubsequence`.
   - Update `currentSum -= arr[index]`.
   - Recursive call: `solve(index + 1, currentSubsequence, currentSum)`.

---

## C++ Implementation
### Code with Detailed Comments

```cpp
#include <iostream>
#include <vector>
using namespace std;

class SubsequenceSum {
public:
    /**
     * Recursive function to print subsequences with sum K.
     * @param ind: Current index
     * @param ds: Current subsequence (data structure)
     * @param s: Running sum of elements in ds
     * @param sum: Target sum K
     * @param arr: Input array
     * @param n: Total elements
     */
    void printS(int ind, vector<int> &ds, int s, int sum, int arr[], int n) {
        // Base Case
        if (ind == n) {
            // Check if the running sum matches target K
            if (s == sum) {
                for (auto it : ds) cout << it << " ";
                cout << endl;
            }
            return;
        }

        // Choice 1: PICK
        ds.push_back(arr[ind]);
        s += arr[ind];
        printS(ind + 1, ds, s, sum, arr, n);

        // Choice 2: NO PICK (Backtracking)
        s -= arr[ind]; // Undo the sum change
        ds.pop_back(); // Undo the vector change
        printS(ind + 1, ds, s, sum, arr, n);
    }
};

int main() {
    int arr[] = {1, 2, 1};
    int n = 3;
    int k = 2;
    vector<int> ds;
    
    SubsequenceSum sol;
    sol.printS(0, ds, 0, k, arr, n);
    
    return 0;
}
```

## Complexity Analysis
- **Time Complexity**: **O(2^N * N)**
    - Still visits all 2^N possible subsequences.
    - Printing takes O(N) in the worst case.
- **Space Complexity**: **O(N)**
    - Recursion stack depth is N.

## Variations (Hidden Gems)
Striver also covers two important patterns in this lecture:

## Special Variant: Print ONLY ONE Subsequence
In many interviews, you are asked to print just any one valid subsequence and then stop. This is more efficient than generating all 2^N paths if a match is found early.

### Intuition: The Boolean Return Technique
To achieve this, we change the function return type from `void` to `bool`.
- If the base case matches, return `true`.
- If a recursive call returns `true`, immediately return `true` to the parent (avoiding further calls).
- If all paths fail, return `false`.

### Visual Dry Run (Stopping Early)
Input: `[1, 2, 1]`, `K=2`
```
f(0, 0)
 ↳ Pick 1: f(1, 1)
    ↳ Pick 2: f(2, 3) (Base Case: n=3, Sum=3 != 2) -> return false
    ↳ No Pick 2: f(2, 1)
       ↳ Pick 1: f(3, 2) (Base Case: n=3, Sum=2 == 2) -> return true!
    ↳ f(2, 1) received true -> return true!
 ↳ f(1, 1) received true -> return true!
f(0, 0) received true -> STOP. (Does not explore "No Pick 1")
```

### C++ Code for "Print Any One"
```cpp
bool printAnyOne(int ind, vector<int> &ds, int s, int sum, int arr[], int n) {
    // Base Case
    if (ind == n) {
        if (s == sum) {
            for (auto it : ds) cout << it << " ";
            cout << endl;
            return true; // Match found
        }
        return false; // No match on this path
    }

    // Pick
    ds.push_back(arr[ind]);
    if (printAnyOne(ind + 1, ds, s + arr[ind], sum, arr, n) == true) {
        return true; // Found in left branch, stop and return true
    }
    
    // No Pick (Backtrack)
    ds.pop_back();
    if (printAnyOne(ind + 1, ds, s, sum, arr, n) == true) {
        return true; // Found in right branch, stop and return true
    }
    
    return false; // Not found in either
}
```

### B. Count Subsequences with Sum K
To count instead of printing, return `1` for a match and `0` for no match.
```cpp
int countS(int ind, int s, int sum, int arr[], int n) {
    if (ind == n) {
        return (s == sum) ? 1 : 0;
    }
    // Sum returns from left and right branches
    int left = countS(ind + 1, s + arr[ind], sum, arr, n);
    int right = countS(ind + 1, s, sum, arr, n);
    return left + right;
}
```

## Key Insights & Interview Tips
- **Backtracking is Mandatory**: When passing `s` by value, the sum undoes itself. However, when using a vector (passed by reference), `pop_back` is essential to keep the state clean for the next branch.
- **Return Type Optimization**: Using `bool` to stop early or `int` to count are standard techniques to modify the output of a "Pick/No Pick" template.
- **Interview Tip**: Mention that if the array only contains **positive integers**, we can add a "Pruning" step: if `currentSum > K`, return immediately. This is a common optimization for search problems.
