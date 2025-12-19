# 23. Rotate Matrix by 90 Degrees
**LeetCode**: [48 - Rotate Image](https://leetcode.com/problems/rotate-image/)  
**Difficulty**: Medium

## Problem Statement
You are given an `n x n` 2D matrix representing an image. Rotate the image by 90 degrees (clockwise) in-place.

You have to rotate the image in-place, which means you have to modify the input 2D matrix directly. DO NOT allocate another 2D matrix and do the rotation.

## Examples

### Example 1
```
Input: matrix = [[1,2,3],[4,5,6],[7,8,9]]
Output: [[7,4,1],[8,5,2],[9,6,3]]

Visual:
1 2 3       7 4 1
4 5 6  -->  8 5 2
7 8 9       9 6 3
```

### Example 2
```
Input: matrix = [[5,1,9,11],[2,4,8,10],[13,3,6,7],[15,14,12,16]]
Output: [[15,13,2,5],[14,3,4,1],[12,6,8,9],[16,7,10,11]]

Visual:
5  1  9  11       15 13  2  5
2  4  8  10  -->  14  3  4  1
13 3  6  7        12  6  8  9
15 14 12 16       16  7 10 11
```

## Constraints
- `n == matrix.length == matrix[i].length`
- `1 <= n <= 20`
- `-1000 <= matrix[i][j] <= 1000`

## Visual Explanation

### ASCII Diagram
```
90° Clockwise Rotation Pattern:

Original:           Transpose:          Reverse Rows:
[0,0] [0,1] [0,2]   [0,0] [1,0] [2,0]   [2,0] [1,0] [0,0]
[1,0] [1,1] [1,2]   [0,1] [1,1] [2,1]   [2,1] [1,1] [0,1]
[2,0] [2,1] [2,2]   [0,2] [1,2] [2,2]   [2,2] [1,2] [0,2]

Position mapping after 90° rotation:
(i,j) --> (j, n-1-i)

Example with 3x3:
  1 2 3       1 4 7       7 4 1
  4 5 6  -->  2 5 8  -->  8 5 2
  7 8 9       3 6 9       9 6 3
  Original    Transpose   Reverse each row

Key Pattern: Transpose + Reverse Rows = 90° Clockwise Rotation
```

## Approach & Intuition

**Key Insight**: 90-degree clockwise rotation can be achieved by two simple operations:
1. **Transpose** the matrix (swap across main diagonal)
2. **Reverse** each row

**Why this works**:
- Transpose swaps (i,j) with (j,i): `[0,1] --> [1,0]`
- After transpose: element at (i,j) is now at (j,i)
- Reversing row j puts element from column i at column (n-1-i)
- Final position: (j, n-1-i) which is exactly where (i,j) goes in 90° rotation!

**Alternative Approach**: 
- Rotate layer by layer from outside to inside
- More complex to implement but same time complexity

## Algorithm Steps

**Optimal Two-Step Approach**:

1. **Transpose the matrix**:
   - Swap elements across the main diagonal
   - For each i from 0 to n-1:
     - For each j from i+1 to n-1:
       - Swap matrix[i][j] with matrix[j][i]

2. **Reverse each row**:
   - For each row i from 0 to n-1:
     - Reverse the elements in row i
     - Use two pointers: left=0, right=n-1
     - Swap and move pointers until they meet

## Dry Run (Step-by-Step Execution)

**Input**: `matrix = [[1,2,3],[4,5,6],[7,8,9]]`

**Step 1: Transpose**
```
Initial:
1 2 3
4 5 6
7 8 9

i=0, j=1: swap(matrix[0][1], matrix[1][0])
1 4 3
2 5 6
7 8 9

i=0, j=2: swap(matrix[0][2], matrix[2][0])
1 4 7
2 5 6
3 8 9

i=1, j=2: swap(matrix[1][2], matrix[2][1])
1 4 7
2 5 8
3 6 9

After Transpose:
1 4 7
2 5 8
3 6 9
```

**Step 2: Reverse Each Row**
```
Row 0: [1,4,7] → [7,4,1]
Row 1: [2,5,8] → [8,5,2]
Row 2: [3,6,9] → [9,6,3]

Final Result:
7 4 1
8 5 2
9 6 3
```

**Verification of position mapping**:
- Original (0,0)=1 → Final (0,2)=1 ✓ [formula: (0,0)→(0,2)]
- Original (0,2)=3 → Final (2,2)=3 ✓ [formula: (0,2)→(2,0)→(2,2) after reverse]
- Original (2,0)=7 → Final (0,0)=7 ✓ [formula: (2,0)→(0,2)→(0,0) after reverse]

## C++ Solution

### Code with Detailed Comments

```cpp
class Solution {
public:
    void rotate(vector<vector<int>>& matrix) {
        int n = matrix.size();
        
        // Step 1: Transpose the matrix
        // Swap elements across the main diagonal (i,j) <--> (j,i)
        // We only need to iterate over the upper triangle of the matrix
        // because swapping is symmetric
        for (int i = 0; i < n; i++) {
            // Start j from i+1 to avoid swapping diagonal elements
            // and to avoid swapping the same pair twice
            for (int j = i + 1; j < n; j++) {
                // Swap matrix[i][j] with matrix[j][i]
                int temp = matrix[i][j];
                matrix[i][j] = matrix[j][i];
                matrix[j][i] = temp;
            }
        }
        
        // Step 2: Reverse each row
        // After transpose, reversing rows gives us 90° clockwise rotation
        for (int i = 0; i < n; i++) {
            // Use two-pointer approach to reverse row i
            int left = 0;
            int right = n - 1;
            
            while (left < right) {
                // Swap elements at left and right positions
                int temp = matrix[i][left];
                matrix[i][left] = matrix[i][right];
                matrix[i][right] = temp;
                
                // Move pointers towards center
                left++;
                right--;
            }
        }
    }
};
```

### Alternative: Using STL reverse

```cpp
class Solution {
public:
    void rotate(vector<vector<int>>& matrix) {
        int n = matrix.size();
        
        // Step 1: Transpose
        for (int i = 0; i < n; i++) {
            for (int j = i + 1; j < n; j++) {
                swap(matrix[i][j], matrix[j][i]);
            }
        }
        
        // Step 2: Reverse each row using STL
        for (int i = 0; i < n; i++) {
            reverse(matrix[i].begin(), matrix[i].end());
        }
    }
};
```

### Complexity Analysis
- **Time Complexity**: O(n²) - We visit each cell twice: once during transpose and once during row reversal. Total cells = n², so O(n²)
- **Space Complexity**: O(1) - Only using a constant amount of extra space for temp variable. All operations are in-place.

## Edge Cases
- **1x1 matrix**: `[[1]]` → `[[1]]` (no change needed)
- **2x2 matrix**: `[[1,2],[3,4]]` → `[[3,1],[4,2]]`
- **Matrix with negative numbers**: Works the same way
- **Matrix with duplicate values**: Order is preserved correctly
- **All same values**: `[[5,5],[5,5]]` → `[[5,5],[5,5]]`

## Key Insights & Interview Tips

**Important Observations**:
- Transpose + Reverse Rows = 90° Clockwise
- Transpose + Reverse Columns = 90° Counterclockwise
- For clockwise: (i,j) → (j, n-1-i)
- For counterclockwise: (i,j) → (n-1-j, i)
- Pattern recognition is key to solving this elegantly

**Common Mistakes to Avoid**:
- Transposing the entire matrix instead of just upper triangle (causes double swap)
- Starting j from 0 instead of i+1 in transpose loop
- Trying to rotate by directly moving elements (very complex)
- Forgetting that this is an in-place operation

**What Interviewers Look For**:
- Recognition of the transpose + reverse pattern
- Understanding why we only iterate upper triangle during transpose
- Ability to explain the position mapping formula
- Knowledge of counterclockwise rotation variant
- Clean, readable code

**Follow-up Questions**:
- Q: "How would you rotate counterclockwise?"
- A: Transpose then reverse each column (or reverse rows then transpose)

- Q: "Can you rotate by 180 degrees?"
- A: Apply 90° rotation twice, or reverse rows then reverse columns

- Q: "What about non-square matrices?"
- A: Cannot rotate in-place; need extra space. 90° rotation changes dimensions (m×n → n×m)

- Q: "How to rotate layer by layer?"
- A: More complex approach - rotate outer layer, then recursively inner layers
