# 6. Linear Search Algorithm

**LeetCode**: N/A (Foundational Algorithm)  
**Difficulty**: Easy

## Problem Statement

Given an array of integers and a target value, find the index of the target value in the array using linear search. If the target is not found, return -1.

**Linear Search** is the simplest search algorithm that sequentially checks each element until the target is found or the array ends.

## Examples

### Example 1
```
Input: arr = [5, 3, 8, 4, 2], target = 8
Output: 2
Explanation: Element 8 is found at index 2.
```

### Example 2
```
Input: arr = [1, 2, 3, 4, 5], target = 6
Output: -1
Explanation: Element 6 is not in the array.
```

### Example 3
```
Input: arr = [10, 20, 30, 40], target = 10
Output: 0
Explanation: Element 10 is at index 0 (first position).
```

## Constraints
- 0 ≤ arr.length ≤ 10^4
- -10^9 ≤ arr[i], target ≤ 10^9

## Visual Explanation

### ASCII Diagram - Linear Search Process

```
Array: [5, 3, 8, 4, 2]
Target: 8

Step 0: Start at index 0
  [5, 3, 8, 4, 2]
   ↑
  Check: 5 == 8? NO

Step 1: Move to index 1
  [5, 3, 8, 4, 2]
      ↑
  Check: 3 == 8? NO

Step 2: Move to index 2
  [5, 3, 8, 4, 2]
         ↑
  Check: 8 == 8? YES! FOUND!
  Return index 2

Search stops as soon as element is found.

Visualization:
  Start → [5] → NO
        ↓
       [3] → NO
        ↓
       [8] → YES! → Return 2
       
If element wasn't found:
  Start → [5] → NO → [3] → NO → [8] → NO → [4] → NO → [2] → NO → End
  Return -1
```

## Approach & Intuition

**Linear Search** is the most straightforward search algorithm:

1. Start from the first element
2. Compare each element with the target
3. If match found, return the index
4. If end of array reached without finding target, return -1

**Characteristics**:
- **Works on any array** (sorted or unsorted)
- **Simple to implement** - just one loop
- **Stops early** if element found (best case O(1))
- **Must check every element** in worst case (target not present)

**When to use**:
- Array is small
- Array is unsorted (binary search requires sorted array)
- Only searching once (not worth sorting first)

## Algorithm Steps

1. For each index i from 0 to n-1:
   - If arr[i] == target, return i
2. If loop completes, return -1 (not found)

## Dry Run (Step-by-Step Execution)

Array: `[5, 3, 8, 4, 2]`, target = 8

```
Initial State:
arr = [5, 3, 8, 4, 2]
target = 8
n = 5

Iteration 0: i = 0
  arr[0] = 5
  5 == 8? NO
  Continue to next element...

Iteration 1: i = 1
  arr[1] = 3
  3 == 8? NO
  Continue to next element...

Iteration 2: i = 2
  arr[2] = 8
  8 == 8? YES! MATCH FOUND!
  Return i = 2

Search terminates early (didn't need to check indices 3 and 4)
Result: 2 ✓
```

### Example 2: Target Not Found

Array: `[1, 2, 3, 4, 5]`, target = 6

```
Initial State:
arr = [1, 2, 3, 4, 5]
target = 6
n = 5

Iteration 0: i = 0, arr[0] = 1 → 1 == 6? NO
Iteration 1: i = 1, arr[1] = 2 → 2 == 6? NO
Iteration 2: i = 2, arr[2] = 3 → 3 == 6? NO
Iteration 3: i = 3, arr[3] = 4 → 4 == 6? NO
Iteration 4: i = 4, arr[4] = 5 → 5 == 6? NO

Loop completed without finding target.
Result: -1 (not found) ✓
```

## C++ Solution

### Code with Detailed Comments

