# 05. Multiple Recursion Calls (Fibonacci)
**LeetCode**: [509 - Fibonacci Number](https://leetcode.com/problems/fibonacci-number/)  
**Difficulty**: Easy (Conceptually Medium)

## Overview
Multiple recursion occurs when a function calls itself more than once in a single execution path. The most classic example is calculating the **Nth Fibonacci Number**, where each state depends on the sum of two preceding states.

---

## 1. The Fibonacci Concept
The Fibonacci sequence is defined as:
- `f(0) = 0`
- `f(1) = 1`
- `f(n) = f(n-1) + f(n-2)` for `n > 1`

### Recursive Formula
The function "branches out" into two separate recursive paths. The first call `f(n-1)` completes its entire subtree before the second call `f(n-2)` begins.

---

## 2. Visual Explanation: Recursion Tree
Calculating `f(4)` creates a tree structure where each node makes two calls.

```
          f(4)
         /    \
      f(3)    f(2)
     /   \    /   \
  f(2)  f(1) f(1) f(0)
 /   \
f(1) f(0)
```
**Execution Order**:
The computer follows a **Depth-First Search (DFS)** pattern. It goes all the way left before coming back up and exploring the right branches.

---

## 3. Algorithm Steps
1. Define a function `fib(n)`.
2. **Base Case**: If `n <= 1`, return `n` (handles 0 and 1).
3. **Recursive Step**: Return the sum of `fib(n-1)` and `fib(n-2)`.

---

## C++ Implementation
### Code with Detailed Comments

```cpp
#include <iostream>
using namespace std;

class MultipleRecursion {
public:
    /**
     * Calculates the Nth Fibonacci number using recursion.
     * Note: This is an inefficient approach (exponential time) 
     * but perfect for understanding multiple recursion calls.
     */
    int fibonacci(int n) {
        // Base Case: f(0)=0 and f(1)=1
        if (n <= 1) {
            return n;
        }
        
        // Multiple Recursion Calls:
        // 1. The first call fib(n-1) goes deep into its subtree
        // 2. Once it returns, the second call fib(n-2) starts
        // 3. Their results are added and returned to the caller
        int last = fibonacci(n - 1);
        int slast = fibonacci(n - 2);
        
        return last + slast;
    }
};

int main() {
    MultipleRecursion mr;
    int n = 4;
    cout << "Fibonacci(" << n << ") = " << mr.fibonacci(n) << endl;
    return 0;
}
```

## Complexity Analysis
- **Time Complexity**: **O(2^N)** (Exponential)
    - Every level of the recursion tree approximately doubles the number of calls. 
    - Specifically, it's about O(1.618^N) - the Golden Ratio, but commonly referred to as exponential.
- **Space Complexity**: **O(N)**
    - The maximum depth of the call stack at any point is equal to N (the height of the tree).

## Key Insights & Interview Tips
- **Overlapping Subproblems**: In the tree for `f(4)`, notice that `f(2)` is calculated twice. This redundancy is why the recursive approach is slow for large N. (This leads to the concept of **Dynamic Programming**).
- **Stack Memory**: Even though there are many calls, they aren't all on the stack at the same time. The stack depth is only as deep as the tallest branch.
- **Base Case**: Without `n <= 1`, recursion for `f(0)` would call `f(-1)`, leading to an infinite loop and stack overflow.
- **Interview Tip**: Always mention the exponential time complexity and why it's inefficient. Interviewers use this to transition into discussions about Memoization or Iterative solutions.
