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
#### 331. Verify Preorder Serialization of a Binary Tree

One way to serialize a binary tree is to use pre-order traversal. When we encounter a non-null node, we record the node's value. If it is a null node, we record using a sentinel value such as #.
Verify if valid without reconstruct tree.
https://leetcode.com/problems/verify-preorder-serialization-of-a-binary-tree/

用容量判断！！

We just need to remember how many empty slots we have during the process.

Initially we have one ( for the root ).

for each node we check if we still have empty slots to put it in.

a null node occupies one slot.
a non-null node occupies one slot before he creates two more. the net gain is one.
```python
class Solution(object):
    def isValidSerialization(self, preorder):
        """
        :type preorder: str
        :rtype: bool
        """
        # remember how many empty slots we have
        # non-null nodes occupy one slot but create two new slots
        # null nodes occupy one slot
        
        p = preorder.split(',')
        
        #initially we have one empty slot to put the root in it
        slot = 1
        for node in p:
            
            # no empty slot to put the current node
            if slot == 0:
                return False
                
            # a null node?
            if node == '#':
                # ocuppy slot
                slot -= 1
            else:
                # create new slot
                slot += 1
        
        #we don't allow empty slots at the end
        return slot==0
```
