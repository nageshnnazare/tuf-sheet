# 15. N-Queens Problem
**LeetCode**: [51 - N-Queens](https://leetcode.com/problems/n-queens/)  
**Difficulty**: Hard (Classic Backtracking)

## Problem Statement
The **N-Queens** puzzle is the problem of placing `N` chess queens on an `N x N` chessboard such that no two queens attack each other. This means:
1. No two queens are in the same **row**.
2. No two queens are in the same **column**.
3. No two queens are on the same **diagonal**.

## Examples
### Example 1
Input: `n = 4`  
Output: `[[".Q..","...Q","Q...","..Q."],["..Q.","Q...","...Q",".Q.."]]`  
Explanation: There are two valid ways to place 4 queens on a 4x4 board.

## Constraints
- `1 <= n <= 9` (N=10+ becomes very slow due to N! complexity)

## Visual Explanation
### ASCII Diagram: The Geometry of Diagonals
To check safety in $O(1)$, we map every cell $(r, c)$ to a unique index in three hashing arrays:

1. **Left Row**: Every cell in a horizontal line has the same `row` index.
2. **Lower Diagonal (/)**: Every cell in these diagonals has the same `row + col` sum.
   - Example (4x4): Cells $(0,2), (1,1), (2,0)$ all have sum `2`.
3. **Upper Diagonal (\)**: Every cell in these diagonals has the same `row - col` difference. To avoid negative indices, we use `(n-1) + (col - row)`.
   - Example (4x4): Cells $(0,0), (1,1), (2,2), (3,3)$ all have diff `0`, mapping to index `3`.

```
Board (4x4) Indices:
(0,0) (0,1) (0,2) (0,3)
(1,0) (1,1) (1,2) (1,3)
(2,0) (2,1) (2,2) (2,3)
(3,0) (3,1) (3,2) (3,3)

Lower Diagonals (row + col):
0  1  2  3
1  2  3  4
2  3  4  5
3  4  5  6  (Indices range from 0 to 2n-2)
```

## Approach & Intuition
The N-Queens problem is a "Search and Backtrack" challenge.
- **Backtracking**: We place one queen per column. For each column, we try every row. If a placement is "safe", we move to the next column. If no row in the next column is safe, we "backtrack" and move our current queen to the next available row.
- **Optimization (Hashing)**: A naive implementation checks all three attack lines (Row, Upper Diag, Lower Diag) in $O(N)$ for every cell. By using three boolean arrays (hashes), we can check safety in **$O(1)$**.
- **State management**: When a queen is placed at `(r, c)`, we mark `leftRow[r]`, `lowerDiag[r+c]`, and `upperDiag[n-1+c-r]` as `1`. When we backtrack, we reset them to `0`.

## Algorithm Steps
1. **Initialize** the board with `.` and create three hashing arrays (size $N$, $2N-1$, $2N-1$).
2. **`solve(col)`**:
   - **Base Case**: If `col == n`, all queens are placed. Add the current board configuration to the result list.
   - **Iterative Decisions**: For `row` from `0` to `n-1`:
     - If `(row, col)` is safe using hash checks:
       - **Place**: Mark hashes and set `board[row][col] = 'Q'`.
       - **Recurse**: `solve(col + 1)`.
       - **Backtrack**: Unmark hashes and reset `board[row][col] = '.'`.

## Dry Run (Step-by-Step Execution)
Input: `n = 4`
1. **Start `solve(0)`**:
   - `row 0`: Place Q at (0,0). Hashes: `row[0]=1, low[0]=1, up[3]=1`. 
   - Call `solve(1)`.
2. **`solve(1)`**:
   - `row 0`: Occupied (row hash).
   - `row 1`: LowerDiag conflict (1+1=2 vs 0+0=0, wait, look at diag tree). Occupied (0,0) is diagonal to (1,1).
   - `row 2`: Safe! Place Q at (2,1). Hashes: `row[2]=1, low[3]=1, up[2]=1`.
   - Call `solve(2)`.
3. **`solve(2)`**:
   - `row 0`: Occupied.
   - `row 1`: Occupied. 
   - `row 2`: Occupied.
   - `row 3`: Occupied.
   - No rows safe. **BACKTRACK** to `solve(1)`.
4. **`solve(1)`**: 
   - Move Q from (2,1) to (3,1). Hashes: `row[3]=1, low[4]=1, up[1]=1`.
   - ... (Process continues until all valid boards are found).

## C++ Solution
### Code with Detailed Comments
```cpp
#include <iostream>
#include <vector>
#include <string>
using namespace std;

class Solution {
public:
    /**
     * Recursive solver using Branch and Bound (Hashing).
     */
    void findSolutions(int col, vector<string> &board, vector<vector<string>> &ans, 
                       vector<int> &leftRow, vector<int> &upperDiag, vector<int> &lowerDiag, int n) {
        
        // Base Case: Successfully placed a queen in every column
        if (col == n) {
            ans.push_back(board);
            return;
        }

        for (int row = 0; row < n; row++) {
            // THE O(1) SAFETY CHECK:
            // 1. row: horizontal line
            // 2. lowerDiag (sum is constant for / diagonals)
            // 3. upperDiag (difference is constant for \ diagonals)
            if (leftRow[row] == 0 && 
                lowerDiag[row + col] == 0 && 
                upperDiag[n - 1 + col - row] == 0) {
                
                // 1. PLACE
                board[row][col] = 'Q';
                leftRow[row] = 1;
                lowerDiag[row + col] = 1;
                upperDiag[n - 1 + col - row] = 1;
                
                // 2. RECURSE
                findSolutions(col + 1, board, ans, leftRow, upperDiag, lowerDiag, n);
                
                // 3. BACKTRACK (Reset state for next row choice in this column)
                board[row][col] = '.';
                leftRow[row] = 0;
                lowerDiag[row + col] = 0;
                upperDiag[n - 1 + col - row] = 0;
            }
        }
    }

    vector<vector<string>> solveNQueens(int n) {
        vector<vector<string>> ans;
        vector<string> board(n, string(n, '.'));
        
        // Optimization structures to avoid O(N) checking in every step
        vector<int> leftRow(n, 0);                 // Horizontal attacks
        vector<int> upperDiag(2 * n - 1, 0);       // \ attacks
        vector<int> lowerDiag(2 * n - 1, 0);       // / attacks
        
        findSolutions(0, board, ans, leftRow, upperDiag, lowerDiag, n);
        return ans;
    }
};
```

### Complexity Analysis
- **Time Complexity**: **O(N!)** - We attempt to place N queens. For the first queen, we have N options, for the second roughly N-2, etc.
- **Space Complexity**: **O(N^2)** - To store the chessboard strings and the resulting vectors. The recursion stack is $O(N)$.

## Edge Cases
- **n=1**: Trivially `[["Q"]]`.
- **n=2, 3**: Returns `[]` as no valid configuration exists (Queens are always attacking).
- **Large n**: For $n > 12$, compute time increases drastically due to the factorial nature of the search space.

## Key Insights & Interview Tips
- **The Diagonal Logic**: Being able to explain *why* `row+col` stays constant for one diagonal and `row-col` for the other is a hallmark of a strong candidate.
- **Branch and Bound**: Mention that hashing transforms a simple backtracking algorithm into a **Branch and Bound** algorithm by cutting off invalid paths in constant time.
- **Interview Tip**: If you implement it column by column, mention that you could also do it row by row—the logic is perfectly symmetrical.
