# 01. Introduction to Graphs
**LeetCode**: [N/A - General Concept]  
**Difficulty**: Easy

## Problem Statement
A **Graph** is a non-linear data structure consisting of a finite set of **vertices** (or nodes) and a set of **edges** which connect a pair of vertices. It is used to represent complex relationships between objects, such as social networks, city maps, or the internet.

## Examples
### Example 1: Simple Undirected Graph
Nodes: `{1, 2, 3}`  
Edges: `{(1,2), (2,3), (3,1)}`  
This forms a triangle where all nodes are connected.

### Example 2: Directed Graph (Digraph)
Nodes: `{A, B}`  
Edge: `A -> B`  
You can go from A to B, but not from B to A.

## Constraints
- **Self-loops**: An edge that connects a vertex to itself.
- **Multi-edges**: Multiple edges between the same two vertices.
*Note: In most algorithm problems, we assume simple graphs (no loops/multi-edges) unless stated otherwise.*

## Visual Explanation
### ASCII Diagram: Types of Graphs
```
Undirected Graph:      Directed Graph:      Weighted Graph:
   (1)---(2)             (1)--->(2)           (1)--5--(2)
    |   /                 ^     /              |     /
    |  /                  |    /               10   3
   (3)                   (3)<--                |   /
                                              (3)--
```

## Approach & Intuition
### Core Terminology
1. **Nodes/Vertices**: The actual data points.
2. **Edges**: The connections between nodes.
3. **Directed vs Undirected**:
   - **Undirected**: Edges are bidirectional. If there's an edge between $u$ and $v$, you can go in both directions.
   - **Directed**: Edges have a direction. $u \to v$ means you can only go from $u$ to $v$.
4. **Degree of a Vertex**:
   - **Undirected**: Number of edges connected to the vertex.
   - **Directed**: Split into **In-degree** (edges coming in) and **Out-degree** (edges going out).
5. **Path**: A sequence of vertices where each adjacent pair is connected by an edge.
6. **Cycle**: A path that starts and ends at the same vertex.

### Properties
- **Handshaking Lemma**: In an undirected graph, the sum of all degrees is exactly twice the number of edges: $\sum \text{deg}(v) = 2 \times E$.
- **Connected Graph**: A graph where there is a path between every pair of vertices.

## Algorithm Steps
(This is a conceptual file, no code implementation required)
1. Identify the number of nodes (V) and edges (E).
2. Determine if the graph is directed or undirected.
3. Choose the appropriate representation (Matrix vs List).

## Dry Run (Step-by-Step Execution)
Conceptual trace for calculating total degree:
Graph: `(1-2), (2-3), (3-1)` (Undirected triangle)
- Node 1: Connected to 2, 3. Degree = 2.
- Node 2: Connected to 1, 3. Degree = 2.
- Node 3: Connected to 1, 2. Degree = 2.
- Total Degree = $2+2+2 = 6$.
- Number of Edges (E) = 3.
- Verification: $6 = 2 \times 3$ (Matches Handshaking Lemma).

## C++ Solution
### Code with Detailed Comments
```cpp
// General structure for problems involving graph size
#include <iostream>
using namespace std;

int main() {
    int n, m; // n = nodes, m = edges
    cin >> n >> m;
    
    // Total degree sum logic (Undirected)
    int total_degree = 2 * m;
    
    cout << "Number of nodes: " << n << endl;
    cout << "Number of edges: " << m << endl;
    cout << "Sum of degrees in an undirected graph: " << total_degree << endl;
    
    return 0;
}
```

### Complexity Analysis
- **Time Complexity**: **O(1)** for property calculation.
- **Space Complexity**: **O(1)**.

## Edge Cases
- **Null Graph**: Zero nodes.
- **Trivial Graph**: One node, zero edges.
- **Complete Graph**: Every node is connected to every other node. Number of edges = $V(V-1)/2$.

## Key Insights & Interview Tips
- **Sparse vs Dense**: 
  - **Sparse**: Edges are much less than $V^2$.
  - **Dense**: Edges are close to $V^2$.
- **The Lemma**: Interviewers often ask "Can a graph exist where the sum of degrees is odd?" Answer: No, because sum of degrees must be $2E$ (even).
- **Interview Tip**: Always start by clarifying if the graph is directed and if it contains weights.
