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
