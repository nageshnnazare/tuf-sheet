# 04. Reverse an Array & Check Palindrome
**Source**: [Striver's Recursion Series - L4](https://youtu.be/twuUyfS9X-I)  
**Difficulty**: Easy (Foundational)

## Overview
Reversing an array and checking if a string is a palindrome are classic problems that can be solved recursively by swapping or comparing elements from both ends. We will explore two recursive variations:
1. **Two-Pointer Recursion**: Passing `left` and `right` indices.
2. **Single-Pointer Recursion**: Passing only index `i` and calculating the other side using array size.

---

## 1. Reverse an Array
### Intuition
To reverse an array, we swap the first and last elements, then recursively call the function for the remaining inner part of the array.

### Visual Explanation
```
Array: [1, 2, 3, 4, 5]
Indices: 0  1  2  3  4

Call 1: swap(arr[0], arr[4]) -> [5, 2, 3, 4, 1]
Call 2: swap(arr[1], arr[3]) -> [5, 4, 3, 2, 1]
Call 3: l=2, r=2. Base Case: Return.
```

### Approach 1: Two Pointers (l, r)
```cpp
void reverse(int l, int r, int arr[]) {
    if(l >= r) return;
    swap(arr[l], arr[r]);
    reverse(l + 1, r - 1, arr);
}
```

### Approach 2: Single Pointer (i)
```cpp
void reverse(int i, int n, int arr[]) {
    if(i >= n/2) return;
    swap(arr[i], arr[n - i - 1]);
    reverse(i + 1, n, arr);
}
```

---

## 2. Check if String is Palindrome
### Intuition
A string is a palindrome if it reads the same forwards and backwards. Recursively, we check if the characters at index `i` and `n-i-1` match. If any pair doesn't match, it's not a palindrome.

### Code
```cpp
bool isPalindrome(int i, string &s) {
    if(i >= s.size() / 2) return true;
    if(s[i] != s[s.size() - i - 1]) return false;
    return isPalindrome(i + 1, s);
}
```

---

## C++ Implementation (Complete)
### Code with Detailed Comments

```cpp
#include <iostream>
#include <vector>
#include <string>
#include <algorithm>
using namespace std;

class FunctionalRecursion {
public:
    // 1. Reverse an Array (Single Pointer Method)
    // i: current index starting from 0
    // n: size of the array
    void reverseArray(int i, int n, vector<int>& arr) {
        // Base Case: If we reach or cross the middle
        if (i >= n / 2) return;
        
        // Swap elements at symmetric positions
        swap(arr[i], arr[n - i - 1]);
        
        // Recursive call for the next index
        reverseArray(i + 1, n, arr);
    }

    // 2. Check if String is Palindrome
    // i: current index starting from 0
    bool isPalindrome(int i, string& s) {
        // Base Case: If we reached the middle, all pairs matched
        if (i >= s.size() / 2) return true;
        
        // Logical check: If current pair doesn't match
        if (s[i] != s[s.size() - i - 1]) return false;
        
        // Move to the next inner pair
        return isPalindrome(i + 1, s);
    }
};

int main() {
    FunctionalRecursion fr;

    // Test Array Reversal
    vector<int> arr = {1, 2, 3, 4, 5};
    fr.reverseArray(0, arr.size(), arr);
    cout << "Reversed Array: ";
    for (int x : arr) cout << x << " ";
    cout << endl;

    // Test Palindrome
    string s = "MADAM";
    if (fr.isPalindrome(0, s)) cout << s << " is a Palindrome" << endl;
    else cout << s << " is NOT a Palindrome" << endl;

    return 0;
}
```

## Complexity Analysis
- **Time Complexity**: O(N/2) ≈ O(N) for both, as we visit half of the elements.
- **Space Complexity**: O(N/2) ≈ O(N) due to the recursion stack depth.

## Key Insights & Interview Tips
- **Index Math**: For an index `i`, its symmetric companion in an array of size `n` is always `n - i - 1`.
- **Base Case**: The condition `i >= n/2` ensures that the recursion stops exactly at the midpoint, avoiding redundant swaps or comparisons.
- **Why Functional?**: The Palindrome check returns a boolean value (`true`/`false`) that propagates back up the stack, which is the hallmark of functional recursion.
- **Optimization**: For very large arrays/strings, the iterative two-pointer approach is preferred to save memory (O(1) space).
