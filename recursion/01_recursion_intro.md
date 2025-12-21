# 01. Introduction to Recursion
**Source**: [Striver's Recursion Series - L1](https://youtu.be/yVdKa8dnKiE)  
**Difficulty**: Easy (Foundational)

## Concept Overview
Recursion is a programming technique where a function calls itself, directly or indirectly, until it reaches a specific condition called the **Base Case**. It is used to solve problems that can be broken down into smaller, similar sub-problems.

## The Core Components
1. **Recursion Condition**: The logic that performs a task and calls the function again with updated parameters.
2. **Base Case**: The condition where the recursion stops. Without a base case, the function will call itself infinitely.

## Visual Explanation
### ASCII Diagram: The Call Flow
```
main()
  ↓
func() --Calls--> func() --Calls--> func() --Base Case Hit!--
  ↑                 ↑                 ↑
[Returns] ←---- [Returns] ←---- [Returns]
```

### Recursion Tree
For a simple function `f(n)` that prints `n` and calls `f(n-1)`:
```
f(3)
 ↳ Print 3
 ↳ f(2)
    ↳ Print 2
    ↳ f(1)
       ↳ Print 1
       ↳ f(0) [Base Case Hit!]
```

## Stack Memory & Overflow
### Call Stack
Whenever a function is called, it is pushed onto the **Stack**. It remains there until it completes its execution and returns. In recursion, if there is no base case, the stack keeps growing until it runs out of memory.

### Stack Overflow
A **Stack Overflow** occurs when the recursion exceeds the maximum memory limit allocated for the call stack. This usually happens due to:
- Missing Base Case
- Base Case condition never being met
- Input too large for the stack limit

## Algorithm Steps (Simple Print Example)
1. Define a function `printNumbers(n)`.
2. **Base Case**: If `n == 0`, return.
3. Perform the task: `Print n`.
4. **Recursive Step**: Call `printNumbers(n - 1)`.

## Dry Run (Step-by-Step Execution)
**Function**: `f(n) { if(n==3) return; print(n); f(n+1); }`
**Input**: `f(0)`

| Step | Call | Console | Stack State | Remark |
|------|------|---------|-------------|--------|
| 1 | `f(0)` | `0` | `[f(0)]` | Prints 0, calls f(1) |
| 2 | `f(1)` | `1` | `[f(0), f(1)]` | Prints 1, calls f(2) |
| 3 | `f(2)` | `2` | `[f(0), f(1), f(2)]` | Prints 2, calls f(3) |
| 4 | `f(3)` | - | `[...]` | Base Case Hit! Returns. |
| 5 | Returns | - | `[]` | All calls pop off stack. |

## C++ Solution
### Code with Detailed Comments

```cpp
#include <iostream>
using namespace std;

// Function to demonstrate simple recursion
void printCount(int count) {
    // 1. BASE CASE
    // If we don't have this, the function will call itself infinitely
    // leading to a STACK OVERFLOW.
    if (count == 3) {
        return;
    }
    
    // 2. TASK
    // Print the current value of count
    cout << count << endl;
    
    // 3. RECURSIVE CALL
    // The function calls itself with an incremented value
    printCount(count + 1);
}

int main() {
    // Start recursion with an initial value
    printCount(0);
    return 0;
}
```

### Complexity Analysis
- **Time Complexity**: O(N) - Where N is the number of times the function calls itself.
- **Space Complexity**: O(N) - Due to the recursion stack storing information for each function call.

## Edge Cases
- **Wrong Base Condition**: `if(n < 0)` when the recursion only goes upwards.
- **Logical Error in Parameters**: Passing the same `count` value infinitely.

## Key Insights & Interview Tips
- **Always start with the Base Case**: It's the first thing you should define in a recursive function.
- **Visualize with a Tree**: For complex recursion (like Fibonacci), always draw a recursion tree to understand the overlapping subproblems.
- **Stack Memory is Finite**: If you expect millions of recursive calls, consider an iterative approach to avoid stack overflow.
- **What Interviewers Look For**: Clear identification of base cases and the ability to trace the call stack accurately.
