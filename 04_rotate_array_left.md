# 4. Rotate Array Left by D Places

**LeetCode**: [#189 - Rotate Array](https://leetcode.com/problems/rotate-array/) (adapted for left rotation)  
**Difficulty**: Medium

## Problem Statement

Given an array of integers and a number `d`, rotate the array to the **left** by `d` positions. The rotation should be done in-place with O(1) extra space.

**Left rotation** means elements move towards the left, and elements from the beginning wrap around to the end.

## Examples

### Example 1
```
Input: arr = [1, 2, 3, 4, 5, 6, 7], d = 2
Output: [3, 4, 5, 6, 7, 1, 2]
Explanation: After rotating left by 2:
  - First rotation: [2, 3, 4, 5, 6, 7, 1]
  - Second rotation: [3, 4, 5, 6, 7, 1, 2]
```

### Example 2
```
Input: arr = [1, 2, 3, 4, 5], d = 7
Output: [3, 4, 5, 1, 2]
Explanation: d = 7 is same as d = 7 % 5 = 2 (complete rotations don't change array)
```

### Example 3
```
Input: arr = [1, 2, 3], d = 0
Output: [1, 2, 3]
Explanation: 0 rotations means no change.
```

## Constraints
- 1 ≤ arr.length ≤ 10^5
- -10^9 ≤ arr[i] ≤ 10^9
- 0 ≤ d ≤ 10^9

## Visual Explanation

### ASCII Diagram - Reversal Algorithm

```
Original Array: [1, 2, 3, 4, 5, 6, 7]
Goal: Rotate left by d = 3

Method: THREE REVERSALS

Step 1: Reverse first d elements (0 to d-1)
  Original:  [1, 2, 3 | 4, 5, 6, 7]
                ↓  ↓  ↓
  Reversed:  [3, 2, 1 | 4, 5, 6, 7]
  
Step 2: Reverse remaining elements (d to n-1)
  Current:   [3, 2, 1 | 4, 5, 6, 7]
                         ↓  ↓  ↓  ↓
  Reversed:  [3, 2, 1 | 7, 6, 5, 4]
  
Step 3: Reverse entire array
  Current:   [3, 2, 1, 7, 6, 5, 4]
              ↓  ↓  ↓  ↓  ↓  ↓  ↓
  Reversed:  [4, 5, 6, 7, 1, 2, 3]
  
Result: [4, 5, 6, 7, 1, 2, 3] ✓

Visual Representation:
  Original:  ┌──┬──┬──┬──┬──┬──┬──┐
             │1 │2 │3 │4 │5 │6 │7 │
             └──┴──┴──┴──┴──┴──┴──┘
                      ↓
  Rotate Left by 3 positions
                      ↓
  Result:    ┌──┬──┬──┬──┬──┬──┬──┐
             │4 │5 │6 │7 │1 │2 │3 │
             └──┴──┴──┴──┴──┴──┴──┘
```

### Alternative: Conceptual View

```
Rotate Left by d = 3:

Original:
  Index: 0  1  2  3  4  5  6
  Value: 1  2  3  4  5  6  7
         └──┘─└──────────┘
        These   These stay
        go to   in same
        end     relative order

After Rotation:
  Index: 0  1  2  3  4  5  6
  Value: 4  5  6  7  1  2  3
         └──────────┘─└──┘
         From right   From left
```

## Approach & Intuition

### Approach 1: Reversal Algorithm (Optimal) ⭐

The **reversal algorithm** is elegant and uses O(1) space:

1. Reverse the first `d` elements
2. Reverse the remaining `n-d` elements  
3. Reverse the entire array

**Why this works:**
- Reversing puts elements in opposite positions
- First two reversals separate the two parts
- Final reversal brings them to correct positions

**Example Trace:**
- Original: `[1,2,3,4,5,6,7]`, d=3
- After step 1: `[3,2,1,4,5,6,7]` (first 3 reversed)
- After step 2: `[3,2,1,7,6,5,4]` (last 4 reversed)
- After step 3: `[4,5,6,7,1,2,3]` (entire array reversed)

### Approach 2: Using Extra Space (Simple but not optimal)

- Create temp array to store first d elements
- Shift remaining elements to left
- Copy temp elements to end
- Time: O(n), Space: O(d)

### Approach 3: One by One Rotation (Naive)

- Rotate by 1 position, d times
- Time: O(n×d), Space: O(1)
- Too slow for large d

## Algorithm Steps

**Optimal Reversal Method:**

1. Handle edge cases: if d == 0 or array size ≤ 1, return as is
2. Normalize d: `d = d % n` (to handle d > n)
3. Reverse array from index 0 to d-1
4. Reverse array from index d to n-1
5. Reverse entire array from 0 to n-1

**Helper function:** `reverse(arr, start, end)` - reverses elements between indices start and end

## Dry Run (Step-by-Step Execution)

Array: `[1, 2, 3, 4, 5, 6, 7]`, d = 3, n = 7

```
Initial State:
arr = [1, 2, 3, 4, 5, 6, 7]
d = 3
n = 7

Step 0: Normalize d
  d = d % n = 3 % 7 = 3 (no change needed)

Step 1: Reverse first d elements (indices 0 to 2)
  Before: [1, 2, 3, 4, 5, 6, 7]
           ↓     ↓
  Reverse: arr[0] ↔ arr[2]: [3, 2, 1, 4, 5, 6, 7]
  arr[1] stays same (middle element)
  After: [3, 2, 1, 4, 5, 6, 7]

Step 2: Reverse remaining elements (indices 3 to 6)
  Before: [3, 2, 1, 4, 5, 6, 7]
                    ↓        ↓
  Swap pairs:
    arr[3] ↔ arr[6]: [3, 2, 1, 7, 5, 6, 4]
    arr[4] ↔ arr[5]: [3, 2, 1, 7, 6, 5, 4]
  After: [3, 2, 1, 7, 6, 5, 4]

Step 3: Reverse entire array (indices 0 to 6)
  Before: [3, 2, 1, 7, 6, 5, 4]
           ↓                 ↓
  Swap pairs:
    arr[0] ↔ arr[6]: [4, 2, 1, 7, 6, 5, 3]
    arr[1] ↔ arr[5]: [4, 5, 1, 7, 6, 2, 3]
    arr[2] ↔ arr[4]: [4, 5, 6, 7, 1, 2, 3]
    arr[3] stays (middle)
  After: [4, 5, 6, 7, 1, 2, 3]

Final Result: [4, 5, 6, 7, 1, 2, 3] ✓
```

### Detailed Reversal Function Trace:

```
Function: reverse(arr, 0, 2) - Reverse first 3 elements

Initial: arr = [1, 2, 3, 4, 5, 6, 7], start = 0, end = 2

  left = 0, right = 2
  Iteration 1:
    Swap arr[0] and arr[2]: [3, 2, 1, 4, 5, 6, 7]
    left++, right-- → left = 1, right = 1
  
  left == right (single middle element, stop)
  
Result: [3, 2, 1, 4, 5, 6, 7]
```

## C++ Solution

### Code with Detailed Comments

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;

class Solution {
private:
    // Helper function to reverse a portion of the array
    // Reverses elements from index 'start' to 'end' (inclusive)
    void reverse(vector<int>& arr, int start, int end) {
        // Use two pointers: one from start, one from end
        // Swap elements and move pointers toward center
        while (start < end) {
            // Swap elements at start and end positions
            int temp = arr[start];
            arr[start] = arr[end];
            arr[end] = temp;
            
            // Move pointers closer to center
            start++;
            end--;
        }
        // When start >= end, we've reversed all elements in range
    }
    
public:
    // Function to rotate array left by d positions using Reversal Algorithm
    void rotateLeft(vector<int>& arr, int d) {
        int n = arr.size();
        
        // Edge case: Empty array or single element
        if (n <= 1) {
            return; // Nothing to rotate
        }
        
        // Edge case: If d is 0, no rotation needed
        if (d == 0) {
            return;
        }
        
        // Normalize d: If d > n, we only need d % n rotations
        // Example: rotating by 7 in array of size 5 is same as rotating by 2
        d = d % n;
        
        // After normalization, if d becomes 0, no rotation needed
        if (d == 0) {
            return;
        }
        
        // Reversal Algorithm - 3 steps:
        
        // Step 1: Reverse first d elements
        // This puts the elements that will move to end in reverse order
        reverse(arr, 0, d - 1);
        
        // Step 2: Reverse remaining n-d elements
        // This puts the elements that will be at front in reverse order
        reverse(arr, d, n - 1);
        
        // Step 3: Reverse the entire array
        // This brings both parts to their correct final positions
        reverse(arr, 0, n - 1);
    }
    
    // Alternative: Rotate right by d positions (for comparison)
    void rotateRight(vector<int>& arr, int d) {
        int n = arr.size();
        if (n <= 1) return;
        
        d = d % n;
        if (d == 0) return;
        
        // For right rotation, the logic is slightly different:
        // Reverse last d elements, then first n-d, then entire array
        // OR simply: rotate left by (n - d)
        rotateLeft(arr, n - d);
    }
};

// Driver code for testing
int main() {
    Solution sol;
    
    // Test Case 1: Normal rotation
    vector<int> arr1 = {1, 2, 3, 4, 5, 6, 7};
    cout << "Original: [1, 2, 3, 4, 5, 6, 7]" << endl;
    sol.rotateLeft(arr1, 3);
    cout << "After rotating left by 3: [";
    for (int i = 0; i < arr1.size(); i++) {
        cout << arr1[i];
        if (i < arr1.size() - 1) cout << ", ";
    }
    cout << "]" << endl << endl;
    
    // Test Case 2: d > n
    vector<int> arr2 = {1, 2, 3, 4, 5};
    cout << "Original: [1, 2, 3, 4, 5]" << endl;
    sol.rotateLeft(arr2, 7); // 7 % 5 = 2
    cout << "After rotating left by 7 (same as 2): [";
    for (int i = 0; i < arr2.size(); i++) {
        cout << arr2[i];
        if (i < arr2.size() - 1) cout << ", ";
    }
    cout << "]" << endl << endl;
    
    // Test Case 3: d = 0
    vector<int> arr3 = {1, 2, 3};
    cout << "Original: [1, 2, 3]" << endl;
    sol.rotateLeft(arr3, 0);
    cout << "After rotating left by 0: [";
    for (int i = 0; i < arr3.size(); i++) {
        cout << arr3[i];
        if (i < arr3.size() - 1) cout << ", ";
    }
    cout << "]" << endl << endl;
    
    // Test Case 4: d = n (full rotation, returns to original)
    vector<int> arr4 = {1, 2, 3, 4};
    cout << "Original: [1, 2, 3, 4]" << endl;
    sol.rotateLeft(arr4, 4);
    cout << "After rotating left by 4 (full cycle): [";
    for (int i = 0; i < arr4.size(); i++) {
        cout << arr4[i];
        if (i < arr4.size() - 1) cout << ", ";
    }
    cout << "]" << endl;
    
    return 0;
}
```

### Complexity Analysis

- **Time Complexity**: O(n) - We perform three reversals, each taking O(n/2) time in worst case. Total: O(n/2 + n/2 + n) = O(2n) = O(n).

- **Space Complexity**: O(1) - Only using a few variables for indices and temp storage during swap. No additional arrays or data structures used. True in-place algorithm.

## Edge Cases

1. **Empty array**: `[]` → Return as is
2. **Single element**: `[5]` → Return as is (can't rotate)
3. **d = 0**: No rotation needed
4. **d = n**: Full rotation, array returns to original state
5. **d > n**: Use `d % n` to normalize (e.g., d=7, n=5 → effective d=2)
6. **d is very large**: Modulo handles this: `d = d % n`
7. **Negative numbers**: Algorithm works fine: `[-1, -2, -3]`, d=1 → `[-2, -3, -1]`

## Key Insights & Interview Tips

1. **Why Reversal Algorithm is Clever**:
   - No extra space needed
   - Easy to implement
   - Works for both left and right rotations
   - Only requires a simple reverse operation

2. **Right vs Left Rotation**:
   - Left rotation by d = Right rotation by (n - d)
   - LeetCode #189 asks for right rotation, but logic is same

3. **Handle Large d Values**: Always use `d = d % n` to avoid unnecessary work.

4. **Three Approaches Comparison**:
   ```
   | Method           | Time  | Space | Notes                    |
   |------------------|-------|-------|--------------------------|
   | Reversal         | O(n)  | O(1)  | Optimal ⭐               |
   | Extra Array      | O(n)  | O(d)  | Simple but uses space    |
   | One-by-one       | O(n×d)| O(1)  | Too slow for large d     |
   ```

5. **Interview Follow-ups**:
   - "Can you do it in O(1) space?" → Yes, reversal algorithm
   - "What about rotating right?" → Same algo, use n-d
   - "What if you can use extra space?" → Simpler but less optimal

6. **Related Problems**:
   - Rotate a 2D matrix
   - Cyclic rotation
   - Rotate a linked list
   - Block swap algorithm (another O(1) space method)

7. **Common Mistakes**:
   - Forgetting to handle d > n case
   - Off-by-one errors in reverse function
   - Not handling d = 0 or d = n
   - Using O(n) space when O(1) is required

8. **Alternative: Juggling Algorithm**:
   - Another O(1) space method using GCD
   - More complex to understand
   - Reversal is simpler and equally efficient

9. **Visualization Tip**: Think of rotation as "cut and paste":
   - Cut first d elements
   - Shift rest to left
   - Paste cut elements at end
   - Reversal achieves this without actual cutting/pasting

10. **Code Variations**:
    - Can use `std::reverse()` from `<algorithm>` instead of custom reverse
    - Some prefer iterative one-by-one for very small d values (d < 3)
