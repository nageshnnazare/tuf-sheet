# 09. Quick Sort
**LeetCode**: [912 - Sort an Array](https://leetcode.com/problems/sort-an-array/)  
**Difficulty**: Medium

## Problem Statement
Given an array of integers `nums`, sort the array in ascending order using the **Quick Sort** algorithm. Quick Sort is a Divide and Conquer algorithm that picks an element as a **pivot** and partitions the array around it such that elements smaller than the pivot are on the left and larger elements are on the right.

## Examples
### Example 1
Input: `nums = [4, 6, 2, 5, 7, 9, 1, 3]`  
Output: `[1, 2, 3, 4, 5, 6, 7, 9]`  
Explanation: After the first partition with pivot 4, the array might look like `[3, 1, 2, 4, 7, 9, 5, 6]`, where 4 is in its final sorted position.

## Constraints
- `1 <= nums.length <= 5 * 10^4`
- `-5 * 10^4 <= nums[i] <= 5 * 10^4`

## Visual Explanation
### ASCII Diagram: The Partitioning Mechanics
Partitioning is the heart of Quick Sort. It places the **pivot** at its correct sorted position in one pass.

```
Initial: [4, 6, 2, 5, 7, 9, 1, 3], low=0, high=7
Pivot = arr[low] = 4

1. i starts at low, j starts at high.
2. i moves RIGHT until arr[i] > pivot.
3. j moves LEFT until arr[j] <= pivot.
4. Swap arr[i] and arr[j] if i < j.
5. Repeat until i and j cross.
6. Swap pivot (arr[low]) with arr[j].

Result after j and i cross:
[3, 1, 2] | 4 | [5, 7, 9, 6]
 (<= Pivot) (P) (> Pivot)
```

## Approach & Intuition
Quick Sort is an **In-Place** Divide and Conquer algorithm. Unlike Merge Sort, it doesn't need an extra array for the sorting work.
- **The Pivot**: We pick an element (the pivot) and use it as a "fence".
- **The Partition**: We rearrange the array so all elements $\le$ pivot are to the left and all elements $>$ pivot are to the right. 
- **The Recursive Step**: After partitioning, the pivot is at its final sorted index `j`. We then recursively sort the left sub-array `[low...j-1]` and the right sub-array `[j+1...high]`.
- **Efficiency**: Quick Sort is often faster than Merge Sort in practice because it has better cache locality and smaller constant factors.

## Algorithm Steps
1. **`quickSort(low, high)`**:
   - If `low < high`:
     - `pIndex = partition(low, high)`
     - `quickSort(low, pIndex - 1)`
     - `quickSort(pIndex + 1, high)`

2. **`partition(low, high)`**:
   - `pivot = arr[low]`, `i = low`, `j = high`.
   - While `i < j`:
     - Increment `i` while `arr[i] <= pivot`.
     - Decrement `j` while `arr[j] > pivot`.
     - If `i < j`, `swap(arr[i], arr[j])`.
   - After the loop, `swap(arr[low], arr[j])`.
   - Return `j` (the final index of the pivot).

## Dry Run (Step-by-Step Execution)
Input: `[4, 6, 2, 5, 3]`, `low=0`, `high=4`
1. `pivot = 4`, `i = 0`, `j = 4`.
2. `i` moves to index 1 (`arr[1]=6 > 4`).
3. `j` moves to index 4 (`arr[4]=3 <= 4`).
4. `i < j` (1 < 4), swap: `[4, 3, 2, 5, 6]`.
5. Continue: `i` moves to index 3 (`arr[3]=5 > 4`).
6. `j` moves to index 2 (`arr[2]=2 <= 4`).
7. `i < j` is false (3 < 2). Break.
8. Swap `arr[0]` (4) with `arr[j]` (2).
9. Result: `[2, 3, 4, 5, 6]`. Pivot index = 2.

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
     * partition: Places the pivot at its correct sorted position.
     * All elements to the left are <= pivot, all to the right are > pivot.
     */
    int partition(vector<int> &arr, int low, int high) {
        int pivot = arr[low]; // Picking first element as pivot
        int i = low;
        int j = high;

        while (i < j) {
            // Find the first element greater than the pivot from the left
            while (arr[i] <= pivot && i <= high - 1) {
                i++;
            }
            // Find the first element smaller than or equal to pivot from the right
            while (arr[j] > pivot && j >= low + 1) {
                j--;
            }
            // If i and j haven't crossed, swap elements at i and j
            if (i < j) swap(arr[i], arr[j]);
        }
        
        // Final Step: Place the pivot in its correct sorted position (index j)
        // Everything to the left of j is <= pivot, and everything to the right is > pivot
        swap(arr[low], arr[j]);
        return j; // Return the pivot's final index
    }

    /**
     * quickSort: Recursive Divide and Conquer function.
     */
    void quickSort(vector<int> &arr, int low, int high) {
        if (low < high) {
            // 1. Partition the array and get the pivot's final position
            int pIndex = partition(arr, low, high);
            
            // 2. Recursively sort the left sub-array (elements < pivot)
            quickSort(arr, low, pIndex - 1);
            
            // 3. Recursively sort the right sub-array (elements > pivot)
            quickSort(arr, pIndex + 1, high);
        }
    }
};

int main() {
    vector<int> arr = {4, 6, 2, 5, 7, 9, 1, 3};
    Solution sol;
    sol.quickSort(arr, 0, arr.size() - 1);
    
    for (int x : arr) cout << x << " ";
    return 0;
}
```

### Complexity Analysis
- **Time Complexity**: 
    - **Average/Best**: **O(N log N)** - Occurs when the pivot splits the array into roughly equal halves at each step.
    - **Worst**: **O(N^2)** - Occurs when the input is already sorted or reverse sorted (if always picking the first/last element as pivot).
- **Space Complexity**: **O(1)** auxiliary space.
    - Unlike Merge Sort, Quick Sort sorts **in-place**.
    - The recursion stack takes $O(log N)$ space on average ($O(N)$ worst case).

## Edge Cases
- **Sorted Input**: Can lead to $O(N^2)$ if pivot choice is not randomized.
- **Identical Elements**: Handled correctly by the `<= pivot` and `> pivot` logic.
- **Small Arrays (size 0/1)**: Handled by the `low < high` condition.

## Key Insights & Interview Tips
- **Pivot Selection**: Mention that picking a **random pivot** or using the **median-of-three** method almost always prevents the $O(N^2)$ worst-case in real-world scenarios.
- **In-Place**: Emphasize that Quick Sort is an *internal* sorting algorithm that is very cache-friendly.
- **Stability**: Quick Sort is **Unstable**. The relative order of duplicate elements might change.
- **Interview Tip**: If asked to compare Merge Sort and Quick Sort, say: "Merge Sort is stable and better for linked lists or external sorting; Quick Sort is faster on average for arrays and uses less extra memory."
