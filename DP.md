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
### Interval 区间型
#### 312. Burst Balloons

Given n balloons, indexed from 0 to n-1. Each balloon is painted with a number on it represented by array nums. You are asked to burst all the balloons. If the you burst balloon i you will get nums[left] * nums[i] * nums[right] coins. Here left and right are adjacent indices of i. After the burst, the left and right then becomes adjacent.

Find the maximum coins you can collect by bursting the balloons wisely.
> 重构问题，倒着想： 最后一个气球k戳破时，左边i-k，右边k-j是两个子问题

> 区间型DP，注意loop顺序： 区间长度增长方向。 格外注意loop边界

> t: O(n^3),sp O(n^2)

> time complexity: O(状态数*每个计算时间)
```python
class Solution:
    def maxCoins(self, nums: List[int]) -> int:
        # state: f[i][j]: max coins to collect for balloons b/t i to j, exclusive
        # initial: f[i][i+1] = 0, no balloons to burst
        # trans: for k in (i,j), f[i][j] = max(f[i][k] + nums[i]*nums[k]*nums[j] + f[k][j])
        # order: len of (i-j) increase
        
        # add ones to both sides.
        a = [1] + nums + [1]
        f = [[0 for _ in range(len(a))] for _ in range(len(a))]
        for l in range(1,len(a)):
            for i in range(len(a)-l):
                j = i + l
                if j - i == 1:
                    continue             # 相邻边界，state已在init时设为0
                for k in range(i+1,j):
                    f[i][j] = max(f[i][j],f[i][k] + a[i]*a[k]*a[j] + f[k][j])
        return f[0][-1]
```

#### 486. Predict the Winner (两头取石子题)

Given an array of scores that are non-negative integers. Player 1 picks one of the numbers from either end of the array followed by the player 2 and then player 1 and so on. Each time a player picks a number, that number will not be available for the next player. This continues until all the scores have been chosen. The player with the maximum score wins.

Given an array of scores, predict whether player 1 is the winner. You can assume each player plays to maximize his score.
> 两头取 -- final state不唯一。变换思考角度， 区间
> 区间型iter顺序： 区间大小从小到大
```python
class Solution:
    def PredictTheWinner(self, nums: List[int]) -> bool:
        # state: Sx is from this point forward, sum_p1 - sum_p2
        # f[i][j]: Sx facing nums[i...j] inclusive
        # cal from small len to large. j-i
        # initial f[i][i] = nums[i]
        # transitional: 
        # f[i][j] = max(nums[i]-f[i+1][j],nums[j]-f[i][j-1])
        # final state >= 0 win
        
        # get state array nxn
        n = len(nums)
        f = [[None for _ in range(n)] for _ in range(n)]
        # initial states, l == 0:
        for i in range(n):
            f[i][i] = nums[i]
        for l in range(1,n):
        # len from 1- n, len == 0 already proccessed in init state above
        # len == 石子数 - 1
            for i in range(n-l):
                j = l + i
                f[i][j] = max(nums[i] - f[i+1][j], nums[j] - f[i][j-1])
        return f[0][-1]>=0
```
### 双序列型
#### 72. Edit Distance
Given two words word1 and word2, find the minimum number of operations required to convert word1 to word2.

You have the following 3 operations permitted on a word:

Insert a character
Delete a character
Replace a character
> 双序列： 涉及比较or操作两个序列 -- mxn的状态数组。可优化 -- 如只与i-1，j-1相关。

> 从结尾开始思考： 本题对最后一位进行判断，从而分出子问题。if match，如何，else，三种操作对应比较的字符串状态。

> 需要注意：开多大state array，初始状态怎么定义。 具体来说，状态0 对应空串，这方便定义初始状态。同时对f[i][j]比较的是word1[i-1]和word2[j-1]
```python
class Solution:
    def minDistance(self, word1: str, word2: str) -> int:
        # state f[i][j] w1 to i-1-th, and w2 to j-1-th min edit distance
        # i or j == 0 means empty str from that word
        # initial states f[0][j] = j, f[i][0] = i
        # f[i][j] = if w1[i-1] == w2[j-1], f[i-1][j-1]
        #           else, 1+ min(f[i-1][j],f[i][j-1],f[i-1][j-1])
        
        m,n = len(word1),len(word2)
        f = [[None for _ in range(n+1)] for _ in range(m+1)]
        
        for i in range(m+1):
            for j in range(n+1):
                if i == 0:
                    f[i][j] = j
                elif j == 0:
                    f[i][j] = i
                else:
                    if word1[i-1] == word2[j-1]:
                        f[i][j] = f[i-1][j-1]
                        continue
                    f[i][j] = 1+ min(f[i-1][j],f[i][j-1],f[i-1][j-1])
        return f[-1][-1]
```
#### similar variation. 1035. Uncrossed Lines
> 将数组看作字符串，连接则为A或B中进行删除操作。求最多matching。 转移方程的区别是相等时+1。

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
#### 264. Ugly Number II (用pointer辅助transition func）

Write a program to find the n-th ugly number.

Ugly numbers are positive numbers whose prime factors only include 2, 3, 5. 
```python
class Solution:
    def nthUglyNumber(self, n: int) -> int:
        # state: u[i] i+1-th ugly number
        # initial state: u[0] = 0
        # next u[i] must be some u[x] * 2,3 or 5
        # key point is what's this x?
        # use 3 pointers to track x. x start from 0 - the smallest
        # each u[x] can be used 3 times - w/ 2,3 or 5. after that, it's another u[y]
        # trasitional: u[i] = min(u[p1]*2,u[p2]*3,u[p3]*5)
        
        u = [0 for _ in range(n)]
        u[0] = 1
        p2,p3,p5 = 0,0,0
        for i in range(1,n):
            n2,n3,n5 = u[p2]*2, u[p3]*3, u[p5]*5
            u[i] = min(n2,n3,n5)
            if n2 == u[i]:
                p2+=1
            if n3 == u[i]:
                p3+=1
            if n5 == u[i]:
                p5+=1
        return u[-1]
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
