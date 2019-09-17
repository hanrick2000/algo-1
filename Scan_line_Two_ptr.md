#### 253. Meeting Rooms II
Given an array of meeting time intervals consisting of start and end times [[s1,e1],[s2,e2],...] (si < ei), find the minimum number of conference rooms required.

Example 1:

Input: [[0, 30],[5, 10],[15, 20]]
Output: 2

思路 1: 扫描线
```
拆分区间为 [(start,1),(end,-1),..]
sort
loop through and record count max
```
```python
class Solution:
    def minMeetingRooms(self, intervals: List[List[int]]) -> int:
        events = []
        for start,end in intervals:
            events.append((start, 1))
            events.append((end  ,-1))
        events.sort()
        # print(events)
        
        c = maxc = 0
        for event,sign in events:
            if sign == 1:
                c += 1
            elif sign == -1:
                c -= 1
            maxc = max(maxc,c)
            
        return maxc
```

思路 2: heap
```
sort: 按照开始时间排序
min heap: maintain end time of each occupied meeting room:
loop over sorted list of intervals:
    if start time later than heap top: update;
    else: add end time to heap
return len(heap)
```

```python 
class Solution:
    def minMeetingRooms(self, intervals: List[List[int]]) -> int:
        intervals.sort(key = lambda x: x[0]) # sort by start time
        # maintain minheap for end time of each occupied room
        minheap = []
        for start,end in intervals:
            if minheap and minheap[0]<= start:
                # if exists meeting room that ends before this one starts, 
                # has to be min val in heap, update end time
                heapq.heapreplace(minheap,end)
            else:
                #otherwise add new room
                heapq.heappush(minheap,end)
                
        return len(minheap)
```

延伸： 986. Interval List Intersections

Given two lists of closed intervals, each list of intervals is pairwise disjoint and in sorted order.

Return the intersection of these two interval lists.

延续扫描线思路， 先拆分区间，排列（依题意按start time升序，开始优先于结束 -- closed intervals）
loop over and count， 每当count 1->2, add intersect start; count 2->1, and intersect end.