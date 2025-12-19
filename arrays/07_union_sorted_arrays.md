# 7. Union of Two Sorted Arrays

**LeetCode**: N/A (Similar to [#88 - Merge Sorted Array](https://leetcode.com/problems/merge-sorted-array/))  
**Difficulty**: Easy

## Problem Statement

Given two sorted arrays, find their **union**. The union should contain all unique elements from both arrays in sorted order.

**Union**: A ∪ B contains all elements that are in A or B or both, without duplicates.

## Examples

### Example 1
```
Input: arr1 = [1, 2, 3, 4, 5], arr2 = [2, 3, 5, 6]
Output: [1, 2, 3, 4, 5, 6]
Explanation: All unique elements from both arrays.
```

### Example 2
```
Input: arr1 = [1, 3, 5], arr2 = [2, 4, 6]
Output: [1, 2, 3, 4, 5, 6]
Explanation: No common elements, all are included.
```

### Example 3
```
Input: arr1 = [1, 1, 2], arr2 = [2, 3, 3]
Output: [1, 2, 3]
Explanation: Duplicates within and across arrays are removed.
```

## Constraints
- 0 ≤ arr1.length, arr2.length ≤ 10^5
- -10^9 ≤ arr1[i], arr2[i] ≤ 10^9
- Both arrays are sorted in non-decreasing order

## Visual Explanation

### ASCII Diagram - Two Pointer Merge

```
arr1: [1, 2, 3, 4, 5]
       ↑
      i=0

arr2: [2, 3, 5, 6]
       ↑
      j=0

union: []

Step 1: Compare arr1[0]=1 vs arr2[0]=2
  1 < 2 → Take 1 from arr1
  union: [1], i=1, j=0

Step 2: Compare arr1[1]=2 vs arr2[0]=2
  2 == 2 → Take 2, skip both (avoid duplicate)
  union: [1, 2], i=2, j=1

Step 3: Compare arr1[2]=3 vs arr2[1]=3
  3 == 3 → Take 3, skip both
  union: [1, 2, 3], i=3, j=2

Step 4: Compare arr1[3]=4 vs arr2[2]=5
  4 < 5 → Take 4 from arr1
  union: [1, 2, 3, 4], i=4, j=2

Step 5: Compare arr1[4]=5 vs arr2[2]=5
  5 == 5 → Take 5, skip both
  union: [1, 2, 3, 4, 5], i=5, j=3

Step 6: arr1 exhausted, take remaining from arr2
  arr2[3]=6
  union: [1, 2, 3, 4, 5, 6]

Final: [1, 2, 3, 4, 5, 6]

Visualization:
  arr1: [1, 2, 3, 4, 5] ───┐
  arr2: [2, 3, 5, 6]    ───┤
                           ↓
        Merge (take smaller, skip duplicates)
                           ↓
  union: [1, 2, 3, 4, 5, 6]
```

## Approach & Intuition

Since both arrays are **already sorted**, we can use **two-pointer technique** to merge them efficiently:

1. Use pointer `i` for arr1 and `j` for arr2
2. Compare elements at both pointers:
   - If arr1[i] < arr2[j]: Add arr1[i] to result, increment i
   - If arr1[i] > arr2[j]: Add arr2[j] to result, increment j
   - If arr1[i] == arr2[j]: Add element once, increment both i and j
3. Add remaining elements from whichever array is not exhausted
4. Skip consecutive duplicates within same array

**Why this works**:
- Sorted arrays allow linear merge
- Smaller element always goes first (maintains sorted order)
- Equal elements taken once (union property)

## Algorithm Steps

1. Initialize empty result array and pointers i=0, j=0
2. While both i < n1 and j < n2:
   - If arr1[i] < arr2[j]:
     - Add arr1[i] to result (if not duplicate of last added)
     - Increment i
   - Else if arr1[i] > arr2[j]:
     - Add arr2[j] to result (if not duplicate)
     - Increment j
   - Else (arr1[i] == arr2[j]):
     - Add element to result (if not duplicate)
     - Increment both i and j
3. Add remaining elements from arr1 (if any)
4. Add remaining elements from arr2 (if any)
5. Return result

## Dry Run (Step-by-Step Execution)

Arrays: `arr1 = [1, 2, 3, 4, 5]`, `arr2 = [2, 3, 5, 6]`

```
Initial State:
arr1 = [1, 2, 3, 4, 5], n1 = 5
arr2 = [2, 3, 5, 6], n2 = 4
i = 0, j = 0
union = []

Iteration 1:
  arr1[0]=1, arr2[0]=2
  1 < 2 → Add 1 to union
  union = [1], i = 1, j = 0

Iteration 2:
  arr1[1]=2, arr2[0]=2
  2 == 2 → Add 2 to union, increment both
  union = [1, 2], i = 2, j = 1

Iteration 3:
  arr1[2]=3, arr2[1]=3
  3 == 3 → Add 3 to union, increment both
  union = [1, 2, 3], i = 3, j = 2

Iteration 4:
  arr1[3]=4, arr2[2]=5
  4 < 5 → Add 4 to union
  union = [1, 2, 3, 4], i = 4, j = 2

Iteration 5:
  arr1[4]=5, arr2[2]=5
  5 == 5 → Add 5 to union, increment both
  union = [1, 2, 3, 4, 5], i = 5, j = 3

Check remaining:
  i = 5 (exhausted arr1)
  j = 3 < 4 → Add remaining from arr2
  arr2[3] = 6
  union = [1, 2, 3, 4, 5, 6]

Final Result: [1, 2, 3, 4, 5, 6] ✓
```

## C++ Solution

### Code with Detailed Comments

```cpp
#include <iostream>
#include <vector>
using namespace std;

class Solution {
public:
    // Function to find union of two sorted arrays
    vector<int> findUnion(vector<int>& arr1, vector<int>& arr2) {
        int n1 = arr1.size();
        int n2 = arr2.size();
        
        // Result vector to store union
        vector<int> unionArr;
        
        // Two pointers for both arrays
        int i = 0, j = 0;
        
        // Merge while both arrays have elements
        while (i < n1 && j < n2) {
            // Case 1: arr1[i] is smaller - take it
            if (arr1[i] < arr2[j]) {
                // Add to union only if it's different from last added element
                // This skips duplicates within arr1
                if (unionArr.empty() || unionArr.back() != arr1[i]) {
                    unionArr.push_back(arr1[i]);
                }
                i++; // Move to next element in arr1
            }
            // Case 2: arr2[j] is smaller - take it
            else if (arr1[i] > arr2[j]) {
                // Add to union only if not duplicate
                if (unionArr.empty() || unionArr.back() != arr2[j]) {
                    unionArr.push_back(arr2[j]);
                }
                j++; // Move to next element in arr2
            }
            // Case 3: Both elements are equal - take one
            else {
                // Add to union only if not duplicate
                if (unionArr.empty() || unionArr.back() != arr1[i]) {
                    unionArr.push_back(arr1[i]);
                }
                // Increment both pointers to skip the duplicate
                i++;
                j++;
            }
        }
        
        // Add remaining elements from arr1 (if any)
        while (i < n1) {
            // Check for duplicates before adding
            if (unionArr.empty() || unionArr.back() != arr1[i]) {
                unionArr.push_back(arr1[i]);
            }
            i++;
        }
        
        // Add remaining elements from arr2 (if any)
        while (j < n2) {
            // Check for duplicates before adding
            if (unionArr.empty() || unionArr.back() != arr2[j]) {
                unionArr.push_back(arr2[j]);
            }
            j++;
        }
        
        return unionArr;
    }
};

// Driver code
int main() {
    Solution sol;
    
    // Test Case 1
    vector<int> arr1 = {1, 2, 3, 4, 5};
    vector<int> arr2 = {2, 3, 5, 6};
    vector<int> result = sol.findUnion(arr1, arr2);
    
    cout << "Array 1: [1, 2, 3, 4, 5]" << endl;
    cout << "Array 2: [2, 3, 5, 6]" << endl;
    cout << "Union: [";
    for (int i = 0; i < result.size(); i++) {
        cout << result[i];
        if (i < result.size() - 1) cout << ", ";
    }
    cout << "]" << endl << endl;
    
    // Test Case 2: No common elements
    vector<int> arr3 = {1, 3, 5};
    vector<int> arr4 = {2, 4, 6};
    result = sol.findUnion(arr3, arr4);
    
    cout << "Array 1: [1, 3, 5]" << endl;
    cout << "Array 2: [2, 4, 6]" << endl;
    cout << "Union: [";
    for (int i = 0; i < result.size(); i++) {
        cout << result[i];
        if (i < result.size() - 1) cout << ", ";
    }
    cout << "]" << endl << endl;
    
    // Test Case 3: With duplicates
    vector<int> arr5 = {1, 1, 2};
    vector<int> arr6 = {2, 3, 3};
    result = sol.findUnion(arr5, arr6);
    
    cout << "Array 1: [1, 1, 2]" << endl;
    cout << "Array 2: [2, 3, 3]" << endl;
    cout << "Union: [";
    for (int i = 0; i < result.size(); i++) {
        cout << result[i];
        if (i < result.size() - 1) cout << ", ";
    }
    cout << "]" << endl;
    
    return 0;
}
```

### Complexity Analysis

- **Time Complexity**: O(n1 + n2) - We traverse both arrays once using two pointers. Each element is visited exactly once.

- **Space Complexity**: O(n1 + n2) - In worst case (no duplicates), union contains all elements from both arrays. If we don't count the output array, space is O(1).

## Edge Cases

1. **Both arrays empty**: `[], []` → `[]`
2. **One array empty**: `[1,2], []` → `[1,2]`
3. **Identical arrays**: `[1,2,3], [1,2,3]` → `[1,2,3]`
4. **No common elements**: `[1,3], [2,4]` → `[1,2,3,4]`
5. **One is subset**: `[2,3], [1,2,3,4]` → `[1,2,3,4]`
6. **All duplicates**: `[1,1,1], [1,1]` → `[1]`
7. **Negative numbers**: `[-3,-1], [-2,0]` → `[-3,-2,-1,0]`

## Key Insights & Interview Tips

1. **Sorted Arrays Advantage**: The fact that arrays are sorted allows O(n+m) solution. For unsorted, we'd need O(n log n) to sort first.

2. **Union vs Intersection**:
   - **Union**: All unique elements (OR operation)
   - **Intersection**: Only common elements (AND operation)

3. **Duplicate Handling**: Checking `unionArr.back() != current` ensures we don't add consecutive duplicates.

4. **Alternative Using Set**:
   ```cpp
   set<int> s(arr1.begin(), arr1.end());
   s.insert(arr2.begin(), arr2.end());
   return vector<int>(s.begin(), s.end());
   ```
   Time: O((n+m) log(n+m)), simpler but slower.

5. **Interview Follow-ups**:
   - "What about intersection?" → Only add when elements are equal
   - "What if arrays aren't sorted?" → Sort first or use set
   - "Find union of k sorted arrays?" → Use min-heap or merge pairs

6. **Common Mistakes**:
   - Not skipping duplicates within same array
   - Forgetting to add remaining elements after one array exhausts
   - Not checking if unionArr is empty before accessing `back()`

7. **Optimization**: If one array is much smaller, binary search each element in larger array might be better for some cases.

8. **Related Problems**:
   - Merge k sorted arrays
   - Intersection of two arrays
   - Difference of two arrays (A - B)

