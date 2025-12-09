# 19. Leaders in an Array

**LeetCode**: N/A (GeeksforGeeks Problem)  
**Difficulty**: Easy

## Problem Statement

Given an array of integers, find all the **leaders** in the array.

An element is a **leader** if it is **greater than or equal to** all the elements to its right side. The rightmost element is always a leader.

**Note**: Return the leaders in the order they appear in the array (left to right).

## Examples

### Example 1
```
Input: arr = [16, 17, 4, 3, 5, 2]
Output: [17, 5, 2]
Explanation:
- 17 is greater than all elements to its right
- 5 is greater than all elements to its right (only 2)
- 2 is the rightmost element (always a leader)
```

### Example 2
```
Input: arr = [1, 2, 3, 4, 5]
Output: [5]
Explanation: Only 5 is greater than all elements to its right (none).
```

### Example 3
```
Input: arr = [5, 4, 3, 2, 1]
Output: [5, 4, 3, 2, 1]
Explanation: All elements are leaders (descending order).
```

### Example 4
```
Input: arr = [7, 10, 4, 10, 6, 5, 2]
Output: [10, 10, 6, 5, 2]
```

## Constraints
- 1 ≤ arr.length ≤ 10^5
- -10^9 ≤ arr[i] ≤ 10^9

## Visual Explanation

### ASCII Diagram - Right to Left Scan

```
Array: [16, 17, 4, 3, 5, 2]

Brute Force (checking each element):
  16: Check if >= all right [17,4,3,5,2] → NO (16 < 17)
  17: Check if >= all right [4,3,5,2] → YES ✓
  4:  Check if >= all right [3,5,2] → NO (4 < 5)
  3:  Check if >= all right [5,2] → NO (3 < 5)
  5:  Check if >= all right [2] → YES ✓
  2:  Rightmost → YES ✓

Optimal (Right to Left with max tracking):
  
  Start from right → left:
  [16, 17, 4, 3, 5, 2]
                     ↑
  i=5: arr[5]=2, max_right=2
    2 >= 2 → Leader! ✓
    
  [16, 17, 4, 3, 5, 2]
                  ↑
  i=4: arr[4]=5, max_right=2
    5 >= 2 → Leader! ✓
    Update max_right = 5
    
  [16, 17, 4, 3, 5, 2]
               ↑
  i=3: arr[3]=3, max_right=5
    3 >= 5? NO
    
  [16, 17, 4, 3, 5, 2]
            ↑
  i=2: arr[2]=4, max_right=5
    4 >= 5? NO
    
  [16, 17, 4, 3, 5, 2]
       ↑
  i=1: arr[1]=17, max_right=5
    17 >= 5 → Leader! ✓
    Update max_right = 17
    
  [16, 17, 4, 3, 5, 2]
   ↑
  i=0: arr[0]=16, max_right=17
    16 >= 17? NO

Leaders found (in reverse): [2, 5, 17]
Reverse to get original order: [17, 5, 2] ✓
```

## Approach & Intuition

### Approach 1: Brute Force (Nested Loop)
- For each element, check all elements to its right
- If greater than all, it's a leader
- Time: O(n²), Space: O(1)

### Approach 2: Right to Left Scan (Optimal) ⭐

**Key Insight**: 
- Scan from **right to left**
- Track the **maximum seen so far** from the right
- If current element ≥ max_right, it's a leader
- Update max_right

**Why this works**:
- An element is a leader if it's ≥ all elements to its right
- The maximum of all right elements represents the "toughest" comparison
- If current ≥ max of right, it's ≥ all individual right elements

**Tradeoff**:
- Leaders are found in reverse order
- Need to reverse result for original order

## Algorithm Steps

**Optimal Approach:**

1. Initialize leaders = [], max_right = -∞
2. Traverse from right to left (i from n-1 to 0):
   - If arr[i] >= max_right:
     - It's a leader, add to result
     - Update max_right = arr[i]
