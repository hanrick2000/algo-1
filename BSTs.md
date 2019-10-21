## Binary Search Tree

注意应用BST的特性解题

### 基础题型

#### 98. Validate Binary Search Tree

Given a binary tree, determine if it is a valid binary search tree (BST).
 根据定义：
 - left < root < right
 - both left and right is BST
 在root的上下界限为正负无穷，其left： lower - root.val, right: root.val - higher
 
 recursive solution
 
```python 
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:
    def isValidBST(self, root: TreeNode, lower_thread = float('-inf'), higher_thread = float('inf')) -> bool:
        if not root:
            return True
        if not root.left:
            left = True
        else:
            left = (lower_thread < root.left.val < root.val) and \
            self.isValidBST(root.left,lower_thread,root.val)
        if not root.right:
            right = True
        else:
            right = (root.val < root.right.val < higher_thread) and \
            self.isValidBST(root.right, root.val, higher_thread)
        return left and right
```

#### 1008. Construct Binary Search Tree from Preorder Traversal

Return the root node of a binary search tree that matches the given preorder traversal.
```
preorder traversal of BST: 
 first item: root
 find first idx in list > root.val
   idx found:
   root.left  = reconstruct(list[1:idx])
   root.right = reconstruct(list[idx:])
   idx not found -- no right tree:
   root.left = reconstruct(list[1:])
   root.right = None
   
```
延伸题型：

__108. Convert Sorted Array to Binary Search Tree （same as construct BST from inorder traversal)__

Get mid = len(array)//2. root = Treenode(mid), root.left = construct(array[:mid]), root.right = construct(array[mid+1:])




#### 530. Minimum Absolute Difference in BST [easy]

Given a binary search tree with non-negative values, find the minimum absolute difference between values of any two nodes.

特性：inorder traversal of BST is sorted. compare pairwise then.

__self.prev = root.val__: get smallest from right tree.__why?__
```python
class Solution:
    def getMinimumDifference(self, root: TreeNode) -> int:
        self.mindiff = sys.maxsize
        self.prev =  sys.maxsize
        def inorder_traversal(root: TreeNode) -> None:
            if not root:
                return 
            inorder_traversal(root.left)
            print(self.prev,root.val)
            self.mindiff = min(self.mindiff, abs(root.val - self.prev))
            self.prev = root.val
            inorder_traversal(root.right)
            
        inorder_traversal(root)
        return self.mindiff
```
类似题型：

__230. Kth Smallest Element in a BST__

利用BST inorder is sorted, inorder traversal and record counter to k. 

注意counter的位置。最小值的left是空的，从root记。
```
    self.inorder_helper(root.left,k)
    self.i += 1
    if self.i == k:
        self.ans = root.val
    self.inorder_helper(root.right,k)
```



#### 701. Insert into a Binary Search Tree

Given the root node of a binary search tree (BST) and a value to be inserted into the tree, insert the value into the BST. Return the root node of the BST after the insertion. It is guaranteed that the new value does not exist in the original BST.

Note that there may exist multiple valid ways for the insertion, as long as the tree remains a BST after insertion. You can return any of them.

只需要插入， DFS找到位置插入。
```python
class Solution:
    def insertIntoBST(self, root: TreeNode, val: int) -> TreeNode:
        def dfs_helper(root: TreeNode,val: int) -> None:
            if val > root.val:
                if not root.right:
                    root.right = TreeNode(val)
                else:
                    self.insertIntoBST(root.right,val)
            elif val < root.val:
                if not root.left:
                    root.left = TreeNode(val)
                else:
                    self.insertIntoBST(root.left,val)
        dfs_helper(root,val)
        return root
```


#### 450. Delete Node in a BST :star:

Given a root node reference of a BST and a key, delete the node with the given key in the BST. Return the root node reference (possibly updated) of the BST.

Basically, the deletion can be divided into two stages:

Search for a node to remove.
If the node is found, delete the node.
Note: Time complexity should be O(height of tree).

```python
class Solution:
    def deleteNode(self, root: TreeNode, key: int) -> TreeNode:
        if not root:
            return root
        if key > root.val:
            root.right = self.deleteNode(root.right,key)
        elif key < root.val:
            root.left  = self.deleteNode(root.left ,key)
        else:
            # key == root.val
            if not root.right:
                return root.left
            else:
                # find smallest from right subtree to put on root
                temp = root.right
                while temp.left:
                    temp = temp.left
                    
                root.val = temp.val
                root.right = self.deleteNode(root.right,temp.val)
        return root
```

#### 173. Binary Search Tree Iterator

Implement an iterator over a binary search tree (BST). Your iterator will be initialized with the root node of a BST.

Calling next() will return the next smallest number in the BST.

