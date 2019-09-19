## DP 例题

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
