## Memo

### 1. Sort

__sorted(a) vs. a.sort()__

list.sort() method is only defined for lists. In contrast, the sorted() function accepts any iterable.

list.sort() method modifies the list in-place (and returns None to avoid confusion). Usually it’s less convenient than sorted() - but if you don’t need the original list, it’s slightly more efficient.

__Key Functions__

Both list.sort() and sorted() have a key parameter 
```
sorted(student_objects, key=lambda student: student.age)

sorted(student_tuples, key=itemgetter(2), reverse=True)
```
__Other Notes__

__The sort routines are guaranteed to use `__lt__()` when making comparisons between two objects.__ So, it is easy to add a standard sort order to a class by defining an `__lt__()` method:
```
>>>
>>> Student.__lt__ = lambda self, other: self.age < other.age
>>> sorted(student_objects)
[('dave', 'B', 10), ('jane', 'B', 12), ('john', 'A', 15)]
```

Key functions need not depend directly on the objects being sorted. A key function can also access external resources. For instance, if the student grades are stored in a dictionary, they can be used to sort a separate list of student names:
```
>>>
>>> students = ['dave', 'john', 'jane']
>>> newgrades = {'john': 'F', 'jane':'A', 'dave': 'C'}
>>> sorted(students, key=newgrades.__getitem__)
['jane', 'dave', 'john']
```


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
