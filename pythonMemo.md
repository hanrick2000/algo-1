## Memo

### 0. Built-ins

#### 0.1 zip(*iterables)

Returns an iterator of tuples, where the i-th tuple contains the i-th element from each of the argument sequences or iterables. The iterator __stops when the shortest input iterable is exhausted__. With a single iterable argument, it returns an iterator of 1-tuples. With no arguments, it returns an empty iterator. 

zip() in conjunction with the * operator can be used to unzip a list:
```
>>> x = [1, 2, 3]
>>> y = [4, 5, 6]
>>> zipped = zip(x, y)
>>> list(zipped)
[(1, 4), (2, 5), (3, 6)]
>>> x2, y2 = zip(*zip(x, y))
>>> x == list(x2) and y == list(y2)
True
```
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

#### 3.1 Counter

class collections.Counter([iterable-or-mapping])

A Counter is a dict subclass for counting hashable objects. It is a collection where elements are stored as dictionary keys and their counts are stored as dictionary values. Counts are allowed to be any integer value including zero or negative counts.
>The Counter class itself is a dictionary subclass with no restrictions on its keys and values. The values are intended to be numbers representing counts, but you could store anything in the value field.The most_common() method requires only that the values be orderable.

Counter objects support three methods beyond those available for all dictionaries:

`elements()`: Return an iterator over elements repeating each as many times as its count. Elements are returned in __arbitrary order__. If an element’s count is less than one, elements() will ignore it. requires integer counts.

`most_common([n])`: Return a list of the n most common elements and their counts from the most common to the least. If n is omitted or None, most_common() returns all elements in the counter. Elements with __equal counts are ordered arbitrarily__.

`subtract([iterable-or-mapping])`: Elements are subtracted from an iterable or from another mapping (or counter). Like dict.update() but subtracts counts instead of replacing them. Both inputs and outputs may be zero or negative.

The usual dictionary methods are available for Counter objects except for: fromkeys (_not implemented_), update([iterable-or-mapping])
(_Elements are counted from an iterable or added-in from another mapping (or counter). Like dict.update() but adds counts instead of replacing them. Also, the iterable is expected to be a sequence of elements, not a sequence of (key, value) pairs._)

### 4. Variable Scope

In python all the variables inside a function are __global__ if they are not assigned any value to them. That means if a variable is __only referenced inside a function, it is global__. However if we __assign any value__ to a variable inside a function, its scope becomes local to that __unless explicitly declared global__.

Explicitly declare scope with `global` or `nonlocal`. Nonlocal binds the variable to nearest enclosing scope except global.

``` python
class Solution:
    def intervalIntersection(self, A: List[List[int]], B: List[List[int]]) -> List[List[int]]:
        events = [] #break down events of interval start and end
        def recode_intervals(A: List[List[int]]) -> None:
            for start,end in A:
                events.append((start, 1))
                events.append((end  , -1))
        
        recode_intervals(A)
        recode_intervals(B)
        events.sort(key = lambda x: (x[0],-x[1])) # sort ascending start time, adding first
        old_counter = 0
        ans = []
        oneinterval = []
        def add_interval():
            nonlocal oneinterval
            if old_counter == 1 and counter == 2:
                # start of intersection
                oneinterval.append(num)
            elif old_counter == 2 and counter == 1:
                # end of intersection
                oneinterval.append(num)
                ans.append(oneinterval)
                oneinterval = []
            
        for num,sign in events:
            counter = old_counter
            if sign == 1:
                counter += 1
                add_interval()
            elif sign == -1:
                counter -= 1
                add_interval()
            old_counter = counter
        return ans
```

So take away is:

1. Use global keyword with a variable if only you are assigning value to that variable inside a function and you want to overwrite the value of variable declared in global scope.

2. Use nonlocal keyword in nested scopes.
