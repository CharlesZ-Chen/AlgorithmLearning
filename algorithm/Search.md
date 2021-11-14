## Graph: vertices V and ordered edges (u, v)

common representations:
 - Adjacency Matrix: 
    2d V*V array
 	adj[i][j] = 1 represents edge (i, j)
 Pros:
   - edge operation is O(1): create/remove/query edge

 Cons:
   - space expensive. O(V^2) space

 - Adjacency List
   Array of lists
   List<Int>[] adj

   	adj[i] represents edges start from vertice i

# Search

- Data Structure Abstraction: Is it a graph? Is it a tree?

- Node definition:

  class Node {
    // state fields interested

    List<Node> getNeighbors/getChild() { // what are the neighbors/child nodes to traverse }
  }

- Search Strategy:
 - DFS/BFS

- Search Optimization / Space Prune:
 - **Memorization**

## DFS

Stack or recursive method.
```java
void dfs(graph) {
  // init visted
  visited;

  // init starting node
  stack.push(node);

  while(!stack.isEmpty()) {
    // search action
    curNode = stack.pop();
    if (visted(curNode)) continue;

     operateOn(curNode);

     for (node : curNode.neighbours) {
        if(!visited(node)) stack.push(node)
     }
  }
}
```

### Backtracking

Essentialy, it is DFS in a recursive fasion.

```java
void backtracking(<global state>, <Search Space>, <Prune Flag>, <Level Flag>) {
  if (Level Flag reach max) {
    return;
  }

  if (Find targeted state) {
    record in global state
    return;
  }

  for (node in Search Space) {
    if (Prune Flag) {
      continue;
    }

    // Prepare next level search
    backtracking(...)
    // Roll back as needed
  }
}
```

- leetcode questions:
  - https://leetcode.com/problems/combination-sum/
  - https://leetcode.com/problems/combination-sum-ii/
  - https://leetcode.com/problems/combination-sum-iii/``

- Apply backtracking in blindfold/interactive env:
  - https://leetcode.com/problems/robot-room-cleaner/

- ** Gotchas **:
 - **Avoid** passing **mutable/shared states** in recursive backtracking function. Backtrack condition is tricky to write correct in this case.

 For example ([word search ii](https://leetcode.com/problems/word-search-ii/)):

 Passing an immutable TrieNode reference to represent current searching string:
 ```
  public void dfs(List<String> result, int row, int col, Trie.TrieNode preNode) {
 ```

 is better than passing & maintaining a StringBuffer to represent current searching string:

 ```
 public void dfs(List<String> result, int row, int col, StringBuffer curStr)
 ```

 As the formmer do not need to worry about backtrack the TrieNode reference, while the latter need to take care of backtrack/reset StringBuffer to a correct state for every function exist point.

## BFS

```java
void bfs(graph) {
  // init visted
  visited;

  // init starting node
  queue.add(node);

  while(!queue.isEmpty()) {
    // search action
    curNode = queue.offer();
    if (visted(curNode)) continue;

     operateOn(curNode);

     for (node : curNode.neighbours) {
        if(!visited(node)) queue.add(node)
     }
  }
}
```

### BFS Visiting Strategy


```java
// [Not Recommended] Visited Marking Strategy A
while(!queue.isEmpty()) {
    cur = queue.poll();
    visited.add(cur);

    for(next : cur.nexts) {
      if(!visited.contains(next)) {
        queue.offer(next);
      }
    }
}


// [Preferred] Visited Marking Strategy B
queue.add(start);
visited.add(start);
while(!queue.isEmpty()) {
  cur = queue.poll();

  for(next : cur.nexts) {
    if(!visited.contains(next)) {
      queue.offer(next);
      visited.add(next);
    }
  }
}
```

B will prune paths at the same level where there is already a path can visit the same visiting state.
Practically, B can search much lesser than A, for a optimal searching problem. (though A & B has the same Big O complexity).


## Binary Search

