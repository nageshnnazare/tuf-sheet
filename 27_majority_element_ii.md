# 27. Majority Element II
**LeetCode**: [229 - Majority Element II](https://leetcode.com/problems/majority-element-ii/)  
**Difficulty**: Medium

## Problem Statement
Given an integer array of size `n`, find all elements that appear more than `⌊n/3⌋` times.

Follow-up: Could you solve the problem in linear time and in O(1) space?

## Examples

### Example 1
```
Input: nums = [3,2,3]
Output: [3]
Explanation: 3 appears 2 times, and 2/3 > 1/3, so 3 is majority element
```

### Example 2
```
Input: nums = [1]
Output: [1]
```

### Example 3
```
Input: nums = [1,2]
Output: [1,2]
Explanation: Both appear 1 time, and 1/2 > 1/3, so both are majority elements
```

### Example 4
```
Input: nums = [2,2,1,1,1,2,2]
Output: [2]
Explanation: 2 appears 4 times (4/7 > 1/3), 1 appears 3 times (3/7 < 1/2 but > 1/3)? 
Wait, 3/7 = 0.428... and 1/3 = 0.333..., so yes, both should qualify.
Actually: n=7, ⌊7/3⌋ = 2, so need > 2 occurrences.
2 appears 4 times (>2) ✓
1 appears 3 times (>2) ✓
Output: [2,1] or [1,2]
```

## Constraints
- `1 <= nums.length <= 5 × 10^4`
- `-10^9 <= nums[i] <= 10^9`

## Visual Explanation

### ASCII Diagram
```
Key Insight: At most 2 elements can appear more than ⌊n/3⌋ times

Proof:
- If 3 elements each appear > n/3 times
- Total occurrences > 3 × (n/3) = n
- Impossible! Array has only n elements

Boyer-Moore Extended Algorithm:

Array: [1, 2, 1, 2, 1, 3, 4]
       n = 7, threshold = ⌊7/3⌋ = 2 (need > 2, so >= 3)

Step 1: Find candidates (at most 2)
       Track two candidates with counters

Step 2: Verify candidates
       Count actual occurrences

Voting Process:
i=0: 1   → candidate1=1, count1=1
i=1: 2   → candidate2=2, count2=1
i=2: 1   → matches c1, count1=2
i=3: 2   → matches c2, count2=2
i=4: 1   → matches c1, count1=3
i=5: 3   → different, decrement both: count1=2, count2=1
i=6: 4   → different, decrement both: count1=1, count2=0

Candidates: 1 (count=1), 2 (count=0)

Verify:
1 appears 3 times (>2) ✓
2 appears 2 times (not >2) ✗

Result: [1]
```

## Approach & Intuition

**Brute Force**: Use hash map to count frequencies - O(n) time, O(n) space

**Optimal - Boyer-Moore Voting Algorithm (Extended)**:

**Key Insights**:
1. At most 2 elements can appear more than n/3 times (pigeonhole principle)
2. Use voting to find up to 2 candidates
3. Must verify candidates (different from Majority Element I)

**Why two candidates**:
- Similar to Moore's voting but maintain 2 potential candidates
- If current element doesn't match either candidate:
  - If both have counts, decrement both
  - If one has count 0, replace it

**Why verification needed**:
- Candidates are potential majority elements
- They might not actually appear > n/3 times
- Example: [1,2,3] gives candidates but none qualify

## Algorithm Steps

**Two-Phase Algorithm**:

**Phase 1: Find candidates (at most 2)**

1. Initialize: `candidate1 = 0, candidate2 = 0, count1 = 0, count2 = 0`

2. For each number in array:
   - If number == candidate1: increment count1
   - Else if number == candidate2: increment count2
   - Else if count1 == 0: set candidate1 = number, count1 = 1
   - Else if count2 == 0: set candidate2 = number, count2 = 1
   - Else: decrement both count1 and count2 (voting out)

**Phase 2: Verify candidates**

3. Count actual occurrences of candidate1 and candidate2

4. Add to result if count > ⌊n/3⌋

## Dry Run (Step-by-Step Execution)

**Input**: `nums = [3,2,3,1,1,1,3]`, n = 7, threshold = ⌊7/3⌋ = 2 (need > 2)

**Phase 1: Find Candidates**
```
Initial: c1=0, c2=0, count1=0, count2=0

i=0, num=3:
  count1==0 → c1=3, count1=1
  State: c1=3(1), c2=0(0)

i=1, num=2:
  count2==0 → c2=2, count2=1
  State: c1=3(1), c2=2(1)

i=2, num=3:
  num==c1 → count1++
  State: c1=3(2), c2=2(1)

i=3, num=1:
  num≠c1, num≠c2, both counts>0 → count1--, count2--
  State: c1=3(1), c2=2(0)

i=4, num=1:
  count2==0 → c2=1, count2=1
  State: c1=3(1), c2=1(1)

i=5, num=1:
  num==c2 → count2++
  State: c1=3(1), c2=1(2)

i=6, num=3:
  num==c1 → count1++
  State: c1=3(2), c2=1(2)

Candidates: 3 and 1
```

**Phase 2: Verify**
```
Count occurrences:
3 appears: positions [0,2,6] = 3 times
1 appears: positions [3,4,5] = 3 times

Check threshold (need > 2):
3: 3 > 2 ✓
1: 3 > 2 ✓

Result: [3, 1]
```

## C++ Solution

### Code with Detailed Comments

```cpp
class Solution {
public:
    vector<int> majorityElement(vector<int>& nums) {
        int n = nums.size();
        
        // Phase 1: Find potential candidates using extended Boyer-Moore
        int candidate1 = 0, candidate2 = 0;
        int count1 = 0, count2 = 0;
        
        for (int num : nums) {
            // Check if current number matches existing candidates
            if (num == candidate1) {
                count1++;
            }
            else if (num == candidate2) {
                count2++;
            }
            // If count1 is 0, replace candidate1
            else if (count1 == 0) {
                candidate1 = num;
                count1 = 1;
            }
            // If count2 is 0, replace candidate2
            else if (count2 == 0) {
                candidate2 = num;
                count2 = 1;
            }
            // Current number doesn't match either candidate
            // and both candidates have supporters
            // Vote out: decrement both counts
            else {
                count1--;
                count2--;
            }
        }
        
        // Phase 2: Verify candidates by counting actual occurrences
        // Candidates might not actually be majority elements
        count1 = 0;
        count2 = 0;
        
        for (int num : nums) {
            if (num == candidate1) count1++;
            else if (num == candidate2) count2++;
        }
        
        vector<int> result;
        
        // Need to appear more than ⌊n/3⌋ times
        if (count1 > n / 3) {
            result.push_back(candidate1);
        }
        if (count2 > n / 3) {
            result.push_back(candidate2);
        }
        
        return result;
    }
};
```

### Complexity Analysis
- **Time Complexity**: O(n) - Two passes through the array: one for finding candidates, one for verification
- **Space Complexity**: O(1) - Only using constant extra space for candidates and counters (not counting output)

## Edge Cases
- **Single element**: `[1]` → `[1]`
- **Two elements**: `[1,2]` → `[1,2]` (both appear > n/3 times)
- **All same**: `[1,1,1]` → `[1]`
- **No majority**: `[1,2,3]` → `[]` (each appears 1/3 times, not > 1/3)
- **Exactly n/3**: `[1,1,2,2,3,3]` → `[]` (each appears exactly 2/6 = 1/3, not > 1/3)
- **Two majority elements**: `[1,1,1,2,2,2,3]` → `[1,2]`
- **Negative numbers**: Works the same way
- **Large numbers**: Within int range per constraints

## Key Insights & Interview Tips

**Important Observations**:
- Maximum 2 elements can appear > n/3 times (pigeonhole principle)
- Verification phase is REQUIRED (unlike Majority Element I where > n/2 is guaranteed)
- Candidates from voting might not meet the threshold
- Order in result doesn't matter

**Common Mistakes to Avoid**:
- Forgetting verification phase (candidates might not be majority)
- Not handling the case when candidate1 == candidate2
- Checking >= n/3 instead of > n/3
- Forgetting else if for candidate2 check (would make both candidates same)
- Not resetting counts to 0 before verification

**What Interviewers Look For**:
- Understanding of why at most 2 elements can qualify
- Ability to extend Boyer-Moore from 1 to 2 candidates
- Recognition that verification is necessary
- Clean handling of the voting logic
- Knowledge of space optimization

**Follow-up Questions**:
- Q: "Why do we need to verify candidates?"
- A: Voting guarantees candidates are most frequent, but not necessarily > n/3. Example: [1,2,3] gives wrong answer without verification.

- Q: "Can there be 3 majority elements?"
- A: No. If 3 elements each appear > n/3, total > n elements (impossible).

- Q: "What about > n/k for general k?"
- A: Extend to (k-1) candidates. At most (k-1) elements can appear > n/k times.

- Q: "Why decrement both counts when element doesn't match?"
- A: Voting mechanism - if current element doesn't support either candidate, it votes against both.

**Comparison with Majority Element I**:
| Aspect | Majority I (> n/2) | Majority II (> n/3) |
|--------|-------------------|---------------------|
| Max elements | 1 | 2 |
| Candidates | 1 | 2 |
| Verification | Not needed | Required |
| Space | O(1) | O(1) |

**Related Problems**:
- LeetCode 169: Majority Element (> n/2)
- LeetCode 1157: Online Majority Element In Subarray
- General k case (> n/k) - interview discussion topic
