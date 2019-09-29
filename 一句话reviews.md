
#### 281. Zigzag Iterator
> 用queue，FIFO，滚动next

#### 402. Remove K Digits
> greedy, stack. 
第一遍： if num[i] > num[i+1]: 可删。
第二遍： 剩余的从后向前删除至k

#### 128. Longest Consecutive Sequence
> union find - connect临接的数，问题转成问maxsize of union
另，可用set()转化--O(n)，再loop

### BFS
#### 310. Minimum Height Trees
> leaves: one degree -- only one neighbors
> BFS while remaining == 2: 由边缘到中心。最后剩1-2个可作root

### Stack
#### 503. Next Greater Element II
> 用stack暂存，遇到next great，pop一串儿
> 解决cycle问题： loop 两次！！
