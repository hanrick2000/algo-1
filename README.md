# algo

## Qs in question

> #### 399. Evaluate Division

### Two pointers/sliding window

#### 1. (560). Subarray Sum Equals K

Given an array of integers and an integer k, you need to find the total number of continuous subarrays whose sum equals to k.

Example 1:
Input:nums = [1,1,1], k = 2
Output: 2
```python
class Solution:  
# Let's remember count[V], the number of previous prefix sums with value V. If our newest prefix sum has value W, and W-V == K, then we add count[V] to our answer.

# This is because at time t, A[0] + A[1] + ... + A[t-1] = W, and there are count[V] indices j with j < t-1 and A[0] + A[1] + ... + A[j] = V. Thus, there are count[V] subarrays A[j+1] + A[j+2] + ... + A[t-1] = K.

    def subarraySum(self, A, K):
        count = collections.Counter()
        count[0] = 1
        ans = su = 0
        for x in A:
            su += x
            ans += count[su-K]
            count[su] += 1
        return ans
```
#### (287). Find the Duplicate Number
Given an array nums containing n + 1 integers where each integer is between 1 and n (inclusive), prove that at least one duplicate number must exist. Assume that there is only one duplicate number, find the duplicate one.

Example 1:

Input: [1,3,4,2,2]
Output: 2
Example 2:

Input: [3,1,3,4,2]
Output: 3

>This solution is based on binary search.

>At first the search space is numbers between 1 to n. Each time I select a number mid (which is the one in the middle) and count all the numbers equal to or less than mid. Then if the count is more than mid, the search space will be [1 mid] otherwise [mid+1 n]. I do this until search space is only one number.

>Let's say n=10 and I select mid=5. Then I count all the numbers in the array which are less than equal mid. If the there are more than 5 numbers that are less than 5, then by Pigeonhole Principle (https://en.wikipedia.org/wiki/Pigeonhole_principle) one of them has occurred more than once. So I shrink the search space from [1 10] to [1 5]. Otherwise the duplicate number is in the second half so for the next step the search space would be [6 10].
```python
class Solution(object):
    def findDuplicate(self, nums):
        """
        :type nums: List[int]
        :rtype: int
        """
        low = 1
        high = len(nums)-1
        
        while low < high:
            mid = low+(high-low)/2
            count = 0
            for i in nums:
                if i <= mid:
                    count+=1
            if count <= mid:
                low = mid+1
            else:
                high = mid
        return low
```

#### (424). Longest Repeating Character Replacement

Given a string s that consists of only uppercase English letters, you can perform at most k operations on that string.

In one operation, you can choose any character of the string and change it to any other uppercase English character.

Find the length of the longest sub-string containing all repeating letters you can get after performing the above operations.

Note:
Both the string's length and k will not exceed 104.

Example 1:

Input:
s = "ABAB", k = 2

Output:
4

Explanation:
Replace the two 'A's with two 'B's or vice versa.

>Re: [Sliding window](similar to finding longest substring with k distinct characters)

>Similar idea in Python but allowing any character, not just uppercase English letters [Updated based on comments below]:
```python
def characterReplacement(self, s, k):
    res = lo = hi = 0
    counts = collections.Counter()
    for hi in range(1, len(s)+1):
        counts[s[hi-1]] += 1
        max_char_n = counts.most_common(1)[0][1]
        if hi - lo - max_char_n > k:
            counts[s[lo]] -= 1
            lo += 1
    return hi - lo
```
[Original code in order to understand comment from @StefanPochmann was:]
```python
def characterReplacement(self, s, k):
    res = lo = 0
    counts = collections.Counter()
    for hi in range(len(s)):
        counts[s[hi]] += 1
        max_char_n = counts.most_common(1)[0][1]
        while (hi - lo - max_char_n + 1 > k):
            counts[s[lo]] -= 1
            lo += 1
        res = max(res, hi - lo + 1)
    return res
```
## Qs to review

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
### 2. Trie

#### 	Longest Word in Dictionary

### 3. DFS/backtracking

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

### X. Others

#### 253. Meeting Rooms II
Given an array of meeting time intervals consisting of start and end times [[s1,e1],[s2,e2],...] (si < ei), find the minimum number of conference rooms required.

Example 1:

Input: [[0, 30],[5, 10],[15, 20]]
Output: 2

思路 1: 扫描线
```
拆分区间为 [(start,1),(end,-1),..]
sort
loop through and record count max
```
```python
class Solution:
    def minMeetingRooms(self, intervals: List[List[int]]) -> int:
        events = []
        for start,end in intervals:
            events.append((start, 1))
            events.append((end  ,-1))
        events.sort()
        # print(events)
        
        c = maxc = 0
        for event,sign in events:
            if sign == 1:
                c += 1
            elif sign == -1:
                c -= 1
            maxc = max(maxc,c)
            
        return maxc
```

思路 2: heap
```
sort: 按照开始时间排序
min heap: maintain end time of each occupied meeting room:
loop over sorted list of intervals:
    if start time later than heap top: update;
    else: add end time to heap
return len(heap)
```

```python 
class Solution:
    def minMeetingRooms(self, intervals: List[List[int]]) -> int:
        intervals.sort(key = lambda x: x[0]) # sort by start time
        # maintain minheap for end time of each occupied room
        minheap = []
        for start,end in intervals:
            if minheap and minheap[0]<= start:
                # if exists meeting room that ends before this one starts, 
                # has to be min val in heap, update end time
                heapq.heapreplace(minheap,end)
            else:
                #otherwise add new room
                heapq.heappush(minheap,end)
                
        return len(minheap)
```
