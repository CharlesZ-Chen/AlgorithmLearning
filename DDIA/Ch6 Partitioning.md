### Partitioning and Replication

### Partitioning of Key-Value Data

Goal: spread the data and the query load **evenly** across nodes.


#### Partitioning by Key Range

Assign a continuous range of keys to each partition.

Partition boundaries need adapt to the data.

Pros:
- range scans are easy (within each partiion, keys in sorted order)

Cons:
- Hotspot problem: e.g. writes may focus on a single partition (e.g. key is timestamp, and write current occuring records to the database)


#### Partitioning by Hash of Key

A good hash function takes skewed data and makes it uniformly distributed.

- assign each partition a range of hash

Cons:
 - efficient range queries are now lost (any range query has to be sent to all partitions since keys are now scattered across all partitions by hash)
   - Compromise: Compund primary key -- first part of primary key is used for hash. Other columns used as a concatenated index for sorting data.


#### Skewed Workloads and Relieving Hot Spots

- Celebrity key issue: an extreme load of writes & reads to a single key, in which case that hash will not distribute load to multiple partitions.

  - Mitigation:
    - Application level compensation: Bookkeeping celebrity keys (a small set of really hot keys) -- then append random number on it to distribute the load
       - additional complexity: now reads will need to read all distributed partition of this hot key & merge read result properly

    - Hybrid approach: having another database mecahnism for hot keys. E.g. Twitter's hybrid approach of handling celebrity keys: by managing them in  Relational DB.


#### Partitioning and Secondary Indexes

Problem of seconadry indexes on paritions: they don't quite fit into partitions (as parititions are partitioned based on primary key, already).

Solutions:

- Document-based partitioning
- Term-based partitioning

##### Partitioning Secondary Indexes by Document

Each partition maintains its own secondary indexes (index on document fields) -- it is a **local index** approach.

Pros:
 - Easy for write. Write just write to the corresponding partition, and the local index will update coorespondingly.
Cons:
 - reading may need read from all partitions then merge the result


##### Partitioning Sceondary Indexes by Term

Construct a **global index** that covers data in all partitions, and partition this **global index** by the term (or the hash of the term).

Pros:
 - More efficient read -- now read is through the partition contains the targeted term.

Cons:
 - Write becomes difficult -- a single write may need update multiple secondary global indexes.


In practice, updates to global secondary indexes are often **asynchronous**.


#### Rebalancing Partitions
Motivation: Adaptive to changes like query throughout, data size grow, and infrastructure failures.

Solution: rebalancing partitions by moving data arounds cross nodes.

Requirements:

- After rebalancing, the load should share fairly between the nodes.

- Zero down time: while rebalancing, database should still able accept write & read requests

- minimum data movements: no more data than necessary should be moved to minimize network & disk I/O load

##### Strategies for Rebalancing

###### **Not** to do it: hash mod N

This cause moving data much more than necessary: add/remove nodes cause a re-hash on all data!

###### Fixed number of partitions

Allocated a large number of partitions that are more than number of nodes in the cluster.

Then assign multiple partitions to each node.

Partition is the atomic unit of rebalancing data. (similar to virtual node concept in consistent hashing)

**Difficulty**: how to choose the right number of partitions in ahead. (as usually, paritions are allocated with a fixed number for simpler operation model to avoid merge & split partitions)

In this case, **partition size** is proportionally to the dataset size.

###### Dynamic partitioning

Similar idea to fixed number of partitions on having multiple parititons on one node.

Now parititons are created dynamically though, with splitting if a partition grow too large, and merge to adjacent partitions if it shrink too small.

In this case, **partition number** is proportionally to the dataset size.

###### Partitioning proportinally to nodes

Make the **number of partitions** proportional to the **number of nodes**.

In other words, to have a **fixed** number of partitions on each node.

###### Request Routing

**ZooKeeper**!



