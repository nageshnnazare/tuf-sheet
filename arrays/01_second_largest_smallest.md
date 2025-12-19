# 1. Second Largest & Second Smallest Element in Array

**LeetCode**: N/A (Foundational Concept)  
**Difficulty**: Easy

## Problem Statement

Given an array of integers, find both the second largest and second smallest elements in the array. If the array has less than 2 elements or if second largest/smallest doesn't exist, return appropriate values.

## Examples

### Example 1
```
Input: arr = [12, 35, 1, 10, 34, 1]
Output: Second Largest = 34, Second Smallest = 10
Explanation: Largest is 35, so second largest is 34. Smallest is 1, so second smallest is 10.
```

### Example 2
```
Input: arr = [10, 5, 10]
Output: Second Largest = 5, Second Smallest = 5
Explanation: After removing duplicates conceptually, we have 10 and 5.
```

### Example 3
```
Input: arr = [7]
Output: Second Largest = -1, Second Smallest = -1
Explanation: Array has only one element, no second largest/smallest exists.
```

## Constraints
- 1 ≤ arr.length ≤ 10^5
- -10^9 ≤ arr[i] ≤ 10^9
- Array may contain duplicates

## Visual Explanation

### ASCII Diagram - Finding Second Largest

```
Array: [12, 35, 1, 10, 34, 1]

Initial State:
largest = -∞
secondLargest = -∞

Step 1: arr[0] = 12
  12 > -∞ → Update largest
  largest = 12, secondLargest = -∞

Step 2: arr[1] = 35
  35 > 12 → New largest found!
  secondLargest = largest (12)
  largest = 35
  
Step 3: arr[2] = 1
  1 < 35 and 1 < 12 → No change
  
Step 4: arr[3] = 10
  10 < 35 but 10 < 12 → No change
  
Step 5: arr[4] = 34
  34 < 35 but 34 > 12 → New second largest!
  secondLargest = 34
  
Step 6: arr[5] = 1
  1 < 35 and 1 < 34 → No change

Final: largest = 35, secondLargest = 34

Visualization:
    35 ←── Largest
    34 ←── Second Largest
    12
    10
     1
     1
```

## Approach & Intuition

The key insight is to maintain two variables while traversing the array:
1. **For Second Largest**: Track the largest and second largest values
2. **For Second Smallest**: Track the smallest and second smallest values

**Why this works:**
- We only need one pass through the array
- At each element, we compare with current largest/smallest
- If we find a new largest, the old largest becomes second largest
- If we find a value between largest and second largest, it becomes new second largest

## Algorithm Steps

### For Second Largest:
1. Initialize `largest = INT_MIN` and `secondLargest = INT_MIN`
2. For each element in array:
   - If element > largest: secondLargest = largest, largest = element
   - Else if element > secondLargest AND element != largest: secondLargest = element
3. Return secondLargest (or -1 if not found)

### For Second Smallest:
1. Initialize `smallest = INT_MAX` and `secondSmallest = INT_MAX`
2. For each element in array:
   - If element < smallest: secondSmallest = smallest, smallest = element
   - Else if element < secondSmallest AND element != smallest: secondSmallest = element
3. Return secondSmallest (or -1 if not found)

## Dry Run (Step-by-Step Execution)

Let's trace with array: `[12, 35, 1, 10, 34, 1]`

### Finding Second Largest:

```
Initial: largest = INT_MIN, secondLargest = INT_MIN

Iteration 0: arr[0] = 12
  12 > INT_MIN (largest) → YES
  secondLargest = INT_MIN (old largest)
  largest = 12
  State: largest = 12, secondLargest = INT_MIN

Iteration 1: arr[1] = 35
  35 > 12 (largest) → YES
  secondLargest = 12 (old largest)
  largest = 35
  State: largest = 35, secondLargest = 12

Iteration 2: arr[2] = 1
  1 > 35 (largest) → NO
  1 > 12 (secondLargest) AND 1 != 35 → NO
  State: largest = 35, secondLargest = 12

Iteration 3: arr[3] = 10
  10 > 35 (largest) → NO
  10 > 12 (secondLargest) AND 10 != 35 → NO
  State: largest = 35, secondLargest = 12

Iteration 4: arr[4] = 34
  34 > 35 (largest) → NO
  34 > 12 (secondLargest) AND 34 != 35 → YES
  secondLargest = 34
  State: largest = 35, secondLargest = 34

Iteration 5: arr[5] = 1
  1 > 35 (largest) → NO
  1 > 34 (secondLargest) AND 1 != 35 → NO
  State: largest = 35, secondLargest = 34

Final Result: Second Largest = 34
```

