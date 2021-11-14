#### Background

- Data partitioning: distributing data across a set of servers

- Data replication: making multiple copies of data and storing them in different servers

#### Data Partitioning

Key challenges:

- which node a particular piece of data will be stored

- when add/remove nodes, how do we know what data will be moved from existing to new nodes. How can we minimize data movement

##### Naive Approach: hashing with modulo by # of servers

server(data) = hash(data) % number(servers)

Problem: when add/remove servers, we need to remap **ALL** existing hash keys & remap **ALL** data.

##### Consistent Hashing

Key concept:

- Ring: stores data in a ring

- Range: each server holds a range of data

- token: represents the start of the range for a given server

- Range calculation:
  - start: server token value
  - end: next token value - 1


Key problem it resolved:

- Only neighbor node is affected when adding/removing nodes

New problem it creates:

- result in non-uniform data & load distribution

With a manual and fixed division of the ranges, it suffers:

- expensive rebalancing data load (recomputing tokens when add/remove nodes)

- hotspots: some nodes may significantly hold more data than others, if data is not evenly distributed

- node rebuilding: node has its own dedicated replica nodes. Rebuilding a node is put pressures to dedicated replicas

###### Virtual Nodes

Key concepts:

-  dvide hash ranges into smaller tokens, represented as virtural nodes

- assign multiple vNodes (tokens) to a single physical node

- randomly distribute vNodes cross the clusser, and generally non-contiguous assignments to a single physical node

Key benifit:

- spread data load more evenly across physical nodes (even given a non evenly distributed data load)

- speed up rebalancing cluster: add/remove physical node now becomes moving vNodes around (and multiple physical nodes can contribute)

- support heterogeneous cluster: more vNodes on more powerful physical nodes

- reduce probability of hotspots by randomly assign vNodes across physical nodes

###### Data Replication

Key concepts:

- Replication factor: number of nodes will recieve the copy of same data. E.g. factor of 2 means 2 physical nodes hold same copy of data

- eventually consistent: copies of data don't always to be identical as long as they designed to be eventually consistent
  - for high availability (reduce block time on synring data copies)


- implementation
 - coordinator node: locally store the data for a given hash key
 - successor nodes: N - 1 clockwise on the ring -- for holding replicas
