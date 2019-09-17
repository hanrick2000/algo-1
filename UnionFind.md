
### 1. Union Find

#### 547. Friend Circles

i,j is friend given in matrix, loop half of matrix to build UF, keep track of # of groups at connect
return groups

#### 737. Sentence Similarity II

list of word pairs --> build UF.
for 2 word list in question, find parent for each and then compare.

#### 1101.The Earliest Moment When Everyone Become Friends

pair of friends + time
 UF, record # of groups and time. update when connect.

#### 1061. Lexicographically Smallest Equivalent String

build UF with A and B, at union, let the smaller char be parent.
for string S, loop and find.

#### (947). Most Stones Removed with Same Row or Column

On a 2D plane, we place stones at some integer coordinate points.  Each coordinate point may have at most one stone.

Now, a move consists of removing a stone that shares a column or row with another stone on the grid.

What is the largest possible number of moves we can make?

 

Example 1:

Input: stones = [[0,0],[0,1],[1,0],[1,2],[2,1],[2,2]]
Output: 5
Example 2:

Input: stones = [[0,0],[0,2],[1,1],[2,0],[2,2]]
Output: 3

> Define connected as stone with same row/col. Q is asking for (# stones - # connected islands)

```python
class Solution:
    def __init__(self):
        self.fathers = {}
        self.counts  = None
    def find(self,x):
        path = []
        while x != self.fathers[x]:
            path.append(x)
            x = self.fathers[x]
        for node in path:
            self.fathers[node] = x
        return x
            
    def union(self,x,y):
        rootx = self.find(x)
        rooty = self.find(y)
        if rootx != rooty:
            self.fathers[rootx] = rooty
            self.counts -= 1
            
    def connect_by_rowcol(self,connect_dict):
        for i, connects in connect_dict.items():
            if len(connects)<2:
                continue
            for node in connects[1:]:
                self.union(node,connects[0])
            
    def removeStones(self, stones: List[List[int]]) -> int:
        if not stones:
            return 0
        self.fathers = {(i,j): (i,j) for i,j in stones}
        self.counts = len(stones)
        cols = collections.defaultdict(list)
        rows = collections.defaultdict(list)
        for x,y in stones:
            rows[x].append((x,y))
            cols[y].append((x,y))
        self.connect_by_rowcol(rows)
        self.connect_by_rowcol(cols)
        return len(stones)-self.counts
```
