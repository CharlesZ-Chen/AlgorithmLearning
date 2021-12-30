#### Idea

- there are 2 state machines in the problem. Use pointers to represent current state of these state machines.

- Define state transition rules for each pointer

- Define the global meaning on intepretating the state each pointer represents.


#### Example Questions (that 2 pointers have more elegant solution that naive simulation)
- https://leetcode.com/problems/interval-list-intersections
  - pointer to maintain current smallest endpoint for each list
  - state transition rule: compare the pointers and update the global smallest

- Naive merge also works with same big O though, just not elegant as 2 pointers in impl.

