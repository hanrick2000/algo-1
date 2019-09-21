## DP 例题

### Grid

#### 64. Minimum Path Sum

Given a m x n grid filled with non-negative numbers, find a path from top left to bottom right which minimizes the sum of all numbers along its path.

Note: You can only move either down or right at any point in time.

```python
class Solution:
    def minPathSum(self, grid: List[List[int]]) -> int:
        # state: f[i][j]
        # final state: f[m-1][n-1]
        # f[i][j] = min(f[i-1][j],f[i][j-1])+grid[i][j]
        # initial state and boundary states:
        # f[0][0] = 0; i==0 or j==0 -- only coming from one direction
        if not grid or not grid[0]:
            return 0
        # init state function
        f = [[sys.maxsize for _ in range(len(grid[0]))] for _ in range(len(grid))]
        for i in range(len(grid)):
            for j in range(len(grid[0])):
                if i == 0 and j == 0:
                    f[i][j] = grid[0][0] # initial state
                # boundary states will skip one of below accordingly
                if i != 0:
                    f[i][j] = min(f[i][j],f[i-1][j]+grid[i][j])
                if j != 0:
                    f[i][j] = min(f[i][j],f[i][j-1]+grid[i][j])
        return f[-1][-1]
```
#### variation: 63. Unique Paths II

A robot is located at the top-left corner of a m x n grid (marked 'Start' in the diagram below). The robot can only move either down or right at any point in time. The robot is trying to reach the bottom-right corner of the grid (marked 'Finish' in the diagram below).

Now consider if some obstacles (marked as 1 in grid) are added to the grids. How many unique paths would there be?

```python
class Solution:
    def uniquePathsWithObstacles(self, obstacleGrid: List[List[int]]) -> int:
        # state: f[i][j]
        # final state: f[-1][-1]
        # initial state: f[0][0] = 1
        # limits: i/j = 0 -- one dir; left/above is 1 -- one dir
        # transitional: f[i][j] = f[i-1][j]+f[i][j-1]
        if not obstacleGrid or not obstacleGrid[0]:
            return 0
        # inital default state at 0
        f = [[0 for _ in range(len(obstacleGrid[0]))] for _ in range(len(obstacleGrid))]
        for i in range(len(obstacleGrid)):
            for j in range(len(obstacleGrid[0])):
                if obstacleGrid[i][j] == 1: # don't cal for obstacle states - cannot reach, use default
                    continue
                if i == 0 and j == 0:
                    f[i][j] = 1
                if i != 0 and obstacleGrid[i-1][j] != 1:
                    f[i][j] += f[i-1][j]
                if j != 0 and obstacleGrid[i][j-1] != 1:
                    f[i][j] += f[i][j-1]
        return f[-1][-1]
```
#### 221. Maximal Square

Given a 2D binary matrix filled with 0's and 1's, find the largest square containing only 1's and return its area.

```python
class Solution:
    def maximalSquare(self, matrix: List[List[str]]) -> int:
        # f[i][j]: size of square formed from left and up ending at [i][j] - size*size = area
        # initials:
        # f[0][0] = 1 if matrix[0][0] = '1'
        # i == 0 or j == 0: f max at 1
        # transitional: [i][j]能构成square的条件是(自身，左，上，斜上)都是‘1’，且取其各自能构成square中最小的。 且边长+1. -> 画图论证
        # 因为不构成square的f是0，自身为‘1’就构成边长是1的square，可直接取最小。
        # f[i][j] = min(左，上，斜上) + 1
        if not matrix or not matrix[0]:
            return 0
        f = [[0 for _ in range(len(matrix[0]))] for _ in range(len(matrix))]
        f[0][0] = 1 if matrix[0][0] == '1' else 0
        maxsize = 0
        for i in range(len(matrix)):
            for j in range(len(matrix[0])):
                if i == 0 or j == 0:
                    f[i][j] = 1 if matrix[i][j] == '1' else 0
                elif matrix[i][j] == '1':
                    f[i][j] = min(f[i-1][j-1],f[i-1][j],f[i][j-1]) + 1
                else:
                    f[i][j] = 0
                maxsize = max(maxsize,f[i][j])
        return maxsize*maxsize
```
### 不知道类型

#### 91. Decode Ways

A message containing letters from A-Z is being encoded to numbers using the following mapping:

'A' -> 1, 'B' -> 2, ..., 'Z' -> 26

