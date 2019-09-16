## Memo

### 1. Sort
### 2. heapq
__heapq.heappush(heap, item)__

Push the value item onto the heap, maintaining the heap invariant.

__heapq.heappop(heap)__

Pop and return the smallest item from the heap, maintaining the heap invariant. If the heap is empty, IndexError is raised.
heapq.heappushpop(heap, item)
Push item on the heap, then pop and return the smallest item from the heap. The combined action runs more efficiently than heappush() followed by a separate call to heappop().

__heapq.heapify(x)__

Transform list x into a heap, in-place, in linear time.

__heapq.heapreplace(heap, item)__

Pop and return the smallest item from the heap, and also push the new item. The heap size doesn’t change. If the heap is empty, IndexError is raised. This is more efficient than heappop() followed by heappush(), and can be more appropriate when using a fixed-size heap. Note that the value returned may be larger than item! That constrains reasonable uses of this routine unless written as part of a conditional replacement:

>peek the top of heap: heap[0]
>length of heap: len(heap)

remove from heap?

see if possible to wait and pop

### 3. Collections
