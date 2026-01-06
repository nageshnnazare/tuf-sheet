# 03. Connected Components
**LeetCode**: [323 - Number of Connected Components in an Undirected Graph](https://leetcode.com/problems/number-of-connected-components-in-an-undirected-graph/) (Premium)  
**Difficulty**: Medium

## Problem Statement
In an undirected graph, a **connected component** is a maximal subgraph in which any two vertices are connected to each other by paths. A single graph may consist of several disconnected components. The challenge is to ensure our algorithms (like BFS/DFS) visit every single node in the graph, regardless of whether it's reachable from the starting node.

## Examples
### Example 1: Disconnected Graph
Nodes: `{1, 2, 3, 4, 5}`  
Edges: `(1,2), (4,5)`  
- Component 1: `{1, 2}`
- Component 2: `{3}` (Single node is its own component)
- Component 3: `{4, 5}`
Total Components: 3.

## Constraints
- **Number of Nodes (V)**: Up to $10^5$.
- **Number of Edges (E)**: Up to $10^5$.

## Visual Explanation
### ASCII Diagram: Multi-component Traversal
```
[1]---[2]       [3]       [4]---[5]
    A            B            C
```
If we start BFS/DFS at node 1, we only visit Component A. To visit all nodes, we need a loop that checks every node.

## Approach & Intuition
Standard algorithms usually start from a single node. To handle multiple components:
1. Maintain a `visited` array initialized to 0.
2. Iterate through all nodes from `1` to `V`.
3. If node `i` is **not visited**:
   - Increment the `count` of components.
   - Start a traversal (BFS/DFS) from node `i` to mark all reachable nodes in that component as visited.

### The "Wrapper" Pattern
This is the standard boilerplate for every graph problem to handle disconnected entries.
```cpp
for (int i = 1; i <= n; i++) {
    if (!vis[i]) {
        count++;
        bfs/dfs(i, adj, vis);
    }
}
```

## Algorithm Steps
1. Create an adjacency list from the input.
2. Initialize `visited[V+1]` with 0.
3. Initialize `count = 0`.
4. Loop `i` from 1 to `V`:
   - If `visited[i] == 0`:
     - `count++`.
     - Call DFS (or BFS) starting at `i`.
5. Return `count`.

## Dry Run (Step-by-Step Execution)
Graph: `(1-2), (4-5)`, Nodes: 5
1. `vis = [0, 0, 0, 0, 0]`, `count = 0`.
2. `i = 1`: `vis[1]` is 0.
   - `count = 1`.
   - `dfs(1)`: visits 1 and 2.
   - `vis = [1, 1, 0, 0, 0]`.
3. `i = 2`: `vis[2]` is 1. Skip.
4. `i = 3`: `vis[3]` is 0.
   - `count = 2`.
   - `dfs(3)`: visits 3.
   - `vis = [1, 1, 1, 0, 0]`.
5. `i = 4`: `vis[4]` is 0.
   - `count = 3`.
   - `dfs(4)`: visits 4 and 5.
   - `vis = [1, 1, 1, 1, 1]`.
6. `i = 5`: `vis[5]` is 1. Skip.
7. Result: 3 components.

## C++ Solution
### Code with Detailed Comments
```cpp
#include <iostream>
#include <vector>
using namespace std;

class Solution {
public:
    /**
     * Standard DFS to mark all nodes in a single component.
     */
    void dfs(int node, vector<int> adj[], int vis[]) {
        vis[node] = 1; // Mark as visited
        
        // Visit all unvisited neighbors
        for (auto it : adj[node]) {
            if (!vis[it]) {
                dfs(it, adj, vis);
            }
        }
    }

    /**
     * Counts connected components using the multi-component wrapper.
     */
    int countComponents(int n, vector<int> adj[]) {
        int vis[n + 1] = {0}; // Initialize visited array
        int count = 0;

        for (int i = 1; i <= n; i++) {
            // If the node hasn't been visited, it belongs to a new component
            if (!vis[i]) {
                count++;
                dfs(i, adj, vis);
            }
        }
        return count;
    }
};

int main() {
    int n = 5;
    vector<int> adj[6];
    adj[1].push_back(2); adj[2].push_back(1);
    adj[4].push_back(5); adj[5].push_back(4);

    Solution sol;
    cout << "Total Components: " << sol.countComponents(n, adj) << endl;
    return 0;
}
```

### Complexity Analysis
- **Time Complexity**: **O(V + 2E)**
    - The outer loop runs $V$ times.
    - The DFS explores every edge twice (once from each end) in an undirected graph.
- **Space Complexity**: **O(V)**
    - For the `visited` array and the recursion stack depth.

## Edge Cases
- **Completely Disconnected**: Graph with no edges. Count = N.
- **Complete Graph**: Every node is connected. Count = 1.
- **Lone Nodes**: Nodes with no edges. Each counts as its own component.

## Key Insights & Interview Tips
- **The Wrapper**: In interviews, if you forget the `for(i=1..n)` wrapper, your code will only solve for "reachable" nodes. This is a very common mistake.
- **Directed Graphs**: The concept is called **Strongly Connected Components (SCC)**, which is significantly more complex (requires Tarjan's or Kosaraju's algorithm).
- **Interview Tip**: Mention that `Union-Find` (Disjoint Set Union) is a powerful alternative for counting connected components, especially in dynamic graphs where edges are added over time.
