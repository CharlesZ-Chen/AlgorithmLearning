### Sorting Properties

- Stablility: if elements with equal value can kept their relative order in original input, after sorting

- base sort, counting sort, bubble sort, merge sort are **stable sortings**

- selection sort, heap sort, quick sort are **not** stable sortings
 - Q: Why they are not stable? 

 - A: principally, depends on implementation. For implementations based on in-place swap, they are not stable.
  This is because stability maintained by only swap direct neighbours. If swap occurs beyond neighbours, stability won't maintained.

 E.g. consider below example for selection sort:

 `5_1, 6, 5_2, 2, 8`

- on 1st swap: `2, 6, 5_2, 5_1, 8`, relative order of original 5s is broken by this swap.

### The Basic Sorts

#### TODO: Selection Sort

#### TODO: Bubble Sort

#### TODO: Insertion Sort

#### QuickSort (partition-exchange sort)

Idea: Divide and conquer.

Steps:

1. partition array to 2 parts, with relative order maintained (e.g. for all elements in part A < for all elements in part B)
2. apply quickSort to 2 parts recursively.
3. Once recursion end, array get sorted.

##### Optimizations

- mid = avg(first, mid, last): prevent degrade on ordered input
- apply insert sort for short input
- for each partition sort, for all elements E.val = mid, gathering them around mid: prevent degrade on input with a lot equal elements

###### Practical Implementations

- 3-way Radix Quicksort (introde a new partition: for all elements in new P has e.val == mid)

- Introsort: combine quicksort with heap sort -> limit max-recursive depths for QS as log n -> convert sort to heap sort once exceed this limit.

##### Applications

- Find kth element in armotized O(n) time

Idea: 

1. for each round of quicksort, we have 2 partitions P1 < P2.
2. We can then apply binary search via checking size of each partition: 
 2.1 if size(P1) >= k: recursively search in P1 only
 2.2 if size(P1) < k: recursively search (k - P1.size - 1)th element in P2

#### TODO: Counting Sort

#### TODO: Radix Sort

#### TODO: Heapsort

#### TODO: Bucket Sort

#### TODO: Shell Sort