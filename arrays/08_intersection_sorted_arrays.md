# 8. Intersection of Two Sorted Arrays

**LeetCode**: [#349 - Intersection of Two Arrays](https://leetcode.com/problems/intersection-of-two-arrays/) / [#350 - Intersection of Two Arrays II](https://leetcode.com/problems/intersection-of-two-arrays-ii/)  
**Difficulty**: Easy

## Problem Statement

Given two sorted arrays, find their **intersection**. The intersection should contain only the elements that are common to both arrays.

**Intersection**: A ∩ B contains all elements that are in both A and B.

**Note**: The result can contain duplicates if both arrays have duplicate common elements (LeetCode #350 version).

## Examples

### Example 1
```
Input: arr1 = [1, 2, 2, 3, 4], arr2 = [2, 2, 3, 5]
Output: [2, 2, 3]
Explanation: 2 appears twice in both, 3 appears once in both.
```

### Example 2
```
Input: arr1 = [1, 3, 5], arr2 = [2, 4, 6]
Output: []
Explanation: No common elements.
```

### Example 3
```
Input: arr1 = [1, 2, 3], arr2 = [2, 3, 4]
Output: [2, 3]
Explanation: 2 and 3 are common.
```

## Constraints
- 0 ≤ arr1.length, arr2.length ≤ 10^5
- -10^9 ≤ arr1[i], arr2[i] ≤ 10^9
- Both arrays are sorted in non-decreasing order

## Visual Explanation

### ASCII Diagram - Two Pointer Technique

```
arr1: [1, 2, 2, 3, 4]
       ↑
      i=0

arr2: [2, 2, 3, 5]
       ↑
      j=0

intersection: []

Step 1: arr1[0]=1, arr2[0]=2
  1 < 2 → Not a match, move i
  i=1, j=0

Step 2: arr1[1]=2, arr2[0]=2
  2 == 2 → MATCH! Add 2
  intersection: [2], i=2, j=1

Step 3: arr1[2]=2, arr2[1]=2
  2 == 2 → MATCH! Add 2
  intersection: [2, 2], i=3, j=2

Step 4: arr1[3]=3, arr2[2]=3
  3 == 3 → MATCH! Add 3
  intersection: [2, 2, 3], i=4, j=3

Step 5: arr1[4]=4, arr2[3]=5
  4 < 5 → Not a match, move i
  i=5 (exhausted arr1)

Result: [2, 2, 3]

Visual Concept:
  arr1: [1, 2, 2, 3, 4]
            ✓  ✓  ✓       ← Common elements
  arr2:    [2, 2, 3, 5]
            ✓  ✓  ✓       ← Common elements
            
  intersection: [2, 2, 3]
```

## Approach & Intuition

**Two-Pointer Technique** (for sorted arrays):

1. Use pointer `i` for arr1 and `j` for arr2
2. Compare elements:
   - If arr1[i] == arr2[j]: Found common element! Add and increment both
   - If arr1[i] < arr2[j]: Element in arr1 is too small, increment i
   - If arr1[i] > arr2[j]: Element in arr2 is too small, increment j
3. Stop when either array is exhausted

**Why this works**:
- Sorted arrays allow skipping non-matching elements efficiently
- Only when elements match, they're part of intersection
- If arr1[i] < arr2[j], arr1[i] can't match anything later in arr2 (sorted property)

## Algorithm Steps

1. Initialize i = 0, j = 0, result = []
2. While i < n1 AND j < n2:
   - If arr1[i] == arr2[j]:
     - Add arr1[i] to result
     - Increment both i and j
   - Else if arr1[i] < arr2[j]:
     - Increment i
   - Else:
     - Increment j
3. Return result

## Dry Run (Step-by-Step Execution)

Arrays: `arr1 = [1, 2, 2, 3, 4]`, `arr2 = [2, 2, 3, 5]`

```
Initial State:
arr1 = [1, 2, 2, 3, 4], n1 = 5
arr2 = [2, 2, 3, 5], n2 = 4
i = 0, j = 0
intersection = []

Iteration 1:
  arr1[0] = 1, arr2[0] = 2
  1 < 2 → Move i forward
  i = 1, j = 0

Iteration 2:
  arr1[1] = 2, arr2[0] = 2
  2 == 2 → MATCH!
  Add 2 to intersection
  intersection = [2]
  i = 2, j = 1

Iteration 3:
  arr1[2] = 2, arr2[1] = 2
  2 == 2 → MATCH!
  Add 2 to intersection
  intersection = [2, 2]
  i = 3, j = 2

Iteration 4:
  arr1[3] = 3, arr2[2] = 3
  3 == 3 → MATCH!
  Add 3 to intersection
  intersection = [2, 2, 3]
  i = 4, j = 3

Iteration 5:
  arr1[4] = 4, arr2[3] = 5
  4 < 5 → Move i forward
  i = 5, j = 3

i = 5 >= n1, loop ends

Final Result: [2, 2, 3] ✓
```

## C++ Solution

### Code with Detailed Comments

```cpp
#include <iostream>
#include <vector>
using namespace std;

class Solution {
public:
    // Function to find intersection of two sorted arrays (with duplicates)
    // LeetCode #350 version
    vector<int> intersect(vector<int>& arr1, vector<int>& arr2) {
        int n1 = arr1.size();
        int n2 = arr2.size();
        
        // Result vector to store intersection
        vector<int> result;
        
        // Two pointers for both arrays
        int i = 0, j = 0;
        
        // Traverse both arrays simultaneously
        while (i < n1 && j < n2) {
            // Case 1: Elements are equal - this is a common element!
            if (arr1[i] == arr2[j]) {
                // Add to result (allows duplicates)
                result.push_back(arr1[i]);
                
                // Move both pointers forward
                // We've used this element from both arrays
                i++;
                j++;
            }
            // Case 2: arr1[i] is smaller
            // Since arr2 is sorted, arr1[i] can't match anything in arr2
            // Move i forward to find a larger element
            else if (arr1[i] < arr2[j]) {
                i++;
            }
            // Case 3: arr2[j] is smaller
            // Since arr1 is sorted, arr2[j] can't match anything in arr1
            // Move j forward to find a larger element
            else {
                j++;
            }
            
            // Note: We don't need to check remaining elements after
            // one array is exhausted because intersection requires
            // elements from BOTH arrays
        }
        
        return result;
    }
    
    // Alternative: Find unique intersection (LeetCode #349)
    // Each element appears at most once in result
    vector<int> intersection(vector<int>& arr1, vector<int>& arr2) {
        int n1 = arr1.size();
        int n2 = arr2.size();
        
        vector<int> result;
        int i = 0, j = 0;
        
        while (i < n1 && j < n2) {
            if (arr1[i] == arr2[j]) {
                // Add only if not duplicate of previous result
                if (result.empty() || result.back() != arr1[i]) {
                    result.push_back(arr1[i]);
                }
                i++;
                j++;
            }
            else if (arr1[i] < arr2[j]) {
                i++;
            }
            else {
                j++;
            }
        }
        
        return result;
    }
};

// Driver code
int main() {
    Solution sol;
    
    // Test Case 1: With duplicates
    vector<int> arr1 = {1, 2, 2, 3, 4};
    vector<int> arr2 = {2, 2, 3, 5};
    
    cout << "Array 1: [1, 2, 2, 3, 4]" << endl;
    cout << "Array 2: [2, 2, 3, 5]" << endl;
    
    vector<int> result1 = sol.intersect(arr1, arr2);
    cout << "Intersection (with duplicates): [";
    for (int i = 0; i < result1.size(); i++) {
        cout << result1[i];
        if (i < result1.size() - 1) cout << ", ";
    }
    cout << "]" << endl;
    
    vector<int> result2 = sol.intersection(arr1, arr2);
    cout << "Intersection (unique only): [";
    for (int i = 0; i < result2.size(); i++) {
        cout << result2[i];
        if (i < result2.size() - 1) cout << ", ";
    }
    cout << "]" << endl << endl;
    
    // Test Case 2: No common elements
    vector<int> arr3 = {1, 3, 5};
    vector<int> arr4 = {2, 4, 6};
    
    cout << "Array 1: [1, 3, 5]" << endl;
    cout << "Array 2: [2, 4, 6]" << endl;
    
    result1 = sol.intersect(arr3, arr4);
    cout << "Intersection: [";
    for (int i = 0; i < result1.size(); i++) {
        cout << result1[i];
        if (i < result1.size() - 1) cout << ", ";
    }
    cout << "]" << " (empty - no common elements)" << endl << endl;
    
    // Test Case 3: All common
    vector<int> arr5 = {1, 2, 3};
    vector<int> arr6 = {1, 2, 3};
    
    cout << "Array 1: [1, 2, 3]" << endl;
    cout << "Array 2: [1, 2, 3]" << endl;
    
    result1 = sol.intersect(arr5, arr6);
    cout << "Intersection: [";
    for (int i = 0; i < result1.size(); i++) {
        cout << result1[i];
        if (i < result1.size() - 1) cout << ", ";
    }
    cout << "]" << endl;
    
    return 0;
}
```

### Complexity Analysis

- **Time Complexity**: O(n1 + n2) - We traverse both arrays once using two pointers. Each element is visited at most once.

- **Space Complexity**: O(min(n1, n2)) - In worst case, all elements from smaller array are in intersection. If we don't count output array, space is O(1).

## Edge Cases

1. **Both arrays empty**: `[], []` → `[]`
2. **One array empty**: `[1,2], []` → `[]`
3. **No common elements**: `[1,3], [2,4]` → `[]`
4. **All elements common**: `[1,2,3], [1,2,3]` → `[1,2,3]`
5. **One element arrays**: `[5], [5]` → `[5]`
6. **Duplicates**: `[1,1,2], [1,2,2]` → `[1,2,2]`
7. **Negative numbers**: `[-3,-1,0], [-3,0,2]` → `[-3,0]`

## Key Insights & Interview Tips

1. **Sorted vs Unsorted**:
   - **Sorted**: Two pointers O(n+m)
   - **Unsorted**: Hash set O(n+m) but O(n) space

2. **Two Versions**:
   - **With duplicates** (#350): Count frequency in both
   - **Unique only** (#349): Skip consecutive duplicates

3. **Alternative Hash Map Approach** (for unsorted):
   ```cpp
   // Count frequency in arr1
   unordered_map<int, int> freq;
   for (int num : arr1) freq[num]++;
   
   // Check arr2 and decrement
   vector<int> result;
   for (int num : arr2) {
       if (freq[num] > 0) {
           result.push_back(num);
           freq[num]--;
       }
   }
   ```

4. **Interview Follow-ups**:
   - "What if arrays aren't sorted?" → Use hash map
   - "What if one array is much larger?" → Binary search smaller array's elements in larger
   - "Find union instead?" → Add all unique elements

5. **Common Mistakes**:
   - Continuing after one array exhausts (intersection needs elements from BOTH)
   - Not handling empty arrays
   - Confusing with union (intersection is AND, union is OR)

6. **Optimization**: If one array is much smaller, iterate through it and binary search in larger array: O(m log n).

7. **Related Problems**:
   - Union of arrays
   - Symmetric difference
   - Check if one array is subset of another

8. **Real-world Usage**:
   - Finding common friends on social media
   - Database joins
   - Finding common keywords in documents

