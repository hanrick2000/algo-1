## DFS/backtracking
### Combination sums
#### 39. Combination Sum

Given a set of candidate numbers (candidates) (without duplicates) and a target number (target), find all unique combinations in candidates where the candidate numbers sums to target.

The same repeated number may be chosen from candidates unlimited number of times.

```python 
class Solution:
    def combinationSum(self, candidates: List[int], target: int) -> List[List[int]]:
        self.res = []
        candidates.sort()
        def dfs_helper(candidates,target,pre = []):
            for i in range(len(candidates)):
                nt = target - candidates[i]
                if nt<0:
                    return []
                if nt == 0
                    self.res.append(pre+[candidates[i]])
                if nt > 0:
                    ### include current char in dfs -- repeat use
                    dfs_helper(candidates[i:],nt,pre+[candidates[i]])
        dfs_helper(candidates,target)
        return self.res
```

#### 40. Combination Sum II

Given a collection of candidate numbers (candidates) and a target number (target), find all unique combinations in candidates where the candidate numbers sums to target.

Each number in candidates may __only be used once__ in the combination.

```python
class Solution:
    def combinationSum2(self, candidates: List[int], target: int) -> List[List[int]]:
        self.res = []
        def dfs_helper(can: List[int],target: int,pre: List[List[int]]):
            for i in range(len(can)):
                ### For duplicated candidates, bypass second one 
                if i > 0 and can[i]==can[i-1]:
                    continue
                nextt = target - can[i]
                if nextt < 0:
                    return []
                if nextt == 0:
                    self.res.append( pre + [can[i]])
                if nextt > 0:
                    dfs_helper(can[i+1:],nextt,pre+[can[i]]) #dfs, next char
        ## has to sort candidates before dfs
        dfs_helper(sorted(candidates),target,[])
        return self.res
```
#### 216. Combination Sum III

Add len of ans requirement. Pass to dfs.注意终止条件。
#### 377. Combination Sum IV (Permutation sum)

Note that different sequences are counted as different combinations.

`dfs_helper(candidates,nt,pre+[candidates[i]])` include full list instead of partial list. However, DFS solution only works on small targets. Too slow for deeper stacks. Use DP.


#### (131). Palindrome Partitioning

Given a string s, partition s such that every substring of the partition is a palindrome.

Return all possible palindrome partitioning of s.

Example:

Input: "aab"
Output:
[
  ["aa","b"],
  ["a","a","b"]
]
```python
class Solution(object):
    def partition(self, s):
        """
        :type s: str
        :rtype: List[List[str]]
        """
        out = []
        def isPalindrome(st):
            return st == st[::-1]
        
        def dfs(curr=[], index=1):
            if index == len(s) + 1:
                out.append(list(curr))
                return
            for i in range(index, len(s)+1):
                if isPalindrome(s[index-1:i]):
                    curr.append(s[index-1:i])
                    dfs(curr, i+1)
                    curr.pop()
        dfs()
        return out
```
