# 25. Count Subarrays with Sum Equal to K
**LeetCode**: [560 - Subarray Sum Equals K](https://leetcode.com/problems/subarray-sum-equals-k/)  
**Difficulty**: Medium

## Problem Statement
Given an array of integers `nums` and an integer `k`, return the total number of subarrays whose sum equals to `k`.

A subarray is a contiguous non-empty sequence of elements within an array.

## Examples

### Example 1
```
Input: nums = [1,1,1], k = 2
Output: 2
Explanation: Subarrays [1,1] appears twice:
- indices [0,1]: 1+1 = 2
- indices [1,2]: 1+1 = 2
```

### Example 2
```
Input: nums = [1,2,3], k = 3
Output: 2
Explanation: 
- indices [0,2]: 1+2 = 3 ✗ (sum = 6, not 3)
- indices [0,0]: 1 ✗
- indices [1,1]: 2 ✗
- indices [2,2]: 3 ✓
- indices [0,1]: 1+2 = 3 ✓
```

### Example 3
```
Input: nums = [1,-1,0], k = 0
Output: 3
Explanation:
- indices [0,1]: 1+(-1) = 0 ✓
- indices [1,2]: -1+0 = 0 ✗ (sum = -1)
- indices [2,2]: 0 ✓
- indices [0,2]: 1+(-1)+0 = 0 ✓
```

## Constraints
- `1 <= nums.length <= 2 × 10^4`
- `-1000 <= nums[i] <= 1000`
- `-10^7 <= k <= 10^7`

## Visual Explanation

### ASCII Diagram
```
Array: [1, 2, 3, -3, 1, 1, 1], k = 3

Prefix Sum Array:
index:    0   1   2   3   4   5   6   7
nums:     -   1   2   3  -3   1   1   1
prefix:   0   1   3   6   3   4   5   6

Key Insight: If prefix[j] - prefix[i] = k
             Then subarray from (i, j] has sum k

Finding subarrays with sum = 3:
prefix[2] - prefix[0] = 3 - 0 = 3 ✓  → subarray [1,2]
prefix[4] - prefix[1] = 3 - 0 = 3 ✓  → subarray [2,3,-3]
prefix[4] - prefix[0] = 3 - 0 = 3 ✓  → subarray [1,2,3,-3]
prefix[6] - prefix[3] = 5 - 2 = 3 ✓  → subarray [-3,1,1,1]

Formula: currentSum - k = previousSum
         If previousSum exists in map, we found subarray(s)!
```

## Approach & Intuition

**Brute Force** (O(n²)):
- Try all possible subarrays
- Calculate sum for each and count those equal to k

**Optimal Approach - Prefix Sum + Hash Map** (O(n)):

**Key Insight**: 
- If we know prefix sum up to index j is `prefixSum[j]`
- And we've seen prefix sum `prefixSum[i]` before
- Then `prefixSum[j] - prefixSum[i] = k` means subarray (i, j] has sum k

**Formula**: `currentSum - k = requiredPrefixSum`
- If `requiredPrefixSum` exists in our map, we found subarray(s)!

**Why HashMap**:
- Store frequency of each prefix sum seen so far
- When we see currentSum, check if (currentSum - k) was seen before
- The count of (currentSum - k) tells us how many subarrays end at current position

## Algorithm Steps

**Prefix Sum with Hash Map Approach**:

1. **Initialize**:
   - Create hash map to store prefix sum frequencies
   - Add base case: `map[0] = 1` (empty subarray with sum 0)
   - `currentSum = 0`, `count = 0`

2. **For each element in array**:
   - Add current element to `currentSum`
   - Calculate `requiredSum = currentSum - k`
   - If `requiredSum` exists in map:
     - Add its frequency to count (found that many subarrays)
   - Add/update `currentSum` in map

3. **Return count**

## Dry Run (Step-by-Step Execution)

**Input**: `nums = [1,2,3,-3,1], k = 3`

**Initialization**:
- `map = {0: 1}` (base case: prefix sum 0 seen once)
- `currentSum = 0, count = 0`

```
i=0, nums[0]=1:
  currentSum = 0 + 1 = 1
  requiredSum = 1 - 3 = -2
  map[-2] doesn't exist, count = 0
  map = {0:1, 1:1}

i=1, nums[1]=2:
  currentSum = 1 + 2 = 3
  requiredSum = 3 - 3 = 0
  map[0] = 1 exists! count += 1 → count = 1
  (Found subarray [1,2] with sum 3)
  map = {0:1, 1:1, 3:1}

i=2, nums[2]=3:
  currentSum = 3 + 3 = 6
  requiredSum = 6 - 3 = 3
  map[3] = 1 exists! count += 1 → count = 2
  (Found subarray [3] with sum 3)
  map = {0:1, 1:1, 3:1, 6:1}

i=3, nums[3]=-3:
  currentSum = 6 + (-3) = 3
  requiredSum = 3 - 3 = 0
  map[0] = 1 exists! count += 1 → count = 3
  (Found subarray [1,2,3,-3] with sum 3)
  map = {0:1, 1:1, 3:2, 6:1}
  Note: map[3] is now 2 (seen prefix sum 3 twice)

i=4, nums[4]=1:
  currentSum = 3 + 1 = 4
  requiredSum = 4 - 3 = 1
  map[1] = 1 exists! count += 1 → count = 4
  (Found subarray [2,3,-3,1] with sum 3)
  map = {0:1, 1:1, 3:2, 6:1, 4:1}
```

**Final Output**: count = 4 ✓

**Found Subarrays**:
1. [1,2] (indices 0-1)
2. [3] (index 2)
3. [1,2,3,-3] (indices 0-3)
4. [2,3,-3,1] (indices 1-4)

## C++ Solution

### Code with Detailed Comments

```cpp
class Solution {
public:
    int subarraySum(vector<int>& nums, int k) {
        // Map to store frequency of each prefix sum
        // Key: prefix sum, Value: how many times we've seen this sum
        unordered_map<int, int> prefixSumCount;
        
        // Base case: prefix sum of 0 occurs once (before array starts)
        // This handles subarrays that start from index 0
        prefixSumCount[0] = 1;
        
        int currentSum = 0;  // Running prefix sum
        int count = 0;        // Count of subarrays with sum k
        
        for (int num : nums) {
            // Add current element to running sum
            currentSum += num;
            
            // Calculate what prefix sum we need to have seen before
            // If we've seen (currentSum - k) before, it means there's
            // a subarray ending at current position with sum k
            int requiredSum = currentSum - k;
            
            // If requiredSum exists in map, add its frequency to count
            // This is because each occurrence of requiredSum represents
            // a different starting point for a subarray with sum k
            if (prefixSumCount.find(requiredSum) != prefixSumCount.end()) {
                count += prefixSumCount[requiredSum];
            }
            
            // Add current prefix sum to map (or increment if exists)
            prefixSumCount[currentSum]++;
        }
        
        return count;
    }
};
```

### Alternative: More concise version

```cpp
class Solution {
public:
    int subarraySum(vector<int>& nums, int k) {
        unordered_map<int, int> prefixSumCount;
        prefixSumCount[0] = 1;
        
        int currentSum = 0, count = 0;
        
        for (int num : nums) {
            currentSum += num;
            // Directly add count if key exists (defaults to 0 if not)
            count += prefixSumCount[currentSum - k];
            prefixSumCount[currentSum]++;
        }
        
        return count;
    }
};
```

### Complexity Analysis
- **Time Complexity**: O(n) - Single pass through array. Hash map operations (insert/find) are O(1) average case.
- **Space Complexity**: O(n) - In worst case, all prefix sums are unique, so map stores n entries.

## Edge Cases
- **Single element equals k**: `nums = [5], k = 5` → Output: 1
- **No subarray with sum k**: `nums = [1,2,3], k = 10` → Output: 0
- **Array with negative numbers**: `nums = [1,-1,0], k = 0` → Output: 3
- **Multiple subarrays**: `nums = [1,1,1], k = 2` → Output: 2
- **k = 0 with zeros in array**: `nums = [0,0,0], k = 0` → Output: 6 (all subarrays)
- **Entire array sums to k**: Should be counted
- **Negative k**: Works the same way

## Key Insights & Interview Tips

**Important Observations**:
- Cannot use sliding window (array has negative numbers)
- Prefix sum transforms problem: finding subarrays → finding pairs of prefix sums
- `currentSum - k` tells us what previous sum we're looking for
- HashMap stores frequencies, not just existence (multiple subarrays can start at different positions)
- Base case `map[0] = 1` is crucial for subarrays starting from index 0

**Common Mistakes to Avoid**:
- Forgetting base case `prefixSumCount[0] = 1`
- Storing only boolean in map instead of count
- Updating map before checking for requiredSum (wrong order)
- Trying to use two-pointer/sliding window (doesn't work with negatives)
- Not handling case where entire array sums to k

**What Interviewers Look For**:
- Recognition that brute force is O(n²)
- Understanding of prefix sum concept
- Ability to explain why hash map is needed
- Knowledge of when sliding window doesn't work
- Handling of negative numbers and k=0 cases

**Follow-up Questions**:
- Q: "What if array only has positive numbers?"
- A: Could use sliding window for "sum >= k" but for "sum = k" with positives only, hash map is still simpler

- Q: "Can you do it in O(1) space?"
- A: No, we need to remember previous prefix sums. However, if array was sorted and all positive, we could use two pointers.

- Q: "How is this different from longest subarray with sum k?"
- A: Here we count all subarrays. For longest, we'd store the earliest index for each prefix sum, not frequency.

- Q: "What about product equals k?"
- A: Similar approach but more complex - need to handle zeros differently, use division instead of subtraction.

**Related Problems**:
- LeetCode 523: Continuous Subarray Sum (sum = multiple of k)
- LeetCode 525: Contiguous Array (equal 0s and 1s)
- LeetCode 974: Subarray Sums Divisible by K
