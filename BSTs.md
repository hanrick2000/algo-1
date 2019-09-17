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

