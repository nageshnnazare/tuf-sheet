# 31. Merge Overlapping Intervals
**LeetCode**: [56 - Merge Intervals](https://leetcode.com/problems/merge-intervals/)  
**Difficulty**: Medium

## Problem Statement
Given an array of `intervals` where `intervals[i] = [start_i, end_i]`, merge all overlapping intervals, and return an array of the non-overlapping intervals that cover all the intervals in the input.

## Examples

### Example 1
```
Input: intervals = [[1,3],[2,6],[8,10],[15,18]]
Output: [[1,6],[8,10],[15,18]]
Explanation: Intervals [1,3] and [2,6] overlap, so merge them into [1,6]
```

### Example 2
```
Input: intervals = [[1,4],[4,5]]
Output: [[1,5]]
Explanation: Intervals [1,4] and [4,5] are considered overlapping
```

### Example 3
```
Input: intervals = [[1,4],[0,4]]
Output: [[0,4]]
Explanation: [0,4] encompasses [1,4]
```

## Constraints
- `1 <= intervals.length <= 10^4`
- `intervals[i].length == 2`
- `0 <= start_i <= end_i <= 10^4`

## Visual Explanation

### ASCII Diagram
```
Before sorting:
[1,3]   [2,6]   [8,10]  [15,18]
|--|
  |-----|
              |---|
                        |-----|

After sorting by start time:
[1,3]   [2,6]   [8,10]  [15,18]
|--|
  |-----|
              |---|
                        |-----|

Merging process:
Step 1: Start with [1,3]
Step 2: [2,6] overlaps with [1,3] (2 <= 3)
        → Merge to [1, max(3,6)] = [1,6]
Step 3: [8,10] doesn't overlap with [1,6] (8 > 6)
        → Add [1,6] to result, start new interval [8,10]
Step 4: [15,18] doesn't overlap with [8,10] (15 > 10)
        → Add [8,10], start [15,18]
Step 5: Add final [15,18]

Result: [[1,6], [8,10], [15,18]]

Overlap condition:
Two intervals [a,b] and [c,d] overlap if:
  c <= b (assuming a <= c after sorting)

Visual:
  a----b          Overlapping
     c----d

  a----b          Not overlapping
          c----d
```

## Approach & Intuition

**Key Insight**: 
- Sort intervals by start time
- Then linearly merge intervals that overlap
- Two sorted intervals overlap if: `next.start <= current.end`

**Why sorting helps**:
- After sorting by start time, we only need to check if current interval overlaps with previous
- Don't need to check all pairs (would be O(n²))
- Can process in single pass after sorting

**Merging logic**:
- If next interval starts before or when current ends → they overlap
- Merged interval: `[current.start, max(current.end, next.end)]`

## Algorithm Steps

1. **Sort intervals** by start time

2. **Initialize result** with first interval

3. **For each interval from index 1 to n-1**:
   
   a. Get last interval in result
   
   b. **If current interval overlaps with last interval**:
      - Check: `current.start <= last.end`
      - Merge: Update `last.end = max(last.end, current.end)`
   
   c. **Else** (no overlap):
      - Add current interval to result

4. **Return result**

## Dry Run (Step-by-Step Execution)

**Input**: `intervals = [[1,3],[2,6],[8,10],[15,18]]`

**Step 1: Sort by start time**
```
Already sorted: [[1,3],[2,6],[8,10],[15,18]]
```

**Step 2: Initialize**
```
result = [[1,3]]
```

**Step 3: Process remaining intervals**

```
i=1, current=[2,6]:
  last = [1,3]
  Check overlap: 2 <= 3? YES
  Merge: last.end = max(3, 6) = 6
  result = [[1,6]]

i=2, current=[8,10]:
  last = [1,6]
  Check overlap: 8 <= 6? NO
  No overlap, add current
  result = [[1,6], [8,10]]

i=3, current=[15,18]:
  last = [8,10]
  Check overlap: 15 <= 10? NO
  No overlap, add current
  result = [[1,6], [8,10], [15,18]]
```

**Final Output**: `[[1,6], [8,10], [15,18]]` ✓

## C++ Solution

### Code with Detailed Comments

```cpp
class Solution {
public:
    vector<vector<int>> merge(vector<vector<int>>& intervals) {
        // Handle edge case: empty input
        if (intervals.empty()) {
            return {};
        }
        
        // Step 1: Sort intervals by start time
        // This allows us to process intervals in order and only check
        // if current interval overlaps with the last merged interval
        // Time: O(n log n)
        sort(intervals.begin(), intervals.end());
        
        // Step 2: Initialize result with the first interval
        // We'll merge subsequent intervals into this as we go
        vector<vector<int>> result;
        result.push_back(intervals[0]);
        
        // Step 3: Process each interval starting from the second one
        for (int i = 1; i < intervals.size(); i++) {
            // Get reference to the last interval in result
            // This is the interval we might merge with
            vector<int>& last = result.back();
            
            // Get current interval we're processing
            int currentStart = intervals[i][0];
            int currentEnd = intervals[i][1];
            
            // Check if current interval overlaps with last interval
            // They overlap if current starts before or when last ends
            // After sorting, we know: currentStart >= last[0]
            // So we only need to check: currentStart <= last[1]
            if (currentStart <= last[1]) {
                // Intervals overlap - merge them
                // The merged interval starts at last[0] (already set)
                // and ends at the maximum of the two end points
                // We need max because current might be contained in last
                // Example: last=[1,5], current=[2,3] → merge to [1,5]
                last[1] = max(last[1], currentEnd);
            }
            else {
                // No overlap - current interval is separate
                // Add it as a new interval to result
                result.push_back(intervals[i]);
            }
        }
        
        return result;
    }
};
```

### Alternative: Using structured binding (C++17)

```cpp
class Solution {
public:
    vector<vector<int>> merge(vector<vector<int>>& intervals) {
        if (intervals.empty()) return {};
        
        sort(intervals.begin(), intervals.end());
        
        vector<vector<int>> result;
        result.push_back(intervals[0]);
        
        for (int i = 1; i < intervals.size(); i++) {
            auto& [lastStart, lastEnd] = result.back();
            auto [currStart, currEnd] = intervals[i];
            
            if (currStart <= lastEnd) {
                // Merge: update end of last interval
                lastEnd = max(lastEnd, currEnd);
            } else {
                // No overlap: add new interval
                result.push_back({currStart, currEnd});
            }
        }
        
        return result;
    }
};
```

### Complexity Analysis
- **Time Complexity**: O(n log n) - Dominated by sorting. The merging pass is O(n).
- **Space Complexity**: O(1) or O(n) - Depends on sorting implementation. If we don't count output array, only O(1) extra space used. Some sorts use O(log n) or O(n) space.

## Edge Cases
- **Single interval**: `[[1,3]]` → `[[1,3]]`
- **All overlap**: `[[1,4],[2,5],[3,6]]` → `[[1,6]]`
- **None overlap**: `[[1,2],[3,4],[5,6]]` → `[[1,2],[3,4],[5,6]]`
- **Touching intervals**: `[[1,2],[2,3]]` → `[[1,3]]` (considered overlapping)
- **One contains another**: `[[1,5],[2,3]]` → `[[1,5]]`
- **Same intervals**: `[[1,3],[1,3]]` → `[[1,3]]`
- **Reverse order input**: Sorting handles this
- **Already merged**: `[[1,10]]` → `[[1,10]]`

## Key Insights & Interview Tips

**Important Observations**:
- Sorting is the key step that makes algorithm efficient
- After sorting, only need to check overlap with previous interval
- Overlap condition: `next.start <= current.end` (after sorting)
- When merging, take max of end points (not just second end)
- Touching intervals (end1 == start2) are considered overlapping

**Common Mistakes to Avoid**:
- Not sorting intervals first
- Using && `current.start >= last.start` in overlap condition (redundant after sort)
- Using `current.end` instead of `max(last.end, current.end)` when merging
- Not handling case where current interval is contained in previous
- Treating touching intervals as non-overlapping (depends on problem statement)
- Modifying intervals in-place without considering it might affect sorting

**What Interviewers Look For**:
- Recognition that sorting simplifies the problem
- Understanding of overlap condition
- Proper merging logic (using max for end point)
- Handling edge cases (contained intervals, touching intervals)
- Clean code with good variable names

**Follow-up Questions**:
- Q: "What if intervals can have negative values?"
- A: Algorithm works the same way, sorting still works correctly

- Q: "What if you can't modify the input?"
- A: Create a copy before sorting, or sort indices instead

- Q: "How would you insert a new interval into already merged intervals?"
- A: LeetCode 57 - can do in O(n) without full re-sort

- Q: "What if intervals come as a stream?"
- A: Need different approach, can't pre-sort. Use interval tree or similar data structure.

- Q: "How to find overlapping interval pairs (not merge)?"
- A: After sorting, use two pointers or sweep line algorithm

**Related Problems**:
- LeetCode 57: Insert Interval
- LeetCode 252: Meeting Rooms (can attend all meetings?)
- LeetCode 253: Meeting Rooms II (minimum rooms needed)
- LeetCode 435: Non-overlapping Intervals (minimum removals)
- LeetCode 986: Interval List Intersections

**Interval Patterns**:
```
Overlap conditions (assuming sorted by start):
  [a,b] and [c,d] where a <= c

  Overlap: c <= b
  No overlap: c > b

Merge result: [a, max(b,d)]

Types of overlaps:
1. Partial:  |-----|
                |-----|

2. Complete: |----------|
               |-----|

3. Touching:  |-----|
                    |-----|
```

**Alternative Approach** (not better, but conceptual):
- Use sweep line algorithm with events (start/end)
- Track open intervals, merge when count changes
- More complex, same O(n log n) complexity