Given a non-empty string containing only digits, determine the total number of ways to decode it.
```python
class Solution:
    def isValid(self,s:str) -> bool:
        if len(s) == 1 and s != '0':
            return True
        if len(s) == 2 and '10' <= s <= '26':
            return True
        return False
        
    def numDecodings(self, s: str) -> int:
        # f[i] = f[i-1]|s[i-1] is valid char + f[i-2]|s[i-2] is valid char
        # initial states: f[0] = 1
        # limits: i == 1: no f[i-2]
        # order of states: f[0] ...f[n]
        if not s:
            return 1
        f = [0 for _ in range(len(s)+1)]
        f[0] = 1
        if self.isValid(s[0]):
            f[1] = f[0]
        for i in range(2,len(s)+1):
            if self.isValid(s[i-1]):
                f[i] += f[i-1] 
            if self.isValid(s[i-2:i]):
                f[i] += f[i-2] 
        return f[-1]
        
```

#### 322. Coin Change

You are given coins of different denominations and a total amount of money amount. Write a function to compute the fewest number of coins that you need to make up that amount. If that amount of money cannot be made up by any combination of the coins, return -1.
```python
class Solution:
    def coinChange(self, coins: List[int], amount: int) -> int:
        # state: f[i] # of coins to make up to amount i
        # initial states, for each coin: f[coin] = 1; f[0] = 0
        ## transitional: f[i] = min(f[i-coin]+1)
        # boundaries: i>coin; if coin>amount, no use of add to state.
        f = [sys.maxsize for _ in range(amount+1)]
        f[0] = 0
        for coin in coins:
            if coin <= amount:
                f[coin] = 1
        for i in range(1,amount+1):
            for coin in coins:
                if i > coin:
                    f[i] = min(f[i],f[i-coin]+1)
        if f[-1] == sys.maxsize:
            return -1
        return f[-1]
```

#### 983. Minimum Cost For Tickets

In a country popular for train travel, you have planned some train travelling one year in advance.  The days of the year that you will travel is given as an array days.  Each day is an integer from 1 to 365. Train tickets are sold in 3 different ways:

costs[1day pass,7 days pass, 30 days pass]

Return the minimum number of dollars you need to travel every day in the given list of days.

Input: days = [1,4,6,7,8,20], costs = [2,7,15]

难点在state计算

```python
class Solution:
    def mincostTickets(self, days: List[int], costs: List[int]) -> int:
        # state: f[i] least cost to travel upto i days.i is idx in days.
        # transitional: f[i] = min(f[day_to_idx(days[i]-passday)]+cost[passday])
        # initial: f[0] = 0
        # boundary: days[i]<passday -> cost[passday]
        
        day_to_idx = {v:i for i,v in enumerate(days)}   # lookup dict for days value to index
        passday_dict = {0:1,1:7,2:30}                   # effective days per pass id
        # state 0, index of days
        f = [sys.maxsize for _ in range(len(days)+1)]
        f[0] = 0                                        # initial state 0: no travel, cost 0
        for i in range(1,len(days)+1):
            for passdays,passcost in enumerate(costs):
                pre = days[i-1]-passday_dict[passdays]  # this day - pass effective day: last prior state to reach w/ this pass
                if pre in day_to_idx:
                    idx = day_to_idx[pre]+1             # if prior day in days list, idx for state is plus 1
                elif pre <= 0:
                    idx = 0                             # if this pass covers through the first day to travel, use f[0] as prior
                else:                                   # Otherwise, find first index in days smaller (bisect -1) -- plus 1 for state
                    idx = bisect.bisect_left(days,days[i-1]-passday_dict[passdays])
                f[i] = min(f[i],f[idx]+passcost)
        return f[-1]
    
```

### Search 类型？

#### 120. Triangle

Given a triangle, find the minimum path sum from top to bottom. Each step you may move to adjacent numbers on the row below.

> 如果顺序想，第i行最优不一定来源于i-1行最优。Bottom up/从目的地出发: 最后一行的minpath是确定的，此后第k行开始的minpath必是其children的minpath之一
```python
class Solution:
    def minimumTotal(self, triangle: List[List[int]]) -> int:
        # bottom up, on row k: state dp[i] rep minpath from end to k+1 row on each idx
        # initial dp[i] = triangle[-1][i]
        # transi: for row k, choose min from 2 children in k+1:
        # dp[i] = min(dp[i],dp[i+1])+triangle[k][i]
        
        dp = [triangle[-1][i] for i in range(len(triangle[-1]))]
        for k in range(len(triangle)-2,-1,-1):
            for i in range(len(triangle[k])):
                dp[i] = min(dp[i],dp[i+1]) + triangle[k][i]
        return dp[0]
```
