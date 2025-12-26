# 17. M-Coloring Problem
**GeeksforGeeks**: [M-Coloring Problem](https://www.geeksforgeeks.org/problems/m-coloring-problem-1587115620/1)  
**Difficulty**: Medium (Graph Backtracking)

## Problem Statement
Given an undirected graph and a number `M`, determine if the graph can be colored with at most `M` colors such that no two adjacent vertices have the same color. In graph theory, this is the classic "Vertex Coloring" problem. The graph is represented as an adjacency matrix.

## Examples
### Example 1
Input: `N = 4`, `M = 3`, `Edges = (0,1),(1,2),(2,3),(3,0),(0,2)`  
Output: `1` (True)  
Explanation: We can color node 0 with Red, 1 with Green, 2 with Blue, and 3 with Green. No adjacent nodes share a color.

### Example 2
Input: `N = 3`, `M = 2`, `Edges = (0,1),(1,2),(2,0)`  
Output: `0` (False)  
Explanation: This is a triangle (Complete Graph K3). It requires 3 colors, so 2 colors are insufficient.

## Constraints
- `1 <= N <= 20`
- `1 <= E <= N*(N-1)/2`
- `1 <= M <= N`

## Visual Explanation
### ASCII Diagram: Conflict Resolution
Consider a 4-node graph (Square + diagonal):
```
    (0)---(1)    Colors available: {R, G, B}
     | \   |
     |  \  |
    (3)---(2)

1. Color (0) -> R
2. Color (1) -> G (Safe, not connected to any G)
3. Color (2) -> B (Safe, connected to 0(R) and 1(G))
4. Color (3) -> G (Safe, connected to 0(R) and 2(B))
Result: Possible!
```

## Approach & Intuition
The problem is a variant of **Constraint Satisfaction**. We need to assign colors to vertices one by one while satisfying the constraint that `color[u] != color[v]` for all edges `(u, v)`.
- **Backtracking Strategy**: We attempt to color node `0`, then recurse to node `1`, and so on.
- **Trial & Error**: At each node, we try every color from `1` to `M`. If a color is valid (no neighbors have it), we proceed. If we hit a dead-end where no color works for the current node, we backtrack and change the color of the previous node.
- **Early Exit**: Since we only need to know if *any* coloring exists, we use a boolean return type to stop immediately upon a successful configuration.

## Algorithm Steps
1. **Initialize**: Create a `color` array of size `N`, initialized to 0 (no color).
2. **`solve(node)`**:
   - **Base Case**: If `node == N`, we have successfully colored all vertices. Return `true`.
   - **Trial**: For `clr` from `1` to `M`:
     - Check `isSafe(node, clr)`:
       - Iterate through all vertices `k`. If `graph[node][k] == 1` and `color[k] == clr`, return `false`.
     - If `isSafe`:
       - Assign `color[node] = clr`.
       - If `solve(node + 1)` is `true`, return `true`.
       - **Backtrack**: Reset `color[node] = 0`.
   - Return `false` if no color works for this node.

## Dry Run (Step-by-Step Execution)
Input: 3 nodes (Triangle), M=2
1. `solve(node 0)`:
   - Try color 1. `isSafe` (no neighbors colored). `color[0]=1`.
   - Call `solve(1)`.
2. `solve(node 1)`:
   - Try color 1. `isSafe` fails (neighbor node 0 already has color 1).
   - Try color 2. `isSafe` passes. `color[1]=2`.
   - Call `solve(2)`.
3. `solve(node 2)`:
   - Try color 1. `isSafe` fails (neighbor node 0 has color 1).
   - Try color 2. `isSafe` fails (neighbor node 1 has color 2).
   - All colors (1 to 2) exhausted. Return `false`.
4. Back at `solve(node 1)`:
   - Color 2 failed for downstream. All colors for node 1 exhausted. Return `false`.
5. Back at `solve(node 0)`:
   - Try color 2. `color[0]=2`.
   - Call `solve(1)`.
   - ... (Mirror process, will eventually return `false` for the whole graph).

## C++ Solution
### Code with Detailed Comments
```cpp
#include <iostream>
#include <vector>
using namespace std;

class Solution {
public:
    /**
     * Helper to verify if color 'col' can be assigned to 'node'.
     * Checks all potential neighbors in the adjacency matrix.
     */
    bool isSafe(int node, int color[], bool graph[101][101], int n, int col) {
        for (int k = 0; k < n; k++) {
            // Check if there's an edge AND the neighbor shares the same color
            // graph[k][node] presence indicates adjacency
            if (k != node && graph[k][node] == 1 && color[k] == col) {
                return false;
            }
        }
        return true;
    }

    /**
     * Recursive core of the backtracking algorithm.
     * Uses a boolean return to implement early-exit/pruning.
     */
    bool solve(int node, int color[], int m, int n, bool graph[101][101]) {
        // Base Case: All vertices have been assigned a valid color
        if (node == n) return true;

        // Try assigning each color from 1 to M to the current node
        for (int i = 1; i <= m; i++) {
            if (isSafe(node, color, graph, n, i)) {
                // 1. PLACE: Assign color
                color[node] = i; 
                
                // 2. RECURSE: Move to the next vertex
                if (solve(node + 1, color, m, n, graph)) return true;
                
                // 3. BACKTRACK: Reset for the next trial at this same level
                color[node] = 0; 
            }
        }
        
        // No color between 1 and M could lead to a solution for this node
        return false;
    }

    /**
     * Main entry point for the graph coloring problem.
     * @param graph: Adjacency matrix (101x101 max size)
     * @param m: Maximum number of colors allowed
     * @param n: Number of vertices
     */
    bool graphColoring(bool graph[101][101], int m, int n) {
        // color[i] stores the color assigned to node i (0 indicates uncolored)
        int color[n];
        for (int i = 0; i < n; i++) color[i] = 0;
        
        return solve(0, color, m, n, graph);
    }
};

int main() {
    // Example test: Triangle graph
    bool graph[101][101] = {0};
    graph[0][1] = 1; graph[1][0] = 1;
    graph[1][2] = 1; graph[2][1] = 1;
    graph[2][0] = 1; graph[0][2] = 1;

    Solution sol;
    int m = 3, n = 3;
    if (sol.graphColoring(graph, m, n)) {
        cout << "Possible to color graph with " << m << " colors." << endl;
    } else {
        cout << "Not possible." << endl;
    }

    return 0;
}
```

### Complexity Analysis
- **Time Complexity**: **O(M^N)**
    - In the absolute worst case (a disconnected graph or a highly complex one), we might try all `M` colors for each of the `N` vertices. 
- **Space Complexity**: **O(N)**
    - The `color` array takes O(N) space.
    - The recursion stack can reach a depth of N.

## Edge Cases
- **M = 1**: Only possible if the graph has no edges.
- **Complete Graph (Kn)**: Requires exactly `N` colors.
- **Disconnected Graph**: Backtracking will visit all components if done correctly; however, standard implementation usually solves one connected component naturally through adjacency.
- **No Edges**: Always possible to color with 1 color.

## Key Insights & Interview Tips
- **Chromatic Number**: The minimum number of colors needed to color a graph is the *Chromatic Number* $\chi(G)$. 
- **Planar Graphs**: The "Four Color Theorem" states that any planar graph can be colored with at most 4 colors.
- **Adjacency Representation**: Note that the problem uses an **Adjacency Matrix**. If it used an **Adjacency List**, the `isSafe` check would be `for(auto neighbor : adj[node])`.
- **Interview Tip**: Mention that this problem is **NP-Complete**, which is why we must rely on backtracking (exponential time) rather than a simple polynomial-time greedy algorithm (which doesn't always find the minimum coloring).
