
## Tree Traversal
### 第一类： Maximum / Minimum / Average / Sum / Paths
分治法；dfs；
#### 508. Most Frequent Subtree Sum
or __Minimum Subtree sum__
> 分治。 sum_root = sum_left + sum_right + root.val

#### 257. Binary Tree Paths (Given a binary tree, return all root-to-leaf paths.)
> DFS, 用parameter传递prefix。终止条件if leaf。
#### LCA
236. Lowest Common Ancestor of a Binary Tree
> 分治。recursive search，在左子树找到，在右子树找到，两边都找到
235. Lowest Common Ancestor of a Binary Search Tree
> BST: if min(q,p)<=root.val<=max(q,p) -- LCA is root,否则，都小在左边找，都大在右边
1123. Lowest Common Ancestor of Deepest Leaves
> 反向的. 由leaf向上recursive，记录从下向上的level和leaves的lca。
> 如果left，right的level一样，则lca为root
```python
class Solution:
    def lcaDeepestLeaves(self, root: TreeNode) -> TreeNode:
        def dfs_helper(root): # return lv, lca
            if not root:
                return 0,None
            lv1,lca1 = dfs_helper(root.left)
            lv2,lca2 = dfs_helper(root.right)
            if lv1 > lv2:
                return lv1 + 1, lca1
            if lv1 < lv2:
                return lv2 + 1, lca2
            return lv1+1,root
        return dfs_helper(root)[1]
```
#### 1026. Maximum Difference Between Node and Ancestor
> parameter 带pathmax，pathmin下循环。
> 左右子树分治，取 max(maxl,maxr,abs(pathmax-root.val),abs(pathmin-root.val))
#### 979. Distribute Coins in Binary Tree
> 本质还是divide&conquer。难点在如何抽象coin的移动
```python
def distributeCoins(self, root: TreeNode) -> int:
    self.moves = 0
    def dfs_helper(root):
        if not root:
            return 0
        left_to_parent  = dfs_helper(root.left)
        right_to_parent = dfs_helper(root.right)
        # 记录移动数，上下均为一次移动。注意这里不论方向，都只在父节点记录一次，不存在重复
        self.moves += abs(left_to_parent) + abs(right_to_parent)
        # 向parent移动的coin数，即结余coin数， 负数是反方向
        # 本节点coin + 左右子树挪过来的 - 1（本节点要保留的一个）
        return root.val + left_to_parent + right_to_parent - 1
    dfs_helper(root)
    return self.moves
```
### 第二类： 二叉树结构变化
#### Flattern Binary Tree to Linked List
### 第三类： BST
> 利用性质： in-order traversal mono: non-decreasing; 左右子树的range。
#### 230. Kth Smallest Element in BST 
> inorder traversal的第k个
#### 173. Binary Search Tree Iterator (next smallest number in the BST.)
> BST 性质。最左边的节点最小。利用stack存储一路向左路径上的root。
> 每次pop最后一个，并将其右子树一路向左push to stack
#### 270. Closest Binary Search Tree Value
> recursive
> iterative: search based BST, record path along the way. 找path上差最小的 - return min(path,key = lambda x: abs(target-x))
#### 450. Delete Node in a BST
> insert node? search range?
#### 285. Inorder Successor in BST
> do inorder traversal, and mark and stop at next in order.
or:
```python
def inorderSuccessor(self, root, p):
    if not root: return None
    if root.val>p.val: return self.inorderSuccessor(root.left,p) or root 
    return self.inorderSuccessor(root.right,p)
```
iterative: stack, inorder traversal
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

### 其他类，遍历相关：
### level traversal
#### 987. Vertical Order Traversal of a Binary Tree
> 题目要求了上层在前，同层按大小排列：level traversal, order at each level
> 在队列中加入(node,col)，col 从0 开始，左-1右+1
#### 117. Populating Next Right Pointers in Each Node II
> 按层遍历，要加向右的pointer，需要每层的node先push stack，再从右至左加pointer
#### * 96. Unique Binary Search Trees (DP)
Given n, how many structurally unique BST's (binary search trees) that store values 1 ... n?
> 子问题思路： 以每个数分别为root，则左右子树为子问题
```python
def numTrees(self, n: int) -> int:
    # dp[i] record # of unique bsts w/ val 1...i
    # initials: dp[0] = 1 (no tree), dp[1] = 1 (one tree)
    # trans: dp[i] =sum( dp[j-1]*dp[i-j] |j as root) for all js
    # when j is root, 1-(j-1) form left bst, and (j+1) -- i form right bst.
    # for # of bst, (j+1) -- i is same as 1--(j-i) 每个元素减j

    dp = [0 for _ in range(n+1)]
    dp[0] = dp[1] = 1
    for i in range(2,n+1):
        for j in range(1,i+1):
            dp[i] += dp[j-1]*dp[i-j]
    return dp[-1]
```

### inorder traversal
#### 450. Delete Node in a BST
