#### 238. Product of Array Except Self

two pass: 从小到大， 从大到小

- 第一遍：从1开始，加入[], 并update p = n[0]*n[1]...*n[i]
- 第二遍：从最后，加入[]，并update q = n[len(n)-1]*n[len(n)-2]...*n[i]
- 注意，两次都是先加入，在update，可保证不包含当前数

```python
class Solution:
    def productExceptSelf(self, nums: List[int]) -> List[int]:
        p = 1
        res = []
        for i in nums:
            res.append(p)
            p *= i
        q = 1
        for i in range(len(nums)-1,-1,-1):
            res[i] *= q
            q *= nums[i]
        return res
```
