# 02. Basic Recursion Problems
**Source**: [Striver's Recursion Series - L2](https://youtu.be/un6PyYnS6dM)  
**Difficulty**: Easy (Foundational)

## Overview
This guide covers foundational recursion problems that demonstrate parameter-controlled execution and the concept of **Backtracking** (performing actions after the recursive call returns).

---

## 1. Print "Striver" N times
### Logic
Print the name and call the function for `i + 1` until `i > N`.

```cpp
void printName(int i, int n) {
   if(i > n) return;
   cout << "Striver" << endl;
   printName(i + 1, n);
}
```
**Complexity**: Time: O(N), Space: O(N)

---

## 2. Print 1 to N
### Logic
Print `i` and call the function for `i + 1`.

```cpp
void print1ToN(int i, int n) {
   if(i > n) return;
   cout << i << endl;
   print1ToN(i + 1, n);
}
```
**Complexity**: Time: O(N), Space: O(N)

---

## 3. Print N to 1
### Logic
Print `i` and call the function for `i - 1`.

```cpp
void printNTo1(int i, int n) {
   if(i < 1) return;
   cout << i << endl;
   printNTo1(i - 1, n);
}
```
**Complexity**: Time: O(N), Space: O(N)

---

## 4. Print 1 to N (Using Backtracking)
### Intuition
To print 1 to N without using an incrementing parameter (using `f(i, n)` where we call `f(i-1, n)`), we perform the print **after** the recursive call. This ensures that the base case is reached first, and printing starts as the stack unwinds.

### Visual Explanation
```
f(3, 3) 
  ↳ calls f(2, 3)
      ↳ calls f(1, 3)
          ↳ calls f(0, 3) [Base Case: Return]
      ↳ [Backtrack] prints 1
  ↳ [Backtrack] prints 2
↳ [Backtrack] prints 3
```

### Code
```cpp
void print1ToNBacktrack(int i, int n) {
   if(i < 1) return;
   // Call first, print later
   print1ToNBacktrack(i - 1, n);
   cout << i << endl;
}
```

---

## 5. Print N to 1 (Using Backtracking)
### Intuition
Similar to above, to print N down to 1 using a call like `f(i+1, n)`, we wait for the calls to reach the end (N) and then print while returning.

### Code
```cpp
void printNTo1Backtrack(int i, int n) {
   if(i > n) return;
   // Call first, print later
   printNTo1Backtrack(i + 1, n);
   cout << i << endl;
}
```

---

## C++ Implementation (Complete)
### Code with Detailed Comments

```cpp
#include <iostream>
#include <string>
using namespace std;

class BasicRecursion {
public:
    // 1. Print Name N times
    void printName(int i, int n) {
        if (i > n) return;
        cout << "Striver" << endl;
        printName(i + 1, n);
    }

    // 2. Print 1 to N (Linear)
    void print1ToN(int i, int n) {
        if (i > n) return;
        cout << i << endl;
        print1ToN(i + 1, n);
    }

    // 3. Print N to 1 (Linear)
    void printNTo1(int i, int n) {
        if (i < 1) return;
        cout << i << endl;
        printNTo1(i - 1, n);
    }

    // 4. Print 1 to N (Backtracking)
    // We call (i-1) but want to print 1..N
    void print1ToNBacktrack(int i, int n) {
        if (i < 1) return;
        // The recursive call goes all the way to i=0
        print1ToNBacktrack(i - 1, n);
        // This line executes during stack unwinding
        cout << i << endl;
    }

    // 5. Print N to 1 (Backtracking)
    // We call (i+1) but want to print N..1
    void printNTo1Backtrack(int i, int n) {
        if (i > n) return;
        // The recursive call goes all the way up to i=n+1
        printNTo1Backtrack(i + 1, n);
        // This line executes as functions return
        cout << i << endl;
    }
};

int main() {
    BasicRecursion br;
    int n = 5;

    cout << "--- 1 to N (BT) ---" << endl;
    br.print1ToNBacktrack(n, n); 

    cout << "--- N to 1 (BT) ---" << endl;
    br.printNTo1Backtrack(1, n);

    return 0;
}
```

## Complexity Analysis
- **Time Complexity**: O(N) for all functions as we make exactly N calls.
- **Space Complexity**: O(N) for all functions due to the implicit recursion stack depth.

## Key Insights & Interview Tips
- **Backtracking vs. Forward Recursion**: In forward recursion, the task is done **before** the call (`print...call`). In backtracking (or after-call logic), the task is done **after** the call returns (`call...print`).
- **Parameter Control**: Pay close attention to whether your base case matches your increment/decrement logic (e.g., `i > n` with `i+1`).
- **Interview Tip**: If an interviewer asks to print 1 to N but forbids incrementing the parameter in the call, they are testing your understanding of the call stack (Backtracking).
