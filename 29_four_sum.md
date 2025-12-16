# 29. Four Sum
**LeetCode**: [18 - 4Sum](https://leetcode.com/problems/4sum/)  
**Difficulty**: Medium

## Problem Statement
Given an array `nums` of `n` integers, return an array of all the unique quadruplets `[nums[a], nums[b], nums[c], nums[d]]` such that:
- `0 <= a, b, c, d < n`
- `a`, `b`, `c`, and `d` are distinct
- `nums[a] + nums[b] + nums[c] + nums[d] == target`

You may return the answer in any order. The solution set must not contain duplicate quadruplets.

## Examples

### Example 1
```
Input: nums = [1,0,-1,0,-2,2], target = 0
Output: [[-2,-1,1,2],[-2,0,0,2],[-1,0,0,1]]
```

### Example 2
```
Input: nums = [2,2,2,2,2], target = 8
Output: [[2,2,2,2]]
```

### Example 3
```
Input: nums = [], target = 0
Output: []
```

## Constraints
- `1 <= nums.length <= 200`
- `-10^9 <= nums[i] <= 10^9`
- `-10^9 <= target <= 10^9`

## Visual Explanation

### ASCII Diagram
```
Strategy: Sort + Fix two elements + Two Pointer (generalization of 3Sum)

Sorted array: [-2, -1, 0, 0, 1, 2], target = 0

Fix i and j, then use two pointers for remaining array:

i=0, j=1, nums[i]=-2, nums[j]=-1:
  [-2, -1, 0, 0, 1, 2]
    ↑   ↑  L      R
    i   j
  
  Target for L and R: 0 - (-2) - (-1) = 3
  nums[L] + nums[R] = 0 + 2 = 2 < 3 → L++
  
  [-2, -1, 0, 0, 1, 2]
    ↑   ↑     L   R
  
  0 + 2 = 2 < 3 → L++
  
  [-2, -1, 0, 0, 1, 2]
    ↑   ↑        LR
  
  1 + 2 = 3 ✓ Found: [-2, -1, 1, 2]

i=0, j=2, nums[i]=-2, nums[j]=0:
  [-2, -1, 0, 0, 1, 2]
    ↑      ↑  L   R
    i      j
  
  Target: 0 - (-2) - 0 = 2
  0 + 2 = 2 ✓ Found: [-2, 0, 0, 2]

Continue for all i, j combinations...

Duplicate Handling:
- Skip duplicate i values
- Skip duplicate j values
- Skip duplicate L and R values after finding quadruplet
```

## Approach & Intuition

**Brute Force**: Try all quadruplets - O(n⁴) time

**Optimized Approach - Sort + Two Nested Loops + Two Pointers**:

**Key Insights**:
1. **Generalization of 3Sum**: Fix two elements instead of one
2. **Sort first**: Enables two-pointer technique and duplicate skipping
3. **Reduce to 2Sum**: After fixing i and j, use two pointers for remaining
4. **Skip duplicates**: At all four levels (i, j, left, right)

**Structure**:
- 4Sum = Fix one element + 3Sum
- 3Sum = Fix one element + 2Sum
- 2Sum = Two pointers

**Optimization**:
- Early termination when sum cannot possibly equal target
- Skip duplicate values at each level

## Algorithm Steps

1. **Sort the array** in ascending order

2. **For each index i from 0 to n-4**:
   
   a. **Skip duplicates for i**:
      - If i > 0 and nums[i] == nums[i-1], continue
   
   b. **Early termination check**:
      - If nums[i] + nums[i+1] + nums[i+2] + nums[i+3] > target, break
      - If nums[i] + nums[n-3] + nums[n-2] + nums[n-1] < target, continue
   
   c. **For each index j from i+1 to n-3**:
      
      i. **Skip duplicates for j**:
         - If j > i+1 and nums[j] == nums[j-1], continue
      
      ii. **Early termination check** (similar to above)
      
      iii. **Two-pointer search**:
         - Initialize: left = j+1, right = n-1
         - Target sum = target - nums[i] - nums[j]
         
      iv. **While left < right**:
         - Calculate: sum = nums[left] + nums[right]
         
         - If sum == target:
           * Add quadruplet [nums[i], nums[j], nums[left], nums[right]]
           * Skip duplicates for left and right
           * Move both pointers
         
         - If sum < target: left++
         - If sum > target: right--

3. **Return result**

## Dry Run (Step-by-Step Execution)

**Input**: `nums = [1,0,-1,0,-2,2], target = 0`

**Step 1: Sort**
```
Original: [1, 0, -1, 0, -2, 2]
Sorted:   [-2, -1, 0, 0, 1, 2]
```

**Step 2: Nested loops with two pointers**

```
i=0, nums[0]=-2:

  j=1, nums[1]=-1:
    Target for L,R: 0-(-2)-(-1) = 3
    [-2, -1, 0, 0, 1, 2]
          ↑  L      R
    
    L=2, R=5: 0+2 = 2 < 3 → L++
    L=3, R=5: 0+2 = 2 < 3 → L++
    L=4, R=5: 1+2 = 3 ✓ Found: [-2,-1,1,2]
    L++, R--
    L=5, R=4: L >= R, exit
  
  j=2, nums[2]=0:
    Target: 0-(-2)-0 = 2
    [-2, -1, 0, 0, 1, 2]
              ↑  L   R
    
    L=3, R=5: 0+2 = 2 ✓ Found: [-2,0,0,2]
    Skip duplicate L: 0==0 (no more)
    L++, R--
    L=4, R=4: L >= R, exit
  
  j=3, nums[3]=0:
    Skip duplicate j (0 == 0)

  j=4, nums[4]=1:
    Target: 0-(-2)-1 = 1
    [-2, -1, 0, 0, 1, 2]
                    ↑  LR
    L=5, R=5: L >= R, exit

i=1, nums[1]=-1:

  j=2, nums[2]=0:
    Target: 0-(-1)-0 = 1
    [-2, -1, 0, 0, 1, 2]
              ↑  L   R
    
    L=3, R=5: 0+2 = 2 > 1 → R--
    L=3, R=4: 0+1 = 1 ✓ Found: [-1,0,0,1]
    L++, R--
    L=4, R=3: L >= R, exit
  
  j=3: Skip duplicate (0 == 0)
  
  j=4, nums[4]=1:
    Target: 0-(-1)-1 = 0
    [-2, -1, 0, 0, 1, 2]
                    ↑  LR
    L=5, R=5: L >= R, exit

i=2: Remaining elements < 4, exit
```

**Final Result**: `[[-2,-1,1,2], [-2,0,0,2], [-1,0,0,1]]` ✓

## C++ Solution

### Code with Detailed Comments

```cpp
class Solution {
public:
    vector<vector<int>> fourSum(vector<int>& nums, int target) {
        vector<vector<int>> result;
        int n = nums.size();
        
        // Need at least 4 elements for a quadruplet
        if (n < 4) {
            return result;
        }
        
        // Step 1: Sort array to enable two-pointer technique
        // and make duplicate handling easier
        sort(nums.begin(), nums.end());
        
        // Step 2: Fix first element (i)
        // We need at least 3 elements after i, so i goes up to n-4
        for (int i = 0; i < n - 3; i++) {
            // Skip duplicate values for i to avoid duplicate quadruplets
            if (i > 0 && nums[i] == nums[i - 1]) {
                continue;
            }
            
            // Optimization: If smallest possible sum with i is > target, break
            // nums[i] + nums[i+1] + nums[i+2] + nums[i+3] is the minimum sum
            // we can get with current i (since array is sorted)
            if ((long long)nums[i] + nums[i+1] + nums[i+2] + nums[i+3] > target) {
                break;
            }
            
            // Optimization: If largest possible sum with i is < target, continue
            // nums[i] + nums[n-3] + nums[n-2] + nums[n-1] is the maximum sum
            // we can get with current i
            if ((long long)nums[i] + nums[n-3] + nums[n-2] + nums[n-1] < target) {
                continue;
            }
            
            // Step 3: Fix second element (j)
            // We need at least 2 elements after j, so j goes up to n-3
            for (int j = i + 1; j < n - 2; j++) {
                // Skip duplicate values for j
                // Check j > i+1 (not just j > 0) to avoid skipping valid first j
                if (j > i + 1 && nums[j] == nums[j - 1]) {
                    continue;
                }
                
                // Optimization: Check if minimum possible sum is > target
                if ((long long)nums[i] + nums[j] + nums[j+1] + nums[j+2] > target) {
                    break;
                }
                
                // Optimization: Check if maximum possible sum is < target
                if ((long long)nums[i] + nums[j] + nums[n-2] + nums[n-1] < target) {
                    continue;
                }
                
                // Step 4: Two-pointer approach for remaining elements
                int left = j + 1;
                int right = n - 1;
                
                // We need: nums[left] + nums[right] = target - nums[i] - nums[j]
                // Use long long to avoid overflow when calculating target2
                long long target2 = (long long)target - nums[i] - nums[j];
                
                while (left < right) {
                    long long sum = (long long)nums[left] + nums[right];
                    
                    if (sum == target2) {
                        // Found a valid quadruplet
                        result.push_back({nums[i], nums[j], nums[left], nums[right]});
                        
                        // Skip duplicate values for left pointer
                        // This prevents finding the same quadruplet multiple times
                        while (left < right && nums[left] == nums[left + 1]) {
                            left++;
                        }
                        
                        // Skip duplicate values for right pointer
                        while (left < right && nums[right] == nums[right - 1]) {
                            right--;
                        }
                        
                        // Move both pointers to continue searching
                        left++;
                        right--;
                    }
                    else if (sum < target2) {
                        // Sum is too small, we need a larger value
                        // Move left pointer to the right to increase sum
                        left++;
                    }
                    else {
                        // Sum is too large, we need a smaller value
                        // Move right pointer to the left to decrease sum
                        right--;
                    }
                }
            }
        }
        
        return result;
    }
};
```

### Complexity Analysis
- **Time Complexity**: O(n³) - Sorting takes O(n log n). Two nested loops give O(n²), and for each iteration, the two-pointer search takes O(n). Total: O(n log n + n³) = O(n³)
- **Space Complexity**: O(1) - Not counting output space and sorting space. Only using constant extra variables.

## Edge Cases
- **Less than 4 elements**: `[1,2]` → `[]` (not enough elements)
- **Exactly 4 elements**: `[1,0,-1,0], target=0` → `[[-1,0,0,1]]`
- **All same elements**: `[2,2,2,2,2], target=8` → `[[2,2,2,2]]`
- **No solution**: `[1,2,3,4], target=100` → `[]`
- **Multiple solutions**: Should find all unique quadruplets
- **Negative target**: `[-1,-1,-1,-1], target=-4` → `[[-1,-1,-1,-1]]`
- **Large numbers**: Use `long long` to avoid overflow in sum calculations
- **Many duplicates**: Proper skipping prevents duplicate quadruplets

## Key Insights & Interview Tips

**Important Observations**:
- 4Sum is a generalization of 3Sum (which is generalization of 2Sum)
- Pattern: kSum = Fix one element + (k-1)Sum
- Overflow prevention is important (use `long long` for sums)
- Early termination optimizations can significantly speed up execution
- Duplicate handling at all 4 levels prevents duplicate results

**Common Mistakes to Avoid**:
- Integer overflow when summing large numbers (use `long long`)
- Not handling duplicates properly at all levels
- Wrong boundary conditions (i < n-3, j < n-2, not i < n)
- Forgetting j > i+1 check when skipping duplicates for j
- Not breaking/continuing appropriately in optimizations
- Comparing with wrong sum (using target instead of target2)

**What Interviewers Look For**:
- Recognition of pattern from 2Sum → 3Sum → 4Sum
- Understanding of generalization to kSum
- Proper overflow handling
- Optimization techniques (early termination)
- Clean duplicate handling
- Ability to explain time complexity progression

**Follow-up Questions**:
- Q: "How would you solve kSum for general k?"
- A: Recursive approach: kSum(k, start, target) = fix one element + kSum(k-1, start+1, newTarget). Base case is 2Sum with two pointers.

- Q: "Can you do better than O(n³)?"
- A: No for general case. O(n³) is optimal for 4Sum. Hash map approaches don't improve worst-case complexity and use more space.

- Q: "How would you handle very large numbers to avoid overflow?"
- A: Use `long long` throughout, or compare (target - nums[i] - nums[j]) with (nums[left] + nums[right]) carefully.

- Q: "What if target can be any 64-bit integer?"
- A: Rearrange comparisons to avoid overflow: instead of sum == target, use (nums[left] == target - sum_so_far - nums[right]).

**Time Complexity Progression**:
```
2Sum: O(n) with two pointers (sorted array)
3Sum: O(n²) = O(n) × 2Sum
4Sum: O(n³) = O(n²) × 2Sum
kSum: O(n^(k-1))
```

**Related Problems**:
- LeetCode 1: Two Sum
- LeetCode 15: 3Sum
- LeetCode 454: 4Sum II (different problem - counting tuples)
- LeetCode 1099: Two Sum Less Than K

**Recursive kSum Template** (Advanced):
```cpp
// Generalized kSum solution (for k >= 2)
vector<vector<int>> kSum(vector<int>& nums, int target, int k, int start) {
    vector<vector<int>> result;
    
    if (k == 2) {
        // Base case: Two Sum with two pointers
        // ... implementation ...
        return result;
    }
    
    // Fix one element and recursively solve (k-1)Sum
    for (int i = start; i < nums.size() - k + 1; i++) {
        if (i > start && nums[i] == nums[i-1]) continue;
        
        auto subResults = kSum(nums, target - nums[i], k - 1, i + 1);
        
        for (auto& sub : subResults) {
            sub.insert(sub.begin(), nums[i]);
            result.push_back(sub);
        }
    }
    
    return result;
}
```

**Optimizations Explained**:
- **Min sum check**: If smallest possible sum > target, no point checking larger values
- **Max sum check**: If largest possible sum < target, current value too small, try next
- **Long long usage**: Prevents overflow when nums[i] and target are near INT_MAX/INT_MIN
