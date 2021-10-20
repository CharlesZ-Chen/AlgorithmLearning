### Union Find & Disjoint Set

- data structure: Disjoint Set

- Operations:
  - Find: determine which subset a particular element is in.
  - Union: Join 2 subsets into a single subset.


#### Implementation

Methodology: representive element -- for each sub set S, select a element e, to represents this set.

##### Naive Implementation: array

define int[] f, where f[i] is the representive element e for element i.

Pro & Cons:
 - Find Operation is fast. O(1)
 - Union Operation is slow. O(n): For each merge between S1 & S2, needs at least min(S1, S2) updates in array f.


##### Practical Implementation: Tree Structure

define a forest, where each tree represents a sub set S, with its representive element e is the root.

- Find(x):
```
 if x.parent = x
  return x
 return Find(x.parent)
```

- Union(s1, s2):
```
 s1.parent = s2
```

Pro & Cons:
 - Find Operation is slow with O(n). Becaue trees are highly inbalanced.
 - Union Operation is now easy & fast with O(1)

###### Optimizations

- **path compression**: we don't care about tree shapes as long as it maintains the correct root (representive element).

Therefore, for each Find operation, compression the path from x to root (redirect all elements on the path as a direct child of root):

- Optimized Find(x):
```
if x.parent = x
  return x

x.parent = Find(x.parent)

return x.parent
```

This will get an armotized O(log n) for find operation.


- **merge by rank**:
General principle behind this optimization: we can huristically merge 2 data structures by always merge the "smaller" one to the "larger" one, so that only the query cost on "smaller" one is increased. This will reach an armotized O(n * log n) complexity for merging all the data structure.

Specifically to Disjoint set, lets define rank of the tree as "size of its element" (or height without path compression). When merge S1 and S2, we can always huristically merge the subset with smaller rank to the other set with larger rank.

- with above 2 optimizations, Find(x) can reach a O(alpha(n)) complexity, where alpha is a function grows slower than log n, also approximatly near a constant for a very huge n (n < 2^10^19729).

Usually with **path compression** is a good enough performance for Union Find.

- Implementation simplification: Array representation

Combine Naive implementaion with path compression, we can get a faster enough DSU without complicated tree object stuffs (which takes more time to code):

- define int[] f, where f[i] is the representive element e for element i.


```
Find(x):
  if f[x] == x: return x
  return f[x] = Find(f[x])

Union(s1, s2):
  f[s1] = s2
```