### Finding Second Smallest:

```
Initial: smallest = INT_MAX, secondSmallest = INT_MAX

Iteration 0: arr[0] = 12
  12 < INT_MAX (smallest) → YES
  secondSmallest = INT_MAX (old smallest)
  smallest = 12
  State: smallest = 12, secondSmallest = INT_MAX

Iteration 1: arr[1] = 35
  35 < 12 (smallest) → NO
  35 < INT_MAX (secondSmallest) AND 35 != 12 → YES
  secondSmallest = 35
  State: smallest = 12, secondSmallest = 35

Iteration 2: arr[2] = 1
  1 < 12 (smallest) → YES
  secondSmallest = 12 (old smallest)
  smallest = 1
  State: smallest = 1, secondSmallest = 12

Iteration 3: arr[3] = 10
  10 < 1 (smallest) → NO
  10 < 12 (secondSmallest) AND 10 != 1 → YES
  secondSmallest = 10
  State: smallest = 1, secondSmallest = 10

Iteration 4: arr[4] = 34
  34 < 1 (smallest) → NO
  34 < 10 (secondSmallest) AND 34 != 1 → NO
  State: smallest = 1, secondSmallest = 10

Iteration 5: arr[5] = 1
  1 < 1 (smallest) → NO (equal, not less than)
  1 < 10 (secondSmallest) AND 1 != 1 → NO (equal to smallest)
  State: smallest = 1, secondSmallest = 10

Final Result: Second Smallest = 10
```

## C++ Solution

### Code with Detailed Comments

```cpp
#include <iostream>
#include <vector>
#include <climits>
using namespace std;

class Solution {
public:
    // Function to find the second largest element
    int findSecondLargest(vector<int>& arr) {
        int n = arr.size();
        
        // Edge case: If array has less than 2 elements, no second largest exists
        if (n < 2) {
            return -1;
        }
        
        // Initialize largest and second largest to the smallest possible integer
        int largest = INT_MIN;
        int secondLargest = INT_MIN;
        
        // Traverse through the array once
        for (int i = 0; i < n; i++) {
            // Case 1: Current element is greater than largest
            // This means we found a new largest element
            if (arr[i] > largest) {
                // Before updating largest, save it as second largest
                secondLargest = largest;
                // Update largest to current element
                largest = arr[i];
            }
            // Case 2: Current element is not the largest, but could be second largest
            // Check if it's greater than secondLargest AND not equal to largest
            // (We don't want duplicates of largest to become second largest)
            else if (arr[i] > secondLargest && arr[i] != largest) {
                secondLargest = arr[i];
            }
        }
        
        // If secondLargest is still INT_MIN, it means no second largest was found
        // This happens when all elements are the same
        return (secondLargest == INT_MIN) ? -1 : secondLargest;
    }
    
    // Function to find the second smallest element
    int findSecondSmallest(vector<int>& arr) {
        int n = arr.size();
        
        // Edge case: If array has less than 2 elements, no second smallest exists
        if (n < 2) {
            return -1;
        }
        
        // Initialize smallest and second smallest to the largest possible integer
        int smallest = INT_MAX;
        int secondSmallest = INT_MAX;
        
        // Traverse through the array once
        for (int i = 0; i < n; i++) {
            // Case 1: Current element is smaller than smallest
            // This means we found a new smallest element
            if (arr[i] < smallest) {
                // Before updating smallest, save it as second smallest
                secondSmallest = smallest;
                // Update smallest to current element
                smallest = arr[i];
            }
            // Case 2: Current element is not the smallest, but could be second smallest
            // Check if it's smaller than secondSmallest AND not equal to smallest
            // (We don't want duplicates of smallest to become second smallest)
            else if (arr[i] < secondSmallest && arr[i] != smallest) {
                secondSmallest = arr[i];
            }
        }
        
        // If secondSmallest is still INT_MAX, it means no second smallest was found
        // This happens when all elements are the same
        return (secondSmallest == INT_MAX) ? -1 : secondSmallest;
    }
    
    // Combined function to find both
    pair<int, int> findSecondLargestAndSmallest(vector<int>& arr) {
        int secondLargest = findSecondLargest(arr);
        int secondSmallest = findSecondSmallest(arr);
        return {secondLargest, secondSmallest};
    }
};

// Driver code for testing
int main() {
    Solution sol;
    
    // Test Case 1
    vector<int> arr1 = {12, 35, 1, 10, 34, 1};
    auto result1 = sol.findSecondLargestAndSmallest(arr1);
    cout << "Array: [12, 35, 1, 10, 34, 1]" << endl;
    cout << "Second Largest: " << result1.first << endl;
    cout << "Second Smallest: " << result1.second << endl << endl;
    
    // Test Case 2
    vector<int> arr2 = {10, 5, 10};
    auto result2 = sol.findSecondLargestAndSmallest(arr2);
    cout << "Array: [10, 5, 10]" << endl;
    cout << "Second Largest: " << result2.first << endl;
    cout << "Second Smallest: " << result2.second << endl << endl;
    
    // Test Case 3
    vector<int> arr3 = {7};
    auto result3 = sol.findSecondLargestAndSmallest(arr3);
    cout << "Array: [7]" << endl;
    cout << "Second Largest: " << result3.first << endl;
    cout << "Second Smallest: " << result3.second << endl;
    
    return 0;
}
```

