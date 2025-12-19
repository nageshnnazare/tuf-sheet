# 34. Count Inversions in Array
**LeetCode**: Related to sorting and merge sort problems  
**Difficulty**: Hard

## Problem Statement
Given an array of integers, count the number of inversions in the array. An inversion is a pair of indices (i, j) such that `i < j` and `arr[i] > arr[j]`.

In other words, count pairs where a larger element appears before a smaller element.

## Examples

### Example 1
```
Input: arr = [2,4,1,3,5]
Output: 3
Explanation: Inversions are (2,1), (4,1), (4,3)
- 2 > 1 at positions 0, 2
- 4 > 1 at positions 1, 2
- 4 > 3 at positions 1, 3
```

### Example 2
```
Input: arr = [5,5,5]
Output: 0
Explanation: No inversions (equal elements don't count)
```

### Example 3
```
Input: arr = [5,4,3,2,1]
Output: 10
Explanation: Every pair is an inversion (reverse sorted)
For n=5: inversions = 4+3+2+1 = 10 = n*(n-1)/2
```

## Constraints
- `1 <= arr.length <= 10^5`
- `-10^9 <= arr[i] <= 10^9`

## Visual Explanation

### ASCII Diagram
```
Array: [2, 4, 1, 3, 5]

Brute Force - Check all pairs:
i=0, arr[0]=2:
  j=1: 2 < 4, no
  j=2: 2 > 1, YES (2,1) ✓
  j=3: 2 < 3, no
  j=4: 2 < 5, no

i=1, arr[1]=4:
  j=2: 4 > 1, YES (4,1) ✓
  j=3: 4 > 3, YES (4,3) ✓
  j=4: 4 < 5, no

...
Total: 3 inversions

Modified Merge Sort:
During merge of sorted subarrays, count inversions

[2, 4, 1, 3, 5]
      ↓
[2, 4] [1, 3, 5]  ← Divide
  ↓       ↓
[2] [4] [1] [3,5]  ← Divide further
         ↓
        [1] [3] [5]

Now merge and count:
Merge [3] and [5]: sorted [3,5], inversions = 0
Merge [1] and [3,5]: sorted [1,3,5], inversions = 2
  (1 < 3 but 1 comes from left, so (3,1) and (5,1) but wait...)
  
Actually during merge:
When we pick from right subarray before left,
it means ALL remaining elements in left are inversions!

Merge [2,4] and [1,3,5]:
  Compare 2 and 1: pick 1 (2 inversions: 2>1, 4>1)
  Compare 2 and 3: pick 2
  Compare 4 and 3: pick 3 (1 inversion: 4>3)
  ...
```

## Approach & Intuition

**Brute Force** - O(n²):
- Check all pairs (i,j) where i <j
- Count pairs where arr[i] > arr[j]

**Optimal - Modified Merge Sort** - O(n log n):

**Key Insight**:
- During merge step of merge sort, we can count inversions
- When we pick element from right subarray instead of left, it forms inversions
- Number of inversions = number of elements remaining in left subarray

**Why merge sort**:
- Left and right subarrays are sorted
- If right[j] < left[i], then right[j] is also less than all elements after left[i]
- Count = (number of remaining elements in left) = (mid - i + 1)

**Three types of inversions**:
1. Within left subarray (counted during left recursion)
2. Within right subarray (counted during right recursion)
3. Between left and right subarrays (counted during merge)

## Algorithm Steps

**Modified Merge Sort**:

1. **Base case**: If array has 0 or 1 element, return 0

2. **Divide**:
   - Find mid point
   - Count inversions in left half recursively
   - Count inversions in right half recursively

3. **Merge and count cross-inversions**:
   - While merging two sorted halves:
     - If element from left <= element from right:
       * Pick from left, no new inversions
     - If element from right < element from left:
       * Pick from right
       * ALL remaining elements in left form inversions
       * Count = (mid - leftIndex + 1)

4. **Return total**: left inversions + right inversions + merge inversions

## Dry Run (Step-by-Step Execution)

**Input**: `arr = [2,4,1,3,5]`

