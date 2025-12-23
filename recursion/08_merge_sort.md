# 08. Merge Sort
**Algorithm**: [Merge Sort Algorithm](https://en.wikipedia.org/wiki/Merge_sort)  
**Difficulty**: Medium (Foundational)

## Overview
Merge Sort is a **Divide and Conquer** algorithm. It works by recursively dividing an array into two halves, sorting them, and then merging the sorted halves back together.

---

## 1. The Divide & Conquer Strategy
1. **Divide**: Find the middle of the array to divide it into two halves.
2. **Conquer**: Recursively sort both halves using Merge Sort.
3. **Combine (Merge)**: Merge the two sorted halves into a single sorted array.

### Visual Explanation: The Recursion Tree
For input: `[3, 1, 2, 4, 1, 5, 2, 6]`
```
          [3, 1, 2, 4, 1, 5, 2, 6]
              /            \
        [3, 1, 2, 4]      [1, 5, 2, 6]
          /    \            /    \
      [3, 1]  [2, 4]    [1, 5]  [2, 6]
       /  \    /  \      /  \    /  \
     [3] [1] [2] [4]    [1] [5] [2] [6]
      \  /    \  /      \  /    \  /
      [1, 3]  [2, 4]    [1, 5]  [2, 6]
          \    /            \    /
        [1, 2, 3, 4]      [1, 2, 5, 6]
              \            /
          [1, 1, 2, 2, 3, 4, 5, 6]
```

---

## 2. The Merge Logic
The "Merge" step is where the sorting actually happens. We use two pointers to compare elements from both sorted halves and pick the smaller one to place into a temporary array.

### Step-by-Step Merge Example
Sorted Left: `[1, 3]`, Sorted Right: `[2, 4]`
1. Compare `1` and `2`: Pick `1`. Temp: `[1]`
2. Compare `3` and `2`: Pick `2`. Temp: `[1, 2]`
3. Compare `3` and `4`: Pick `3`. Temp: `[1, 2, 3]`
4. Remaining in Right: `4`. Temp: `[1, 2, 3, 4]`

---

## 3. Algorithm Steps
1. Define `mergeSort(arr, low, high)`.
2. **Base Case**: If `low >= high`, return (the array has 1 or 0 elements).
3. **Recursive Split**:
   - `mid = (low + high) / 2`.
   - `mergeSort(arr, low, mid)`.
   - `mergeSort(arr, mid + 1, high)`.
4. **Merge**: Call `merge(arr, low, mid, high)` to combine the sorted parts.

---

## C++ Implementation
### Code with Detailed Comments

```cpp
#include <iostream>
#include <vector>
using namespace std;

class MergeSort {
public:
    /**
     * Merges two sorted sub-arrays into a single sorted sub-array.
     * @param arr: The original array
     * @param low: Start index of the left half
     * @param mid: End index of the left half
     * @param high: End index of the right half
     */
    void merge(vector<int> &arr, int low, int mid, int high) {
        vector<int> temp; // Temporary array to store merged elements
        int left = low;      // Starting index of left half
        int right = mid + 1; // Starting index of right half

        // 1. Compare elements from both halves and pick the smaller one
        while (left <= mid && right <= high) {
            if (arr[left] <= arr[right]) {
                temp.push_back(arr[left]);
                left++;
            } else {
                temp.push_back(arr[right]);
                right++;
            }
        }

        // 2. If elements on the left half are still left
        while (left <= mid) {
            temp.push_back(arr[left]);
            left++;
        }

        // 3. If elements on the right half are still left
        while (right <= high) {
            temp.push_back(arr[right]);
            right++;
        }

        // 4. Transfer all elements from temp back to the original array
        for (int i = low; i <= high; i++) {
            arr[i] = temp[i - low];
        }
    }

    /**
     * Recursively divides the array and triggers the merge logic.
     */
    void sort(vector<int> &arr, int low, int high) {
        // Base case: 1 or 0 elements
        if (low >= high) return;

        int mid = (low + high) / 2;

        // Recursively sort the left half
        sort(arr, low, mid);
        // Recursively sort the right half
        sort(arr, mid + 1, high);

        // Merge the two sorted halves
        merge(arr, low, mid, high);
    }
};

int main() {
    vector<int> arr = {4, 2, 1, 6, 7};
    int n = arr.size();

    MergeSort ms;
    ms.sort(arr, 0, n - 1);

    cout << "Sorted Array: ";
    for (int x : arr) cout << x << " ";
    cout << endl;

    return 0;
}
```

## Complexity Analysis
- **Time Complexity**: **O(N log N)**
    - At each level, we divide the array in half (log N levels).
    - At each level, the total work done merging is N.
- **Space Complexity**: **O(N)**
    - We use a temporary vector to store elements during the merge process.

## Key Insights & Interview Tips
- **Stability**: Merge Sort is a **Stable** sorting algorithm (it preserves the relative order of duplicate elements).
- **Space Trade-off**: Unlike Quick Sort, Merge Sort is NOT an in-place sorting algorithm because it requires O(N) extra space.
- **Linked Lists**: Merge Sort is often the preferred choice for sorting linked lists because it doesn't require random access.
- **Interview Tip**: Always mention that while Merge Sort is consistent (O(N log N) even in worst case), its O(N) space complexity is its main drawback compared to Heap Sort or Quick Sort.
