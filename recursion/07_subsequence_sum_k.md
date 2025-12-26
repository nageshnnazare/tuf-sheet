# 07. Subsequences with Sum K
**LeetCode**: [Problem Variation of 78 - Subsets]  
**Difficulty**: Medium

## Problem Statement
Given an array of integers, print all unique subsequences whose sum equals a given target value **K**. 

## Examples
### Example 1
Input: `arr = [1, 2, 1]`, `K = 2`  
Output: 
```
1 1 
2 
```
Explanation: The subsequences `{1, 1}` and `{2}` both sum up to the target value 2.

## Constraints
- `1 <= arr.length <= 20`
- `0 <= arr[i] <= 100` (Negative values might require a complete tree traversal without pruning)
- `1 <= K <= 1000`

## Visual Explanation
### ASCII Diagram: Filtering the Subsequence Tree
```
Input: [1, 2, 1], K = 2

          f(0, 0, [])
         /           \
     [1] f(1, 1)      [] f(1, 0)
     /      \         /      \
[1,2]f(2,3) [1]f(2,1) [2]f(2,2) []f(2,0)
  /          /          /          /
(X) Sum=4  [1,1]Sum=2 (MATCH!)  [2,1]Sum=3 (X)  [2]Sum=2 (MATCH!) ...
```

## Approach & Intuition
We build upon the "Pick and No Pick" pattern by maintaining a running `sum`. At each leaf node (base case), we check if the running sum matches target `K`. 

**Variations covered**:
- **Print All**: Standard void recursion.
- **Print Any One**: Boolean recursion (returns true immediately after first match).
- **Count All**: Integer recursion (returns 1 for match, 0 for no match, sums them up).

## Algorithm Steps
1. Define `solve(index, currentVector, currentSum)`.
2. **Base Case**: If `index == n`:
   - If `currentSum == K`, print/count/return.
   - Return.
3. **Pick**:
   - Add `arr[index]` to `currentVector`.
   - `currentSum += arr[index]`.
   - Recursively call `solve(index + 1)`.
4. **No Pick**:
   - `currentSum -= arr[index]`.
   - Remove `arr[index]` from `currentVector` (**Backtrack**).
   - Recursively call `solve(index + 1)`.

## Dry Run (Step-by-Step Execution)
Input: `arr = [1, 1]`, `K = 1`
1. `f(0, 0)`: Pick `1`. `sum=1`. Call `f(1, 1)`.
2. `f(1, 1)`: Pick `1`. `sum=2`. Call `f(2, 2)`.
3. `f(2, 2)`: **Base Case**. `2 != 1`. Return.
4. `f(1, 1)`: No Pick `1`. `sum=1`. Call `f(2, 1)`.
5. `f(2, 1)`: **Base Case**. `1 == 1`. **Print {1}**. Return.
6. `f(0, 0)`: No Pick `1`. `sum=0`. Call `f(1, 0)`.
7. `f(1, 0)`: Pick `1`. `sum=1`. Call `f(2, 1)`.
8. `f(2, 1)`: **Base Case**. `1 == 1`. **Print {1}**. Return.
9. `f(1, 0)`: No Pick `1`. `sum=0`. Call `f(2, 0)`.
10. `f(2, 0)`: **Base Case**. `0 != 1`. Return.

## C++ Solution
### Code with Detailed Comments
```cpp
#include <iostream>
#include <vector>
using namespace std;

class Solution {
public:
    /**
     * Prints all subsequences with sum K.
     */
    void printAll(int ind, vector<int> &ds, int s, int sum, int arr[], int n) {
        if (ind == n) {
            if (s == sum) { // Condition check at leaf
                for (auto it : ds) cout << it << " ";
                cout << endl;
            }
            return;
        }

        // Choice 1: PICK
        ds.push_back(arr[ind]);
        s += arr[ind];
        printAll(ind + 1, ds, s, sum, arr, n);

        // Choice 2: NO PICK (Backtrack)
        s -= arr[ind];
        ds.pop_back();
        printAll(ind + 1, ds, s, sum, arr, n);
    }

    /**
     * Prints any one subsequence and stops.
     */
    bool printAnyOne(int ind, vector<int> &ds, int s, int sum, int arr[], int n) {
        if (ind == n) {
            if (s == sum) {
                for (auto it : ds) cout << it << " ";
                cout << endl;
                return true; // Signal match found
            }
            return false;
        }

        ds.push_back(arr[ind]);
        if (printAnyOne(ind + 1, ds, s + arr[ind], sum, arr, n)) return true;
        
        ds.pop_back();
        if (printAnyOne(ind + 1, ds, s, sum, arr, n)) return true;
        
        return false; // Signal no match on this path
    }
};

int main() {
    int arr[] = {1, 2, 1};
    int n = 3, k = 2;
    vector<int> ds;
    Solution sol;
    
    cout << "All Subsequences:" << endl;
    sol.printAll(0, ds, 0, k, arr, n);
    
    return 0;
}
```

### Complexity Analysis
- **Time Complexity**: **O(2^N * N)**
    - 2^N states to visit. 
    - Printing takes O(N) time.
- **Space Complexity**: **O(N)**
    - Recursion stack depth is proportional to N.

## Edge Cases
- **Target Sum 0**: If 0 is target, an empty subsequence is a valid solution.
- **Large Array**: For N > 20, the complexity becomes prohibitive.
- **Positive Weights Only**: If array has only positive integers, we can prune: `if(currentSum > K) return`.

## Key Insights & Interview Tips
- **Boolean Return**: Use `bool` to stop early once the first solution is found. This is a powerful backtracking optimization.
- **Counting**: To count, instead of printing, let the left and right branches return counts and sum them: `return left + right`.
- **Interview Tip**: Emphasize manual state management (`pop_back` and `sum -= arr[i]`) if the interviewer asks about memory optimization (avoiding passing objects by value).
