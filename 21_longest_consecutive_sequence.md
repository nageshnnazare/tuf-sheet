# 21. Longest Consecutive Sequence

**LeetCode**: [128. Longest Consecutive Sequence](https://leetcode.com/problems/longest-consecutive-sequence/)  
**Difficulty**: Medium

## Problem Statement

Given an unsorted array of integers `nums`, return the length of the longest consecutive elements sequence.

You must write an algorithm that runs in `O(n)` time.

## Examples

### Example 1
```
Input: nums = [100, 4, 200, 1, 3, 2]
Output: 4
Explanation: The longest consecutive elements sequence is [1, 2, 3, 4]. Therefore its length is 4.
```

### Example 2
```
Input: nums = [0, 3, 7, 2, 5, 8, 4, 6, 0, 1]
Output: 9
Explanation: The longest consecutive sequence is [0, 1, 2, 3, 4, 5, 6, 7, 8].
```

## Constraints
- 0 <= nums.length <= 10^5
- -10^9 <= nums[i] <= 10^9

## Visual Explanation

### ASCII Diagram - Set Lookups

```
Array: [100, 4, 200, 1, 3, 2]

Step 1: Insert all into HashSet
Set: {100, 4, 200, 1, 3, 2}

Step 2: Iterate and find starts of sequences
(A number 'x' is a start if 'x-1' is NOT in the set)

1. Val = 100
   Is 99 in Set? NO. -> 100 is a start.
   Count streak: 100... 101? No.
   Length = 1.

2. Val = 4
   Is 3 in Set? YES. -> 4 is NOT a start. Ignore.

3. Val = 200
   Is 199 in Set? NO. -> 200 is a start.
   Count streak: 200... 201? No.
   Length = 1.

4. Val = 1
   Is 0 in Set? NO. -> 1 is a start.
   Count streak:
     1 (in set)
     2 (in set)
     3 (in set)
     4 (in set)
     5 (not in set)
   Length = 4. Update Max = 4.

5. Val = 3
   Is 2 in Set? YES. -> Ignore.

6. Val = 2
   Is 1 in Set? YES. -> Ignore.

Final Result: 4
```

## Approach & Intuition

### Naive Approach: Brute Force
- Consider every element `x` as a potential starting point.
- Search for `x+1`, `x+2`, etc., in the array using linear search.
- Track the maximum length found.
- **Time Complexity**: O(N^2) or O(N^3) depending on implementation. **TLE**.

### Better Approach: Sorting
- Sort the array first.
- Iterate and check if `nums[i] == nums[i-1] + 1`.
- Need to handle duplicates (skip them).
- **Time Complexity**: O(N log N) due to sorting.
- **Space Complexity**: O(1) or O(N) depending on sort.
- **Limitation**: Question asks for O(N).

### Optimal Approach: HashSet (O(N))
- Put all elements into a **HashSet** for O(1) lookups.
- Iterate through the set (or array).
- To avoid redundant counting, strictly identify the **start** of a sequence.
  - An element `x` is the start of a sequence ONLY IF `x-1` is *not* in the set.
- If `x` is a start, initiate a while loop to find `x+1`, `x+2`, ... until the sequence breaks.
- Measure length and update global maximum.
- **Why O(N)?** Each element is inserted once. Then, each number is visited at most twice during the sequence building (once as a check, once as part of the count).

## Algorithm Steps

1. Handle edge case: if `nums` is empty, return 0.
2. Insert all elements of `nums` into a HashSet `st` to allow O(1) existence checks.
3. Initialize `longest` = 0.
4. Iterate through each number `it` in `st` (or `nums`):
   - Check if `it - 1` exists in `st`.
   - **If NO**: `it` is the start of a sequence.
     - Set `currentNum = it` and `currentStreak = 1`.
     - While `currentNum + 1` exists in `st`:
       - Increment `currentNum`.
       - Increment `currentStreak`.
     - Update `longest = max(longest, currentStreak)`.
   - **If YES**: `it` is part of a sequence but not the start. Skip it.
5. Return `longest`.

## Dry Run (Step-by-Step Execution)

Input: `nums = [100, 4, 200, 1, 3, 2]`

1. **Initialization**:
   - `st` = {100, 4, 200, 1, 3, 2}
   - `longest` = 0

2. **Iteration**:

   - **Pick 100**:
     - Check `99`. In set? NO.
     - 100 is start.
     - Check `101`. In set? NO.
     - Streak = 1. `longest` = 1.

   - **Pick 4**:
     - Check `3`. In set? YES.
     - 4 is not start. Skip.

   - **Pick 200**:
     - Check `199`. In set? NO.
     - 200 is start.
     - Check `201`. In set? NO.
     - Streak = 1. `longest` = 1.

   - **Pick 1**:
     - Check `0`. In set? NO.
     - 1 is start.
     - Check `2`. YES. (Streak 2)
     - Check `3`. YES. (Streak 3)
     - Check `4`. YES. (Streak 4)
     - Check `5`. NO.
     - Streak = 4. `longest` = max(1, 4) = 4.

   - **Pick 3**:
     - Check `2`. In set? YES. Skip.

   - **Pick 2**:
     - Check `1`. In set? YES. Skip.

3. **Result**: Return 4.

## C++ Solution

### Code with Detailed Comments

```cpp
#include <iostream>
#include <vector>
#include <unordered_set>
#include <algorithm>
using namespace std;

class Solution {
public:
    int longestConsecutive(vector<int>& nums) {
        int n = nums.size();
        if (n == 0) return 0;

        // 1. Insert all elements into a HashSet
        // This allows O(1) lookups
        unordered_set<int> st;
        for (int i = 0; i < n; i++) {
            st.insert(nums[i]);
        }

        int longest = 0;

        // 2. Iterate through the set elements provided optimal logic
        // We could also iterate nums, but set handles duplicates implicitly
        for (auto it : st) {
            
            // 3. Check if 'it' is a starting number
            // If 'it-1' exists, then 'it' is part of a sequence starting earlier
            // So we skip it to avoid O(N^2) work
            if (st.find(it - 1) == st.end()) {
                
                // Found a starting number
                int cnt = 1;
                int x = it;

                // 4. Count the consecutive sequence
                while (st.find(x + 1) != st.end()) {
                    x = x + 1;
                    cnt = cnt + 1;
                }
                
                // 5. Update maximum length
                longest = max(longest, cnt);
            }
        }
        
        return longest;
    }
};

// Driver Code
int main() {
    Solution sol;
    vector<int> nums = {100, 4, 200, 1, 3, 2};
    cout << "Longest Consecutive Sequence: " << sol.longestConsecutive(nums) << endl;
    
    // Example 2
    vector<int> nums2 = {0, 3, 7, 2, 5, 8, 4, 6, 0, 1};
    cout << "Longest Consecutive Sequence: " << sol.longestConsecutive(nums2) << endl;
    return 0;
}
```

### Complexity Analysis

- **Time Complexity**: **O(N)**.
  - Inserting into set takes O(N) average.
  - We iterate the set once. The inner `while` loop runs only when a number is the *start* of a sequence. Across the whole process, each number is visited at most twice (once in outer loop check, once in inner loop accumulation). Thus, linear time strict.
  
- **Space Complexity**: **O(N)**.
  - We use an `unordered_set` to store `N` elements.

## Edge Cases

- **Empty Array**: Return 0.
- **Single Element**: Return 1.
- **Duplicates**: Handled by Set.
- **Negative Numbers**: Handled correctly.

## Key Insights & Interview Tips

- **Why Sorting is Sub-optimal**: Sorting destroys the O(N) requirement (O(N log N)).
- **The "Start of Sequence" Check**: The condition `!st.contains(x-1)` is the crux of the O(N) logic.
- **Set vs Map**: Use Set for existence. O(N) space.