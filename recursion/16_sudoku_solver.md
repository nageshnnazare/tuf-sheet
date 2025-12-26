# 16. Sudoku Solver
**LeetCode**: [37 - Sudoku Solver](https://leetcode.com/problems/sudoku-solver/)  
**Difficulty**: Hard

## Problem Statement
Write a program to solve a Sudoku puzzle by filling the empty cells. Empty cells are indicated by the character `.`

A sudoku solution must satisfy all of the following rules:
1. Each of the digits `1-9` must occur exactly once in each row.
2. Each of the digits `1-9` must occur exactly once in each column.
3. Each of the digits `1-9` must occur exactly once in each of the 9 `3x3` sub-boxes of the grid.

## Examples
### Example 1
Input: `board` (a 9x9 grid with some numbers and some '.')  
Output: `board` (the same grid completely filled according to Sudoku rules)

## Constraints
- `board.length == 9`
- `board[i].length == 9`
- `board[i][j]` is a digit or `.`
- It is guaranteed that the input board has only one unique solution.

## Visual Explanation
### ASCII Diagram: Sub-grid Math & Traversal
Solving Sudoku is about **narrowing down possibilities**. 
For any cell `(r, c)`, the index of its 3x3 block top-left corner is `(3*(r/3), 3*(c/3))`.

```
Board Legend: (r, c)
(0,0) (0,1) (0,2) | (0,3) (0,4) (0,5) | (0,6) (0,7) (0,8)
(1,0) (1,1) (1,2) | (1,3) (1,4) (1,5) | (1,6) (1,7) (1,8)
(2,0) (2,1) (2,2) | (2,3) (2,4) (2,5) | (2,6) (2,7) (2,8)
------------------+-------------------+------------------
(3,0) (3,1) (3,2) | (3,3) (3,4) (3,5) | (3,6) (3,7) (3,8)
...               |                   |
```

## Approach & Intuition
Sudoku Solver is a classic **Depth-First Search (DFS)** on the state space of the board. 
- **The State Space**: Every empty cell is a decision point with up to 9 branches.
- **The Pruning**: Many branches are cut early using the `isValid` check. If a number is already in the row, column, or box, that branch is never explored.
- **Recursive Backtracking**: If we fill a cell and then find that no number works for a subsequent cell, our current cell's choice must be wrong. We "undo" (backtrack) and try the next available number.
- **Why Boolean Return?**: This is a **Search Problem**, not an **Enumerate Problem**. We don't want all solutions (there's only one); we want to stop as soon as our DFS finds the goal. A `true` return signals the entire stack to collapse and finish.

## Algorithm Steps
1. **Iterate** over every row `i` and column `j`.
2. If `board[i][j]` is empty (`'.'`):
   - For each candidate `char c` from `'1'` to `'9'`:
     - **Constraint Check**: Use `isValid(board, i, j, c)`.
     - If valid:
       - **Assign**: `board[i][j] = c`.
       - **Recursive Call**: `if (solve(board) == true)` -> Solution found! Propagate `true` up.
       - **Backtrack**: `board[i][j] = '.'` (Placement didn't lead to a solution).
   - If no values 1-9 work for this empty cell, return `false`.
3. If no empty cells are found (loops complete), return `true`.

## Dry Run (Step-by-Step Execution)
Imagine an empty cell at `(0, 2)` and the next at `(0, 5)`.
1. **T1 (t=0ms)**: `solve()` finds `(0, 2)` is empty.
2. **T2**: Tries `'1'`. `isValid` checks Row 0, Col 2, Box 0. Finds '1' already in Row 0. Skips.
3. **T3**: Tries `'2'`. `isValid` passes. `board[0][2]` becomes `'2'`.
4. **T4**: Recursive `solve()` is called. Scans and finds next empty at `(0, 5)`.
5. **T5**: For `(0, 5)`, it tries `'1'` through `'9'`. All fail because of existing board constraints.
6. **T6**: `solve()` for `(0, 5)` returns `false`.
7. **T7**: Back at `(0, 2)`, the loop continues. `board[0][2]` is reset to `'.'`.
8. **T8**: Tries `'3'` for `(0, 2)`. `isValid` passes. `board[0][2]` becomes `'3'`.
9. **T9**: Recursive `solve()` called again. This time, `(0, 5)` might find a valid number.

## C++ Solution
### Code with Detailed Comments
```cpp
#include <iostream>
#include <vector>
using namespace std;

class Solution {
public:
    /**
     * isValid: The core constraint checker.
     * We check three constraints in a single loop for efficiency.
     */
    bool isValid(vector<vector<char>>& board, int row, int col, char c) {
        for (int i = 0; i < 9; i++) {
            // Check horizontal line (Row)
            if (board[row][i] == c) return false;
            
            // Check vertical line (Column)
            if (board[i][col] == c) return false;
            
            // Check 3x3 square (Sub-box)
            // 3*(row/3) gives top row of the box
            // 3*(col/3) gives left col of the box
            // i/3 and i%3 traverses the 3x3 box
            if (board[3 * (row / 3) + i / 3][3 * (col / 3) + i % 3] == c) 
                return false;
        }
        return true;
    }

    /**
     * solve: Recursive backtracking function.
     * Complexity: Exponential, but heavily pruned by isValid.
     */
    bool solve(vector<vector<char>>& board) {
        // Linear scan for the next decision point (empty cell)
        for (int i = 0; i < 9; i++) {
            for (int j = 0; j < 9; j++) {
                
                if (board[i][j] == '.') {
                    // Try all possible digits
                    for (char c = '1'; c <= '9'; c++) {
                        if (isValid(board, i, j, c)) {
                            // 1. PLACE
                            board[i][j] = c;
                            
                            // 2. RECURSE: If this leads to a solution, stop and return true
                            if (solve(board)) return true;
                            
                            // 3. BACKTRACK: If it doesn't, we must reset and try the next char
                            board[i][j] = '.';
                        }
                    }
                    
                    // Critical: If no character 1-9 works here, this board state is invalid
                    return false;
                }
            }
        }
        // Base case implicitly reached: No empty cells found
        return true;
    }

    void solveSudoku(vector<vector<char>>& board) {
        solve(board);
    }
};
```

### Complexity Analysis
- **Time Complexity**: **O(9^(m))** - where $m$ is the number of empty spaces. In reality, it's much faster due to the rules significantly pruning the search tree.
- **Space Complexity**: **O(1)** - The board is modified in-place. The recursion stack can consume memory for up to 81 function calls (one for each cell).

## Edge Cases
- **Completely Empty Board**: Will find one valid solution (starts with 1, 2, 3...).
- **Nearly Full Board**: Solve very quickly.
- **Impossible Board**: `solve` will return `false`, though the problem guarantees a unique solution.

## Key Insights & Interview Tips
- **Pruning**: Sudoku is the best problem to explain the concept of **Pruning**. We don't just "try everything"; we only try what is mathematically possible at that moment.
- **State Restoration**: Always emphasize that the `board[i][j] = '.'` step is what makes it "Backtracking" rather than just a simple recursion.
- **Interview Tip**: If an interviewer asks how to speed this up, mention **Heuristics**. Instead of scanning `(0,0)` to `(8,8)`, always pick the cell with the **fewest possible legal numbers** remaining. This is called the "Most Constrained Variable" heuristic.
