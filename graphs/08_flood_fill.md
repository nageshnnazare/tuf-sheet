# 08. Flood Fill Algorithm
**LeetCode**: [733 - Flood Fill](https://leetcode.com/problems/flood-fill/)  
**Difficulty**: Easy (Matrix Traversal)

## Problem Statement
An image is represented by an `m x n` integer grid `image` where `image[i][j]` represents the pixel value of the image.

You are also given three integers `sr`, `sc`, and `color`. You should perform a **flood fill** on the image starting from the pixel `image[sr][sc]`.

To perform a flood fill, consider the starting pixel, plus any pixels connected **4-directionally** to the starting pixel of the same color as the starting pixel, plus any pixels connected 4-directionally to those pixels (also with the same color), and so on. Replace the color of all of the aforementioned pixels with `color`.

Return the modified image after performing the flood fill.

## Examples
### Example 1
Input: `image = [[1,1,1],[1,1,0],[1,0,1]]`, `sr = 1, sc = 1, color = 2`  
Output: `[[2,2,2],[2,2,0],[2,0,1]]`  
Explanation: From the center at `(1,1)`, all 4-directionally connected pixels with the same color `1` are changed to `2`.

## Constraints
- `m == image.length`
- `n == image[i].length`
- `1 <= m, n <= 50`
- `0 <= image[i][j], color < 2^16`
- `0 <= sr < m`, `0 <= sc < n`

## Visual Explanation
### ASCII Diagram: The "Paint Bucket" Tool
If you've ever used the "Fill" tool in MS Paint, you've used Flood Fill.
```
Initial (sr=1, sc=1):       After Flood Fill (color=2):
[1, 1, 1]                   [2, 2, 2]
[1, 1, 0]                   [2, 2, 0]
[1, 0, 1]                   [2, 0, 1]
```
The logic only spreads through pixels that share the **exact same initial color** as the starting pixel.

## Approach & Intuition
Flood Fill is essentially a traversal (DFS or BFS) constrained by color values.
1. **Identify the Color to Change**: Store the color of `image[sr][sc]` (let's call it `initialColor`).
2. **Handle Special Case**: If `initialColor == color`, we don't need to do anything (returning immediately avoids infinite recursion).
3. **Traverse**: Start a DFS/BFS from `(sr, sc)`.
4. **Logic**:
   - Change current cell color to `color`.
   - For each 4-directional neighbor:
     - If it's within bounds AND has the `initialColor`:
       - Recursively visit it.

## Algorithm Steps
1. Store `initialColor = image[sr][sc]`.
2. If `initialColor == color`, return `image`.
3. Create a result image copy `ans = image`.
4. Define `dfs(r, c, initialColor, newColor, ans)`:
   - `ans[r][c] = newColor`.
   - Loop through 4 directions (Up, Down, Left, Right).
   - For each neighbor `(nr, nc)`:
     - If `nr, nc` safe AND `ans[nr][nc] == initialColor`:
       - `dfs(nr, nc, ...)`.
5. Call `dfs(sr, sc, ...)`.
6. Return `ans`.

## Dry Run (Step-by-Step Execution)
Input: `[[1,1,1],[1,1,0]]`, `sr=1, sc=1, color=2`
1. `initialColor = 1`. `newColor = 2`.
2. `dfs(1, 1)`:
   - `image[1][1] = 2`.
   - Neighbors: 
     - **Up (0,1)**: color 1. `dfs(0,1)`.
       - `image[0,1] = 2`.
       - Neighbors: `(0,0)` color 1, `(0,2)` color 1.
       - ... spreads to `(0,0), (0,2)`.
     - **Left (1,0)**: color 1. `dfs(1,0)`.
       - `image[1,0] = 2`.
     - **Right (1,2)**: color 0. Skip.
3. Result grid is updated.

## C++ Solution
### Code with Detailed Comments
```cpp
#include <vector>
using namespace std;

class Solution {
private:
    /**
     * Recursive DFS worker for Flood Fill.
     */
    void dfs(int r, int c, int initialColor, int newColor, 
             vector<vector<int>>& ans, int dr[], int dc[]) {
        
        // Step 1: Replace current pixel color
        ans[r][c] = newColor;
        
        int n = ans.size();
        int m = ans[0].size();
        
        // Step 2: Explore 4 neighbors
        for (int i = 0; i < 4; i++) {
            int nrow = r + dr[i];
            int ncol = c + dc[i];
            
            // Check boundaries AND if neighbor has the original color
            if (nrow >= 0 && nrow < n && ncol >= 0 && ncol < m &&
                ans[nrow][ncol] == initialColor) {
                
                // Recurse to fill the neighbor
                dfs(nrow, ncol, initialColor, newColor, ans, dr, dc);
            }
        }
    }

public:
    vector<vector<int>> floodFill(vector<vector<int>>& image, int sr, int sc, int color) {
        int initialColor = image[sr][sc];
        
        // IMPORTANT: If start pixel already has the new color, 
        // we must return immediately to prevent infinite recursion.
        if (initialColor == color) return image;
        
        vector<vector<int>> ans = image; // Copy original image
        
        // Pre-define direction vectors for cleaner code
        int dr[] = {-1, 0, 1, 0};
        int dc[] = {0, 1, 0, -1};
        
        dfs(sr, sc, initialColor, color, ans, dr, dc);
        
        return ans;
    }
};
```

### Complexity Analysis
- **Time Complexity**: **O(M * N)**
    - In the worst case, every pixel in the image might have the same initial color and be reached.
- **Space Complexity**: **O(M * N)**
    - For the recursion stack in the worst-case (a grid that is a single long snake-like 1D path).

## Edge Cases
- **New color == Initial color**: Infinite recursion risk if not handled.
- **1x1 Image**: Handled naturally.
- **Starting on an island of different color**: Only the start pixel is changed (if it matched neighbors it would spread, but here we only check neighbors matching the starting pixel).

## Key Insights & Interview Tips
- **BFS Alternative**: You can also use a queue. BFS is often slightly better for memory on very "fat" recursive trees, but DFS is more concise to write.
- **Why no `visited` array?**: Usually, we need a `visited` array in graph traversals. Here, the **act of changing the color** to the `newColor` serves as a "visited" marker. This is only true if `initialColor != newColor`. 
- **Interview Tip**: Always mention the `initialColor == color` check. It's the most common trap in this problem.
