#### Heap

Heap (or priority queue) is a data strcutre that supports insert, remove, and query max/min values.

Essentially a complete balanced binary tree.

It satifised (use max-heap as an example):
- for any node in the tree, its value is smaller than or equal to its parent.


##### Implementation

Use array-representation for a complete balanaced binary tree:

label node from 1 - n.

for a given parent node f[i], its left child node is f[2*i], its right child node is f[2*i + 1]

##### Operations

- Insert: insert an element to the heap. O(log n) as at most traverse hight of a complete balanced binary tree.

Put the element on the bottom of tree (end of array), then `up` it to proper position.

- GetTop: return the top element of heap, without removing it: O(1)

- Extract: remove the top element from heap: swap top element with bottom, remove it, then `down` the swapped bottom to proper position. O(log n)

- Remove: remove an element from heap: swap target element with bottom, remove it, then `up` & `down` the swapped bottom accordingly. O(log n)