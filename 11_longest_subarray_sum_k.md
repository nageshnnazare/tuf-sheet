# 11. Longest Sub-array with Sum K

**LeetCode**: [#325 - Maximum SizeSubarray Sum Equals k](https://leetcode.com/problems/maximum-size-subarray-sum-equals-k/) (Premium) / Similar: [#560 - Subarray Sum Equals K](https://leetcode.com/problems/subarray-sum-equals-k/)  
**Difficulty**: Medium

## Problem Statement

Given an array of integers and a target sum `k`, find the length of the longest contiguous subarray whose sum equals `k`.

## Examples

### Example 1
```
Input: arr = [1, 2, 3, 1, 1, 1, 1, 4, 2, 3], k = 3
Output: 3
Explanation: Subarray [1, 1, 1] has sum 3 and length 3.
```

### Example 2
```
Input: arr = [1, -1, 5, -2, 3], k = 3
Output: 4
Explanation: Subarray [1, -1, 5, -2] sums to 3 with length 4.
```

### Example 3
```
Input: arr = [1, 2, 3], k = 10
Output: 0
Explanation: No subarray sums to 10.
```

## Constraints
- 1 ≤ arr.length ≤ 10^5
- -10^9 ≤ arr[i] ≤ 10^9
- -10^9 ≤ k ≤ 10^9

## Visual Explanation

```
arr = [1, 2, 3, 1, 1, 1, 1, 4, 2, 3], k = 3

Approach: Prefix Sum + Hash Map

prefix_sum array:
index:  0  1  2  3  4  5  6  7  8  9
arr:    1  2  3  1  1  1  1  4  2  3
prefix: 1  3  6  7  8  9 10 14 16 19

Looking for subarrays with sum = 3:

If prefix[j] - prefix[i-1] = k
Then prefix[i-1] = prefix[j] - k

At index 6 (prefix=10):
  Looking for prefix = 10 - 3 = 7
  Found at index 3!
  Length = 6 - 3 = 3
  Subarray: [1,1,1] from index 4 to 6

Visual:
  [1, 2, 3, 1, 1, 1, 1, 4, 2, 3]
           ↑  ←─────→  ↑
         i=3    sum=3   j=6
              (length 3)
```

## Approach & Intuition

**Prefix Sum + Hash Map** (for arrays with negative numbers):

1. Calculate prefix sum at each position
2. Use hash map to store first occurrence of each prefix sum
3. For current prefix_sum, check if (prefix_sum - k) exists
4. If exists, calculate length and update max

**Key Insight**: If prefix[j] - prefix[i] = k, then subarray from i+1 to j has sum k.

## Algorithm Steps

1. Initialize map{0: -1}, maxLen = 0, prefixSum = 0
2. For each index i:
   - Add arr[i] to prefixSum
   - If (prefixSum - k) in map:
     - length = i - map[prefixSum - k]
     - maxLen = max(maxLen, length)
   - If prefixSum not in map:
     - Store map[prefixSum] = i
3. Return maxLen

## Dry Run

Array: `[1, 2, 3, 1, 1, 1]`, k = 3

```
map = {0: -1}, maxLen = 0, prefixSum = 0

i=0, arr[0]=1:
  prefixSum = 1
  Need: 1-3 = -2 (not in map)
  map = {0:-1, 1:0}, maxLen = 0

i=1, arr[1]=2:
  prefixSum = 3
  Need: 3-3 = 0 (found at -1!)
  length = 1-(-1) = 2 [subarray: 1,2]
  maxLen = 2
  map = {0:-1, 1:0, 3:1}

i=2, arr[2]=3:
  prefixSum = 6
  Need: 6-3 = 3 (found at 1!)
  length = 2-1 = 1 [subarray: 3]
  maxLen = 2 (no update)
  map = {0:-1, 1:0, 3:1, 6:2}

i=3, arr[3]=1:
  prefixSum = 7
  Need: 7-3 = 4 (not found)
  map = {..., 7:3}

i=4, arr[4]=1:
  prefixSum = 8
  Need: 8-3 = 5 (not found)
  map = {..., 8:4}

i=5, arr[5]=1:
  prefixSum = 9
  Need: 9-3 = 6 (found at 2!)
  length = 5-2 = 3 [subarray: 1,1,1]
  maxLen = 3
  
Result: 3
```

## C++ Solution

```cpp
#include <iostream>
#include <vector>
#include <unordered_map>
using namespace std;

class Solution {
public:
    int longestSubarraySum(vector<int>& arr, int k) {
        // Hash map to store first occurrence of each prefix sum
        // Key: prefix sum, Value: index where it first occurred
        unordered_map<int, int> prefixMap;
        
        // Initialize with sum 0 at index -1
        // This handles subarrays starting from index 0
        prefixMap[0] = -1;
        
        int prefixSum = 0;  // Running sum
        int maxLen = 0;     // Maximum length found
        
        for (int i = 0; i < arr.size(); i++) {
            // Add current element to prefix sum
            prefixSum += arr[i];
            
            // Check if there's a previous prefix sum such that
            // prefixSum - previousSum = k
            // This means subarray from (previousIndex+1) to i has sum k
            int target = prefixSum - k;
            
            if (prefixMap.find(target) != prefixMap.end()) {
                // Found a subarray with sum k
                int length = i - prefixMap[target];
                maxLen = max(maxLen, length);
            }
            
            // Store prefix sum only if it hasn't been seen before
            // We want the leftmost occurrence for maximum length
            if (prefixMap.find(prefixSum) == prefixMap.end()) {
                prefixMap[prefixSum] = i;
            }
        }
        
        return maxLen;
    }
    
    // For positive numbers only: Sliding Window (more efficient)
    int longestSubarraySumPositive(vector<int>& arr, int k) {
        int left = 0, right = 0;
        int sum = 0;
        int maxLen = 0;
        
        while (right < arr.size()) {
            sum += arr[right];
            
            // Shrink window while sum > k
            while (left <= right && sum > k) {
                sum -= arr[left];
                left++;
            }
            
            // Check if current window has sum k
            if (sum == k) {
                maxLen = max(maxLen, right - left + 1);
            }
            
            right++;
        }
        
        return maxLen;
    }
};

int main() {
    Solution sol;
    
    vector<int> arr1 = {1, 2, 3, 1, 1, 1, 1};
    cout << "Array: [1, 2, 3, 1, 1, 1, 1], k=3" << endl;
    cout << "Longest length: " << sol.longestSubarraySum(arr1, 3) << endl << endl;
    
    vector<int> arr2 = {1, -1, 5, -2, 3};
    cout << "Array: [1, -1, 5, -2, 3], k=3" << endl;
    cout << "Longest length: " << sol.longestSubarraySum(arr2, 3) << endl;
    
    return 0;
}
```

### Complexity Analysis

- **Time**: O(n) - Single pass with hash map operations
- **Space**: O(n) - Hash map stores up to n prefix sums

## Key Insights

1. **Prefix Sum Pattern**: Essential for subarray sum problems
2. **Hash Map**: Stores first occurrence for maximum length
3. **For Positives Only**: Use sliding window O(1) space
4. **With Negatives**: Must use prefix sum + hash map

