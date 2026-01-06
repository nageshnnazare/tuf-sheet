# 05. DFS Traversal
**LeetCode**: [N/A - Standard Algorithm]  
**Difficulty**: Medium

## Problem Statement
**Depth-First Search (DFS)** is a graph traversal algorithm that starts at a root node and explores as far as possible along each branch before backtracking. It is similar to Preorder Traversal in trees.

## Examples
### Example 1
Input: `adj = [[2,3,1], [1], [1,4], [1,2]]`, `start = 1`
Output: `1, 2, 3, 4` (One possible DFS order)
Explanation: From 1, we go deep to 2. From 2, we go deep to 3. From 3, we go deep to 4.

## Constraints
- **V, E**: Up to $10^5$.
- **Starting Node**: Typically node 0 or 1.

## Visual Explanation
### ASCII Diagram: To the Depth
DFS follows a path until it hits a "dead end" (a node with no unvisited neighbors).
```
      (1)         <-- Start
     /   \
   (2)   (3)      
    |     |
   (4)---(5)      

Traversing: 1 -> 2 -> 4 -> 5 -> 3
(At 5, 3 is an unvisited neighbor, so we jump there before backtracking)
```

## Approach & Intuition
DFS uses the **Call Stack** (functional recursion) to keep track of the path.
- **Why Recursion?**: Because recursion naturally handles the "Depth-First" requirement by pushing the current context onto the stack and jumping into a new one.
- **The "Visited" Sentinel**: Just like BFS, we mark nodes as visited. In DFS, we mark a node as soon as we "enter" the function for that node.
- **Process**:
  1. Pick a starting node `u`. Mark it visited.
  2. For every unvisited neighbor `v` of `u`:
     - Recursively call DFS for `v`.
  3. Once all neighbors are visited, the function for `u` returns (backtracks).

## Algorithm Steps
1. Create a `visited` array (size $V+1$).
2. Initialize an empty `dfs` result vector.
3. Define `dfsRecursive(node, adj, vis, result)`:
   - Mark `node` as visited.
   - Push `node` into `result`.
   - For each `neighbor` in `adj[node]`:
     - If `!vis[neighbor]`:
       - `dfsRecursive(neighbor, adj, vis, result)`.
4. Start the traversal with your source node.

## Dry Run (Step-by-Step Execution)
Graph: `1: [2, 3], 2: [1, 4], 3: [1, 5], 4: [2, 5], 5: [3, 4]`
Start at: 1
1. `dfs(1)`: `vis={1}`, `res=[1]`. Calls `dfs(2)`.
2. `dfs(2)`: `vis={1, 2}`, `res=[1, 2]`. Calls `dfs(4)`.
3. `dfs(4)`: `vis={1, 2, 4}`, `res=[1, 2, 4]`. Calls `dfs(5)`.
4. `dfs(5)`: `vis={1, 2, 4, 5}`, `res=[1, 2, 4, 5]`. 
   - Neighbor 3 is unvisited. Calls `dfs(3)`.
5. `dfs(3)`: `vis={1, 2, 4, 5, 3}`, `res=[1, 2, 4, 5, 3]`.
   - Neighbors of 3 are {1, 5}. Both visited.
6. `dfs(3)` returns to `dfs(5)`.
7. `dfs(5)` returns to `dfs(4)`.
8. ... (Recursion collapses).
Final DFS: `1, 2, 4, 5, 3`.

## C++ Solution
### Code with Detailed Comments
```cpp
#include <iostream>
#include <vector>
using namespace std;

class Solution {
private:
    /**
     * The recursive worker function.
     */
    void dfsWorker(int node, vector<int> adj[], int vis[], vector<int> &ls) {
        // Mark node as visited upon entry
        vis[node] = 1;
        ls.push_back(node);
        
        // Explore all adjacent neighbors
        for (auto it : adj[node]) {
            // If the neighbor is not yet visited, recurse into it
            if (!vis[it]) {
                dfsWorker(it, adj, vis, ls);
            }
        }
    }

public:
    /**
     * dfsTraversal: Standard DFS.
     * @param V: Number of vertices
     * @param adj: Adjacency list representation
     * @return: Ordered list of DFS traversal
     */
    vector<int> dfsOfGraph(int V, vector<int> adj[]) {
        int vis[V + 1] = {0}; // Keep track of visited nodes
        vector<int> ls;       // Result list
        
        // Start traversal from node 0 or 1
        int startNode = 0; 
        dfsWorker(startNode, adj, vis, ls);
        
        return ls;
    }
};
```

### Complexity Analysis
- **Time Complexity**: **O(V + 2E)**
    - Each vertex is visited exactly once.
    - Every edge is checked twice (once for each node it connects).
- **Space Complexity**: **O(V)**
    - For the `visited` array, the result vector, and the recursion stack (worst case depth $V$ for a skewed graph/linked list shape).

## Edge Cases
- **Self-loops**: Handled by the `!vis` check.
- **Disconnected Components**: Standard DFS only visits one component. Use the `for(i=1..V)` wrapper if needed.
- **Cycle**: Handled by the `vis` markers.

## Key Insights & Interview Tips
- **DFS vs BFS**:
  - BFS is better for finding the **shortest path**.
  - DFS is better for finding **paths to a target**, detecting **cycles**, and topological sort.
- **Implicit Stack**: DFS uses the system stack. For extremely large $V$ (e.g., $10^6$), you might hit a **Stack Overflow**. In that case, use an explicit `std::stack` for an iterative version.
- **Interview Tip**: If an interviewer asks "When would DFS fail?", mention that it might go very deep in one direction and miss a close target in another branch (where BFS would have found it quickly).
