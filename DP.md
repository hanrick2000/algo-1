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
