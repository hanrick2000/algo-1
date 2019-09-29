
## Tree Traversal

### level traversal
#### 987. Vertical Order Traversal of a Binary Tree
> 题目要求了上层在前，同层按大小排列：level traversal, order at each level
> 在队列中加入(node,col)，col 从0 开始，左-1右+1
#### 117. Populating Next Right Pointers in Each Node II
> 按层遍历，要加向右的pointer，需要每层的node先push stack，再从右至左加pointer

### inorder traversal
#### 285. Inorder Successor in BST
recursive:
```python
def inorderSuccessor(self, root, p):
    if not root: return None
    if root.val>p.val: return self.inorderSuccessor(root.left,p) or root # 在左边或者根
    return self.inorderSuccessor(root.right,p) # 在右边
```
iterative: stack
```python
class Solution:
    def inorderSuccessor(self, root: 'TreeNode', p: 'TreeNode') -> 'TreeNode':
        stack =[]
        found = False
        while root:
            stack.append(root)
            root = root.left
        while stack:
            node = stack.pop()
            if found:
                return node
            if node.val == p.val:
                found = True
            if node.right:
                node = node.right
                while node:
                    stack.append(node)
                    node = node.left
```
#### 450. Delete Node in a BST
