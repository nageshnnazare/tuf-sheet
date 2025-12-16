# 26. Pascal's Triangle
**LeetCode**: [118 - Pascal's Triangle](https://leetcode.com/problems/pascals-triangle/)  
**Difficulty**: Easy

## Problem Statement
Given an integer `numRows`, return the first `numRows` of Pascal's triangle.

In Pascal's triangle, each number is the sum of the two numbers directly above it.

## Examples

### Example 1
```
Input: numRows = 5
Output: [[1],[1,1],[1,2,1],[1,3,3,1],[1,4,6,4,1]]

Visual:
     1
    1 1
   1 2 1
  1 3 3 1
 1 4 6 4 1
```

### Example 2
```
Input: numRows = 1
Output: [[1]]
```

### Example 3
```
Input: numRows = 3
Output: [[1],[1,1],[1,2,1]]

Visual:
  1
 1 1
1 2 1
```

## Constraints
- `1 <= numRows <= 30`

## Visual Explanation

### ASCII Diagram
```
Pascal's Triangle Pattern:

Row 0:              1
Row 1:            1   1
Row 2:          1   2   1
Row 3:        1   3   3   1
Row 4:      1   4   6   4   1
Row 5:    1   5  10  10   5   1

Key Pattern:
- Each row starts and ends with 1
- Middle elements: element[i][j] = element[i-1][j-1] + element[i-1][j]

Example for Row 4, Position 2 (value 6):
Row 3:    1   3   3   1
              ↓   ↓
Row 4:    1   4   6   4   1
              3 + 3 = 6

Combinatorial Formula:
element[i][j] = C(i, j) = i! / (j! × (i-j)!)
Where C(n,r) is "n choose r"

Row 4: C(4,0)  C(4,1)  C(4,2)  C(4,3)  C(4,4)
         1      4       6       4       1
```

## Approach & Intuition

**Key Observations**:
1. Each row has `rowIndex + 1` elements
2. First and last elements are always 1
3. Middle elements are sum of two elements from previous row

**Two Approaches**:

**Approach 1: Dynamic Programming (Build row by row)**
- Generate each row using the previous row
- Time: O(numRows²), Space: O(numRows²)

**Approach 2: Combinatorial Formula**
- Each element is a binomial coefficient C(n, k)
- Can compute directly but prone to overflow
- DP approach is more intuitive and safer

## Algorithm Steps

**Row-by-Row Construction**:

1. **Initialize result** with empty 2D vector

2. **For each row i from 0 to numRows-1**:
   - Create a new row vector of size (i+1)
   - Set first element = 1
   - Set last element = 1
   
3. **For middle elements** (if row has more than 2 elements):
   - For each position j from 1 to i-1:
     - `row[j] = prevRow[j-1] + prevRow[j]`
   
4. **Add row to result**

5. **Return result**

## Dry Run (Step-by-Step Execution)

**Input**: `numRows = 5`

```
Row 0 (i=0):
  Create row: [1]
  result = [[1]]

Row 1 (i=1):
  Create row: [1, 1]
  result = [[1], [1,1]]

Row 2 (i=2):
  Create row: [1, ?, 1]
  prevRow = [1, 1]
  j=1: row[1] = prevRow[0] + prevRow[1] = 1 + 1 = 2
  row = [1, 2, 1]
  result = [[1], [1,1], [1,2,1]]

Row 3 (i=3):
  Create row: [1, ?, ?, 1]
  prevRow = [1, 2, 1]
  j=1: row[1] = prevRow[0] + prevRow[1] = 1 + 2 = 3
  j=2: row[2] = prevRow[1] + prevRow[2] = 2 + 1 = 3
  row = [1, 3, 3, 1]
  result = [[1], [1,1], [1,2,1], [1,3,3,1]]

Row 4 (i=4):
  Create row: [1, ?, ?, ?, 1]
  prevRow = [1, 3, 3, 1]
  j=1: row[1] = prevRow[0] + prevRow[1] = 1 + 3 = 4
  j=2: row[2] = prevRow[1] + prevRow[2] = 3 + 3 = 6
  j=3: row[3] = prevRow[2] + prevRow[3] = 3 + 1 = 4
  row = [1, 4, 6, 4, 1]
  result = [[1], [1,1], [1,2,1], [1,3,3,1], [1,4,6,4,1]]
```

**Final Output**: `[[1], [1,1], [1,2,1], [1,3,3,1], [1,4,6,4,1]]` ✓

## C++ Solution

### Code with Detailed Comments

```cpp
class Solution {
public:
    vector<vector<int>> generate(int numRows) {
        vector<vector<int>> result;
        
        // Generate each row one by one
        for (int i = 0; i < numRows; i++) {
            // Create a new row with (i+1) elements
            // Row 0 has 1 element, row 1 has 2 elements, etc.
            vector<int> row(i + 1);
            
            // First and last elements are always 1
            row[0] = 1;
            row[i] = 1;
            
            // Calculate middle elements using previous row
            // Only needed when row has more than 2 elements
            for (int j = 1; j < i; j++) {
                // Current element = sum of two elements from previous row
                // element[i][j] = element[i-1][j-1] + element[i-1][j]
                row[j] = result[i - 1][j - 1] + result[i - 1][j];
            }
            
            // Add the completed row to result
            result.push_back(row);
        }
        
        return result;
    }
};
```

### Alternative: Using previous row reference

```cpp
class Solution {
public:
    vector<vector<int>> generate(int numRows) {
        vector<vector<int>> result;
        
        for (int i = 0; i < numRows; i++) {
            vector<int> row(i + 1, 1);  // Initialize all to 1
            
            // Calculate middle elements if row exists (i > 0)
            if (i > 0) {
                for (int j = 1; j < i; j++) {
                    row[j] = result.back()[j - 1] + result.back()[j];
                }
            }
            
            result.push_back(row);
        }
        
        return result;
    }
};
```

### Space-optimized (if only last row needed):

```cpp
// If problem asks for only the nth row (LeetCode 119)
class Solution {
public:
    vector<int> getRow(int rowIndex) {
        vector<int> row(rowIndex + 1, 1);
        
        // Build row in-place by updating from right to left
        for (int i = 1; i <= rowIndex; i++) {
            // Update from right to left to avoid overwriting values we need
            for (int j = i - 1; j > 0; j--) {
                row[j] = row[j] + row[j - 1];
            }
        }
        
        return row;
    }
};
```

### Complexity Analysis
- **Time Complexity**: O(numRows²) - For numRows rows, we compute roughly 1+2+3+...+numRows = numRows×(numRows+1)/2 elements
- **Space Complexity**: O(numRows²) - Storing the entire triangle. If we only need the last row, can optimize to O(numRows).

## Edge Cases
- **numRows = 1**: `[[1]]`
- **numRows = 2**: `[[1], [1,1]]`
- **Large numRows (30)**: Should not overflow with int (max value in row 30 is C(30,15) ≈ 155 million, fits in int)
- **Triangle with many rows**: Pattern remains consistent

## Key Insights & Interview Tips

**Important Observations**:
- Pascal's triangle appears in many areas: combinatorics, binomial expansion, probability
- Row n contains binomial coefficients: (1+x)^n = C(n,0) + C(n,1)×x + ... + C(n,n)×x^n
- Symmetry property: row[i][j] = row[i][i-j]
- Sum of row n = 2^n
- Hockey stick identity and other patterns exist

**Common Mistakes to Avoid**:
- Forgetting to initialize first and last elements to 1
- Wrong indexing when accessing previous row
- Not handling first two rows specially
- Trying to use formula without handling overflow carefully
- Updating row left-to-right when building in-place (corrupts values)

**What Interviewers Look For**:
- Clean iterative solution that's easy to understand
- Proper indexing without off-by-one errors
- Knowledge of the mathematical properties (binomial coefficients)
- Ability to optimize space if only nth row is needed
- Understanding of the recurrence relation

**Follow-up Questions**:
- Q: "Can you generate only the nth row without generating previous rows?"
- A: Yes, use in-place update from right to left, or use combination formula with careful overflow handling

- Q: "What's the sum of elements in row n?"
- A: 2^n (each element is C(n,k), sum = (1+1)^n = 2^n by binomial theorem)

- Q: "How is this related to binomial coefficients?"
- A: Each element[n][k] = C(n,k) = n!/(k!×(n-k)!)

- Q: "Can you find any element without building the triangle?"
- A: Yes, use combination formula or use Pascal's identity iteratively

**Related Problems**:
- LeetCode 119: Pascal's Triangle II (return only nth row)
- Binomial coefficient calculation
- Catalan numbers (appear in Pascal's triangle diagonals)

**Mathematical Properties**:
```
Row sums: 1, 2, 4, 8, 16, ... (powers of 2)
Diagonal 1: 1, 1, 1, 1, ... (all ones)
Diagonal 2: 1, 2, 3, 4, ... (natural numbers)
Diagonal 3: 1, 3, 6, 10, ... (triangular numbers)
```
