## BFS

### 小结

#### 常见使用场景：
1. traversal in Graph
  1.1 level order traversal
  1.2 connected component -- 有时可用union find
  1.3 topo sort
2. shortest path (simple graph)
3. 找所有方案的iterative解

#### 297 Serialize and Deserialize Binary Tree (level traversal, 二叉树)
#### 133 Clone Graph (level traversal, 图）

图上的遍历注意记录visited，避免成环
> clone order： clone all points， then copy edges

#### 127 Word Ladder (implicit graph, shortest path)

#### 200 Number of Islands (BFS in matrix) (同可union find解)

#### lintcode 611 knight shortest path （memo 已经过的点的距离，speed up）

### topo sort

算法描述:
1. 统计每个点的入度 （indegree）
2. 将每个入度为 0 的点放入队列(Queue)中作为起始节点
3. 不断从队列中拿出一个点，去掉这个点的所有连边(指向其他点的边)，其他点的相应的入度 - 1 4. 一旦发现新的入度为 0 的点，丢回队列中

#### 207 Course Schedule (implicit topo sort)
#### 210 Course Schedule II
#### 269 Alien Dictionary
#### 444 Sequence Reconstruction

