
#### 281. Zigzag Iterator
> 用queue，FIFO，滚动next

#### 402. Remove K Digits
> greedy, stack. 
第一遍： if num[i] > num[i+1]: 可删。
第二遍： 剩余的从后向前删除至k

#### 128. Longest Consecutive Sequence
> union find - connect临接的数，问题转成问maxsize of union
另，可用set()转化--O(n)，再loop