```cpp
#include <iostream>
#include <vector>
using namespace std;

class Solution {
public:
    // Function to perform linear search
    // Returns index of target if found, -1 otherwise
    int linearSearch(vector<int>& arr, int target) {
        int n = arr.size();
        
        // Edge case: Empty array
        if (n == 0) {
            return -1; // Cannot find anything in empty array
        }
        
        // Iterate through each element sequentially
        for (int i = 0; i < n; i++) {
            // Check if current element matches target
            if (arr[i] == target) {
                // Found! Return the index immediately
                // This is early termination - no need to check rest
                return i;
            }
            // If not match, continue to next element
        }
        
        // If we've checked all elements and haven't returned,
        // it means target is not in the array
        return -1;
    }
    
    // Variation: Find all occurrences of target
    vector<int> linearSearchAll(vector<int>& arr, int target) {
        vector<int> indices; // Store all indices where target is found
        
        // Check every element
        for (int i = 0; i < arr.size(); i++) {
            if (arr[i] == target) {
                // Don't return immediately, store index and continue
                indices.push_back(i);
            }
        }
        
        // Return vector of all indices (empty if not found)
        return indices;
    }
    
    // Variation: Count occurrences of target
    int countOccurrences(vector<int>& arr, int target) {
        int count = 0;
        
        // Check every element and count matches
        for (int i = 0; i < arr.size(); i++) {
            if (arr[i] == target) {
                count++;
            }
        }
        
        return count;
    }
};

// Driver code for testing
int main() {
    Solution sol;
    
    // Test Case 1: Element found
    vector<int> arr1 = {5, 3, 8, 4, 2};
    int target1 = 8;
    cout << "Array: [5, 3, 8, 4, 2], Target: 8" << endl;
    int result1 = sol.linearSearch(arr1, target1);
    cout << "Index: " << result1 << endl << endl;
    
    // Test Case 2: Element not found
    vector<int> arr2 = {1, 2, 3, 4, 5};
    int target2 = 6;
    cout << "Array: [1, 2, 3, 4, 5], Target: 6" << endl;
    int result2 = sol.linearSearch(arr2, target2);
    cout << "Index: " << result2 << " (not found)" << endl << endl;
    
    // Test Case 3: Element at first position
    vector<int> arr3 = {10, 20, 30, 40};
    int target3 = 10;
    cout << "Array: [10, 20, 30, 40], Target: 10" << endl;
    int result3 = sol.linearSearch(arr3, target3);
    cout << "Index: " << result3 << " (best case - first element)" << endl << endl;
    
    // Test Case 4: Find all occurrences
    vector<int> arr4 = {1, 3, 3, 5, 3, 7};
    int target4 = 3;
    cout << "Array: [1, 3, 3, 5, 3, 7], Target: 3" << endl;
    vector<int> allIndices = sol.linearSearchAll(arr4, target4);
    cout << "All indices: [";
    for (int i = 0; i < allIndices.size(); i++) {
        cout << allIndices[i];
        if (i < allIndices.size() - 1) cout << ", ";
    }
    cout << "]" << endl << endl;
    
    // Test Case 5: Count occurrences
    cout << "Count of 3 in array: " << sol.countOccurrences(arr4, target4) << endl;
    
    return 0;
}
```

### Complexity Analysis

- **Time Complexity**: 
  - **Best Case**: O(1) - Target is at first position
  - **Average Case**: O(n/2) = O(n) - Target is in middle
  - **Worst Case**: O(n) - Target at end or not present
  
  Where n is the array size. We potentially check every element.

- **Space Complexity**: O(1) - Only using a loop counter variable. No additional data structures (for basic search).

## Edge Cases

1. **Empty array**: `[]`, target = 5 → Return -1
2. **Single element found**: `[7]`, target = 7 → Return 0
3. **Single element not found**: `[7]`, target = 5 → Return -1
4. **Target at first position**: `[5, 1, 2]`, target = 5 → Return 0 (best case)
5. **Target at last position**: `[1, 2, 5]`, target = 5 → Return 2 (worst case)
6. **Multiple occurrences**: `[3, 5, 3, 8]`, target = 3 → Return 0 (first occurrence)
7. **Negative numbers**: `[-5, -3, -1]`, target = -3 → Return 1

## Key Insights & Interview Tips

1. **Linear Search vs Binary Search**:
   ```
   | Feature          | Linear Search | Binary Search |
   |------------------|---------------|---------------|
   | Array Type       | Any           | Must be sorted|
   | Time Best        | O(1)          | O(1)          |
   | Time Worst       | O(n)          | O(log n)      |
   | Space            | O(1)          | O(1)          |
   | Complexity       | Simple        | More complex  |
   ```

2. **When Linear Search is Better**:
   - Array is unsorted and small
   - Only searching once (not worth sorting first)
   - Array size is very small (< 10 elements)
   - Searching linked list (can't do binary search)

3. **Early Termination**: As soon as we find the target, we return. Don't keep searching!

4. **Common Variations**:
   - Find first occurrence (basic version)
   - Find last occurrence (search from end)
   - Find all occurrences (don't return early)
   - Count occurrences
   - Find min/max while searching

5. **Optimization**: 
   - **Sentinel Search**: Add target at end to avoid bounds checking
   - **Ordered Search**: If array has some order, may terminate early

6. **Related Problems**:
   - Search in 2D matrix (row-wise linear search)
   - Find missing number
   - First and last position in sorted array

7. **Interview Follow-ups**:
   - "What if array is sorted?" → Use binary search for O(log n)
   - "Find all occurrences?" → Don't return early, collect all indices
   - "What if we search multiple times?" → Consider sorting first, then binary search

8. **Common Mistakes**:
   - Returning index when not found instead of -1
   - Not handling empty array
   - Continuing search after finding element (inefficient)

9. **Real-World Usage**:
   - Phone contacts (if unsorted)
   - Unsorted documents
   - Recent history/cache (usually small)
   - When data continuously changes (sorting overhead not worth it)

10. **Comparison with Other Search Methods**:
    - **Hash Table**: O(1) average search but O(n) space
    - **Binary Search**: O(log n) but needs sorted array
    - **Linear Search**: Always works, simple, but slowest for large data

11. **Memory Access Pattern**:
    - Sequential access → good cache performance
    - Better than binary search for very small arrays due to cache

12. **When NOT to Use**:
    - Large sorted array (use binary search)
    - Frequent searches on same array (sort once, binary search many times)
    - Need very fast search (use hash table with O(1))
