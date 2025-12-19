# 28. Three Sum
**LeetCode**: [15 - 3Sum](https://leetcode.com/problems/3sum/)  
**Difficulty**: Medium

## Problem Statement
Given an integer array `nums`, return all the triplets `[nums[i], nums[j], nums[k]]` such that:
- `i != j`, `i != k`, and `j != k`
- `nums[i] + nums[j] + nums[k] == 0`

The solution set must not contain duplicate triplets.

## Examples

### Example 1
```
Input: nums = [-1,0,1,2,-1,-4]
Output: [[-1,-1,2],[-1,0,1]]
Explanation:
nums[0] + nums[1] + nums[2] = (-1) + 0 + 1 = 0
nums[1] + nums[2] + nums[4] = 0 + 1 + (-1) = 0
nums[0] + nums[3] + nums[4] = (-1) + 2 + (-1) = 0
The distinct triplets are [-1,0,1] and [-1,-1,2]
```

### Example 2
```
Input: nums = [0,1,1]
Output: []
Explanation: The only possible triplet does not sum up to 0
```

### Example 3
```
Input: nums = [0,0,0]
Output: [[0,0,0]]
Explanation: The only possible triplet sums up to 0
```

## Constraints
- `3 <= nums.length <= 3000`
- `-10^5 <= nums[i] <= 10^5`

## Visual Explanation

### ASCII Diagram
```
Strategy: Sort + Fix one element + Two Pointer

Sorted array: [-4, -1, -1, 0, 1, 2]
               ↑
              Fix i

For each i, find j and k such that nums[i] + nums[j] + nums[k] = 0

i=0, nums[i]=-4:
[-4, -1, -1, 0, 1, 2]
  ↑   ↑           ↑
  i   j           k
  
  Target: nums[j] + nums[k] = -nums[i] = 4
  -1 + 2 = 1 < 4 → move j right
  
[-4, -1, -1, 0, 1, 2]
  ↑       ↑       ↑
  i       j       k
  
  -1 + 2 = 1 < 4 → move j right
  Continue... no solution for i=0

i=1, nums[i]=-1:
[-4, -1, -1, 0, 1, 2]
      ↑   ↑       ↑
      i   j       k
  
  Target: nums[j] + nums[k] = 1
  -1 + 2 = 1 ✓ Found: [-1, -1, 2]
  Move both pointers and skip duplicates
  
[-4, -1, -1, 0, 1, 2]
      ↑       ↑   ↑
      i       j   k
  
  0 + 1 = 1 ✓ Found: [-1, 0, 1]

Duplicate Handling:
- Skip duplicate values for i
- Skip duplicate values for j and k after finding a triplet
```

## Approach & Intuition

**Brute Force**: Try all triplets - O(n³) time

**Optimized Approach - Sort + Two Pointers**:

**Key Insights**:
1. **Sort the array**: Enables two-pointer technique and easy duplicate skipping
2. **Fix one element**: For each element `i`, find pairs in remaining array that sum to `-nums[i]`
3. **Two pointers**: For the remaining subarray, use left and right pointers
4. **Skip duplicates**: After sorting, duplicates are adjacent - easy to skip

**Why sorting helps**:
- Two-pointer technique works on sorted arrays
- Duplicates become adjacent (easy to skip)
- Can optimize: if `nums[i] > 0`, no point continuing (all remaining sums will be positive)

**Duplicate handling**:
- Skip duplicate `i` values: if `nums[i] == nums[i-1]`, skip
- After finding a triplet, skip duplicate `j` and `k` values

## Algorithm Steps

1. **Sort the array** in ascending order

2. **For each index i from 0 to n-3**:
   
   a. **Skip duplicates for i**: 
      - If i > 0 and nums[i] == nums[i-1], continue
   
   b. **Optimization**: If nums[i] > 0, break (all remaining positive)
   
   c. **Two-pointer search** for remaining array:
      - Initialize: left = i+1, right = n-1
      - Target sum = -nums[i]
      
   d. **While left < right**:
      - Calculate: sum = nums[left] + nums[right]
      
      - If sum == target:
        * Add triplet [nums[i], nums[left], nums[right]]
        * Skip duplicates: move left while nums[left] == nums[left+1]
        * Skip duplicates: move right while nums[right] == nums[right-1]
        * Move both pointers
      
      - If sum < target: move left++
      
      - If sum > target: move right--

3. **Return result**

## Dry Run (Step-by-Step Execution)

**Input**: `nums = [-1,0,1,2,-1,-4]`

**Step 1: Sort**
```
Original: [-1, 0, 1, 2, -1, -4]
Sorted:   [-4, -1, -1, 0, 1, 2]
```

**Step 2: Iterate with two pointers**

```
i=0, nums[0]=-4, target=4
  [-4, -1, -1, 0, 1, 2]
    ↑   L           R
  sum = -1+2 = 1 < 4 → L++
  
  [-4, -1, -1, 0, 1, 2]
    ↑       L       R
  sum = -1+2 = 1 < 4 → L++
  
  [-4, -1, -1, 0, 1, 2]
    ↑           L   R
  sum = 0+2 = 2 < 4 → L++
  
  [-4, -1, -1, 0, 1, 2]
    ↑               LR
  sum = 1+2 = 3 < 4 → L++
  L >= R, exit

i=1, nums[1]=-1, target=1
  [-4, -1, -1, 0, 1, 2]
        ↑   L       R
  sum = -1+2 = 1 == target ✓
  Found: [-1, -1, 2]
  Skip duplicates at L: -1 == -1, L++
  L++, R--
  
  [-4, -1, -1, 0, 1, 2]
        ↑       L   R
  sum = 0+1 = 1 == target ✓
  Found: [-1, 0, 1]
  L++, R--
  
  [-4, -1, -1, 0, 1, 2]
        ↑         LR
  L >= R, exit

i=2, nums[2]=-1
  nums[2] == nums[1], skip duplicate i

i=3, nums[3]=0, target=0
  [-4, -1, -1, 0, 1, 2]
                ↑ L   R
  sum = 1+2 = 3 > 0 → R--
  
  [-4, -1, -1, 0, 1, 2]
                ↑ LR
  L >= R, exit

i=4, nums[4]=1 > 0, break (optimization)
```

**Final Result**: `[[-1,-1,2], [-1,0,1]]` ✓

## C++ Solution

### Code with Detailed Comments

```cpp
class Solution {
public:
    vector<vector<int>> threeSum(vector<int>& nums) {
        vector<vector<int>> result;
        int n = nums.size();
        
        // Step 1: Sort the array to enable two-pointer technique
        // and make duplicate skipping easier
        sort(nums.begin(), nums.end());
        
        // Step 2: Iterate through array, fixing one element at a time
        // We go up to n-2 because we need at least 2 elements after i
        for (int i = 0; i < n - 2; i++) {
            // Skip duplicate values for i to avoid duplicate triplets
            // We check i > 0 to avoid out of bounds on first iteration
            if (i > 0 && nums[i] == nums[i - 1]) {
                continue;
            }
            
            // Optimization: if current number is positive,
            // all remaining numbers are also positive (array is sorted)
            // so sum can never be 0
            if (nums[i] > 0) {
                break;
            }
            
            // Two-pointer approach for the remaining subarray
            int left = i + 1;      // Start from element after i
            int right = n - 1;      // Start from end of array
            int target = -nums[i];  // We need nums[left] + nums[right] = target
            
            while (left < right) {
                int sum = nums[left] + nums[right];
                
                if (sum == target) {
                    // Found a valid triplet
                    result.push_back({nums[i], nums[left], nums[right]});
                    
                    // Skip all duplicate values of nums[left]
                    // This prevents duplicate triplets like [-1,-1,2] appearing twice
                    while (left < right && nums[left] == nums[left + 1]) {
                        left++;
                    }
                    
                    // Skip all duplicate values of nums[right]
                    // Same reason as above
                    while (left < right && nums[right] == nums[right - 1]) {
                        right--;
                    }
                    
                    // Move both pointers after finding a solution
                    // and skipping duplicates
                    left++;
                    right--;
                }
                else if (sum < target) {
                    // Sum is too small, need larger values
                    // Move left pointer right to increase sum
                    left++;
                }
                else {
                    // Sum is too large, need smaller values
                    // Move right pointer left to decrease sum
                    right--;
                }
            }
        }
        
        return result;
    }
};
```

### Complexity Analysis
- **Time Complexity**: O(n²) - Sorting takes O(n log n). The outer loop runs O(n) times, and for each iteration, the two-pointer search takes O(n). Total: O(n log n + n²) = O(n²)
- **Space Complexity**: O(1) or O(n) - Depends on sorting algorithm. If we don't count output space, only O(1) extra space is used. Some sorting algorithms use O(log n) or O(n) space.

## Edge Cases
- **All zeros**: `[0,0,0,0]` → `[[0,0,0]]` (only one unique triplet)
- **No solution**: `[1,2,3]` → `[]` (all positive, cannot sum to 0)
- **Exact 3 elements**: `[-1,0,1]` → `[[-1,0,1]]`
- **Many duplicates**: `[-1,-1,-1,0,1,1,1]` → `[[-1,0,1]]` (duplicates handled correctly)
- **Two positives, one negative**: Common pattern
- **Large array**: Should handle up to 3000 elements efficiently

## Key Insights & Interview Tips

**Important Observations**:
- Sorting is key to enabling two-pointer technique
- Fixing one element reduces 3Sum to 2Sum problem
- Must handle duplicates carefully to avoid duplicate triplets
- Can optimize early exit when nums[i] > 0

**Common Mistakes to Avoid**:
- Not skipping duplicates (produces duplicate triplets)
- Skipping duplicates incorrectly (might miss valid triplets)
- Using set to remove duplicates (slower and uses more space)
- Not handling i > 0 check before nums[i] == nums[i-1]
- Forgetting to move both pointers after finding a triplet
- Wrong boundary conditions (i < n-2, not i < n)

**What Interviewers Look For**:
- Recognition that sorting enables optimization
- Understanding of two-pointer technique
- Proper duplicate handling without using extra space
- Clear explanation of time complexity reduction from O(n³) to O(n²)
- Clean code with good variable names

**Follow-up Questions**:
- Q: "Can you do better than O(n²)?"
- A: No, O(n²) is optimal. Must examine all pairs, which is inherently O(n²).

- Q: "What if you can't modify the input array?"
- A: Create a copy and sort it. Space complexity becomes O(n).

- Q: "How would you solve 3Sum Closest (find sum closest to target)?"
- A: Similar approach, but track minimum difference instead of exact match.

- Q: "What about 4Sum?"
- A: Similar approach but with two nested loops, reducing to 2Sum. O(n³) time.

**Related Problems**:
- LeetCode 1: Two Sum
- LeetCode 16: 3Sum Closest
- LeetCode 18: 4Sum
- LeetCode 259: 3Sum Smaller

**Why Two Pointers Work Here**:
```
Sorted array: [-4, -1, 0, 1, 2]
Fix i=-1, target=1

If sum < target: left pointer must move right (need larger value)
If sum > target: right pointer must move left (need smaller value)
If sum == target: found solution, move both to find more

This works because array is sorted!
```
