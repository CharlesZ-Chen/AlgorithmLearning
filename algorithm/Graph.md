### Classic Graph Problems

#### Directed Graph

- Check circles in a graph
  - topological sort
  - formal definition: In computer science, a topological sort or topological ordering of a directed graph is a linear ordering of its vertices such that for every directed edge uv from vertex u to vertex v, u comes before v in the ordering.
  - wiki: https://en.wikipedia.org/wiki/Topological_sorting
  ```
  // calculate inbound degree for each node in a given directed graph

  // build an initial empty topologic array A

  // for each node with 0 inbound degree, add to a queue

  // for each node in the queue:
     // remove it from the queue
     // for each node in its adj list, reduce their inbound degree by 1
     // if an adj node reached a 0 inbound degree, add it to the queue
     // add this node to the topologic array A

  // compare A with nodes N: if A does not cover all nodes in N, it means there are circles in the graph
  // as those nodes in circles won't be able to have a topologic sort
  
  ```
  - example question: https://leetcode.com/problems/course-schedule/

  - extension: find shortest path in DAG: https://www.ics.uci.edu/~eppstein/161/960208.html
- Calculate basic inbound & outbound degrees

#### Undirected Graph

- Check "strongly connected components" in a graph
 - tarjan's algorithm (check end depth for each dfs path)

### Graph Formulation (How to modeling graphs)

- https://leetcode.com/problems/regions-cut-by-slashes/
