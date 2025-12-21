# 03. Parameterized and Functional Recursion
**Source**: [Striver's Recursion Series - L3](https://youtu.be/69boWkhP2LU)  
**Difficulty**: Easy (Foundational)

## Overview
There are two main ways to solve problems using recursion:
1. **Parameterized Recursion**: The result is passed as a parameter in the recursive calls.
2. **Functional Recursion**: The function returns the result, and calculations are performed on the return values.

We will use the problem of **Sum of first N numbers** to compare both, and **Factorial of N** to demonstrate the functional style.

---

## 1. Sum of first N Numbers (Parameterized)
### Intuition
Maintain a running sum (`sum`) and pass it down the recursion tree. When the base case is hit, print or return the final sum.

```cpp
void sumParameterized(int i, int sum) {
   if(i < 1) {
       cout << sum << endl;
       return;
   }
   sumParameterized(i - 1, sum + i);
}
```
**Explanation**: In `f(3, 0)`, we call `f(2, 3)`, then `f(1, 5)`, then `f(0, 6)`. At `i=0`, we print 6.

---

## 2. Sum of first N Numbers (Functional)
### Intuition
Calculate the sum by saying: `sum(N) = N + sum(N-1)`. The function "waits" for the result of smaller sub-problems to calculate its own result.

### Visual Explanation (Recursion Tree)
```
sum(3)
 ↳ 3 + sum(2)
        ↳ 2 + sum(1)
               ↳ 1 + sum(0)
                      ↳ Base Case: return 0
               ↳ 1 + 0 = 1 (Return 1)
        ↳ 2 + 1 = 3 (Return 3)
 ↳ 3 + 3 = 6 (Return 6)
```

### Code
```cpp
int sumFunctional(int n) {
   if(n == 0) return 0;
   return n + sumFunctional(n - 1);
}
```

---

## 3. Factorial of N (Functional)
### Intuition
`N! = N * (N-1)!`. Similar to the sum problem, we use the return value of the sub-problem.

### Code
```cpp
int factorial(int n) {
   if(n == 0 || n == 1) return 1;
   return n * factorial(n - 1);
}
```

---

## C++ Implementation (Complete)
### Code with Detailed Comments

```cpp
#include <iostream>
using namespace std;

class RecursionStyles {
public:
    // 1. Parameterized Way
    // We pass the running sum as a parameter
    void solveSumParameterized(int i, int sum) {
        if (i < 1) {
            cout << "Sum (Parameterized): " << sum << endl;
            return;
        }
        // Work is done while moving down the recursion
        solveSumParameterized(i - 1, sum + i);
    }

    // 2. Functional Way
    // We return the result of the subproblem
    int solveSumFunctional(int n) {
        if (n == 0) return 0;
        // Calculation happens while returning (stack unwinding)
        return n + solveSumFunctional(n - 1);
    }

    // 3. Factorial (Functional)
    int solveFactorial(int n) {
        if (n == 0 || n == 1) return 1;
        return n * solveFactorial(n - 1);
    }
};

int main() {
    RecursionStyles rs;
    int n = 4;

    rs.solveSumParameterized(n, 0);
    cout << "Sum (Functional): " << rs.solveSumFunctional(n) << endl;
    cout << "Factorial of " << n << ": " << rs.solveFactorial(n) << endl;

    return 0;
}
```

## Complexity Analysis
- **Time Complexity**: O(N) for both styles, as N recursive calls are made.
- **Space Complexity**: O(N) due to the auxiliary space used by the recursion stack.

## Key Insights & Interview Tips
- **Parameterized**: Useful when you just need the result at the end (like printing).
- **Functional**: More commonly used in interviews because it fits the mathematical definition of a problem (recursive relation) and is required when you need to return the result to a caller.
- **Base Case Choice**: For sum, `sum(0) = 0`. For factorial, `fact(0) = 1`. Choosing the correct identity value for the base case is critical.
- **Interview Tip**: If asked about the difference, explain that parameterized recursion focuses on "carrying" the result, while functional recursion focuses on "building" the result on return.
