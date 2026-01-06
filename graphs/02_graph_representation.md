# 02. Graph Representation
**LeetCode**: [N/A - Data Structure Foundation]  
**Difficulty**: Medium

## Problem Statement
How do we store a graph in computer memory? Since a graph consists of vertices and edges, we need an efficient way to represent these connections. There are two primary methods: **Adjacency Matrix** and **Adjacency List**.

## Examples
### Input Format
Commonly, graphs are given as:
- Number of nodes ($N$)
- Number of edges ($M$)
- $M$ lines following, each containing two integers ($u, v$) representing an edge between node $u$ and $v$.

Example Input:
```
3 2
1 2
2 3
```

## Visual Explanation
### ASCII Diagram: Matrix vs List
Graph: `(1-2), (2-3)`
```
Adjacency Matrix (N x N)      Adjacency List (Array of Lists)
      1 2 3                     1 -> [2]
    1 0 1 0                     2 -> [1, 3]
    2 1 0 1                     3 -> [2]
    3 0 1 0
```

## Approach & Intuition
### 1. Adjacency Matrix
We use a 2D array `adj[N+1][N+1]`.
- If there's an edge between $u$ and $v$, `adj[u][v] = 1`.
- For undirected graphs, we also set `adj[v][u] = 1`.
- **Pros**: $O(1)$ to check if an edge exists.
- **Cons**: $O(N^2)$ space regardless of the number of edges. Not feasible for $N > 10^4$.

### 2. Adjacency List
We use an array of vectors: `vector<int> adj[N+1]`.
- For each edge $(u, v)$, we do `adj[u].push_back(v)`.
- For undirected, also `adj[v].push_back(u)`.
- **Pros**: Space efficient ($O(V + 2E)$ for undirected, $O(V + E)$ for directed).
- **Cons**: Check for edge existence takes $O(\text{Degree of vertex})$.

## Algorithm Steps
### Building Adjacency List
1. Initialize an array of empty vectors of size $N+1$.
2. For each edge $(u, v)$:
   - Add $v$ to the list of $u$.
   - **If undirected**: Add $u$ to the list of $v$.
3. If weighted, store pairs `(neighbor, weight)` in the vector.

## Dry Run (Step-by-Step Execution)
Input: `3 2`, edges `(1, 2), (2, 3)` (Undirected)
1. Initialize `adj[1]=[], adj[2]=[], adj[3]=[]`.
2. Edge `(1, 2)`:
   - `adj[1].push_back(2)` -> `adj[1] = [2]`
   - `adj[2].push_back(1)` -> `adj[2] = [1]`
3. Edge `(2, 3)`:
   - `adj[2].push_back(3)` -> `adj[2] = [1, 3]`
   - `adj[3].push_back(2)` -> `adj[3] = [2]`

## C++ Solution
### Code with Detailed Comments
```cpp
#include <iostream>
#include <vector>
using namespace std;

/**
 * Demonstrates both Matrix and List representations.
 */
int main() {
    int n, m; // n: nodes, m: edges
    cin >> n >> m;

    // 1. ADJACENCY MATRIX
    // Space: O(N*N)
    int matrix[n + 1][n + 1] = {0}; 
    for (int i = 0; i < m; i++) {
        int u, v;
        cin >> u >> v;
        matrix[u][v] = 1;
        matrix[v][u] = 1; // For undirected
    }

    // 2. ADJACENCY LIST (Optimal for most cases)
    // Space: O(V + 2E)
    vector<int> adj[n + 1];
    for (int i = 0; i < m; i++) {
        int u, v;
        cin >> u >> v;
        adj[u].push_back(v);
        adj[v].push_back(u); // For undirected
    }

    // 3. WEIGHTED ADJACENCY LIST
    // vector<pair<int, int>> adjWeight[n + 1];
    // Example: adjWeight[u].push_back({v, weight});

    return 0;
}
```

### Complexity Analysis
| Feature | Adjacency Matrix | Adjacency List |
| :--- | :--- | :--- |
| **Space Complexity** | $O(N^2)$ | $O(V + 2E)$ |
| **Edge Check** | $O(1)$ | $O(\text{degree}(v))$ |
| **Add Edge** | $O(1)$ | $O(1)$ |
| **Iterate Neighbors**| $O(N)$ | $O(\text{degree}(v))$ |

## Edge Cases
- **Self-loops**: `matrix[u][u]` or `adj[u].push_back(u)`.
- **Zero-indexed vs One-indexed**: Be careful with array sizing (`N` vs `N+1`).
- **Multiple Components**: Representations work fine for disconnected parts.

## Key Insights & Interview Tips
- **The $10^5$ Rule**: In competitive programming, if $N$ is up to $10^5$, you **must** use an Adjacency List. $O(N^2)$ will exceed memory limits (approx 10GB for $10^5$ ints).
- **Check for Exists**: If your algorithm frequently asks "Is node A connected to node B?", and the graph is dense, Matrix might be better.
- **Interview Tip**: Always write your code using Adjacency List by default, as it handles sparse graphs much better and is the standard for BFS/DFS implementations.
