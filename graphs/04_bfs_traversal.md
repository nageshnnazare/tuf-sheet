# 04. BFS Traversal
**LeetCode**: [N/A - Standard Algorithm]  
**Difficulty**: Medium

## Problem Statement
**Breadth-First Search (BFS)** is a graph traversal algorithm that explores all the neighbor nodes at the present depth before moving on to the nodes at the next depth level. It is similar to Level Order Traversal in trees.

## Examples
### Example 1
Input: `adj = [[2,3,1], [1], [1,4], [1,2]]`, `start = 1`
Output: `1, 2, 3, 4` (One possible BFS order)
Explanation: 1 is the source. Neighbors of 1 are 2, 3. Neighbor of 3 is 4.

## Constraints
- **V, E**: Up to $10^5$.
- **Starting Node**: Typically node 0 or 1.

## Visual Explanation
### ASCII Diagram: Level Order Ripples
Think of throwing a stone in a pond; the ripples expand level by level.
```
      (1)         <-- Level 0
     /   \
   (2)---(6)      <-- Level 1
    |     |
   (3)---(5)      <-- Level 2
     \   /
      (4)         <-- Level 3

BFS Order: 1, 2, 6, 3, 5, 4
```

## Approach & Intuition
BFS uses a **Queue** data structure (FIFO) to track nodes to be visited next.
- **Why Queue?**: To ensure that nodes discovered first are visited first, maintaining the "level-by-level" property.
- **The "Visited" Sentinel**: We must mark nodes as visited as soon as they are pushed into the queue to avoid redundant visits and infinite loops (in cyclic graphs).
- **Process**:
  1. Pick a starting node and push it to the queue. Mark it visited.
  2. While the queue is not empty:
     - Pop the front node `u`.
     - Add `u` to the result list.
     - For every unvisited neighbor `v` of `u`:
       - Mark `v` visited.
       - Push `v` to the queue.

## Algorithm Steps
1. Create a `visited` array (size $V+1$) and a `bfs` result vector.
2. Initialize a `queue` and push the starting node. Mark it visited.
3. While `!q.empty()`:
   - `node = q.front()`, `q.pop()`.
   - `bfs.push_back(node)`.
   - For each `neighbor` in `adj[node]`:
     - If `!visited[neighbor]`:
       - `visited[neighbor] = 1`.
       - `q.push(neighbor)`.
4. Return `bfs` vector.

## Dry Run (Step-by-Step Execution)
Graph: `1: [2, 6], 2: [1, 3, 4], 6: [1, 5], 3: [2, 4], 4: [2, 3, 5], 5: [6, 4]`
Start at: 1
1. **Initial**: `q = [1]`, `vis = {1}`, `bfs = []`.
2. **Pop 1**: `q = []`, `bfs = [1]`.
   - Neighbors of 1 are {2, 6}.
   - Push 2, 6. `vis = {1, 2, 6}`, `q = [2, 6]`.
3. **Pop 2**: `q = [6]`, `bfs = [1, 2]`.
   - Neighbors of 2 are {1, 3, 4}. 1 is visited.
   - Push 3, 4. `vis = {1, 2, 6, 3, 4}`, `q = [6, 3, 4]`.
4. **Pop 6**: `q = [3, 4]`, `bfs = [1, 2, 6]`.
   - Neighbors of 6 are {1, 5}. 1 is visited.
   - Push 5. `vis = {...5}`, `q = [3, 4, 5]`.
5. **Pop 3**: `bfs = [1, 2, 6, 3]`. All neighbors (2, 4) are visited.
6. ... (Continue until queue is empty).
Final BFS: `1, 2, 6, 3, 4, 5`.

## C++ Solution
### Code with Detailed Comments
```cpp
#include <iostream>
#include <vector>
#include <queue>
using namespace std;

class Solution {
public:
    /**
     * bfsTraversal: Standard BFS using a queue.
     * @param V: Number of vertices
     * @param adj: Adjacency list representation
     * @return: Ordered list of BFS traversal
     */
    vector<int> bfsTraversal(int V, vector<int> adj[]) {
        int vis[V + 1] = {0}; // Visited array
        vis[1] = 1;           // Assuming start node is 1
        
        queue<int> q;
        q.push(1);
        
        vector<int> bfs;
        
        while (!q.empty()) {
            int node = q.front();
            q.pop();
            bfs.push_back(node);
            
            // Traverse all connected neighbors
            for (auto it : adj[node]) {
                // If neighbor is not visited, mark it and push to queue
                if (!vis[it]) {
                    vis[it] = 1;
                    q.push(it);
                }
            }
        }
        return bfs;
    }
};
```

### Complexity Analysis
- **Time Complexity**: **O(V + 2E)**
    - Each node is pushed and popped from the queue exactly once ($V$).
    - For every node, we iterate over its neighbors. Total neighbor iterations = sum of degrees = $2E$.
- **Space Complexity**: **O(V)**
    - For the `visited` array, the `queue` (max size $V$), and the result vector.

## Edge Cases
- **Disconnected Graph**: Standard BFS only visits one component. 
- **Wait, what about multiple components?**: The `for(i=1..V)` wrapper from the previous section should be used if the graph is disconnected.
- **Graph with Cycles**: The `visited` check prevents infinite cycles.

## Key Insights & Interview Tips
- **Shortest Path**: BFS is guaranteed to find the **shortest path** in an unweighted graph (in terms of number of edges).
- **Marking Early**: A common bug is marking a node as visited *after* popping it. You must mark it *before* pushing it to prevent the same node from being added to the queue multiple times.
- **Interview Tip**: If asked about space complexity, mention that the queue space depends on the **width** of the graph (maximum number of nodes at any single level).
