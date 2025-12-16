# 24. Spiral Traversal of Matrix
**LeetCode**: [54 - Spiral Matrix](https://leetcode.com/problems/spiral-matrix/)  
**Difficulty**: Medium

## Problem Statement
Given an `m x n` matrix, return all elements of the matrix in spiral order (clockwise from outside to inside).

## Examples

### Example 1
```
Input: matrix = [[1,2,3],[4,5,6],[7,8,9]]
Output: [1,2,3,6,9,8,7,4,5]

Spiral path:
1 → 2 → 3
        ↓
4 → 5   6
↑       ↓
7 ← 8 ← 9
```

### Example 2
```
Input: matrix = [[1,2,3,4],[5,6,7,8],[9,10,11,12]]
Output: [1,2,3,4,8,12,11,10,9,5,6,7]

Spiral path:
1  → 2  → 3  → 4
                ↓
5  → 6  → 7    8
↑              ↓
9  ← 10 ← 11 ← 12
```

## Constraints
- `m == matrix.length`
- `n == matrix[i].length`
- `1 <= m, n <= 10`
- `-100 <= matrix[i][j] <= 100`

## Visual Explanation

### ASCII Diagram
```
4-Boundary Approach:

Initial boundaries for 3x4 matrix:
top = 0, bottom = 2, left = 0, right = 3

[1   2   3   4]  ← top row
[5   6   7   8]
[9   10  11  12] ← bottom row
 ↑           ↑
left        right

Step-by-step traversal:

1. Move RIGHT (top row, left to right):
   [1→ 2→ 3→ 4]
   top++  (top = 1)

2. Move DOWN (right column, top to bottom):
   [4]
   [8↓]
   [12↓]
   right--  (right = 2)

3. Move LEFT (bottom row, right to left):
   [12← 11← 10← 9]
   bottom--  (bottom = 1)

4. Move UP (left column, bottom to top):
   [5↑]
   [9]
   left++  (left = 1)

5. Repeat until boundaries cross
   Result: [1,2,3,4,8,12,11,10,9,5,6,7]
```

## Approach & Intuition

**Key Insight**: Spiral traversal follows a pattern of moving in 4 directions (right → down → left → up) and shrinking boundaries after each direction.

**Strategy**:
1. Maintain 4 boundaries: top, bottom, left, right
2. Traverse in order: RIGHT → DOWN → LEFT → UP
3. After completing each direction, shrink the corresponding boundary
4. Continue until all elements are visited (boundaries don't cross)

**Why boundaries work**:
- Each direction traversal "consumes" one row or column
- Shrinking boundaries ensures we don't revisit cells
- Stop when boundaries cross (top > bottom OR left > right)

## Algorithm Steps

1. **Initialize boundaries**:
   - `top = 0`, `bottom = m-1`
   - `left = 0`, `right = n-1`
   - Create result vector

2. **While boundaries don't cross** (top <= bottom AND left <= right):
   
   a. **Move RIGHT**: Traverse top row from left to right
      - Add matrix[top][i] for i from left to right
      - Increment top (move boundary down)
   
   b. **Move DOWN**: Traverse right column from top to bottom
      - Add matrix[i][right] for i from top to bottom
      - Decrement right (move boundary left)
   
   c. **Move LEFT** (if still valid): Traverse bottom row from right to left
      - Check if top <= bottom (ensure row exists)
      - Add matrix[bottom][i] for i from right to left
      - Decrement bottom (move boundary up)
   
   d. **Move UP** (if still valid): Traverse left column from bottom to top
      - Check if left <= right (ensure column exists)
      - Add matrix[i][left] for i from bottom to top
      - Increment left (move boundary right)

3. **Return result**

## Dry Run (Step-by-Step Execution)

**Input**: `matrix = [[1,2,3],[4,5,6],[7,8,9]]` (3×3)

**Initialization**:
- top = 0, bottom = 2, left = 0, right = 2
- result = []

**Round 1**:
```
Step 1 - Move RIGHT (top row):
Add: 1, 2, 3
top = 1
result = [1,2,3]

Step 2 - Move DOWN (right column):
Add: 6, 9 (from rows 1 and 2)
right = 1
result = [1,2,3,6,9]

Step 3 - Move LEFT (bottom row):
Check: top(1) <= bottom(2) ✓
Add: 8, 7
bottom = 1
result = [1,2,3,6,9,8,7]

Step 4 - Move UP (left column):
Check: left(0) <= right(1) ✓
Add: 4 (from row 1)
left = 1
result = [1,2,3,6,9,8,7,4]
```

**Round 2**:
```
Boundaries: top=1, bottom=1, left=1, right=1
Check: top(1) <= bottom(1) AND left(1) <= right(1) ✓

Step 1 - Move RIGHT:
Add: 5
top = 2
result = [1,2,3,6,9,8,7,4,5]

Step 2 - Move DOWN:
Check: top(2) <= bottom(1) ✗
Loop ends
```

**Final Output**: `[1,2,3,6,9,8,7,4,5]` ✓

## C++ Solution

### Code with Detailed Comments

```cpp
class Solution {
public:
    vector<int> spiralOrder(vector<vector<int>>& matrix) {
        vector<int> result;
        
        // Handle empty matrix
        if (matrix.empty() || matrix[0].empty()) {
            return result;
        }
        
        int m = matrix.size();    // number of rows
        int n = matrix[0].size(); // number of columns
        
        // Initialize four boundaries
        int top = 0;
        int bottom = m - 1;
        int left = 0;
        int right = n - 1;
        
        // Continue while boundaries don't cross
        while (top <= bottom && left <= right) {
            
            // 1. Move RIGHT: Traverse top row from left to right
            for (int col = left; col <= right; col++) {
                result.push_back(matrix[top][col]);
            }
            top++; // Move top boundary down (this row is done)
            
            // 2. Move DOWN: Traverse right column from top to bottom
            for (int row = top; row <= bottom; row++) {
                result.push_back(matrix[row][right]);
            }
            right--; // Move right boundary left (this column is done)
            
            // 3. Move LEFT: Traverse bottom row from right to left
            // Check if there's still a valid row to traverse
            if (top <= bottom) {
                for (int col = right; col >= left; col--) {
                    result.push_back(matrix[bottom][col]);
                }
                bottom--; // Move bottom boundary up (this row is done)
            }
            
            // 4. Move UP: Traverse left column from bottom to top
            // Check if there's still a valid column to traverse
            if (left <= right) {
                for (int row = bottom; row >= top; row--) {
                    result.push_back(matrix[row][left]);
                }
                left++; // Move left boundary right (this column is done)
            }
        }
        
        return result;
    }
};
```

### Why boundary checks are needed for LEFT and UP:

```cpp
// Example where check matters: 1×3 matrix [[1,2,3]]
// After RIGHT: [1,2,3], top=1
// After DOWN: [1,2,3], right=-1
// Without check: Would try LEFT traversal with top=1, bottom=0 (invalid!)
// With check: top(1) > bottom(0), skips LEFT ✓

// Example: 3×1 matrix [[1],[2],[3]]
// After RIGHT: [1], top=1
// After DOWN: [1,2,3], right=-1
// After LEFT: (skipped), bottom=-1
// Without check: Would try UP with left=1, right=-1 (invalid!)
// With check: left(1) > right(-1), skips UP ✓
```

### Complexity Analysis
- **Time Complexity**: O(m × n) - We visit each element exactly once
- **Space Complexity**: O(1) - Not counting the result vector which is required for output. Only using constant extra space for boundaries.

## Edge Cases
- **Single row**: `[[1,2,3]]` → `[1,2,3]`
- **Single column**: `[[1],[2],[3]]` → `[1,2,3]`
- **Single element**: `[[5]]` → `[5]`
- **2×2 matrix**: `[[1,2],[3,4]]` → `[1,2,4,3]`
- **Empty matrix**: `[[]]` → `[]`
- **Rectangle (more rows)**: `[[1,2],[3,4],[5,6]]` → `[1,2,4,6,5,3]`
- **Rectangle (more cols)**: `[[1,2,3],[4,5,6]]` → `[1,2,3,6,5,4]`

## Key Insights & Interview Tips

**Important Observations**:
- Need to check boundaries before LEFT and UP moves (avoid revisiting)
- Each complete round processes outer "layer" of the matrix
- Single row/column matrices need special handling via boundary checks
- Total elements visited = m × n (can verify result size)

**Common Mistakes to Avoid**:
- Forgetting boundary checks for LEFT and UP movements
- Not updating boundaries after each direction
- Using visited array (unnecessary, boundaries are sufficient)
- Wrong loop conditions (should be <= not <)
- Processing boundaries in wrong order

**What Interviewers Look For**:
- Clean boundary management
- Understanding when boundary checks are needed
- Handling rectangular matrices (not just square)
- Clear explanation of the spiral order pattern
- Ability to trace through examples

**Follow-up Questions**:
- Q: "Can you do this recursively?"
- A: Yes, after processing outer layer, recursively process inner matrix with updated boundaries

- Q: "What about spiral matrix II (generate spiral matrix)?"
- A: Same boundary approach, but fill matrix instead of reading

- Q: "How to handle anti-clockwise spiral?"
- A: Change order to DOWN → RIGHT → UP → LEFT

- Q: "Why not use a visited array?"
- A: Boundaries are more efficient - O(1) space vs O(m×n), and naturally avoid revisits

**Key Pattern Recognition**:
- This is a **simulation** problem - simulate the exact process described
- Boundary-based approach is cleaner than direction-based with visited array
- Think in terms of "layers" or "shells" of the matrix