BST特点，沿着左子树一直向左是最小值，将经过的节点加到stack里。
另，因为all of 左子树<root， 每pop一个node，此node右子树中最小值将是全树最小。且此node右子树中的所以节点均 < stack top
故，在next()总将这个右子树的所以左节点依次加入stack
```python
class BSTIterator:
    def __init__(self, root: TreeNode):
        self.stack = []
        while root:
            self.stack.append(root)
            root = root.left

    def next(self) -> int:
        #return the next smallest number
        node = self.stack.pop()
        s = node.val
        # add candidates for next from right tree
        node = node.right
        while node:
            self.stack.append(node)
            node = node.left
        return s

    def hasNext(self) -> bool:
        #return whether we have a next smallest number
        return len(self.stack)>0
```


### 应用题型

#### 729. My Calendar I

Implement a MyCalendar class to store your events. A new event can be added if adding the event will not cause a double booking.

Your class will have the method, book(int start, int end). Formally, this represents a booking on the half open interval [start, end), the range of real numbers x such that start <= x < end.

因有重复插入的需求，关键在提高查找插入的效率，用二叉树存储(start,end) that's already booked. 因无double book, s/e无重叠.
no rebalancing, thus at worst case, O(N)
```python
class TreeNode:
    def __init__(self,start,end):
        self.s = start
        self.e = end
        self.left = None
        self.right = None
class MyCalendar:

    def __init__(self):
        self.root = None
    def book_dfs(self,start: int, end: int, root: TreeNode):
        if end <= root.s:
            if not root.left:
                root.left = TreeNode(start,end)
                return True
            return self.book_dfs(start,end,root.left)
        elif start >= root.e:
            if not root.right:
                root.right = TreeNode(start,end)
                return True
            return self.book_dfs(start,end,root.right)
        else:
            return False
        
    def book(self, start: int, end: int) -> bool:
        if not self.root:
            self.root = TreeNode(start,end)
            return True
        return self.book_dfs(start,end,self.root)
```
另，用 bisect library:
```python
class MyCalendar:
    def __init__(self):
        self.ints = []        
    def book(self, st: 'int', end: 'int') -> 'bool':
        idx = bisect_left(self.ints, (st, end))
        is_left_valid = idx == 0 or self.ints[idx-1][1] <= st
        is_right_valid = idx == len(self.ints) or end <= self.ints[idx][0]
        
        if is_left_valid and is_right_valid:
            self.ints.insert(idx, (st, end))
            return True
        return False
```
#### 932. My Calendar III
插入无限个event， 查询当前时间段book次数
> 区间多次查询，用segment tree
题解示例：time complexity is O(log(n)) - 树的高度 
```python

class MyCalendarThree(object):
'''
Share a short python segment tree implementation. Lazy propagation is implemented to achieve O(1) time complexity.
The usage of ID is similar to the index of array element in a heap -- 2ID and 2ID+1 are the indices of the two kids.
ex. 1 is the root, 2, 3 are the left and right kids of root, so on so forth.
'''
    def __init__(self):
        self.seg = collections.defaultdict(int)
        self.lazy = collections.defaultdict(int)
        
    def book(self, start, end):
        def update(s, e, l = 0, r = 10**9, ID = 1):
            if r <= s or e <= l: return 
            if s <= l < r <= e:
                self.seg[ID] += 1
                self.lazy[ID] += 1
            else:
                m = (l + r) // 2
                update(s, e, l, m, 2 * ID)
                update(s, e, m, r, 2*ID+1)
                self.seg[ID] = self.lazy[ID] + max(self.seg[2*ID], self.seg[2*ID+1])
        update(start, end)
        return self.seg[1] + self.lazy[1]
```

#### 另
#### 855. Exam Room

In an exam room, there are N seats in a single row, numbered 0, 1, 2, ..., N-1.

When a student enters the room, they must sit in the seat that maximizes the distance to the closest person.  If there are multiple such seats, they sit in the seat with the lowest number.  (Also, if no one is in the room, then the student sits at seat number 0.)

使用bisect插入 `bisect.insort(self.occupied_seats,idx)`
简单直白loop相邻两座位，更新距离。

注意用法： 

- `for i,j in zip(list1,list2)`
- loop 前 `curr_d = self.occupied_seats[0]` -- if idx==0 not occupied, initialize dist = dist from 0 to first seated.

```python
# Your ExamRoom object will be instantiated and called as such:
# obj = ExamRoom(N)
# param_1 = obj.seat()
# obj.leave(p)
class ExamRoom:
    def __init__(self, N: int):
        self.N, self.occupied_seats = N, []

    def seat(self) -> int:
        if not self.occupied_seats:
            self.occupied_seats.append(0)
            return 0
        curr_d, idx = self.occupied_seats[0],0
        for s1,s2 in zip(self.occupied_seats,self.occupied_seats[1:]):
            if (s2 - s1)//2>curr_d:
                curr_d, idx = (s2-s1)//2,(s1+s2)//2
        if self.N - 1 - self.occupied_seats[-1] > curr_d: idx = self.N-1
        bisect.insort(self.occupied_seats,idx)
        return idx

    def leave(self, p: int) -> None:
        self.occupied_seats.remove(p)
```
