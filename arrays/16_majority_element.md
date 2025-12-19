# 16. Majority Element in Array

**LeetCode**: [#169 - Majority Element](https://leetcode.com/problems/majority-element/)  
**Difficulty**: Easy

## Problem Statement

Given an array `nums` of size `n`, return the **majority element**.

The majority element is the element that appears **more than ⌊n/2⌋ times**. You may assume that the majority element always exists in the array.

## Examples

### Example 1
```
Input: nums = [3, 2, 3]
Output: 3
Explanation: 3 appears 2 times, which is more than ⌊3/2⌋ = 1.
```

### Example 2
```
Input: nums = [2, 2, 1, 1, 1, 2, 2]
Output: 2
Explanation: 2 appears 4 times, which is more than ⌊7/2⌋ = 3.
```

### Example 3
```
Input: nums = [1]
Output: 1
```

## Constraints
- 1 ≤ nums.length ≤ 5 × 10^4
- -10^9 ≤ nums[i] ≤ 10^9
- Majority element always exists

## Visual Explanation

### ASCII Diagram - Boyer-Moore Voting Algorithm

```
nums = [2, 2, 1, 1, 1, 2, 2]

Concept: Majority element survives cancellation

Process:
  candidate = null, count = 0

Index 0: 2
  count = 0, so pick new candidate
  candidate = 2, count = 1
  [2] count=1

Index 1: 2  
  Same as candidate, count++
  candidate = 2, count = 2
  [2,2] count=2

Index 2: 1
  Different from candidate, count--
  candidate = 2, count = 1
  [2,2,1] → (2,2) vs (1) → count=1

Index 3: 1
  Different from candidate, count--
  candidate = 2, count = 0
  [2,2,1,1] → All cancelled

Index 4: 1
  count = 0, pick new candidate
  candidate = 1, count = 1
  [1] count=1

Index 5: 2
  Different, count--
  candidate = 1, count = 0
  [1,2] cancelled

Index 6: 2
  count = 0, pick new candidate
  candidate = 2, count = 1
  [2] count=1

Result: candidate = 2 ✓

Visualization (Cancellation):
  Start: [2, 2, 1, 1, 1, 2, 2]
  
  After cancellation:
  [2, 2] [1, 1] [1, 2] [2]
    ↓      ↓      ↓    ↓
  cancel cancel    (2 remains)
  
  The majority (2) always survives!
```

## Approach & Intuition

**Boyer-Moore Voting Algorithm** ⭐

**Key Insight**: 
- Majority element appears > n/2 times
- If we pair each majority element with a different element, majority still has leftover unpaired elements
- These unpaired elements will be our candidate!

**Algorithm**:
1. Find candidate through voting (pairing/cancellation)
2. Since majority guaranteed, candidate IS the majority

**Metaphor**: Election with two parties. Majority party will win even if all minority votes cancel majority votes!

## Algorithm Steps

1. Initialize candidate = null, count = 0
2. For each element:
   - If count == 0: Pick new candidate
   - If element == candidate: Increment count
   - Else: Decrement count
3. Return candidate

## Dry Run

```
nums = [2, 2, 1, 1, 1, 2, 2]
candidate = null, count = 0

i=0, nums[0]=2:
  count == 0 → Pick candidate
  candidate = 2, count = 1

i=1, nums[1]=2:
  2 == candidate → Increment
  count = 2

i=2, nums[2]=1:
  1 != candidate → Decrement
  count = 1

i=3, nums[3]=1:
  1 != candidate → Decrement
  count = 0

i=4, nums[4]=1:
  count == 0 → New candidate
  candidate = 1, count = 1

i=5, nums[5]=2:
  2 != candidate → Decrement
  count = 0

i=6, nums[6]=2:
  count == 0 → New candidate
  candidate = 2, count = 1

Final candidate: 2 ✓

Note: If majority not guaranteed, 
need second pass to verify candidate
appears > n/2 times.
```

## C++ Solution

```cpp
#include <iostream>
#include <vector>
#include <unordered_map>
using namespace std;

class Solution {
public:
    // Boyer-Moore Voting Algorithm - Optimal!
    int majorityElement(vector<int>& nums) {
        int candidate = 0;  // Current candidate for majority
        int count = 0;      // Vote count for current candidate
        
        // Phase 1: Find candidate through voting
        for (int num : nums) {
            // If count is 0, we need a new candidate
            // This happens when previous candidate was fully cancelled
            if (count == 0) {
                candidate = num;
                count = 1;
            }
            // If current element matches candidate, it's a vote for candidate
            else if (num == candidate) {
                count++;
            }
            // If current element is different, it cancels one vote
            else {
                count--;
            }
        }
        
        // Phase 2: (Optional) Verify candidate
        // Problem guarantees majority exists, so we can skip this
        // But if not guaranteed, count occurrences of candidate
        
        // int verifyCount = 0;
        // for (int num : nums) {
        //     if (num == candidate) verifyCount++;
        // }
        // if (verifyCount > nums.size() / 2) return candidate;
        // return -1; // No majority exists
        
        return candidate;
    }
    
    // Alternative: Hash Map (uses O(n) space)
    int majorityElementHashMap(vector<int>& nums) {
        unordered_map<int, int> freq;
        int n = nums.size();
        
        // Count frequencies
        for (int num : nums) {
            freq[num]++;
            
            // Early return when majority found
            if (freq[num] > n / 2) {
                return num;
            }
        }
        
        return -1; // Should never reach here if majority guaranteed
    }
    
    // Alternative: Sorting (modifies array)
    int majorityElementSort(vector<int>& nums) {
        // Sort the array
        // Majority element will always be at index n/2
        // Because it appears more than n/2 times!
        sort(nums.begin(), nums.end());
        
        return nums[nums.size() / 2];
    }
};

int main() {
    Solution sol;
    
    // Test Case 1
    vector<int> nums1 = {3, 2, 3};
    cout << "Array: [3, 2, 3]" << endl;
    cout << "Majority element: " << sol.majorityElement(nums1) << endl << endl;
    
    // Test Case 2
    vector<int> nums2 = {2, 2, 1, 1, 1, 2, 2};
    cout << "Array: [2, 2, 1, 1, 1, 2, 2]" << endl;
    cout << "Majority element (Boyer-Moore): " << sol.majorityElement(nums2) << endl;
    cout << "Majority element (HashMap): " << sol.majorityElementHashMap(nums2) << endl << endl;
    
    // Test Case 3: Single element
    vector<int> nums3 = {1};
    cout << "Array: [1]" << endl;
    cout << "Majority element: " << sol.majorityElement(nums3) << endl << endl;
    
    // Test Case 4: All same
    vector<int> nums4 = {5, 5, 5, 5};
    cout << "Array: [5, 5, 5, 5]" << endl;
    cout << "Majority element: " << sol.majorityElement(nums4) << endl;
    
    return 0;
}
```

### Complexity Analysis

**Boyer-Moore Voting**:
- **Time**: O(n) - Single pass (or two if verification needed)
- **Space**: O(1) - Only two variables

**Hash Map**:
- **Time**: O(n) - Single pass
- **Space**: O(n) - Store frequencies

**Sorting**:
- **Time**: O(n log n) - Sorting dominates
- **Space**: O(1) or O(n) depending on sort algorithm

## Edge Cases

1. **Single element**: `[1]` → `1`
2. **All same**: `[5,5,5,5]` → `5`
3. **Majority at start**: `[1,1,1,2,3]` → `1`
4. **Majority at end**: `[2,3,1,1,1]` → `1`
5. **Alternating with clear majority**: `[1,2,1,2,1]` → `1`

## Key Insights & Interview Tips

1. **Boyer-Moore is Brilliant**:
   - Most elegant solution
   - O(1) space unlike hash map
   - Faster than sorting

2. **Why It Works**:
   - Majority > n/2 means it survives cancellation
   - Even if all minority elements cancel with majority, majority has leftover

3. **Three Approaches**:
   ```
   | Method      | Time      | Space | Notes           |
   |-------------|-----------|-------|-----------------|
   | Boyer-Moore | O(n)      | O(1)  | Optimal ⭐      |
   | Hash Map    | O(n)      | O(n)  | Straightforward |
   | Sorting     | O(n logn) | O(1)* | Simple idea     |
   ```

4. **Verification Phase**:
   - Only needed if majority NOT guaranteed
   - Problem states it exists, so skip verification
   - If needed, one more pass: count candidate occurrences

5. **Follow-ups**:
   - "Find all elements appearing > n/3 times?" → Modified Boyer-Moore with 2 candidates
   - "No majority guaranteed?" → Add verification phase
   - "Return count too?" → Track frequency during verification

6. **Common Mistakes**:
   - Not resetting candidate when count reaches 0
   - Incrementing count before checking if count is 0
   - Forgetting that majority is > n/2, not ≥ n/2

7. **Real-world Applications**:
   - Finding dominant opinion in surveys
   - Detecting most frequent error in logs
   - Stream processing (find majority in stream)

8. **Related Problems**:
   - LeetCode #229: Majority Element II (> n/3 times)
   - Finding k most frequent elements
   - Mode in statistics

9. **Interview Pro Tip**: Start with hash map for clarity, then optimize to Boyer-Moore to show advanced knowledge!

10. **Why "Voting"**:
    - Think of it as voting: candidate gets +1 vote when matched, -1 when opposed
    - Majority candidate can never be eliminated (always positive votes remaining)

