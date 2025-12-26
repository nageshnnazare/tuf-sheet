# 08. Merge Sort
**LeetCode**: [912 - Sort an Array](https://leetcode.com/problems/sort-an-array/)  
**Difficulty**: Medium

## Problem Statement
Given an array of integers `nums`, sort the array in ascending order using the **Merge Sort** algorithm. Merge Sort is a classic Divide and Conquer algorithm that recursively splits the array into two halves, sorts them individually, and then merges the sorted halves back together.

## Examples
### Example 1
Input: `nums = [5, 2, 3, 1]`  
Output: `[1, 2, 3, 5]`  
Explanation: The array is halved into `[5, 2]` and `[3, 1]`. After recursive sorting, they become `[2, 5]` and `[1, 3]`. Merging them results in `[1, 2, 3, 5]`.

## Constraints
- `1 <= nums.length <= 5 * 10^4`
- `-5 * 10^4 <= nums[i] <= 5 * 10^4`

## Visual Explanation
### ASCII Diagram: The Merge Tree
Merge Sort works in two phases: **Dividing** (Top-Down) and **Merging** (Bottom-Up).

```
          [3, 1, 4, 2]         <-- Original
             /    \
        [3, 1]    [4, 2]       <-- Divide
        /   \      /   \
      [3]   [1]  [4]   [2]     <-- Base Cases (depth log N)
        \   /      \   /
        [1, 3]    [2, 4]       <-- Merge Phase 1
             \    /
          [1, 2, 3, 4]         <-- Final Sorted Result
```

## Approach & Intuition
Merge Sort is the canonical example of **Recursive Divide and Conquer**.
- **The Concept**: It is easier to sort two small sorted arrays than one large unsorted array.
- **Divide**: We find the midpoint `(low + high) / 2` and split the problem into two sub-problems.
- **Conquer**: We recursively sort the sub-problems.
- **Merge (The Workhorse)**: Once the sub-problems are sorted, we use two pointers to compare elements from each half and pick the smaller one to put into a temporary array.
- **Stability**: Because we use `arr[left] <= arr[right]` during the merge, we preserve the relative order of equal elements, making Merge Sort a **Stable** sorting algorithm.

## Algorithm Steps
1. **`mergeSort(low, high)`**:
   - If `low >= high`, the sub-array has 0 or 1 element (Base Case). Return.
   - Find `mid = (low + high) / 2`.
   - Recurse: `mergeSort(low, mid)`.
   - Recurse: `mergeSort(mid + 1, high)`.
   - **Merge**: Call `merge(low, mid, high)`.

2. **`merge(low, mid, high)`**:
   - Create a `temp` list.
   - Set `left = low`, `right = mid + 1`.
   - While `left <= mid` AND `right <= high`:
     - If `arr[left] <= arr[right]`, push `arr[left]` to `temp` and `left++`.
     - Else, push `arr[right]` to `temp` and `right++`.
   - Push any remaining elements from both halves into `temp`.
   - **Copy Back**: Transfer all elements from `temp` back to `arr[low...high]`.

## Dry Run (Step-by-Step Execution)
Merging `[1, 3]` and `[2, 4]` (Indices 0..3):
1. **Initial**: `left = 0 (val 1)`, `right = 2 (val 2)`. `temp = []`.
2. **Step 1**: `1 <= 2`. `temp = [1]`. `left = 1`.
3. **Step 2**: `3 > 2`. `temp = [1, 2]`. `right = 3`.
4. **Step 3**: `3 <= 4`. `temp = [1, 2, 3]`. `left = 2 (exceeds mid)`.
5. **Step 4**: Copy remaining from right side: `temp = [1, 2, 3, 4]`.
6. **Final**: Copy `temp` back to `arr[0...3]`.

## C++ Solution
### Code with Detailed Comments
```cpp
#include <iostream>
#include <vector>
using namespace std;

class Solution {
public:
    /**
     * merge: The helper function that combines two sorted sub-arrays.
     */
    void merge(vector<int> &arr, int low, int mid, int high) {
        vector<int> temp; // Temporary array for merging
        int left = low;      // Starting index of left half
        int right = mid + 1; // Starting index of right half

        // Compare elements from both halves and store the smaller one in temp
        while (left <= mid && right <= high) {
            if (arr[left] <= arr[right]) {
                temp.push_back(arr[left++]);
            } else {
                temp.push_back(arr[right++]);
            }
        }

        // If elements on the left half are still left
        while (left <= mid) {
            temp.push_back(arr[left++]);
        }

        // If elements on the right half are still left
        while (right <= high) {
            temp.push_back(arr[right++]);
        }

        // Transferring elements from temporary array back to original array
        for (int i = low; i <= high; i++) {
            arr[i] = temp[i - low];
        }
    }

    /**
     * mergeSort: Recursive function to divide the array.
     */
    void mergeSort(vector<int> &arr, int low, int high) {
        // Base case: 1 or 0 elements
        if (low >= high) return;

        int mid = (low + high) / 2;
        
        // Recursive calls for left and right halves
        mergeSort(arr, low, mid);
        mergeSort(arr, mid + 1, high);
        
        // Merge the sorted halves
        merge(arr, low, mid, high);
    }
};

int main() {
    vector<int> arr = {9, 4, 7, 6, 3, 1, 5};
    Solution sol;
    sol.mergeSort(arr, 0, arr.size() - 1);
    
    for (int it : arr) cout << it << " ";
    return 0;
}
```

### Complexity Analysis
- **Time Complexity**: **O(N log N)**
    - The dividing phase happens in $log N$ levels.
    - At each level, we perform $O(N)$ work to merge the elements.
    - This applies to Best, Average, and Worst cases.
- **Space Complexity**: **O(N)**
    - We use a temporary array of size $N$ during the merge process.

## Edge Cases
- **Array of size 1**: Handled by the base case `low >= high`.
- **Already Sorted Array**: Algorithm still runs in O(N log N).
- **Reverse Sorted Array**: Handled efficiently as well.

## Key Insights & Interview Tips
- **Stability**: Merge sort maintains the order of equal elements. This makes it suitable for complex data where items are sorted by one field and then another.
- **Linked Lists**: Merge sort is often the default choice for sorting Linked Lists because it doesn't require random access (unlike Quick Sort).
- **Space Constraint**: If an interviewer asks to sort with $O(1)$ space, Merge Sort is usually disqualified (unless you mention the complex "In-place Merge" variation).
- **Interview Tip**: Emphasize that Merge Sort is widely used in **External Sorting** (sorting data that is too huge for RAM) because it processes data sequentially.