3. Reverse leaders array
4. Return leaders

## Dry Run (Step-by-Step Execution)

Input: `arr = [16, 17, 4, 3, 5, 2]`

```
Initial State:
arr = [16, 17, 4, 3, 5, 2]
n = 6
leaders = []
max_right = -∞ (or INT_MIN)

Iteration 5: i=5, arr[5]=2
  2 >= -∞ → YES, it's a leader!
  leaders.push_back(2)
  leaders = [2]
  max_right = max(-∞, 2) = 2

Iteration 4: i=4, arr[4]=5
  5 >= 2 → YES, it's a leader!
  leaders.push_back(5)
  leaders = [2, 5]
  max_right = max(2, 5) = 5

Iteration 3: i=3, arr[3]=3
  3 >= 5 → NO, not a leader
  max_right = max(5, 3) = 5 (unchanged)

Iteration 2: i=2, arr[2]=4
  4 >= 5 → NO, not a leader
  max_right = max(5, 4) = 5 (unchanged)

Iteration 1: i=1, arr[1]=17
  17 >= 5 → YES, it's a leader!
  leaders.push_back(17)
  leaders = [2, 5, 17]
  max_right = max(5, 17) = 17

Iteration 0: i=0, arr[0]=16
  16 >= 17 → NO, not a leader
  max_right = max(17, 16) = 17 (unchanged)

Current leaders (reverse order): [2, 5, 17]

Reverse to get original left-to-right order:
Result: [17, 5, 2] ✓
```

## C++ Solution

### Code with Detailed Comments

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
#include <climits>
using namespace std;

class Solution {
public:
    // Optimal: Right to left scan with max tracking
    vector<int> leaders(vector<int>& arr) {
        int n = arr.size();
        vector<int> result;
        
        // Track maximum element seen so far from the right
        // Initialize to smallest possible value
        int maxRight = INT_MIN;
        
        // Traverse from right to left
        // This allows us to track maximum of all right elements
        for (int i = n - 1; i >= 0; i--) {
            // Check if current element is >= max of all right elements
            // If yes, it's a leader (greater than all to its right)
            if (arr[i] >= maxRight) {
                // Found a leader!
                result.push_back(arr[i]);
                
                // Update max_right to current element
                // This becomes the new "bar" to beat for left elements
                maxRight = arr[i];
            }
            // If arr[i] < maxRight, not a leader, skip
        }
        
        // Leaders were collected from right to left
        // Reverse to get them in original left-to-right order
        reverse(result.begin(), result.end());
        
        return result;
    }
    
    // Alternative: Brute Force (for comparison)
    // Time: O(n²), Space: O(1) extra
    vector<int> leadersBruteForce(vector<int>& arr) {
        int n = arr.size();
        vector<int> result;
        
        // Check each element
        for (int i = 0; i < n; i++) {
            bool isLeader = true;
            
            // Check if arr[i] >= all elements to its right
            for (int j = i + 1; j < n; j++) {
                if (arr[i] < arr[j]) {
                    isLeader = false;
                    break;
                }
            }
            
            if (isLeader) {
                result.push_back(arr[i]);
            }
        }
        
        return result;
    }
};

