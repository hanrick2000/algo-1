## DFS/backtracking

#### 51. N-Queens
想清楚在dfs里面和外面进行的部分和dfs的parameter
> 因为queens的特点，可固定一个loop： 一直queens不能在同一行共存，逐行摆放 - 第i个摆在第i-1行 （用列也一样，行可以在程序上省些事）
> - use hashmap to remember 不能再放的row, col, row+col, row-col
> - dfs parameter: k queens to find(putting in ith row), pre processed rows layout
> - dfs 里， 终止条件： k == 0: append to result and return
> - dfs里，寻找下一个，已知row id == n-k，loop col to find valid pos, then dfs on: dfsHelper(k-1, pre + [this], res). remember to backtrack hashmap locations after.

#### 78. Subsets

Given a set of distinct integers, nums, return all possible subsets (the power set).
> For each number in list, option to add/not add.
```python
class Solution:
    def subsets(self, nums: List[int]) -> List[List[int]]:
        self.ans = []
        def dfs_helper(nums,i,pre):
            if i == len(nums):
                self.ans.append(pre)
            else:
                dfs_helper(nums,i+1,pre+[nums[i]])
                dfs_helper(nums,i+1,pre)
        dfs_helper(sorted(nums),0,[])
        return self.ans
```
#### 90. Subsets II

Given a collection of integers that might contain duplicates, nums, return all possible subsets (the power set).
> ? deal w/ dups
```
class Solution:
    def subsetsWithDup(self, nums: List[int]) -> List[List[int]]:
        ans = []
        def dfs(nums,i,pre,ans):
            ans.append(pre)
            for x in range(i,len(nums)):
                if x > i and nums[x] == nums[x-1]:
                    continue
                dfs(nums,x+1,pre+[nums[x]],ans)
        dfs(sorted(nums),0,[],ans)
        return ans
```
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

#### 77. Combinations

Given two integers n and k, return all possible combinations of k numbers out of 1 ... n.
```python
class Solution:
    def combine(self, n: int, k: int) -> List[List[int]]:
        self.res = []
        def dfs_helper(n,i,t,pre):
            if t == 0:
                self.res.append(pre)
            for j in range(i,n+1):
                dfs_helper(n,j+1,t-1,pre+[j])
        dfs_helper(n,1,k,[])
        return self.res
```

#### 47. Permutations II

Given a collection of numbers that might contain duplicates, return all possible unique permutations.

>permutation, different than combinations, next search cannot eliminate idx already used.
>do next dfs on `nums[:i]+nums[i+1:]`: all numbers except current.

>Also, since possible of duplicated numbers, only search for the first one. bypass all others.

```python
class Solution:
    def permuteUnique(self, nums: List[int]) -> List[List[int]]:
        self.res = []
        def dfs_helper(nums,target,pre):
            if target == 0:
                self.res.append(pre)
            elif target < 0:
                return []
            else:
                for i in range(len(nums)):
                    if i>0 and nums[i]==nums[i-1]:
                        continue
                    dfs_helper(nums[:i]+nums[i+1:],target-1,pre+[nums[i]])
        dfs_helper(sorted(nums),len(nums),[])
        return self.res
```
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
### Parentheses
#### 22. Generate Parentheses

Given n pairs of parentheses, write a function to generate all combinations of well-formed parentheses.
> Similar to combination sum, now the target is to use up all left( + right ).
> Count left and right separately
> throw away all invalid combinations by `right<left` -> have used more right than left parentheses
```python
class Solution:
    def generateParenthesis(self, n: int) -> List[str]:
        if n <= 0:
            return []
        self.ans = []
        def dfs_helper(left,right,pre):
            if right<left: ## important: trim all invalid results
                return []
            if left == 0 and right == 0:
                self.ans.append(pre)
            if left:
                dfs_helper(left-1,right,pre+'(')
            if right:
                dfs_helper(left,right-1,pre+')')
        dfs_helper(n,n,"")
        return self.ans
```
