# algo

## Qs in question

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