```
Call mergeSort([2,4,1,3,5], 0, 4):
  mid = 2
  
  leftInv = mergeSort([2,4,1], 0, 2):
    mid = 1
    
    leftInv = mergeSort([2,4], 0, 1):
      mid = 0
      leftInv = mergeSort([2], 0, 0) = 0
      rightInv = mergeSort([4], 1, 1) = 0
      mergeInv = merge([2], [4]) = 0
      return 0
    
    rightInv = mergeSort([1], 2, 2) = 0
    
    mergeInv = merge([2,4], [1]):
      Compare 2 and 1:
        1 < 2, pick 1
        Count inversions: 2 elements left in first array (2, 4)
        mergeInv = 2
      Pick remaining: 2, 4
      result = [1,2,4]
      return 2
    
    return 0 + 0 + 2 = 2
  
  rightInv = mergeSort([3,5], 3, 4):
    mid = 3
    leftInv = mergeSort([3], 3, 3) = 0
    rightInv = mergeSort([5], 4, 4) = 0
    mergeInv = merge([3], [5]) = 0
    return 0
  
  mergeInv = merge([1,2,4], [3,5]):
    Compare 1 and 3: 1 < 3, pick 1, count = 0
    Compare 2 and 3: 2 < 3, pick 2, count = 0
    Compare 4 and 3: 3 < 4, pick 3
      Count inversions: 1 element left (4)
      mergeInv = 1
    Pick remaining: 4, 5
    result = [1,2,3,4,5]
    return 1
  
  return 2 + 0 + 1 = 3
```

**Final Output**: 3 inversions ✓

## C++ Solution

### Code with Detailed Comments

```cpp
class Solution {
private:
    // Merge two sorted subarrays and count inversions
    // arr[left...mid] and arr[mid+1...right] are already sorted
    long long mergeAndCount(vector<int>& arr, int left, int mid, int right) {
        // Create temporary arrays for left and right subarrays
        vector<int> leftArr(arr.begin() + left, arr.begin() + mid + 1);
        vector<int> rightArr(arr.begin() + mid + 1, arr.begin() + right + 1);
        
        int i = 0;  // Index for leftArr
        int j = 0;  // Index for rightArr
        int k = left;  // Index for merged array
        long long inversions = 0;
        
        // Merge the two arrays while counting inversions
        while (i < leftArr.size() && j < rightArr.size()) {
            if (leftArr[i] <= rightArr[j]) {
                // Element from left is smaller or equal
                // No new inversions, just place it
                arr[k++] = leftArr[i++];
            } else {
                // Element from right is smaller
                // This means rightArr[j] is smaller than leftArr[i]
                // and also smaller than ALL remaining elements in leftArr
                // (since leftArr is sorted)
                // So we found inversions with all remaining left elements
                arr[k++] = rightArr[j++];
                
                // Count inversions: all remaining elements in leftArr
                // form inversions with current rightArr element
                inversions += (leftArr.size() - i);
            }
        }
        
        // Copy remaining elements from left array (if any)
        while (i < leftArr.size()) {
            arr[k++] = leftArr[i++];
        }
        
        // Copy remaining elements from right array (if any)
        while (j < rightArr.size()) {
            arr[k++] = rightArr[j++];
        }
        
        return inversions;
    }
    
    // Recursive merge sort function that counts inversions
    long long mergeSortAndCount(vector<int>& arr, int left, int right) {
        long long inversions = 0;
        
        // Base case: single element or empty
        if (left >= right) {
            return 0;
        }
        
        // Find middle point
        int mid = left + (right - left) / 2;
        
        // Count inversions in left half
        inversions += mergeSortAndCount(arr, left, mid);
        
        // Count inversions in right half
        inversions += mergeSortAndCount(arr, mid + 1, right);
        
        // Count inversions during merge (cross inversions)
        // These are inversions where one element is in left half
        // and other is in right half
        inversions += mergeAndCount(arr, left, mid, right);
        
        return inversions;
    }
    
public:
    long long countInversions(vector<int>& arr) {
        // Use long long to avoid overflow for large arrays
        // Maximum inversions for array of size n is n*(n-1)/2
        return mergeSortAndCount(arr, 0, arr.size() - 1);
    }
};
```

### Alternative: Without modifying input array

