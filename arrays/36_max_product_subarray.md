# 36. Maximum Product Subarray
**LeetCode**: [152 - Maximum Product Subarray](https://leetcode.com/problems/maximum-product-subarray/)  
**Difficulty**: Medium

## Problem Statement
Given an integer array `nums`, find a contiguous non-empty subarray within the array that has the largest product, and return the product.

The test cases are generated so that the answer will fit in a 32-bit integer.

## Examples

### Example 1
```
Input: nums = [2,3,-2,4]
Output: 6
Explanation: Subarray [2,3] has the largest product = 6
```

### Example 2
```
Input: nums = [-2,0,-1]
Output: 0
Explanation: Product cannot be more than 0
```

### Example 3
```
Input: nums = [-2,3,-4]
Output: 24
Explanation: Subarray [-2,3,-4] has product = 24
```

## Constraints
- `1 <= nums.length <= 2 × 10^4`
- `-10 <= nums[i] <= 10`
- Product of any prefix or suffix is guaranteed to fit in 32-bit integer

## Visual Explanation

### ASCII Diagram
```
Array: [2, 3, -2, 4]

Subarrays and products:
[2] = 2
[2,3] = 6 ✓ (max)
[2,3,-2] = -12
[2,3,-2,4] = -48
[3] = 3
[3,-2] = -6
[3,-2,4] = -24
[-2] = -2
[-2,4] = -8
[4] = 4

Why tracking min matters:
Array: [-2, 3, -4]

At -2: max = -2, min = -2
At 3:  max = 3, min = -6  (-2 * 3)
At -4: max = 24 (min * -4 = -6 * -4)
       min = -12 (max * -4 = 3 * -4)

Key insight: Minimum can become maximum after multiplying by negative!

Three choices at each position:
1. Start new subarray (current element)
2. Extend max product (prevMax * current)
3. Extend min product (prevMin * current)
   - Matters when current is negative
```

## Approach & Intuition

**Brute Force** - O(n²):
- Try all subarrays, calculate product for each

**Optimal - Dynamic Programming** - O(n):

**Key Insights**:
1. **Negative numbers flip min/max**: 
   - Multiplying by negative number flips sign
   - Current minimum * negative = might become maximum
   
2. **Track both min and max**:
   - maxProduct: maximum product ending at current position
   - minProduct: minimum product ending at current position
   
3. **Three candidates at each position**:
   - Start fresh from current element
   - Extend previous max product
   - Extend previous min product (important for negatives!)

4. **Zero resets everything**:
   - Product with 0 becomes 0
   - Must start fresh after 0

**Why track minimum**:
- When we hit a negative number, min becomes max
- Example: min=-10, current=-2 → -10 * -2 = 20 (now max!)

## Algorithm Steps

**Dynamic Programming Approach**:

1. **Initialize**:
   - `maxProduct = nums[0]` (max product ending at index 0)
   - `minProduct = nums[0]` (min product ending at index 0)
   - `result = nums[0]` (global maximum)

2. **For each element from index 1 to n-1**:
   
   a. **Calculate three candidates**:
      - current element alone
      - maxProduct * current
      - minProduct * current
   
   b. **Update maxProduct**: max of three candidates
   
   c. **Update minProduct**: min of three candidates
   
   d. **Update result**: max(result, maxProduct)

3. **Return result**

## Dry Run (Step-by-Step Execution)

**Input**: `nums = [-2,3,-4]`

```
Initialize:
  maxProduct = -2
  minProduct = -2
  result = -2

i=1, nums[1]=3:
  Candidates:
    - current = 3
    - maxProduct * 3 = -2 * 3 = -6
    - minProduct * 3 = -2 * 3 = -6
  
  maxProduct = max(3, -6, -6) = 3
  minProduct = min(3, -6, -6) = -6
  result = max(-2, 3) = 3
  
  State: max=3, min=-6, result=3

i=2, nums[2]=-4:
  Candidates:
    - current = -4
    - maxProduct * -4 = 3 * -4 = -12
    - minProduct * -4 = -6 * -4 = 24  ← min flips to max!
  
  maxProduct = max(-4, -12, 24) = 24
  minProduct = min(-4, -12, 24) = -12
  result = max(3, 24) = 24
  
  State: max=24, min=-12, result=24
```

**Final Output**: 24 ✓

### Another Example: `nums = [2,3,-2,4]`

```
Initialize:
  maxProduct = 2, minProduct = 2, result = 2

i=1, nums[1]=3:
  Candidates: 3, 2*3=6, 2*3=6
  maxProduct = 6
  minProduct = 3
  result = 6

i=2, nums[2]=-2:
  Candidates: -2, 6*-2=-12, 3*-2=-6
  maxProduct = -2
  minProduct = -12
  result = 6

i=3, nums[3]=4:
  Candidates: 4, -2*4=-8, -12*4=-48
  maxProduct = 4
  minProduct = -48
  result = 6
```

**Final Output**: 6 ✓

## C++ Solution

### Code with Detailed Comments

```cpp
class Solution {
public:
    int maxProduct(vector<int>& nums) {
        // Handle empty array (though constraints say n >= 1)
        if (nums.empty()) {
            return 0;
        }
        
        // Initialize with first element
        // maxProduct: maximum product of subarray ending at current position
        // minProduct: minimum product of subarray ending at current position
        // We track min because multiplying by negative can flip min to max
        int maxProduct = nums[0];
        int minProduct = nums[0];
        
        // Result stores the global maximum product found so far
        int result = nums[0];
        
        // Process each element starting from index 1
        for (int i = 1; i < nums.size(); i++) {
            int current = nums[i];
            
            // If current is negative, multiplying by min gives max
            // and multiplying by max gives min
            // So swap them before calculations
            // This is an optimization to simplify the logic
            if (current < 0) {
                swap(maxProduct, minProduct);
            }
            
            // Three options for maximum product ending at i:
            // 1. Start fresh from current element (nums[i])
            // 2. Extend previous maximum product (maxProduct * nums[i])
            // We take max of these two
            maxProduct = max(current, maxProduct * current);
            
            // Similarly for minimum product:
            // 1. Start fresh from current element
            // 2. Extend previous minimum product
            minProduct = min(current, minProduct * current);
            
            // Update global result
            result = max(result, maxProduct);
        }
        
        return result;
    }
};
```

### Alternative: Without swapping (more explicit)

```cpp
class Solution {
public:
    int maxProduct(vector<int>& nums) {
        if (nums.empty()) return 0;
        
        int maxProduct = nums[0];
        int minProduct = nums[0];
        int result = nums[0];
        
        for (int i = 1; i < nums.size(); i++) {
            int current = nums[i];
            
            // Store previous values before updating
            // because we need both for calculations
            int prevMax = maxProduct;
            int prevMin = minProduct;
            
            // Calculate three candidates for maximum:
            // 1. Current element alone (start fresh)
            // 2. Previous max * current (extend max subarray)
            // 3. Previous min * current (important when current is negative)
            maxProduct = max({current, prevMax * current, prevMin * current});
            
            // Calculate three candidates for minimum:
            // Same logic but taking minimum
            minProduct = min({current, prevMax * current, prevMin * current});
            
            // Update global result
            result = max(result, maxProduct);
        }
        
        return result;
    }
};
```

### Alternative: Prefix/Suffix approach

```cpp
class Solution {
public:
    int maxProduct(vector<int>& nums) {
        int n = nums.size();
        int result = nums[0];
        
        // Forward pass: calculate prefix products
        int product = 1;
        for (int i = 0; i < n; i++) {
            product *= nums[i];
            result = max(result, product);
            
            // Reset on zero (product becomes 0, start fresh)
            if (product == 0) product = 1;
        }
        
        // Backward pass: calculate suffix products
        // Handles cases where maximum product is in suffix
        // after an odd number of negatives
        product = 1;
        for (int i = n - 1; i >= 0; i--) {
            product *= nums[i];
            result = max(result, product);
            
            if (product == 0) product = 1;
        }
        
        return result;
    }
};
```

### Complexity Analysis
- **Time Complexity**: O(n) - Single pass through array
- **Space Complexity**: O(1) - Only using constant extra variables

## Edge Cases
- **Single element**: `[5]` → 5, `[-3]` → -3
- **All positive**: `[2,3,4]` → 24 (entire array)
- **All negative odd count**: `[-2,-3]` → 6 (entire array, even negatives)
- **All negative even count**: `[-2,-3,-4]` → max is 12 (exclude one negative)
- **Contains zero**: `[-2,0,3]` → 3 (zero breaks the chain)
- **Multiple zeros**: `[0,2,0,3]` → 3
- **Mix of positive/negative**: Various subarrays possible

## Key Insights & Interview Tips

**Important Observations**:
- Cannot use Kadane's algorithm directly (sum vs product different)
- Negative numbers can flip min to max
- Zero resets everything (product becomes 0)
- Even count of negatives gives positive product
- Odd count of negatives means exclude one to maximize

**Common Mistakes to Avoid**:
- Only tracking maximum (forgetting minimum)
- Not handling negative numbers correctly
- Not resetting after zero
- Integer overflow (though problem guarantees 32-bit fits)
- Treating it like maximum subarray sum problem

**What Interviewers Look For**:
- Recognition that we need to track both min and max
- Understanding of why negatives make it different from sum
- Ability to handle zero as special case
- O(n) time and O(1) space solution
- Clear explanation of DP state transitions

**Follow-up Questions**:
- Q: "Why track minimum product?"
- A: Because multiplying minimum by a negative number can give maximum product.

- Q: "How does zero affect the solution?"
- A: Zero resets product to 0. Must start fresh after zero. Can't include zero in product unless result is 0.

- Q: "Can you use the prefix/suffix approach?"
- A: Yes, scan left-to-right and right-to-left, resetting on zero. Works because max is either in prefix or suffix.

- Q: "What if array can have very large numbers?"
- A: Would need to handle overflow or use long long, but problem guarantees 32-bit.

**Related Problems**:
- LeetCode 53: Maximum Subarray (Kadane's algorithm)
- LeetCode 628: Maximum Product of Three Numbers
- LeetCode 1567: Maximum Length of Subarray With Positive Product

**Comparison with Maximum Subarray Sum**:
| Aspect | Max Sum (Kadane) | Max Product |
|--------|------------------|-------------|
| Track min? | No | Yes (negatives flip) |
| Zero handling | Include in sum | Breaks product |
| Negative effect | Decreases sum | Can increase product |
| DP state | Just max | Max and min |

**Why Prefix/Suffix Works**:
```
Array with negatives:
[-2, 3, -4, 5, -6]

Prefix products:
-2, -6, 24, 120, -720

Suffix products:
-720, 360, 120, -30, -6

Maximum product must appear in either:
- Prefix (before some position)
- Suffix (after some position)

Because if we have odd negatives in entire array,
we must exclude one from either start or end.
```

**Pattern Recognition**:
- This is a **DP problem** with state tracking
- Similar to problems where we need to track multiple states
- Negative numbers introducing "flip" behavior is the twist
- Standard template: track complementary states (min/max)
