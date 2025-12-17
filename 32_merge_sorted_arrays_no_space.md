# 32. Merge Two Sorted Arrays Without Extra Space
**LeetCode**: [88 - Merge Sorted Array](https://leetcode.com/problems/merge-sorted-array/) (Related)  
**Difficulty**: Hard

## Problem Statement
Given two sorted arrays `arr1[]` of size `m` and `arr2[]` of size `n`, merge them in sorted order without using extra space. The final sorted array should be stored in the two given arrays themselves (`arr1[]` should contain first `m` elements and `arr2[]` should contain remaining `n` elements).

**Note**: Different from LeetCode 88 where arr1 has enough space. Here, both arrays have exact size.

## Examples

### Example 1
```
Input: arr1 = [1,3,5,7], arr2 = [0,2,6,8,9]
Output: arr1 = [0,1,2,3], arr2 = [5,6,7,8,9]
Explanation: After merging and sorting, smallest 4 go to arr1, rest to arr2
```

### Example 2
```
Input: arr1 = [1,5,9,10,15,20], arr2 = [2,3,8,13]
Output: arr1 = [1,2,3,5,8,9], arr2 = [10,13,15,20]
```

### Example 3
```
Input: arr1 = [1,2,3], arr2 = [4,5,6]
Output: arr1 = [1,2,3], arr2 = [4,5,6]
Explanation: Already in correct order
```

## Constraints
- `1 <= m, n <= 10^5`
- `1 <= arr1[i], arr2[i] <= 10^9`
- Both arrays are sorted

## Visual Explanation

### ASCII Diagram
```
Gap Method (Shell Sort Concept):

arr1 = [1, 3, 5, 7]
arr2 = [0, 2, 6, 8, 9]
Combined length = 9

Gap = ceil((m+n)/2) = ceil(9/2) = 5

Pass 1 (gap=5):
Compare elements gap distance apart:
Index: 0  1  2  3  4  5  6  7  8
Value: 1  3  5  7  0  2  6  8  9
       |           |
       ↓           ↓
    Compare 1 and 2: 1 < 2, no swap
       
       1  3  5  7  0  2  6  8  9
          |           |
       Compare 3 and 6: 3 < 6, no swap
       
Continue for all pairs...
After pass 1: [1,3,5,7] [0,2,6,8,9]

Pass 2 (gap=2):
After comparing and swapping pairs 2 apart...

Pass 3 (gap=1):
Final pass with gap=1 acts like bubble sort
Result: [0,1,2,3] [5,6,7,8,9]

Key Idea: Start with large gap, gradually reduce
Similar to Shell Sort but adapted for two arrays
```

## Approach & Intuition

**Approach 1: Naive (O(m+n) space)**:
- Merge into temporary array
- Copy back to arr1 and arr2

**Approach 2: Optimal - Gap Method (O(1) space)**:

**Key Insight**:
- Adaptation of Shell Sort for two arrays
- Use gap to compare elements that are `gap` distance apart
- Gradually reduce gap until gap = 1
- Gap calculation: `gap = ceil((m+n)/2)`, then `gap = ceil(gap/2)` in each iteration

**Why it works**:
- Large gap initially moves very out-of-place elements closer
- Smaller gaps fine-tune the positions
- Final gap=1 pass ensures everything is sorted
- All operations are swaps, no extra space needed

**Gap formula**: 
- Initial: `gap = ceil((m+n)/2)`
- Update: `gap = ceil(gap/2)` or `gap = (gap/2) + (gap%2)`
- Stop when: `gap = 0`

## Algorithm Steps

**Gap Method**:

1. **Calculate initial gap**: `gap = ceil((m+n)/2)`

2. **While gap > 0**:
   
   a. **For each pair of indices i and j where j = i + gap**:
      
      - **Case 1**: Both in arr1 (i < m && j < m)
        * If arr1[i] > arr1[j], swap them
      
      - **Case 2**: i in arr1, j in arr2 (i < m && j >= m)
        * Compare arr1[i] with arr2[j-m]
        * If arr1[i] > arr2[j-m], swap them
      
      - **Case 3**: Both in arr2 (i >= m && j >= m)
        * If arr2[i-m] > arr2[j-m], swap them
   
   b. **Update gap**: `gap = gap == 1 ? 0 : ceil(gap/2)`

3. **Arrays are now sorted and properly distributed**

## Dry Run (Step-by-Step Execution)

**Input**: `arr1 = [1,3,5,7], arr2 = [0,2,6,8,9]`  
m = 4, n = 5, total = 9

```
Initial gap = ceil(9/2) = 5

Pass 1 (gap=5):
  i=0, j=5: arr1[0]=1, arr2[1]=2
    1 < 2, no swap
  
  i=1, j=6: arr1[1]=3, arr2[2]=6
    3 < 6, no swap
  
  i=2, j=7: arr1[2]=5, arr2[3]=8
    5 < 8, no swap
  
  i=3, j=8: arr1[3]=7, arr2[4]=9
    7 < 9, no swap
  
  After: arr1=[1,3,5,7], arr2=[0,2,6,8,9]

gap = ceil(5/2) = 3

Pass 2 (gap=3):
  i=0, j=3: arr1[0]=1, arr1[3]=7
    1 < 7, no swap
  
  i=1, j=4: arr1[1]=3, arr2[0]=0
    3 > 0, SWAP
    arr1=[1,0,5,7], arr2=[3,2,6,8,9]
  
  i=2, j=5: arr1[2]=5, arr2[1]=2
    5 > 2, SWAP
    arr1=[1,0,2,7], arr2=[3,5,6,8,9]
  
  i=3, j=6: arr1[3]=7, arr2[2]=6
    7 > 6, SWAP
    arr1=[1,0,2,6], arr2=[3,5,7,8,9]
  
  i=4, j=7: arr2[0]=3, arr2[3]=8
    3 < 8, no swap
  
  i=5, j=8: arr2[1]=5, arr2[4]=9
    5 < 9, no swap

  After: arr1=[1,0,2,6], arr2=[3,5,7,8,9]

gap = ceil(3/2) = 2

Pass 3 (gap=2):
  i=0, j=2: arr1[0]=1, arr1[2]=2
    1 < 2, no swap
  
  i=1, j=3: arr1[1]=0, arr1[3]=6
    0 < 6, no swap
  
  i=2, j=4: arr1[2]=2, arr2[0]=3
    2 < 3, no swap
  
  i=3, j=5: arr1[3]=6, arr2[1]=5
    6 > 5, SWAP
    arr1=[1,0,2,5], arr2=[3,6,7,8,9]
  
  ... continue ...

  After: arr1=[1,0,2,5], arr2=[3,6,7,8,9]

gap = ceil(2/2) = 1

Pass 4 (gap=1):
  Compare adjacent elements and swap if needed
  Like bubble sort pass
  
  After: arr1=[0,1,2,3], arr2=[5,6,7,8,9]

gap = 0, stop
```

**Final Output**: 
- arr1 = [0,1,2,3]
- arr2 = [5,6,7,8,9] ✓

## C++ Solution

### Code with Detailed Comments

```cpp
class Solution {
public:
    void merge(vector<int>& arr1, vector<int>& arr2) {
        int m = arr1.size();
        int n = arr2.size();
        
        // Calculate initial gap using ceil((m+n)/2)
        // We use (m+n+1)/2 for ceiling division without floats
        int gap = (m + n + 1) / 2;
        
        // Continue until gap becomes 0
        while (gap > 0) {
            int i = 0;
            
            // Start j at position gap away from i
            int j = gap;
            
            // Compare and swap elements that are 'gap' distance apart
            while (j < m + n) {
                
                // Case 1: Both pointers in arr1
                if (i < m && j < m) {
                    // Both elements in first array
                    // Swap if left element is greater
                    if (arr1[i] > arr1[j]) {
                        swap(arr1[i], arr1[j]);
                    }
                }
                // Case 2: i in arr1, j in arr2  
                else if (i < m && j >= m) {
                    // First element in arr1, second in arr2
                    // j-m gives the index in arr2
                    if (arr1[i] > arr2[j - m]) {
                        swap(arr1[i], arr2[j - m]);
                    }
                }
                // Case 3: Both pointers in arr2
                else if (i >= m && j >= m) {
                    // Both elements in second array
                    // Convert to arr2 indices by subtracting m
                    if (arr2[i - m] > arr2[j - m]) {
                        swap(arr2[i - m], arr2[j - m]);
                    }
                }
                
                // Move both pointers forward
                i++;
                j++;
            }
            
            // Reduce gap for next iteration
            // If gap is 1, set to 0 to stop
            // Otherwise, calculate new gap as ceil(gap/2)
            if (gap == 1) {
                gap = 0;
            } else {
                gap = (gap + 1) / 2;  // Ceiling division
            }
        }
    }
};
```

### Alternative: Helper function for clarity

```cpp
class Solution {
private:
    // Helper to get element at index i in combined view of both arrays
    int& getElement(vector<int>& arr1, vector<int>& arr2, int index) {
        int m = arr1.size();
        if (index < m) {
            return arr1[index];
        } else {
            return arr2[index - m];
        }
    }
    
public:
    void merge(vector<int>& arr1, vector<int>& arr2) {
        int m = arr1.size();
        int n = arr2.size();
        int gap = (m + n + 1) / 2;
        
        while (gap > 0) {
            for (int i = 0; i + gap < m + n; i++) {
                int j = i + gap;
                
                // Get references to elements (handles arr1/arr2 boundary)
                int& elem1 = getElement(arr1, arr2, i);
                int& elem2 = getElement(arr1, arr2, j);
                
                // Swap if out of order
                if (elem1 > elem2) {
                    swap(elem1, elem2);
                }
            }
            
            gap = (gap == 1) ? 0 : (gap + 1) / 2;
        }
    }
};
```

### Complexity Analysis
- **Time Complexity**: O((m+n) × log(m+n)) - Number of gaps is O(log(m+n)), each gap requires O(m+n) comparisons
- **Space Complexity**: O(1) - Only using constant extra space for variables

## Edge Cases
- **Non-overlapping ranges**: `arr1=[1,2,3], arr2=[4,5,6]` → Already correct
- **Completely reversed**: `arr1=[5,6,7], arr2=[1,2,3]` → Need full rearrangement
- **One array empty**: Not possible per constraints
- **Arrays of different sizes**: Algorithm handles naturally
- **All elements same**: Works correctly
- **Single element arrays**: `arr1=[5], arr2=[1]` → Swap in first pass

## Key Insights & Interview Tips

**Important Observations**:
- Gap method is an in-place variation of Shell Sort
- Each pass with gap > 1 does partial sorting
- Final pass with gap=1 ensures complete sorting
- Works because it progressively reduces inversions
- No need for extra space unlike standard merge

**Common Mistakes to Avoid**:
- Using `gap/2` instead of `ceil(gap/2)` - causes infinite loop
- Not handling the three cases properly (both in arr1, split, both in arr2)
- Off-by-one errors when converting indices between arrays
- Forgetting to stop when gap becomes 0
- Not handling j >= m+n boundary correctly

**What Interviewers Look For**:
- Recognition that standard merge needs O(m+n) space
- Knowledge of gap method / shell sort concept
- Careful handling of array boundaries
- Proper gap calculation (ceiling division)
- Understanding of why algorithm works

**Follow-up Questions**:
- Q: "Why not just sort the combined array?"
- A: We want to maintain the two-array structure. Also, gap method is more cache-friendly for large arrays.

- Q: "Can you prove the correctness?"
- A: Similar proof to Shell Sort - each gap reduces inversions, final gap=1 ensures sorted.

- Q: "What's the worst case number of swaps?"
- A: O((m+n)²) in theory, but typically much better in practice.

- Q: "Why use gap = ceil(n/2) specifically?"
- A: Based on Shell Sort gap sequence. Other sequences work but this is simple and effective.

**Related Problems**:
- LeetCode 88: Merge Sorted Array (with extra space in arr1)
- Shell Sort algorithm
- In-place merge for merge sort

**Gap Method vs Standard Merge**:
| Aspect | Standard Merge | Gap Method |
|--------|----------------|------------|
| Space | O(m+n) | O(1) |
| Time | O(m+n) | O((m+n)log(m+n)) |
| Simplicity | Simple | More complex |
| Use case | When space available | Space constrained |

**Visualization of Gap Sequence**:
```
For m+n = 10:
gap = 5 → 3 → 2 → 1 → 0

For m+n = 9:
gap = 5 → 3 → 2 → 1 → 0

Formula: gap = ceil(gap/2)
Implemented as: gap = (gap + 1) / 2
```