```cpp
class Solution {
private:
    long long mergeAndCount(vector<int>& temp, int left, int mid, int right) {
        vector<int> leftArr(temp.begin() + left, temp.begin() + mid + 1);
        vector<int> rightArr(temp.begin() + mid + 1, temp.begin() + right + 1);
        
        int i = 0, j = 0, k = left;
        long long inversions = 0;
        
        while (i < leftArr.size() && j < rightArr.size()) {
            if (leftArr[i] <= rightArr[j]) {
                temp[k++] = leftArr[i++];
            } else {
                temp[k++] = rightArr[j++];
                inversions += (leftArr.size() - i);
            }
        }
        
        while (i < leftArr.size()) temp[k++] = leftArr[i++];
        while (j < rightArr.size()) temp[k++] = rightArr[j++];
        
        return inversions;
    }
    
    long long mergeSortAndCount(vector<int>& temp, int left, int right) {
        if (left >= right) return 0;
        
        int mid = left + (right - left) / 2;
        long long inversions = 0;
        
        inversions += mergeSortAndCount(temp, left, mid);
        inversions += mergeSortAndCount(temp, mid + 1, right);
        inversions += mergeAndCount(temp, left, mid, right);
        
        return inversions;
    }
    
public:
    long long countInversions(vector<int>& arr) {
        // Create a copy to avoid modifying original array
        vector<int> temp = arr;
        return mergeSortAndCount(temp, 0, temp.size() - 1);
    }
};
```

### Complexity Analysis
- **Time Complexity**: O(n log n) - Same as merge sort. Divide step is O(log n) levels, merge at each level is O(n)
- **Space Complexity**: O(n) - For temporary arrays during merge. Recursion stack is O(log n).

## Edge Cases
- **Single element**: `[5]` → 0 inversions
- **Two elements sorted**: `[1,2]` → 0 inversions
- **Two elements reversed**: `[2,1]` → 1 inversion
- **Reverse sorted**: `[5,4,3,2,1]` → n*(n-1)/2 = 10 inversions (maximum)
- **All same**: `[3,3,3,3]` → 0 inversions
- **Already sorted**: `[1,2,3,4,5]` → 0 inversions
- **Large array**: Use long long to avoid overflow

## Key Insights & Interview Tips

**Important Observations**:
- Maximum inversions = n*(n-1)/2 (reverse sorted array)
- Minimum inversions = 0 (sorted array or all equal)
- Inversions measure "sortedness" - fewer inversions = more sorted
- This is the basis of some sorting lower bound proofs
- Application: Ranking systems, collaborative filtering

**Common Mistakes to Avoid**:
- Integer overflow - use long long for count
- Not handling equal elements correctly (should not count as inversion)
- Wrong counting formula during merge (should be mid-i+1 or remaining elements)
- Modifying original array if problem requires it unchanged
- Forgetting to return total of all three types of inversions

**What Interviewers Look For**:
- Recognition that modified merge sort solves this
- Understanding of how inversions are counted during merge
- Proper handling of overflow
- Knowledge of time complexity improvement from O(n²) to O(n log n)
- Ability to explain why merge sort works for this problem

**Follow-up Questions**:
- Q: "Can you do better than O(n log n)?"
- A: No, counting inversions is Ω(n log n) in comparison model. This is optimal.

- Q: "What if we only need to know if inversions > k?"
- A: Can optimize to stop early, but worst case still O(n log n)

- Q: "How is this related to sorting?"
- A: Number of swaps in bubble sort = number of inversions. Measures how far array is from sorted.

- Q: "Can you use other data structures?"
- A: Yes - Binary Indexed Tree (BIT/Fenwick Tree) or Balanced BST also give O(n log n)

**Related Problems**:
- LeetCode 315: Count of Smaller Numbers After Self
- LeetCode 493: Reverse Pairs (similar but condition different)
- Bubble sort swap count
- Kendall Tau distance (measure of rank correlation)

**Why This Works**:
```
During merge of [L] and [R]:
- Both are sorted
- When R[j] < L[i]:
  * R[j] is less than L[i], L[i+1], ..., L[end]
  * All these form inversions with R[j]
  * Count = (end of L - i + 1)

Example:
L = [2, 4, 7]
R = [1, 3, 5]

When we pick 1 from R:
  1 < 2, and 1 < 4, and 1 < 7
  Inversions: (2,1), (4,1), (7,1)
  Count = 3 = remaining in L
```

**Applications**:
- Measuring similarity in rankings
- Collaborative filtering algorithms
- Analyzing sorting algorithm efficiency
- Distance metric for permutations