### Complexity Analysis

- **Time Complexity**: O(n) - We traverse the array only once to find both second largest and second smallest elements. Each element is visited exactly once.

- **Space Complexity**: O(1) - We only use a constant amount of extra space (4 integer variables: largest, secondLargest, smallest, secondSmallest) regardless of input size.

## Edge Cases

1. **Array with single element**: `[5]` → Return -1 for both
2. **All elements are same**: `[7, 7, 7, 7]` → Return -1 for both (no distinct second element)
3. **Array with only two distinct elements**: `[10, 5]` → Second largest = 5, Second smallest = 10
4. **Array with duplicates of max**: `[5, 10, 10, 3]` → Second largest = 5 (not the duplicate 10)
5. **Negative numbers**: `[-5, -10, -3, -1]` → Second largest = -3, Second smallest = -5
6. **Mix of positive and negative**: `[-5, 10, 3, -1]` → Second largest = 3, Second smallest = -1

## Key Insights & Interview Tips

1. **One Pass vs Two Pass**: This problem can be solved in a single pass. Don't sort the array (O(n log n)) when O(n) is possible.

2. **Handle Duplicates**: The condition `arr[i] != largest` ensures that duplicate values of the largest don't become the second largest.

3. **Initialization Matters**: Using `INT_MIN` and `INT_MAX` as initial values helps handle negative numbers correctly. Some solutions use `arr[0]` and `arr[1]`, which fails for certain edge cases.

4. **Return Value Convention**: Common approaches:
   - Return -1 if not found
   - Return INT_MIN/INT_MAX if not found
   - Throw an exception
   Clarify with interviewer what they expect.

5. **Similar Problems**: This technique extends to finding kth largest/smallest element (though that typically needs a different approach like QuickSelect or Heap).

6. **Interview Follow-ups**:
   - "Can you find the kth largest element?" → Use QuickSelect or Min-Heap
   - "What if we need top k elements?" → Use a Min-Heap of size k
   - "Can you do it in one pass?" → Yes, as shown above

7. **Common Mistakes**:
   - Forgetting to check `arr[i] != largest` allowing duplicates
   - Sorting the array (inefficient)
   - Not handling single element or all same elements
