# 06. Number of Provinces
**LeetCode**: [547 - Number of Provinces](https://leetcode.com/problems/number-of-provinces/)  
**Difficulty**: Medium (Graph Traversal)

## Problem Statement
There are `n` cities. Some of them are connected, while some are not. If city `a` is connected directly with city `b`, and city `b` is connected directly with city `c`, then city `a` is connected indirectly with city `c`.

A **province** is a group of directly or indirectly connected cities and no other cities outside of the group.

You are given an `n x n` matrix `isConnected` where `isConnected[i][j] = 1` if the `ith` city and the `jth` city are directly connected, and `isConnected[i][j] = 0` otherwise. Return the total number of provinces.

## Examples
### Example 1
Input: `isConnected = [[1,1,0],[1,1,0],[0,0,1]]`  
Output: `2`  
Explanation: Cities 0 and 1 form one province, city 2 forms another.

## Constraints
- `1 <= n <= 200`
- `isConnected[i][j]` is `1` or `0`.
- `isConnected[i][i] == 1`
- `isConnected[i][j] == isConnected[j][i]`

## Visual Explanation
### ASCII Diagram: Adjacency Matrix to Components
```
Matrix:          Conceptual Graph:
  0 1 2
0 1 1 0         (0)---(1)    (2)
1 1 1 0
2 0 0 1         Component 1  Component 2

Total Provinces = 2
```

## Approach & Intuition
This problem is another variation of **Finding Connected Components**. 
- The input is an **Adjacency Matrix**, but the graph structure is undirected.
- We need to count how many disconnected sets of nodes exist.
- We use a **Visited Array** and the **Multi-Component Wrapper**.
- For each unvisited node, we start a traversal (BFS or DFS) to mark all reachable nodes as part of the same province. Every time we start a new traversal, we increment our province count.

## Algorithm Steps
1. Get the number of cities `V`.
2. Initialize `visited[V]` with 0.
3. Initialize `provinces = 0`.
4. Loop `i` from `0` to `V-1`:
   - If `!visited[i]`:
     - `provinces++`.
     - Call `dfs(i)` to mark all connected cities.
5. In `dfs(node)`:
   - Mark `node` as visited.
   - For `neighbor` from `0` to `V-1`:
     - If `isConnected[node][neighbor] == 1` AND `!visited[neighbor]`:
       - Recursively call `dfs(neighbor)`.

## Dry Run (Step-by-Step Execution)
Input: `[[1,1,0],[1,1,0],[0,0,1]]`
1. `vis = [0, 0, 0]`, `provinces = 0`.
2. **i = 0**: `vis[0]=0`.
   - `provinces = 1`.
   - `dfs(0)`:
     - `vis[0]=1`.
     - Check neighbors of 0:
       - `j=0`: `isConnected[0][0]=1`, but `vis[0]=1`. Skip.
       - `j=1`: `isConnected[0][1]=1`, `vis[1]=0`. Call `dfs(1)`.
         - **dfs(1)**: `vis[1]=1`. Check neighbors of 1... `j=0` is visited, others 0.
       - `j=2`: `isConnected[0][2]=0`. Skip.
3. **i = 1**: `vis[1]=1`. Skip.
4. **i = 2**: `vis[2]=0`.
   - `provinces = 2`.
   - `dfs(2)`:
     - `vis[2]=1`.
     - Neighbors of 2: only `j=2` is 1, but it's visited.
5. Result: 2.

## C++ Solution
### Code with Detailed Comments
```cpp
#include <vector>
using namespace std;

class Solution {
private:
    /**
     * Standard DFS to traverse the adjacency matrix as a graph.
     */
    void dfs(int node, vector<vector<int>>& isConnected, vector<int>& visited) {
        visited[node] = 1;
        
        // In an adjacency matrix, we check every city 'i' to see if it's connected
        for (int i = 0; i < isConnected.size(); i++) {
            // Check if city 'i' is connected to 'node' and hasn't been visited
            if (isConnected[node][i] == 1 && !visited[i]) {
                dfs(i, isConnected, visited);
            }
        }
    }

public:
    int findCircleNum(vector<vector<int>>& isConnected) {
        int n = isConnected.size();
        vector<int> visited(n, 0); // visited array
        int count = 0;             // province count
        
        // Multi-component wrapper
        for (int i = 0; i < n; i++) {
            if (!visited[i]) {
                count++;           // Found a new province
                dfs(i, isConnected, visited);
            }
        }
        
        return count;
    }
};
```

### Complexity Analysis
- **Time Complexity**: **O(N^2)**
    - The outer loop runs $N$ times.
    - Inside DFS, we iterate through the entire row of the matrix ($N$ times).
    - Total operations: $N \times N$.
- **Space Complexity**: **O(N)**
    - For the `visited` array and the recursion stack depth.

## Edge Cases
- **n=1**: 1 province.
- **Disconnected cities**: All zeros except diagonal. `n` provinces.
- **Fully connected cities**: All ones. 1 province.

## Key Insights & Interview Tips
- **Matrix vs List**: You can solve this directly using the matrix (shown above) or convert it to an adjacency list first. Converting to a list takes $O(N^2)$ but makes DFS $O(V+E)$, which is faster if the graph is sparse.
- **DSU alternative**: This problem is a textbook use case for **Disjoint Set Union (DSU)**. Every `1` in the matrix is a `union` operation.
- **Interview Tip**: If asked to optimize space, mention that if you are allowed to modify the input, you could use the diagonal `isConnected[i][i]` to store visited status (setting it to `2`), but a separate `visited` array is cleaner.
