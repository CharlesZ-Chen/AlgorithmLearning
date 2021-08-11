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

## Backtracing


## Binary Search