// Driver code for testing
int main() {
    Solution sol;
    
    // Test Case 1
    vector<int> arr1 = {16, 17, 4, 3, 5, 2};
    cout << "Array: [16, 17, 4, 3, 5, 2]" << endl;
    vector<int> result1 = sol.leaders(arr1);
    cout << "Leaders: [";
    for (int i = 0; i < result1.size(); i++) {
        cout << result1[i];
        if (i < result1.size() - 1) cout << ", ";
    }
    cout << "]" << endl << endl;
    
    // Test Case 2: Ascending order
    vector<int> arr2 = {1, 2, 3, 4, 5};
    cout << "Array: [1, 2, 3, 4, 5]" << endl;
    vector<int> result2 = sol.leaders(arr2);
    cout << "Leaders: [";
    for (int i = 0; i < result2.size(); i++) {
        cout << result2[i];
        if (i < result2.size() - 1) cout << ", ";
    }
    cout << "]" << endl << endl;
    
    // Test Case 3: Descending order
    vector<int> arr3 = {5, 4, 3, 2, 1};
    cout << "Array: [5, 4, 3, 2, 1]" << endl;
    vector<int> result3 = sol.leaders(arr3);
    cout << "Leaders: [";
    for (int i = 0; i < result3.size(); i++) {
        cout << result3[i];
        if (i < result3.size() - 1) cout << ", ";
    }
    cout << "]" << endl << endl;
    
    // Test Case 4: Single element
    vector<int> arr4 = {10};
    cout << "Array: [10]" << endl;
    vector<int> result4 = sol.leaders(arr4);
    cout << "Leaders: [";
    for (int i = 0; i < result4.size(); i++) {
        cout << result4[i];
        if (i < result4.size() - 1) cout << ", ";
    }
    cout << "]" << endl;
    
    return 0;
}
```

### Complexity Analysis

**Optimal Approach:**
- **Time Complexity**: O(n) - Single pass from right to left + O(n) for reversing = O(2n) = O(n)
- **Space Complexity**: O(k) where k is number of leaders. In worst case (descending array), k = n, so O(n).

**Brute Force:**
- **Time Complexity**: O(n²) - For each element, check all right elements
- **Space Complexity**: O(k) for result

## Edge Cases

1. **Single element**: `[10]` → `[10]` (rightmost always leader)
2. **All ascending**: `[1,2,3,4,5]` → `[5]` (only last)
3. **All descending**: `[5,4,3,2,1]` → `[5,4,3,2,1]` (all leaders)
4. **All same**: `[3,3,3,3]` → `[3,3,3,3]` (all leaders, >= condition)
5. **Negatives**: `[-1,-5,-3,-2]` → `[-1,-2]`
6. **Two elements**: `[5,3]` → `[5,3]`

## Key Insights & Interview Tips

1. **Direction Matters**:
   - Right to left is key insight
   - Allows us to track max in one pass
   - Left to right would require checking all right elements

2. **Why >= and not >?**:
   - Problem typically uses >= (leader can equal right elements)
   - Some variations use > (strictly greater)
   - Clarify with interviewer!

3. **Reverse Order Collection**:
   - Natural consequence of right-to-left scan
   - Small price for O(n) overall complexity
   - Alternative: use deque and push_front (avoids reverse)

4. **Max Tracking Pattern**:
   - Common pattern in array problems
   - Similar to: stock price problems, rain water trapping
   - Remember this technique!

5. **Interview Variations**:
   - "Print leaders in reverse order?" → Don't reverse result
   - "Count leaders only?" → Same algorithm, just count++
   - "Find only the maximum leader?" → Track max while scanning

6. **Common Mistakes**:
   - Scanning left to right (makes it O(n²))
   - Using > instead of >= (or vice versa without clarifying)
   - Forgetting to reverse result
   - Not initializing max_right properly

7. **Related Problems**:
   - Next greater element
   - Stock span problem
   - Monotonic stack problems

8. **Real-World Analogy**:
   - Mountain peaks visible from right
   - Buildings blocking view from right
   - Queue of people where some can see over others

9. **Alternative Implementation**:
   ```cpp
   // Using deque to avoid reverse
   deque<int> dq;
   for (int i = n-1; i >= 0; i--) {
       if (arr[i] >= maxRight) {
           dq.push_front(arr[i]);
           maxRight = arr[i];
       }
   }
   return vector<int>(dq.begin(), dq.end());
   ```

10. **Pro Tip**: This problem teaches the powerful "scan from right with max tracking" pattern that appears in many array problems!

