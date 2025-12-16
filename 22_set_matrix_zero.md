# 22. Set Matrix Zeroes
**LeetCode**: [73 - Set Matrix Zeroes](https://leetcode.com/problems/set-matrix-zeroes/)  
**Difficulty**: Medium

## Problem Statement
Given an `m x n` integer matrix `matrix`, if an element is `0`, set its entire row and column to `0`'s. You must do it in place.

## Examples

### Example 1
```
Input: matrix = [[1,1,1],[1,0,1],[1,1,1]]
Output: [[1,0,1],[0,0,0],[1,0,1]]
```
Explanation: The element at position (1,1) is 0, so row 1 and column 1 are set to 0.

### Example 2
```
Input: matrix = [[0,1,2,0],[3,4,5,2],[1,3,1,5]]
Output: [[0,0,0,0],[0,4,5,0],[0,3,1,0]]
```
Explanation: Elements at (0,0) and (0,3) are 0, so their rows and columns are set to 0.

## Constraints
- `m == matrix.length`
- `n == matrix[0].length`
- `1 <= m, n <= 200`
- `-2^31 <= matrix[i][j] <= 2^31 - 1`

## Visual Explanation

### ASCII Diagram
```
Original Matrix:
[ 1  1  1 ]
[ 1  0  1 ]    Zero found at (1,1)
[ 1  1  1 ]

Step 1: Mark first row and column
[ 1  0  1 ]    col[1] marked
[ 0  0  1 ]    row[1] marked
[ 1  1  1 ]

Step 2: Update matrix using markers
[ 1  0  1 ]
[ 0  0  0 ]    Row 1 all zeros
[ 1  0  1 ]    Col 1 all zeros
```

## Approach & Intuition

**Key Insight**: We need to remember which rows and columns to zero out, but we can't modify the matrix while scanning because we'll lose track of original zeros.

**Three Approaches**:

1. **Brute Force (Extra Space)**: Use separate arrays to mark rows and columns - O(m+n) space
2. **Better (Using Markers)**: Use first row and first column as markers - O(1) space but need special handling
3. **Optimal**: Use first row/column as markers with careful ordering

The optimal approach uses the first row and first column of the matrix itself to store the marker information, achieving O(1) space complexity.

## Algorithm Steps

**Optimal O(1) Space Approach**:

1. **Determine if first row/column need zeroing**: 
   - Check if first row contains any zero
   - Check if first column contains any zero
   - Store these as boolean flags

2. **Use first row/column as markers**:
   - For each cell (i,j) from (1,1) onwards:
     - If matrix[i][j] == 0, mark matrix[i][0] = 0 and matrix[0][j] = 0

3. **Zero out cells based on markers**:
   - For each cell (i,j) from (1,1) onwards:
     - If matrix[i][0] == 0 OR matrix[0][j] == 0, set matrix[i][j] = 0

4. **Handle first row and column**:
   - If first row flag was true, zero out entire first row
   - If first column flag was true, zero out entire first column

## Dry Run (Step-by-Step Execution)

**Input**: `matrix = [[1,1,1],[1,0,1],[1,1,1]]`

**Step 1: Check first row and column**
- First row [1,1,1]: no zeros → `firstRowZero = false`
- First column [1,1,1]: no zeros → `firstColZero = false`

**Step 2: Mark using first row/column**
```
Initial:        After marking:
[ 1  1  1 ]     [ 1  0  1 ]  ← matrix[0][1] = 0 (column marker)
[ 1  0  1 ]     [ 0  0  1 ]  ← matrix[1][0] = 0 (row marker)
[ 1  1  1 ]     [ 1  1  1 ]
```

**Step 3: Zero out based on markers** (start from (1,1))
```
Check (1,1): matrix[1][0]=0 OR matrix[0][1]=0? YES → matrix[1][1]=0
Check (1,2): matrix[1][0]=0? YES → matrix[1][2]=0
Check (2,1): matrix[0][1]=0? YES → matrix[2][1]=0
Check (2,2): matrix[2][0]=0 OR matrix[0][2]=0? NO → unchanged

Result:
[ 1  0  1 ]
[ 0  0  0 ]
[ 1  0  1 ]
```

**Step 4: Handle first row/column**
- `firstRowZero = false`: Don't modify first row
- `firstColZero = false`: Don't modify first column

**Final Output**: `[[1,0,1],[0,0,0],[1,0,1]]` ✓

## C++ Solution

### Code with Detailed Comments

```cpp
class Solution {
public:
    void setZeroes(vector<vector<int>>& matrix) {
        int m = matrix.size();
        int n = matrix[0].size();
        
        // Step 1: Determine if first row and first column need to be zeroed
        // We need to store this information separately because we'll use
        // the first row and column as markers for the rest of the matrix
        bool firstRowZero = false;
        bool firstColZero = false;
        
        // Check if first row contains any zero
        for (int j = 0; j < n; j++) {
            if (matrix[0][j] == 0) {
                firstRowZero = true;
                break;
            }
        }
        
        // Check if first column contains any zero
        for (int i = 0; i < m; i++) {
            if (matrix[i][0] == 0) {
                firstColZero = true;
                break;
            }
        }
        
        // Step 2: Use first row and column as markers for the rest
        // If matrix[i][j] is 0, mark its row and column in the first row/column
        for (int i = 1; i < m; i++) {
            for (int j = 1; j < n; j++) {
                if (matrix[i][j] == 0) {
                    matrix[i][0] = 0;  // Mark this row
                    matrix[0][j] = 0;  // Mark this column
                }
            }
        }
        
        // Step 3: Zero out cells based on markers in first row/column
        // We start from (1,1) to avoid overwriting our markers prematurely
        for (int i = 1; i < m; i++) {
            for (int j = 1; j < n; j++) {
                // If this row or column is marked, zero out the cell
                if (matrix[i][0] == 0 || matrix[0][j] == 0) {
                    matrix[i][j] = 0;
                }
            }
        }
        
        // Step 4: Handle first row and column based on flags
        // This must be done last to avoid losing marker information
        if (firstRowZero) {
            for (int j = 0; j < n; j++) {
                matrix[0][j] = 0;
            }
        }
        
        if (firstColZero) {
            for (int i = 0; i < m; i++) {
                matrix[i][0] = 0;
            }
        }
    }
};
```

### Complexity Analysis
- **Time Complexity**: O(m × n) - We traverse the matrix a constant number of times (3 passes: marking, zeroing, first row/col)
- **Space Complexity**: O(1) - Only using two boolean variables for flags. The first row and column of the input matrix are used as markers, which is in-place.

## Edge Cases
- **Single row matrix**: `[[1,0,1]]` → `[[0,0,0]]`
- **Single column matrix**: `[[1],[0],[1]]` → `[[0],[0],[0]]`
- **All zeros**: `[[0,0],[0,0]]` → `[[0,0],[0,0]]`
- **No zeros**: `[[1,2],[3,4]]` → `[[1,2],[3,4]]`
- **Zero in first row only**: Must propagate to entire column
- **Zero in first column only**: Must propagate to entire row
- **Zero at (0,0)**: Both first row and first column should be zeroed

## Key Insights & Interview Tips

**Important Observations**:
- Cannot zero out cells immediately upon finding a zero - would lose information
- First row and column can serve dual purpose: data + markers
- Must handle first row/column separately to avoid premature overwriting
- Order of operations matters: mark first, then zero out, then handle first row/col

**Common Mistakes to Avoid**:
- Modifying matrix while scanning (loses original zero positions)
- Forgetting to check if first row/column originally had zeros
- Handling first row/column before processing inner cells
- Not starting from (1,1) when applying zeros - would corrupt markers

**What Interviewers Look For**:
- Recognition that O(1) space is achievable using matrix itself
- Ability to handle edge cases (first row/column)
- Understanding of why order of operations matters
- Clear explanation of the marker technique

**Follow-up Questions**:
- Q: "What if you can't modify the input matrix?"
- A: Use O(m+n) space with separate boolean arrays for rows and columns
- Q: "Can you do it in one pass?"
- A: No, you need at least two passes - one to mark and one to zero out
