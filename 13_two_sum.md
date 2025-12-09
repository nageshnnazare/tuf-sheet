# 13. Two Sum

**LeetCode**: [#1 - Two Sum](https://leetcode.com/problems/two-sum/)  
**Difficulty**: Easy

## Problem Statement

Given an array of integers `nums` and an integer `target`, return the **indices** of the two numbers such that they add up to `target`.

**Assumptions**:
- Each input has **exactly one solution**
- You may not use the same element twice
- Return answer in any order

## Examples

### Example 1
```
Input: nums = [2, 7, 11, 15], target = 9
Output: [0, 1]
Explanation: nums[0] + nums[1] = 2 + 7 = 9
```

### Example 2
```
Input: nums = [3, 2, 4], target = 6
Output: [1, 2]
Explanation: nums[1] + nums[2] = 2 + 4 = 6
```

### Example 3
```
Input: nums = [3, 3], target = 6
Output: [0, 1]
```

## Constraints
- 2 ≤ nums.length ≤ 10^4
- -10^9 ≤ nums[i] ≤ 10^9
- -10^9 ≤ target ≤ 10^9
- Only one valid answer exists

## Visual Explanation

```
nums = [2, 7, 11, 15], target = 9

Hash Map Approach:

map = {}

Index 0: nums[0] = 2
  Looking for: 9 - 2 = 7
  7 not in map
  Store: map[2] = 0
  map = {2: 0}

Index 1: nums[1] = 7
  Looking for: 9 - 7 = 2
  2 found in map at index 0! ✓
  Return [0, 1]

Visual:
  nums:    [2,  7, 11, 15]
  indices:  0   1   2   3
            ↓   ↓
         2 + 7 = 9 ✓
```

## Approach & Intuition

**Hash Map** (Optimal):

**Key Insight**: For each number `x`, check if `target - x` exists in hash map.

**Why it works**: 
- If a + b = target, then b = target - a
- Store seen numbers with their indices
- When checking new number, look for complement

## Algorithm Steps

1. Create hash map {value: index}
2. For each `nums[i]`:
   - complement = target - nums[i]
   - If complement in map: return [map[complement], i]
   - Store map[nums[i]] = i
3. Return empty (won't reach if solution guaranteed)

## Dry Run

```
nums = [2, 7, 11, 15], target = 9
map = {}

Iteration 0: i=0, nums[0]=2
  complement = 9 - 2 = 7
  7 not in map
  map[2] = 0
  map = {2: 0}

Iteration 1: i=1, nums[1]=7
  complement = 9 - 7 = 2
  2 found in map!
  map[2] = 0 (index of 2)
  Return [0, 1] ✓

Result: [0, 1]
```

## C++ Solution

```cpp
#include <iostream>
#include <vector>
#include <unordered_map>
using namespace std;

class Solution {
public:
    // Optimal: Hash Map approach
    vector<int> twoSum(vector<int>& nums, int target) {
        // Hash map: {value -> index}
        unordered_map<int, int> numMap;
        
        for (int i = 0; i < nums.size(); i++) {
            // Calculate complement needed
            int complement = target - nums[i];
            
            // Check if complement exists in map
            if (numMap.find(complement) != numMap.end()) {
                // Found! Return both indices
                // complement's index is stored in map
                // current number's index is i
                return {numMap[complement], i};
            }
            
            // Store current number with its index
            // For future iterations to find
            numMap[nums[i]] = i;
        }
        
        // Should never reach here if solution guaranteed
        return {};
    }
    
    // Alternative: Brute Force (for comparison)
    // Time: O(n²), Space: O(1)
    vector<int> twoSumBruteForce(vector<int>& nums, int target) {
        int n = nums.size();
        
        // Check every pair
        for (int i = 0; i < n; i++) {
            for (int j = i + 1; j < n; j++) {
                if (nums[i] + nums[j] == target) {
                    return {i, j};
                }
            }
        }
        
        return {};
    }
};

int main() {
    Solution sol;
    
    // Test Case 1
    vector<int> nums1 = {2, 7, 11, 15};
    vector<int> result1 = sol.twoSum(nums1, 9);
    cout << "Array: [2, 7, 11, 15], target=9" << endl;
    cout << "Indices: [" << result1[0] << ", " << result1[1] << "]" << endl << endl;
    
    // Test Case 2
    vector<int> nums2 = {3, 2, 4};
    vector<int> result2 = sol.twoSum(nums2, 6);
    cout << "Array: [3, 2, 4], target=6" << endl;
    cout << "Indices: [" << result2[0] << ", " << result2[1] << "]" << endl << endl;
    
    // Test Case 3: Duplicates
    vector<int> nums3 = {3, 3};
    vector<int> result3 = sol.twoSum(nums3, 6);
    cout << "Array: [3, 3], target=6" << endl;
    cout << "Indices: [" << result3[0] << ", " << result3[1] << "]" << endl;
    
    return 0;
}
```

### Complexity Analysis

**Hash Map Approach**:
- **Time**: O(n) - Single pass, hash map ops are O(1)
- **Space**: O(n) - Hash map stores up to n elements

**Brute Force**:
- **Time**: O(n²) - Nested loops
- **Space**: O(1) - No extra space

## Edge Cases

1. **Two same numbers**: `[3, 3], target=6` → `[0, 1]`
2. **Negative numbers**: `[-1, -2, -3, -4], target=-6` → Works
3. **Zero sum**: `[-1, 0, 1], target=0` → `[0, 2]`
4. **Large array**: Hash map still O(n)

## Key Insights & Interview Tips

1. **Classic Problem**: One of the most famous coding interview problems

2. **Approaches**:
   ```
   | Method     | Time  | Space | Notes           |
   |------------|-------|-------|-----------------|
   | Brute Force| O(n²) | O(1)  | Too slow        |
   | Hash Map   | O(n)  | O(n)  | Optimal ⭐      |
   | Two Pointer| O(nlogn)| O(1)| If sorted       |
   ```

3. **Why Not Sort?**:
   - Sorting changes indices!
   - Would need to track original indices
   - Hash map is simpler

4. **Common Mistakes**:
   - Using same element twice (check i != j)
   - Returning values instead of indices
   - Not handling duplicates

5. **Follow-ups**:
   - "Return values not indices?" → Simpler, just return [nums[i], nums[j]]
   - "Find all pairs?" → Store all in result vector
   - "Three Sum?" → See LeetCode #15
   - "Is array sorted?" → Use two pointers

6. **Related Problems**:
   - LeetCode #15: Three Sum
   - LeetCode #167: Two Sum II (sorted array)
   - LeetCode #653: Two Sum IV (BST)

7. **Interview Pro Tip**: Mention brute force first, then optimize to hash map to show problem-solving progression!

