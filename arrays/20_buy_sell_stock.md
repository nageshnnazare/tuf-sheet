# 20. Best Time to Buy and Sell Stock

**LeetCode**: [#121 - Best Time to Buy and Sell Stock](https://leetcode.com/problems/best-time-to-buy-and-sell-stock/)  
**Difficulty**: Easy

## Problem Statement

You are given an array `prices` where `prices[i]` is the price of a given stock on the `i`th day.

You want to **maximize your profit** by choosing a **single day** to buy one stock and choosing a **different day in the future** to sell that stock.

Return the **maximum profit** you can achieve from this transaction. If you cannot achieve any profit, return `0`.

**Key Constraints**:
- You must **buy before you sell**
- You can complete **at most one transaction** (one buy + one sell)

## Examples

### Example 1
```
Input: prices = [7, 1, 5, 3, 6, 4]
Output: 5
Explanation: Buy on day 2 (price = 1) and sell on day 5 (price = 6), profit = 6 - 1 = 5.
Note: Buying on day 2 and selling on day 1 is not allowed (must buy before sell).
```

### Example 2
```
Input: prices = [7, 6, 4, 3, 1]
Output: 0
Explanation: No profit possible. Prices only decrease.
```

### Example 3
```
Input: prices = [2, 4, 1]
Output: 2
Explanation: Buy at 2, sell at 4, profit = 2.
```

### Example 4
```
Input: prices = [3, 2, 6, 5, 0, 3]
Output: 4
Explanation: Buy at 2, sell at 6, profit = 4.
```

## Constraints
- 1 ≤ prices.length ≤ 10^5
- 0 ≤ prices[i] ≤ 10^4

## Visual Explanation

### ASCII Diagram - Track Minimum and Calculate Profit

```
Prices: [7, 1, 5, 3, 6, 4]

Concept: For each day, calculate profit if we sell today
         = today's price - minimum price seen so far

Day 0: price = 7
  min_price = 7
  profit = 7 - 7 = 0
  max_profit = 0

Day 1: price = 1
  min_price = min(7, 1) = 1 (new minimum!)
  profit = 1 - 1 = 0
  max_profit = 0

Day 2: price = 5
  min_price = 1 (unchanged)
  profit = 5 - 1 = 4 ✓
  max_profit = max(0, 4) = 4

Day 3: price = 3
  min_price = 1 (unchanged)
  profit = 3 - 1 = 2
  max_profit = max(4, 2) = 4

Day 4: price = 6
  min_price = 1 (unchanged)
  profit = 6 - 1 = 5 ✓ (best so far!)
  max_profit = max(4, 5) = 5

Day 5: price = 4
  min_price = 1 (unchanged)
  profit = 4 - 1 = 3
  max_profit = max(5, 3) = 5

Result: 5 (buy at 1, sell at 6)

Visual Graph:
Price
  7 │ ●
  6 │     ●         ← Sell here
  5 │       ●   ●
  4 │           
  3 │         ●
  2 │
  1 │   ●           ← Buy here
  0 └─────────────────→ Day
      0 1 2 3 4 5

  Min tracked: [7, 1, 1, 1, 1, 1]
  Profit:      [0, 0, 4, 2, 5, 3]
  Max profit:  [0, 0, 4, 4, 5, 5]
```

### Descending Prices (No Profit)

```
Prices: [7, 6, 4, 3, 1]

Day 0: price=7, min=7, profit=0, maxProfit=0
Day 1: price=6, min=6, profit=0, maxProfit=0
Day 2: price=4, min=4, profit=0, maxProfit=0
Day 3: price=3, min=3, profit=0, maxProfit=0
Day 4: price=1, min=1, profit=0, maxProfit=0

Visual:
  7 │ ●
  6 │   ●
  4 │     ●
  3 │       ●
  1 │         ●
  0 └───────────→ Day

Continuously decreasing → No profitable transaction
Result: 0
```

## Approach & Intuition

### Naive Approach: Brute Force
- Try all pairs (buy_day, sell_day) where buy_day < sell_day
- Calculate profit for each pair
- Track maximum
- Time: O(n²), Space: O(1) ❌ Too slow

### Optimal Approach: One-Pass with Min Tracking ⭐

**Key Insight**:
- For each day, we want to know: "If I sell today, what's the best profit?"
- Best profit = today's price - lowest price seen before today
- Track minimum price seen so far as we scan left to right

**Algorithm**:
1. Track `minPrice` (lowest price seen so far)
2. Track `maxProfit` (best profit found so far)
3. For each day:
   - Update minPrice if today is cheaper
   - Calculate profit if selling today = price[today] - minPrice
   - Update maxProfit if this profit is better

**Why this works**:
- We always buy at the lowest price seen so far
- We always calculate potential profit for selling today
- One pass captures all possible profitable transactions

## Algorithm Steps

1. Initialize minPrice = ∞ (or first price), maxProfit = 0
2. For each day i from 0 to n-1:
   - If prices[i] < minPrice:
     - Update minPrice = prices[i]
   - Calculate profit = prices[i] - minPrice
   - If profit > maxProfit:
     - Update maxProfit = profit
3. Return maxProfit

## Dry Run (Step-by-Step Execution)

Input: `prices = [7, 1, 5, 3, 6, 4]`

```
Initial State:
prices = [7, 1, 5, 3, 6, 4]
n = 6
minPrice = ∞ (or INT_MAX)
maxProfit = 0

Iteration 0: day=0, price=7
  Is 7 < ∞? YES
  minPrice = 7
  
  Calculate profit if sell today:
  profit = 7 - 7 = 0
  
  Is 0 > 0? NO
  maxProfit = 0
  
  State: minPrice=7, maxProfit=0

Iteration 1: day=1, price=1
  Is 1 < 7? YES (new minimum!)
  minPrice = 1
  
  Calculate profit if sell today:
  profit = 1 - 1 = 0
  
  Is 0 > 0? NO
  maxProfit = 0
  
  State: minPrice=1, maxProfit=0

Iteration 2: day=2, price=5
  Is 5 < 1? NO
  minPrice = 1 (unchanged)
  
  Calculate profit if sell today:
  profit = 5 - 1 = 4
  
  Is 4 > 0? YES! (first profitable transaction)
  maxProfit = 4
  
  State: minPrice=1, maxProfit=4

Iteration 3: day=3, price=3
  Is 3 < 1? NO
  minPrice = 1 (unchanged)
  
  Calculate profit if sell today:
  profit = 3 - 1 = 2
  
  Is 2 > 4? NO
  maxProfit = 4 (unchanged)
  
  State: minPrice=1, maxProfit=4

Iteration 4: day=4, price=6
  Is 6 < 1? NO
  minPrice = 1 (unchanged)
  
  Calculate profit if sell today:
  profit = 6 - 1 = 5
  
  Is 5 > 4? YES! (better profit found)
  maxProfit = 5
  
  State: minPrice=1, maxProfit=5

Iteration 5: day=5, price=4
  Is 4 < 1? NO
  minPrice = 1 (unchanged)
  
  Calculate profit if sell today:
  profit = 4 - 1 = 3
  
  Is 3 > 5? NO
  maxProfit = 5 (unchanged)
  
  State: minPrice=1, maxProfit=5

Final Result: 5
(Buy on day 1 at price 1, sell on day 4 at price 6)
```

### Trace Table

```
Day | Price | MinPrice | Profit | MaxProfit | Action
----|-------|----------|--------|-----------|------------------
 0  |   7   |    7     |   0    |     0     | First day
 1  |   1   |    1     |   0    |     0     | New min found
 2  |   5   |    1     |   4    |     4     | Profitable!
 3  |   3   |    1     |   2    |     4     | Lower profit
 4  |   6   |    1     |   5    |     5     | Best profit! ✓
 5  |   4   |    1     |   3    |     5     | Final check
```

## C++ Solution

### Code with Detailed Comments

```cpp
#include <iostream>
#include <vector>
#include <climits>
#include <algorithm>
using namespace std;

class Solution {
public:
    // Optimal: Single-pass with min tracking
    int maxProfit(vector<int>& prices) {
        int n = prices.size();
        
        // Edge case: Empty or single price
        // Can't make any transaction with less than 2 prices
        if (n <= 1) {
            return 0;
        }
        
        // Track the minimum price seen so far
        // This represents the best possible buy price up to current day
        int minPrice = INT_MAX;
        
        // Track the maximum profit found so far
        // This is our answer - best profit achievable
        int maxProfit = 0;
        
        // Scan through all prices (each day)
        for (int i = 0; i < n; i++) {
            // Update minimum price if today's price is lower
            // This gives us the best buy opportunity seen so far
            if (prices[i] < minPrice) {
                minPrice = prices[i];
            }
            
            // Calculate profit if we sell today
            // Profit = selling price (today) - buying price (minPrice so far)
            int profit = prices[i] - minPrice;
            
            // Update maximum profit if this transaction is better
            // We're always checking: is selling today better than previous best?
            if (profit > maxProfit) {
                maxProfit = profit;
            }
            
            // Alternative concise version:
            // minPrice = min(minPrice, prices[i]);
            // maxProfit = max(maxProfit, prices[i] - minPrice);
        }
        
        // Return the best profit found
        // If no profitable transaction exists, this will be 0
        return maxProfit;
    }
    
    // Alternative: More concise version (same logic)
    int maxProfitConcise(vector<int>& prices) {
        int minPrice = INT_MAX;
        int maxProfit = 0;
        
        for (int price : prices) {
            // Update minimum price
            minPrice = min(minPrice, price);
            // Update maximum profit
            maxProfit = max(maxProfit, price - minPrice);
        }
        
        return maxProfit;
    }
    
    // For comparison: Brute Force O(n²) approach
    int maxProfitBruteForce(vector<int>& prices) {
        int n = prices.size();
        int maxProfit = 0;
        
        // Try all possible buy days
        for (int buy = 0; buy < n; buy++) {
            // Try all possible sell days after buy day
            for (int sell = buy + 1; sell < n; sell++) {
                // Calculate profit for this pair
                int profit = prices[sell] - prices[buy];
                // Update max if better
                maxProfit = max(maxProfit, profit);
            }
        }
        
        return maxProfit;
    }
};

// Driver code for testing
int main() {
    Solution sol;
    
    // Test Case 1: Normal case with profit
    vector<int> prices1 = {7, 1, 5, 3, 6, 4};
    cout << "Prices: [7, 1, 5, 3, 6, 4]" << endl;
    cout << "Max Profit: " << sol.maxProfit(prices1) << endl;
    cout << "Explanation: Buy at 1, sell at 6, profit = 5" << endl << endl;
    
    // Test Case 2: Decreasing prices (no profit)
    vector<int> prices2 = {7, 6, 4, 3, 1};
    cout << "Prices: [7, 6, 4, 3, 1]" << endl;
    cout << "Max Profit: " << sol.maxProfit(prices2) << endl;
    cout << "Explanation: Prices only decrease, no profit possible" << endl << endl;
    
    // Test Case 3: Simple increase
    vector<int> prices3 = {1, 2, 3, 4, 5};
    cout << "Prices: [1, 2, 3, 4, 5]" << endl;
    cout << "Max Profit: " << sol.maxProfit(prices3) << endl;
    cout << "Explanation: Buy at 1, sell at 5, profit = 4" << endl << endl;
    
    // Test Case 4: Single peak
    vector<int> prices4 = {2, 4, 1};
    cout << "Prices: [2, 4, 1]" << endl;
    cout << "Max Profit: " << sol.maxProfit(prices4) << endl;
    cout << "Explanation: Buy at 2, sell at 4, profit = 2" << endl << endl;
    
    // Test Case 5: Two elements
    vector<int> prices5 = {3, 1};
    cout << "Prices: [3, 1]" << endl;
    cout << "Max Profit: " << sol.maxProfit(prices5) << endl;
    cout << "Explanation: Price decreases, no profit" << endl;
    
    return 0;
}
```

### Complexity Analysis

**Optimal Approach:**
- **Time Complexity**: O(n) - Single pass through the array. Each price is visited exactly once.
- **Space Complexity**: O(1) - Only using two variables (`minPrice` and `maxProfit`). No additional data structures.

**Brute Force:**
- **Time Complexity**: O(n²) - Nested loops checking all pairs
- **Space Complexity**: O(1) - Only variables

## Edge Cases

1. **Single price**: `[5]` → `0` (need at least 2 days)
2. **Two prices ascending**: `[1, 5]` → `4`
3. **Two prices descending**: `[5, 1]` → `0`
4. **All same**: `[3, 3, 3]` → `0`
5. **Strictly increasing**: `[1, 2, 3, 4, 5]` → Buy first, sell last = `4`
6. **Strictly decreasing**: `[5, 4, 3, 2, 1]` → `0`
7. **One valley**: `[5, 1, 5]` → Buy at valley, sell after = `4`

## Key Insights & Interview Tips

1. **Pattern: Track Minimum So Far**:
   - Common pattern in many problems
   - Similar to: rain water trapping, leaders in array
   - Remember this technique!

2. **Why Not Brute Force?**:
   - O(n²) is too slow for n = 10^5
   - Would cause Time Limit Exceeded
   - Always look for O(n) solution first

3. **Order Matters**:
   - Must buy before sell (temporal constraint)
   - Can't look into future when buying
   - This is why we track min "so far", not global min

4. **Why maxProfit Starts at 0**:
   - If no profit possible, we return 0
   - We don't make a losing transaction
   - 0 represents "don't trade"

5. **Variations & Follow-ups**:
   
   **LeetCode #122: Multiple Transactions**
   - Can buy and sell multiple times
   - Solution: Sum all positive differences
   - `profit += max(0, prices[i] - prices[i-1])`
   
   **LeetCode #123: At Most 2 Transactions**
   - More complex DP problem
   - Track state: [first buy, first sell, second buy, second sell]
   
   **LeetCode #188: At Most K Transactions**
   - Generalized version
   - DP with k states
   
   **With Transaction Fee**
   - Subtract fee from each profit calculation
   
   **With Cooldown**
   - Can't buy immediately after selling

6. **Common Mistakes**:
   - Using global min/max instead of "so far"
   - Comparing prices[i] with prices[i-1] only (misses non-adjacent opportunities)
   - Forgetting that profit can be 0
   - Not handling single element case

7. **Real-World Applications**:
   - Actual stock trading (simplified model)
   - Finding best conversion rates
   - Buy low, sell high in any market

8. **Interview Strategy**:
   - Start by stating brute force (shows you understand problem)
   - Explain why it's inefficient
   - Optimize by "what if we track minimum as we go?"
   - This shows problem-solving progression

9. **Extension Questions**:
   - "What if you want to return buy and sell days?" → Track indices when updating max
   - "What about predicting future prices?" → Outside scope (this assumes we know all prices)
   - "Multiple stocks?" → Independent calculations for each

10. **Why This is "Easy"**:
    - Despite seeming complex, the optimal solution is elegant
    - One key insight (track minimum) solves it
    - Common interview question to test optimization skills

11. **Comparison with Similar Problems**:
    ```
    | Problem              | Transactions | Strategy        |
    |----------------------|--------------|-----------------|
    | Best Time I (#121)   | 1            | Track min       |
    | Best Time II (#122)  | Unlimited    | Sum all gains   |
    | Best Time III (#123) | 2            | DP states       |
    | Best Time IV (#188)  | K            | DP with k       |
    ```

12. **Mental Model**:
    Think of it as: "For each day, if I sell today, what's my best profit based on past?"
    - Past determines buy price (minimum seen)
    - Today determines sell price (current price)
    - Track best across all days

