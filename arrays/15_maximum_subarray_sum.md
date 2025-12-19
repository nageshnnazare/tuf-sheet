# 15. Maximum Subarray Sum (Kadane's Algorithm)

**LeetCode**: [#53 - Maximum Subarray](https://leetcode.com/problems/maximum-subarray/)  
**Difficulty**: Medium

## Problem Statement

Given an integer array `nums`, find the contiguous subarray (containing at least one number) which has the largest sum and return its sum.

**Subarray**: A contiguous part of an array.

## Examples

### Example 1
```
Input: nums = [-2, 1, -3, 4, -1, 2, 1, -5, 4]
Output: 6
Explanation: Subarray [4, -1, 2, 1] has the largest sum = 6.
```

### Example 2
```
Input: nums = [1]
Output: 1
```

### Example 3
```
Input: nums = [5, 4, -1, 7, 8]
Output: 23
Explanation: Entire array sums to 23.
```

## Constraints
- 1 ≤ nums.length ≤ 10^5
- -10^4 ≤ nums[i] ≤ 10^4

## Visual Explanation

### ASCII Diagram - Kadane's Algorithm

```
nums = [-2, 1, -3, 4, -1, 2, 1, -5, 4]

Idea: At each position, decide:
  1. Extend current subarray
  2. Start new subarray from here

Process:
Index 0: -2
  currentSum = -2
  maxSum = -2
  Decision: Must include (only element so far)

Index 1: 1
  currentSum = max(1, -2+1) = max(1, -1) = 1
  maxSum = max(-2, 1) = 1
  Decision: Start fresh from 1 (better than extending)

Index 2: -3
  currentSum = max(-3, 1-3) = max(-3, -2) = -2
  maxSum = 1
  Decision: Extend (1-3 = -2, better than just -3)

Index 3: 4
  currentSum = max(4, -2+4) = max(4, 2) = 4
  maxSum = max(1, 4) = 4
  Decision: Start fresh from 4

Index 4-6: Continue extending [4, -1, 2, 1]
  currentSum grows to 6
  maxSum = 6

Visual:
  [-2, 1, -3, 4, -1, 2, 1, -5, 4]
            ←───────→
         maxSum = 6 (this subarray)

Key Insight:
  If current_sum becomes negative, 
  it won't help future elements,
  so start fresh!
```

## Approach & Intuition

**Kadane's Algorithm** - Dynamic Programming:

**Key Insight**: 
- At each position, choose: extend current subarray OR start new subarray
- If current_sum + nums[i] < nums[i], start fresh from nums[i]
- This is equivalent to: `current_sum = max(nums[i], current_sum + nums[i])`

**Why it works**:
- If previous sum is negative, it only reduces our total
- Better to start fresh than carrying negative baggage

## Algorithm Steps

1. Initialize currentSum = nums[0], maxSum = nums[0]
2. For i from 1 to n-1:
   - currentSum = max(nums[i], currentSum + nums[i])
   - maxSum = max(maxSum, currentSum)
3. Return maxSum

## Dry Run

```
nums = [-2, 1, -3, 4, -1, 2, 1, -5, 4]

Initial: currentSum = -2, maxSum = -2

i=1, nums[1]=1:
  currentSum = max(1, -2+1) = max(1, -1) = 1
  maxSum = max(-2, 1) = 1

i=2, nums[2]=-3:
  currentSum = max(-3, 1-3) = max(-3, -2) = -2
  maxSum = max(1, -2) = 1

i=3, nums[3]=4:
  currentSum = max(4, -2+4) = max(4, 2) = 4
  maxSum = max(1, 4) = 4

i=4, nums[4]=-1:
  currentSum = max(-1, 4-1) = max(-1, 3) = 3
  maxSum = max(4, 3) = 4

i=5, nums[5]=2:
  currentSum = max(2, 3+2) = max(2, 5) = 5
  maxSum = max(4, 5) = 5

i=6, nums[6]=1:
  currentSum = max(1, 5+1) = max(1, 6) = 6
  maxSum = max(5, 6) = 6

i=7, nums[7]=-5:
  currentSum = max(-5, 6-5) = max(-5, 1) = 1
  maxSum = max(6, 1) = 6

i=8, nums[8]=4:
  currentSum = max(4, 1+4) = max(4, 5) = 5
  maxSum = max(6, 5) = 6

Result: 6 (subarray [4,-1,2,1])
```

## C++ Solution

```cpp
#include <iostream>
#include <vector>
#include <climits>
using namespace std;

class Solution {
public:
    // Kadane's Algorithm - Optimal DP solution
    int maxSubArray(vector<int>& nums) {
        int n = nums.size();
        
        // Initialize with first element
        // currentSum tracks the maximum sum ending at current position
        int currentSum = nums[0];
        
        // maxSum tracks the overall maximum found so far
        int maxSum = nums[0];
        
        // Process rest of the array
        for (int i = 1; i < n; i++) {
            // At each position, decide:
            // 1. Extend previous subarray: currentSum + nums[i]
            // 2. Start new subarray: nums[i]
            // Choose whichever is larger
            currentSum = max(nums[i], currentSum + nums[i]);
            
            // Update global maximum if current is larger
            maxSum = max(maxSum, currentSum);
        }
        
        return maxSum;
    }
    
    // Alternative: track start and end indices
    struct SubarrayInfo {
        int sum;
        int start;
        int end;
    };
    
    SubarrayInfo maxSubArrayWithIndices(vector<int>& nums) {
        int n = nums.size();
        int currentSum = nums[0];
        int maxSum = nums[0];
        
        int start = 0, end = 0;      // Final subarray boundaries
        int tempStart = 0;           // Temporary start for current subarray
        
        for (int i = 1; i < n; i++) {
            // If starting fresh is better
            if (nums[i] > currentSum + nums[i]) {
                currentSum = nums[i];
                tempStart = i;  // Reset start position
            } else {
                currentSum = currentSum + nums[i];
            }
            
            // Update maximum and boundaries
            if (currentSum > maxSum) {
                maxSum = currentSum;
                start = tempStart;
                end = i;
            }
        }
        
        return {maxSum, start, end};
    }
    
    // For comparison: Brute Force O(n²)
    int maxSubArrayBrute(vector<int>& nums) {
        int n = nums.size();
        int maxSum = INT_MIN;
        
        // Try all possible subarrays
        for (int i = 0; i < n; i++) {
            int currentSum = 0;
            for (int j = i; j < n; j++) {
                currentSum += nums[j];
                maxSum = max(maxSum, currentSum);
            }
        }
        
        return maxSum;
    }
};

int main() {
    Solution sol;
    
    // Test Case 1
    vector<int> nums1 = {-2, 1, -3, 4, -1, 2, 1, -5, 4};
    cout << "Array: [-2, 1, -3, 4, -1, 2, 1, -5, 4]" << endl;
    cout << "Max subarray sum: " << sol.maxSubArray(nums1) << endl;
    
    auto info = sol.maxSubArrayWithIndices(nums1);
    cout << "Subarray: [" << info.start << " to " << info.end << "]" << endl;
    cout << "Elements: [";
    for (int i = info.start; i <= info.end; i++) {
        cout << nums1[i];
        if (i < info.end) cout << ", ";
    }
    cout << "]" << endl << endl;
    
    // Test Case 2: All negative
    vector<int> nums2 = {-2, -1, -3};
    cout << "Array: [-2, -1, -3]" << endl;
    cout << "Max subarray sum: " << sol.maxSubArray(nums2) << " (least negative)" << endl << endl;
    
    // Test Case 3: All positive
    vector<int> nums3 = {5, 4, -1, 7, 8};
    cout << "Array: [5, 4, -1, 7, 8]" << endl;
    cout << "Max subarray sum: " << sol.maxSubArray(nums3) << " (entire array)" << endl;
    
    return 0;
}
```

### Complexity Analysis

**Kadane's Algorithm**:
- **Time**: O(n) - Single pass through array
- **Space**: O(1) - Only two variables

**Brute Force**:
- **Time**: O(n²) - Check all subarrays
- **Space**: O(1)

## Edge Cases

1. **All negative**: Return least negative number
2. **All positive**: Return sum of entire array  
3. **Single element**: Return that element
4. **Mix**: Algorithm handles naturally

## Key Insights & Interview Tips

1. **Kadane's Algorithm** - Classic DP problem
2. **Greedy Choice**: At each step, extend or start fresh
3. **Reset Logic**: If sum goes negative, drop it
4. **When to Reset**: `currentSum < 0` OR `nums[i] > currentSum + nums[i]`
5. **Follow-ups**:
   - "Return the subarray itself?" → Track start/end indices
   - "Maximum circular subarray?" → Handle wrap-around
   - "At least k elements?" → Modified approach
6. **Related**: Maximum product subarray, Best time to buy/sell stock

