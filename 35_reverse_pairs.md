# 35. Reverse Pairs
**LeetCode**: [493 - Reverse Pairs](https://leetcode.com/problems/reverse-pairs/)  
**Difficulty**: Hard

## Problem Statement
Given an integer array `nums`, return the number of **reverse pairs** in the array.

A reverse pair is a pair `(i, j)` where:
- `0 <= i < j < nums.length` and
- `nums[i] > 2 * nums[j]`

## Examples

### Example 1
```
Input: nums = [1,3,2,3,1]
Output: 2
Explanation: Reverse pairs are (3,1) and (3,1)
- nums[1]=3 > 2*nums[4]=2 → (1,4) is a reverse pair
- nums[3]=3 > 2*nums[4]=2 → (3,4) is a reverse pair
```

### Example 2
```
Input: nums = [2,4,3,5,1]
Output: 3
Explanation:
- nums[1]=4 > 2*nums[4]=2 → (1,4)
- nums[2]=3 > 2*nums[4]=2 → (2,4)
- nums[3]=5 > 2*nums[4]=2 → (3,4)
```

### Example 3
```
Input: nums = [5,4,3,2,1]
Output: 4
Explanation: Multiple reverse pairs in descending array
```

## Constraints
- `1 <= nums.length <= 5 × 10^4`
- `-2^31 <= nums[i] <= 2^31 - 1`

## Visual Explanation

### ASCII Diagram
```
Condition: nums[i] > 2 * nums[j] where i < j

Array: [2, 4, 3, 5, 1]

Brute Force:
i=0, nums[0]=2:
  j=1: 2 > 2*4? 2 > 8? NO
  j=2: 2 > 2*3? 2 > 6? NO
  j=3: 2 > 2*5? 2 > 10? NO
  j=4: 2 > 2*1? 2 > 2? NO

i=1, nums[1]=4:
  j=2: 4 > 2*3? 4 > 6? NO
  j=3: 4 > 2*5? 4 > 10? NO
  j=4: 4 > 2*1? 4 > 2? YES ✓

i=2, nums[2]=3:
  j=3: 3 > 2*5? 3 > 10? NO
  j=4: 3 > 2*1? 3 > 2? YES ✓

i=3, nums[3]=5:
  j=4: 5 > 2*1? 5 > 2? YES ✓

Total = 3

Modified Merge Sort Approach:
Before merging, count reverse pairs between left and right subarrays
Then merge as usual

Key difference from inversion count:
- Must count BEFORE merging (different condition)
- Condition is nums[i] > 2*nums[j], not just nums[i] > nums[j]
```

## Approach & Intuition

**Brute Force** - O(n²):
- Check all pairs (i,j) where i < j
- Count where nums[i] > 2 * nums[j]

**Optimal - Modified Merge Sort** - O(n log n):

**Key Insight**:
- Similar to counting inversions but with different condition
- During merge sort, count reverse pairs between left and right subarrays
- **Important**: Count BEFORE merging, then merge separately

**Difference from Inversion Count**:
1. **When to count**: Before merging (not during)
2. **Condition**: `nums[i] > 2 * nums[j]` (not just `>`)
3. **Counting logic**: Use two pointers, different from merge

**Algorithm Structure**:
1. Count reverse pairs between left and right (both sorted)
2. Merge the two sorted subarrays (standard merge)

## Algorithm Steps

**Modified Merge Sort**:

1. **Base case**: If array has 0 or 1 element, return 0

2. **Divide**:
   - Find mid point
   - Count reverse pairs in left half recursively
   - Count reverse pairs in right half recursively

3. **Count cross reverse pairs** (between left and right):
   - Both subarrays are sorted
   - For each element in left subarray:
     - Count how many elements in right satisfy condition
     - Use two pointers for efficiency

4. **Merge** the two sorted subarrays (standard merge sort merge)

5. **Return total**: left pairs + right pairs + cross pairs

## Dry Run (Step-by-Step Execution)

**Input**: `nums = [2,4,3,5,1]`

```
Call mergeSort([2,4,3,5,1], 0, 4):
  mid = 2
  
  leftPairs = mergeSort([2,4,3], 0, 2):
    mid = 1
    
    leftPairs = mergeSort([2,4], 0, 1):
      mid = 0
      leftPairs = mergeSort([2], 0, 0) = 0
      rightPairs = mergeSort([4], 1, 1) = 0
      
      crossPairs = countPairs([2], [4]):
        2 > 2*4? NO
        count = 0
      
      merge([2], [4]) → [2,4]
      return 0
    
    rightPairs = mergeSort([3], 2, 2) = 0
    
    crossPairs = countPairs([2,4], [3]):
      i=0: 2 > 2*3? 2 > 6? NO
      i=1: 4 > 2*3? 4 > 6? NO
      count = 0
    
    merge([2,4], [3]) → [2,3,4]
    return 0
  
  rightPairs = mergeSort([5,1], 3, 4):
    mid = 3
    leftPairs = mergeSort([5], 3, 3) = 0
    rightPairs = mergeSort([1], 4, 4) = 0
    
    crossPairs = countPairs([5], [1]):
      5 > 2*1? 5 > 2? YES
      count = 1
    
    merge([5], [1]) → [1,5]
    return 1
  
  crossPairs = countPairs([2,3,4], [1,5]):
    left = [2,3,4], right = [1,5]
    
    i=0: 2 > 2*1? 2 > 2? NO
         2 > 2*5? 2 > 10? NO
    
    i=1: 3 > 2*1? 3 > 2? YES
         Count elements: right[0] = 1
         3 > 2*5? 3 > 10? NO
         count = 1
    
    i=2: 4 > 2*1? 4 > 2? YES
         Count elements: right[0] = 1
         4 > 2*5? 4 > 10? NO
         count = 2
    
    total cross = 2
  
  merge([2,3,4], [1,5]) → [1,2,3,4,5]
  
  return 0 + 1 + 2 = 3
```

**Final Output**: 3 ✓

## C++ Solution

### Code with Detailed Comments

```cpp
class Solution {
private:
    // Count reverse pairs between two sorted subarrays
    // left subarray: arr[left...mid]
    // right subarray: arr[mid+1...right]
    int countCrossPairs(vector<int>& arr, int left, int mid, int right) {
        int count = 0;
        int j = mid + 1;  // Pointer for right subarray
        
        // For each element in left subarray
        for (int i = left; i <= mid; i++) {
            // Find how many elements in right subarray satisfy:
            // arr[i] > 2 * arr[j]
            
            // Move j forward while condition is satisfied
            // Since both arrays are sorted, once arr[i] <= 2*arr[j],
            // it will be <= 2*arr[j+1], 2*arr[j+2], etc.
            // So all elements from mid+1 to j-1 satisfy the condition
            while (j <= right && (long long)arr[i] > 2LL * arr[j]) {
                j++;
            }
            
            // Count of valid pairs with arr[i]
            // is (j - (mid + 1)) = j - mid - 1
            count += (j - (mid + 1));
        }
        
        return count;
    }
    
    // Standard merge function (same as merge sort)
    void merge(vector<int>& arr, int left, int mid, int right) {
        vector<int> temp;
        
        int i = left;      // Pointer for left subarray
        int j = mid + 1;   // Pointer for right subarray
        
        // Merge two sorted subarrays
        while (i <= mid && j <= right) {
            if (arr[i] <= arr[j]) {
                temp.push_back(arr[i++]);
            } else {
                temp.push_back(arr[j++]);
            }
        }
        
        // Copy remaining elements from left subarray
        while (i <= mid) {
            temp.push_back(arr[i++]);
        }
        
        // Copy remaining elements from right subarray
        while (j <= right) {
            temp.push_back(arr[j++]);
        }
        
        // Copy merged array back to original array
        for (int i = 0; i < temp.size(); i++) {
            arr[left + i] = temp[i];
        }
    }
    
    // Modified merge sort that counts reverse pairs
    int mergeSortAndCount(vector<int>& arr, int left, int right) {
        // Base case: single element or empty
        if (left >= right) {
            return 0;
        }
        
        int mid = left + (right - left) / 2;
        int count = 0;
        
        // Count reverse pairs in left half
        count += mergeSortAndCount(arr, left, mid);
        
        // Count reverse pairs in right half
        count += mergeSortAndCount(arr, mid + 1, right);
        
        // IMPORTANT: Count cross reverse pairs BEFORE merging
        // This is because we need both subarrays to be sorted
        // to efficiently count pairs
        count += countCrossPairs(arr, left, mid, right);
        
        // Now merge the two sorted subarrays
        // (This is separate from counting)
        merge(arr, left, mid, right);
        
        return count;
    }
    
public:
    int reversePairs(vector<int>& nums) {
        // Handle empty array
        if (nums.empty()) {
            return 0;
        }
        
        // Make a copy if we don't want to modify original
        // (Current implementation modifies input)
        return mergeSortAndCount(nums, 0, nums.size() - 1);
    }
};
```

### Alternative: Without modifying original array

```cpp
class Solution {
private:
    int countCrossPairs(vector<int>& temp, int left, int mid, int right) {
        int count = 0;
        int j = mid + 1;
        
        for (int i = left; i <= mid; i++) {
            // Use long long to avoid overflow
            while (j <= right && (long long)temp[i] > 2LL * temp[j]) {
                j++;
            }
            count += (j - mid - 1);
        }
        
        return count;
    }
    
    void merge(vector<int>& temp, int left, int mid, int right) {
        vector<int> merged;
        int i = left, j = mid + 1;
        
        while (i <= mid && j <= right) {
            if (temp[i] <= temp[j]) {
                merged.push_back(temp[i++]);
            } else {
                merged.push_back(temp[j++]);
            }
        }
        
        while (i <= mid) merged.push_back(temp[i++]);
        while (j <= right) merged.push_back(temp[j++]);
        
        for (int i = 0; i < merged.size(); i++) {
            temp[left + i] = merged[i];
        }
    }
    
    int mergeSortAndCount(vector<int>& temp, int left, int right) {
        if (left >= right) return 0;
        
        int mid = left + (right - left) / 2;
        int count = 0;
        
        count += mergeSortAndCount(temp, left, mid);
        count += mergeSortAndCount(temp, mid + 1, right);
        count += countCrossPairs(temp, left, mid, right);
        
        merge(temp, left, mid, right);
        
        return count;
    }
    
public:
    int reversePairs(vector<int>& nums) {
        if (nums.empty()) return 0;
        
        // Work on a copy to preserve original
        vector<int> temp = nums;
        return mergeSortAndCount(temp, 0, temp.size() - 1);
    }
};
```

### Complexity Analysis
- **Time Complexity**: O(n log n) - Similar to merge sort. Counting pairs is O(n) at each level, log n levels.
- **Space Complexity**: O(n) - For temporary array during merge and recursion stack O(log n).

## Edge Cases
- **Single element**: `[5]` → 0 pairs
- **Two elements, condition met**: `[3,1]` → 3 > 2*1, count = 1
- **Two elements, condition not met**: `[1,2]` → 1 > 2*2? NO, count = 0
- **All same**: `[3,3,3]` → 0 pairs
- **Negative numbers**: `[-1,-2]` → -1 > 2*(-2)? -1 > -4? YES, count = 1
- **Large numbers**: Use long long to avoid overflow in 2*nums[j]
- **Empty array**: Return 0

## Key Insights & Interview Tips

**Important Observations**:
- Similar structure to counting inversions but different condition
- Must count BEFORE merging (unlike inversions which count during merge)
- Condition `nums[i] > 2*nums[j]` requires overflow prevention
- Two-pointer technique works because subarrays are sorted
- Once arr[i] <= 2*arr[j], no need to check further in right array for this i

**Common Mistakes to Avoid**:
- Counting during merge instead of before (gives wrong answer)
- Integer overflow when calculating 2*nums[j] (use long long)
- Not resetting j pointer for each i (would miss pairs)
- Confusing with inversion count problem (different condition)
- Modifying array before counting (need sorted subarrays)

**What Interviewers Look For**:
- Understanding of modified merge sort pattern
- Recognition that counting must happen before merging
- Proper overflow handling (2*nums[j] can overflow)
- Efficient two-pointer counting technique
- Knowledge of O(n log n) optimality

**Follow-up Questions**:
- Q: "Why count before merging?"
- A: We need both subarrays sorted to use two-pointer technique efficiently. If we merge first, we lose subarray boundaries.

- Q: "Can you do better than O(n log n)?"
- A: No, this is optimal in comparison model.

- Q: "How is this different from counting inversions?"
- A: Different condition (2* property), and must count before merge not during.

- Q: "What if condition was nums[i] > k * nums[j] for some k?"
- A: Same approach works, just change the condition in counting.

**Related Problems**:
- LeetCode 315: Count of Smaller Numbers After Self
- LeetCode 327: Count of Range Sum
- Counting inversions
- Problems requiring divide and conquer with counting

**Why Count Before Merge**:
```
After merging: [1,2,3,4,5]
We lose information about which came from left vs right!

Before merging:
Left: [2,3,4]
Right: [1,5]

For each in left, scan right:
2 > 2*1? NO
3 > 2*1? YES → count
4 > 2*1? YES → count

This only works because both are sorted!
```

**Two-Pointer Optimization**:
```
Instead of checking all pairs:
For i in left:
    For j in right:
        check condition

We use:
j starts at beginning of right
For each i in left:
    Move j forward while condition holds
    All elements before j satisfy condition with i

This works because:
- arr[i] > 2*arr[j] and arr is sorted
- If arr[i] > 2*arr[j], then arr[i] > 2*arr[k] for all k < j
```
