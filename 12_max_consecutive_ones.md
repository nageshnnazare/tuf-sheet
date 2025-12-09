# 12. Max Consecutive Ones

**LeetCode**: [#485 - Max Consecutive Ones](https://leetcode.com/problems/max-consecutive-ones/)  
**Difficulty**: Easy

## Problem Statement

Given a binary array `nums`, return the maximum number of consecutive `1`s in the array.

## Examples

### Example 1
```
Input: nums = [1, 1, 0, 1, 1, 1]
Output: 3
Explanation: First two 1s and last three 1s. Max is 3.
```

### Example 2
```
Input: nums = [1, 0, 1, 1, 0, 1]
Output: 2
```

## Constraints
- 1 ≤ nums.length ≤ 10^5
- nums[i] is either 0 or 1

## Visual Explanation

```
nums = [1, 1, 0, 1, 1, 1]

Process:
  [1, 1, 0, 1, 1, 1]
   ↑  ↑     ↑  ↑  ↑
   count=1,2  count=1,2,3
   
Index 0: 1 → count=1, max=1
Index 1: 1 → count=2, max=2
Index 2: 0 → reset count=0
Index 3: 1 → count=1
Index 4: 1 → count=2
Index 5: 1 → count=3, max=3

Result: 3
```

## Approach & Intuition

Simple counter:
1. Track current count of consecutive 1s
2. Reset count when 0 is encountered
3. Update maximum count

## Dry Run

```
nums = [1, 1, 0, 1, 1, 1]
count = 0, maxCount = 0

i=0, nums[0]=1:
  count = 1
  maxCount = max(0, 1) = 1

i=1, nums[1]=1:
  count = 2
  maxCount = max(1, 2) = 2

i=2, nums[2]=0:
  count = 0 (reset)
  maxCount = 2

i=3, nums[3]=1:
  count = 1
  maxCount = 2

i=4, nums[4]=1:
  count = 2
  maxCount = 2

i=5, nums[5]=1:
  count = 3
  maxCount = max(2, 3) = 3

Result: 3
```

## C++ Solution

```cpp
#include <iostream>
#include <vector>
using namespace std;

class Solution {
public:
    int findMaxConsecutiveOnes(vector<int>& nums) {
        int maxCount = 0;      // Maximum consecutive 1s found
        int currentCount = 0;  // Current streak of 1s
        
        for (int i = 0; i < nums.size(); i++) {
            if (nums[i] == 1) {
                // Increment current streak
                currentCount++;
                // Update maximum if current is larger
                maxCount = max(maxCount, currentCount);
            } else {
                // Found a 0, reset current streak
                currentCount = 0;
            }
        }
        
        return maxCount;
    }
    
    // Alternative concise version
    int findMaxConsecutiveOnesConcise(vector<int>& nums) {
        int maxCount = 0, count = 0;
        
        for (int num : nums) {
            count = (num == 1) ? count + 1 : 0;
            maxCount = max(maxCount, count);
        }
        
        return maxCount;
    }
};

int main() {
    Solution sol;
    
    vector<int> nums1 = {1, 1, 0, 1, 1, 1};
    cout << "Array: [1, 1, 0, 1, 1, 1]" << endl;
    cout << "Max consecutive ones: " << sol.findMaxConsecutiveOnes(nums1) << endl << endl;
    
    vector<int> nums2 = {1, 0, 1, 1, 0, 1};
    cout << "Array: [1, 0, 1, 1, 0, 1]" << endl;
    cout << "Max consecutive ones: " << sol.findMaxConsecutiveOnes(nums2) << endl;
    
    return 0;
}
```

### Complexity
- **Time**: O(n) - Single pass
- **Space**: O(1) - Only two variables

## Key Insights

1. **Reset Pattern**: Reset counter on encountering 0
2. **Update Maximum**: Check max after each 1
3. **Follow-up**: "Max consecutive 1s with at most k flips" → Sliding window

