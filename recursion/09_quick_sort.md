# 09. Quick Sort
**Algorithm**: [Quick Sort Algorithm](https://en.wikipedia.org/wiki/Quicksort)  
**Difficulty**: Medium (Essential)

## Overview
Quick Sort is a highly efficient, **Divide and Conquer** sorting algorithm. Unlike Merge Sort, it performs sorting **in-place** (requiring very little extra memory) by choosing a "Pivot" element and partitioning the array around it.

---

## 1. The Partition Logic
The heart of Quick Sort is the partitioning step. We pick a **Pivot** (usually the first element) and rearrange the array such that:
1. All elements **smaller than or equal to** the pivot are on the left.
2. All elements **greater than** the pivot are on the right.

### The Two-Pointer Partitioning (i and j)
1. **Pointers**: `i` starts from `low`, `j` starts from `high`.
2. **Move i**: Move `i` forward until you find an element **greater than** the pivot.
3. **Move j**: Move `j` backward until you find an element **smaller than or equal to** the pivot.
4. **Swap**: If `i < j`, swap `arr[i]` and `arr[j]`.
5. **Final Step**: Once `i` crosses `j`, the pivot belongs at index `j`. Swap `arr[low]` with `arr[j]`.

---

## 2. Visual Explanation: Single Partition Step
Input: `[4, 6, 2, 5, 7, 9, 1, 3]`, Pivot = `4`
```
[4, 6, 2, 5, 7, 9, 1, 3]
 ^  ^                 ^
 P  i                 j

1. i stops at 6 (> 4)
2. j stops at 3 (<= 4)
3. Swap i and j: [4, 3, 2, 5, 7, 9, 1, 6]

... repeat ...

Once i crosses j:
[4, 3, 2, 1, 7, 9, 5, 6]
 ^        ^  ^
 P        j  i

Final Swap (Pivot and j):
[1, 3, 2, 4, 7, 9, 5, 6]
          ^
          P is now in its sorted position!
```

---

## 3. Algorithm Steps
1. Define `quickSort(arr, low, high)`.
2. **Base Case**: If `low < high`:
3. **Partition**: `pIndex = partition(arr, low, high)`.
4. **Recursive Call**:
   - `quickSort(arr, low, pIndex - 1)`.
   - `quickSort(arr, pIndex + 1, high)`.

---

## C++ Implementation
### Code with Detailed Comments

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;

class QuickSort {
public:
    /**
     * Places the pivot in its correct sorted position and partitions the array.
     */
    int partition(vector<int> &arr, int low, int high) {
        int pivot = arr[low]; // Picking the first element as pivot
        int i = low;
        int j = high;

        while (i < j) {
            // Find the first element greater than the pivot from the left
            while (arr[i] <= pivot && i <= high - 1) {
                i++;
            }
            // Find the first element smaller than or equal to the pivot from the right
            while (arr[j] > pivot && j >= low + 1) {
                j--;
            }
            // If i and j haven't crossed each other, swap them
            if (i < j) swap(arr[i], arr[j]);
        }
        
        // Pivot belongs at index j where i and j crossed
        swap(arr[low], arr[j]);
        return j; // Return the partition index
    }

    /**
     * Recursive Quick Sort function.
     */
    void sort(vector<int> &arr, int low, int high) {
        if (low < high) {
            // Get the partition index
            int pIndex = partition(arr, low, high);
            
            // Recursively sort elements before and after partition
            sort(arr, low, pIndex - 1);
            sort(arr, pIndex + 1, high);
        }
    }
};

int main() {
    vector<int> arr = {4, 6, 2, 5, 7, 9, 1, 3};
    int n = arr.size();

    QuickSort qs;
    qs.sort(arr, 0, n - 1);

    cout << "Sorted Array: ";
    for (int x : arr) cout << x << " ";
    cout << endl;

    return 0;
}
```

## Complexity Analysis
- **Time Complexity**:
    - **Best/Average**: **O(N log N)** - Occurs when the pivot divides the array into two nearly equal halves.
    - **Worst Case**: **O(N^2)** - Occurs when the array is already sorted and we always pick the first/last element as pivot.
- **Space Complexity**: **O(1)** auxiliary space (excluding recursive stack).

## Key Insights & Interview Tips
- **In-Place**: Quick Sort is highly preferred because it doesn't require extra arrays like Merge Sort.
- **Internal vs External**: Quick Sort is an **Internal Sort** (works in memory).
- **Pivot Variants**: To avoid the O(N^2) worst case, you can pick a **Random Pivot** or use the **Median-of-Three** rule.
- **Unstable**: Quick Sort is **NOT Stable**; the relative order of equal elements may change during swaps.
- **Interview Tip**: If asked why Quick Sort is typically faster than Merge Sort despite the same average complexity, mention **Cache Friendliness** (it works on local elements) and smaller constant factors.
