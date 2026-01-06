# 07. Number of Islands
**LeetCode**: [200 - Number of Islands](https://leetcode.com/problems/number-of-islands/)  
**Difficulty**: Medium (Matrix Traversal)

## Problem Statement
Given an `m x n` 2D binary grid `grid` which represents a map of `'1'`s (land) and `'0'`s (water), return the number of islands.

An **island** is surrounded by water and is formed by connecting adjacent lands horizontally or vertically. You may assume all four edges of the grid are all surrounded by water.

*Note: Some variations (like Striver's A2Z) include diagonal connections (8 directions). We will implement the standard 4-directional version but discuss the 8-directional shift.*

## Examples
### Example 1
Input:
```
grid = [
  ["1","1","1","1","0"],
  ["1","1","0","1","0"],
  ["1","1","0","0","0"],
  ["0","0","0","0","0"]
]
```
Output: `1`

### Example 2
Input:
```
grid = [
  ["1","1","0","0","0"],
  ["1","1","0","0","0"],
  ["0","0","1","0","0"],
  ["0","0","0","1","1"]
]
```
Output: `3`

## Constraints
- `m == grid.length`
- `n == grid[i].length`
- `1 <= m, n <= 300`
- `grid[i][j]` is `'0'` or `'1'`.

## Visual Explanation
### ASCII Diagram: Traversal on a Grid
```
(Island 1)     (Island 2)
[1, 1] [0]     [1, 1]
[1, 0] [0]     [0, 0]
[0, 0] [1]     [0, 0]
      (Island 3)
```
Each island is a "connected component" where nodes are cells `(r, c)` and edges exist between adjacent `'1'`s.

## Approach & Intuition
This is a **Connected Components in a 2D Grid** problem. 
- A grid can be treated as a graph where each cell is a node.
- Two cells are connected if they are both `'1'` and adjacent.
- We iterate through every cell in the grid.
- If we find a `'1'` that hasn't been visited, we found a new island.
- We then start a **BFS** or **DFS** from that cell to mark all connected `'1'`s as visited.

## Algorithm Steps
1. Get dimensions `M` (rows) and `N` (cols).
2. Initialize `visited[M][N]` with 0.
3. Initialize `islands = 0`.
4. Loop `r` from 0 to `M-1`, `c` from 0 to `N-1`:
   - If `grid[r][c] == '1'` AND `!visited[r][c]`:
     - `islands++`.
     - `bfs(r, c)` to mark all connected land cells.
5. Inside `bfs(startR, startC)`:
   - Mark `(startR, startC)` as visited.
   - Push to queue.
   - While queue not empty:
     - Pop `(currR, currC)`.
     - Check its neighbors (Up, Down, Left, Right).
     - If neighbor is within bounds, is `'1'`, and not visited:
       - Mark visited.
       - Push to queue.

## Dry Run (Step-by-Step Execution)
Input: `[[1,1],[0,1]]` (2x2)
1. `vis = [[0,0],[0,0]]`, `islands = 0`.
2. **(0,0)**: `grid=1`, `vis=0`.
   - `islands = 1`. BFS starts.
   - `q = [(0,0)]`, `vis[0][0]=1`.
   - Pop `(0,0)`. Neighbors: `(0,1)` is 1, `(1,0)` is 0.
   - Push `(0,1)`. `vis[0][1]=1`.
   - Pop `(0,1)`. Neighbors: `(1,1)` is 1. `(1,1)` not visited.
   - Push `(1,1)`. `vis[1][1]=1`.
   - Pop `(1,1)`. No more land neighbors.
3. Loops continue, all other `1`s are already `vis=1`.
4. Result: 1.

## C++ Solution
### Code with Detailed Comments
```cpp
#include <vector>
#include <queue>
using namespace std;

class Solution {
private:
    /**
     * Standard BFS for 2D Grid.
     * We use a queue to explore all reachable land cells.
     */
    void bfs(int row, int col, vector<vector<int>>& vis, vector<vector<char>>& grid) {
        vis[row][col] = 1;
        queue<pair<int, int>> q;
        q.push({row, col});
        
        int n = grid.size();
        int m = grid[0].size();
        
        // Define Delta row and Delta col for 4 directions
        // Change these to 8 pairs for 8-directional connectivity
        int dr[] = {-1, 0, 1, 0};
        int dc[] = {0, 1, 0, -1};

        while (!q.empty()) {
            int r = q.front().first;
            int c = q.front().second;
            q.pop();
            
            // Traverse in 4 directions
            for (int i = 0; i < 4; i++) {
                int nrow = r + dr[i];
                int ncol = c + dc[i];
                
                // Boundary check and Land/Visited check
                if (nrow >= 0 && nrow < n && ncol >= 0 && ncol < m 
                    && grid[nrow][ncol] == '1' && !vis[nrow][ncol]) {
                    vis[nrow][ncol] = 1;
                    q.push({nrow, ncol});
                }
            }
        }
    }

public:
    int numIslands(vector<vector<char>>& grid) {
        int n = grid.size();
        int m = grid[0].size();
        
        vector<vector<int>> vis(n, vector<int>(m, 0));
        int count = 0;
        
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < m; j++) {
                // If cell is land and not visited, we found a new island
                if (!vis[i][j] && grid[i][j] == '1') {
                    count++;
                    bfs(i, j, vis, grid);
                }
            }
        }
        return count;
    }
};
```

### Complexity Analysis
- **Time Complexity**: **O(M * N)**
    - Every cell is visited at most constant number of times. 
    - Even if the entire grid is land, each cell enters and exits the queue once.
- **Space Complexity**: **O(M * N)**
    - For the `visited` matrix and the queue (which in the worst case can hold $M \times N$ elements).

## Edge Cases
- **No land**: Grid of all '0's. Output 0.
- **Single cell land**: Output 1.
- **Disconnected tiny islands**: Handle correctly by the nested loops.

## Key Insights & Interview Tips
- **In-place optimization**: Some interviewers allow you to change `grid[i][j]` from `'1'` to `'2'` (visited) to save space. This reduces space complexity to $O(\text{Queue Size})$. **Always ask** before doing this.
- **8 Directions**: If diagonals are allowed, just update the `dr` and `dc` loops to check 8 neighbors instead of 4.
- **Interview Tip**: BFS and DFS are equally valid here. DFS might hit stack overflow on very large grids ($10^6$ cells), so BFS is generally considered "safer" for production-style grid traversals.
