## Handling Changes for data replication

- single-leader

- multi-leader

- leaderless

## Leaders and Followers

- Replica: a node stores a copy of the database

- Leader: authority for all writes to the database -- clients can only write to leaders

- Followers:
  - follows Leader's change by recieving a replication log / change stream
  - applying all writes in the same order as were processed in the Leader


## Sync / Async Replication

- Synchronous Replication:
   - Leader Ack to Client once all sync followers updated sucessfully
   - Pros:
     - Ensured that Leader's failure will not lost data (as sync followers ensured to have up-to-date replicas)
   - Cons:
     - Sync followers failure blocks client writes

- Asynchronous Replication:
   - Leader Ack immediated to Client once Leader successfully updated. Followers are asynchronously updated.
   - Pros:
     - Followers failure does not blocks client writes.
   - Cons:
     - Leader's failure will result data lost even Ack to client that write successfully (for those writes havn't successfully updated in followers)

- Semi-Asynchronous Replication:
   - Choose a subset of followers to be syncrhonous (a small set), to increase data durability.
   - Rest followers are Async.
   - If a sync follower failed, promote some async followers to be Sync follower.


- TODO: Chain Replication

## Setting Up New Followers

- take a consistent snapshot of the leader's database

- copy the snapshot to the new follower

- request all changes since snapshot from the leader
  - This requires the snapshot is associated with an exact position in the leader's replication log.

## Handling Node Outages

### Follower failure: Catch-up recovery

- Similar to setting up new followers, except that follower first recover from its disk log. Then caught up with leader.

### Leader failure: Failover

- Determining leader failure: E.g. hearbeat timeout

- Choosing a new leader: goal: minimize data loss. Getting all nodes agree on a new leader.

- Reconfiguring system to use new leader:
  - Client to send write requests to new leader
  - followers to follow new leader
  - step down old leader to a follower when its back


#### Failover is fraught with things that can go wrong


- How to handle async replications? E.g. those async writes havn't updated to followers, when a leader failed and a new leader promoted

- Simple discard async writes can be dangerous if other storage systems need to be coordinated with DB contents.
  - https://github.blog/2012-09-14-github-availability-this-week/

- Split Brain problem: two nodes both believes that they are the leader

## Implementation of Replication Logs

### Statement-based Replication

Leader logs every write request (statement). Then send statement logs to followers for a replay.

- Cons:
  - Cannot handle non-deterministic functions
  - Tricky to handle statements that relies on existed data in database (e.g. autoincrementing column)
  - Statements that has side effects are tricky to handle (unless they 100% deterministic)

### Write-Ahead Log (WAP) shipping

Log is an append-only sequence of bytes containing all writes to the database.

- Cons:
  - WAL log described data on a very low level -- it records which bytes were changed in which disk blocks
  - Make replication couples with storage engine


### Logical (row) based Replication


### Trigger-based Replication

## Problems with Replication Lag

Sync replication does not work well practically (a single follower failure blocks the entire system); However async replications introduces issues when replication lag between leaders and followers are significant enough to be a real rather than theoratical issue.


### Reading Your Own Writes

Guarantee that user will always reads their own writes (but no guarantee for other's writes). Some solutions:

- Leader/Follower Reading Strategy:
  - Read from leader for content that user may modify. E.g. Read user's own profile from leader, other's profiles from followers.
  - Time buffers.
    - E.g. track time of the last update. For 1 minutes after the last update, all reads from the leader.
    - Prevent followers serve read request if their replication log has > 1 mins delay from the leader.
- client to remember timestamp of its most recent write (e.g. consistency token)

- If multiple data centers involved: any requests that needs to be serveed by the leader must be routed to the datacenter contains the leader.


#### Cross-device Read-your-own Writes

- client timestamp (consistency token) approach becomes more difficult. Now needs to centralize this metadata cross devices.

- If replicas distributed to different datacenters, need to route requests from all of a user's devices to the same datacenters.


### Monotonic Reads

It's possible for a user to see things moving backward in time.

E.g. User read updates from a follower with little lag, then refresh that read from a follower with great lag.

**Monotonic reads** is a guarantee that user will never see things moving backward in time. I.e. for a sequence of **read** from a user, they will not read older data after having previously read newer data.

A possible solution:

- Make sure each user will always makes their reads from the same replica
 - Cons: if a replica fails, user requests will need to reroute to another replica (**?? how to ensure Monotonic Read in this case?**)


### Consistent Prefix Reads

Ensure no violation of causality (cause and effect). I.e. if a sequence of writes occurs in a certain order, guarantee that anyone reading these writes will see them appear in the same order.

A possible solution:
 
- Make sure any writes that are causally related are written to the same partition
 - Cons: some application might not able to achieve this efficiently.
 - Follow up: "The 'happens-before' relationship and concurrency"


## Multi-Leader Replication


### Handling Write Conflicts

#### Sync vs Async Conflict Detection

#### Conflict Avoidance

#### Converging toward a Consistent State

#### Custom Conflict Resolution Logic

### Multi-Leader Replication Topologies

A **replication topology** describes the communication paths along which writes are propagated from one node to another.

E.g. 
 - Circular Topology
 - Star Topology
 - All-to-all Topology


## Leaderless Replication

### Writing to the Database When a Node Is Down

In Leader Replicaiton, if the leader node is down during processing writes, a failover is needed.

In Leaderless Replication, such failover does not exist.

Solution:
- Write and Read to multiple replicas in parallel. Write & Read status decided by multiple responses form these replicas.


### Read repair and anti-entropy


## Sloppy Quorums and Hinted Handoff

Situation: in a large cluster, client can connect to some nodes, but lost connection to the dedicated `w` or `r` nodes during network interruption.

**Sloppy Quorum**: accept writes anyways, and write them to some temp nodes that not the dedicated `w` nodes, and handoff to the dedicated `w` nodes once network recovered.
 - Use case: Increasing write availability.
 - Side effect: quorum formula `w + r > n` no longer ensure latest value for a key will always been successfully read. (as some writes may temp outside of `n` nodes).

**Hinted handoff**: writes that accepted by temp nodes send to appropriate "homw" nodes once network fixed.


## Detecting Concurrent Writes

Situation: Events may arrive in a different order at different nodes.

Result: Database may becomes permanent inconsistent (e.g. for write events set(A), set(B) -- B may arrive as the later write at some nodes, while other nodes have A as the later write.)

Solution Handling write conflicts:

- Last write wins (discarding concurrent writes)

Idea: enforce an arbitrary order on the events (e.g. by timestamp) -- so that we can discard "older" one by overwrite it with "recent" one.

- Side effect: reduce durability. For multiple conccurent writes to the same key, client may recieve multiple uccessful ack but only 1 of the write is actually persisted. (with other silently discarded).

- To avoid lost of durability: make sure each key only **write once** (i.e. keys treated as immutable after first write).

### The "happens-before" relationship and concurrency

Definition: An operation A **happens before** another operation B if: 1) B knows about A 2) depends on A 3) builds upon A in some way.

### Capturing the happens-before relationship

- server maintains a version number for every key. Increments the version number every time that key is written, and store with the written value with the new version number.

- When a client reads a key, the server returns all values that have not been overr-wrriten, as well as the latest version nubmer. A client must read a key before writing.
  - Over-written: if a version represents a prior write in a happens-before relationship (e.g. a more recent version built/write upon this version), then this version has been over-written.

- When a client writes a key, it must include the version number from the prior read, and it must merge together all values that it recieved in the prior read.

- When the server recieves a write with a particular version number, it can over-write all values with that version number or below, but it must keep all values with a higher version number.

Note: When a write includes the version number from a prior read, that tells us which previous state the write is based on.

Client-side merging: things may get tricker if "removal" are allowed (so that in shopping cart example, simply take "union" as the merge conflict solution may not work as expected -- may require a "deletion marker" to prevent re-add a removed element back (e.g. element removed in 1 sibling, but persist in other sibling(s) which result union adds it back.)

**Scale to multiple write replicas**: instead a single version number, let each write replica maintain its own incremental version number, with the collection of version numbers of all replicas act as a **version vector** (to indicate which values to overwrite and which values to keep as siblings).


To fix staled data on an unavailable node that comes back online:

- Read Repair: client detects stale response and fix the stale replica

- Anti-entropy: a background process running in each replica to detect data discrepency & fix them

#### Quorums for reading and writing

Quorums read and writes formula:

- `w`: every write must be confirmed by `w` nodes to be considered successful

- `r`: every read must be read from `r` nodes (to guarantee that at least one of `r` rodes contains the most up to date value)

- `n`: total nubmer of nodes

Formula: `w + r > n`

- Larger `w` benefits more on write side, larger `r` benefits more on read side


Normally reads and writes are always sent to all `n` replicas in parallel -- `w` and `r` determine how many node we wait for


#### Limitations of Quorum Consistency

There are edge-cases that even with `w + r > n` formula, Quorum Consistency still may not hold.

- If a sloppy quorum is used (what is a sloppy quorum?)

- If two writes occur concurrently (not clear which one happened first, and therefore, need to merge the concurrent writes).

- If a write happens concurrently with a read.

- If a write succeeded on some replicas but failed on others, and overall succeeded on fewer than `w` replicas.

- If a node carrying a new value fails and restore from a replica with old value. (result in number of replicas with newer value may fall below `w`)


Note: Quorum Consistency practically is not easy to achieve, and better to treat the formula as a tuning on probablity of stale values being read, instead of an absolute guarantees.


#### Monitoring Staleness

 

### Sloppy Quorums and Hinted Handoff

Situation: trade-off on should return errors to all requests that cannot reach a quorum, or should we accepts writes but write into some nodes that are not the designated n "home" nodes for the write value.

**Sloppy Quorum**: Writes and reads still require `w` and `r` successfully responses, but those may include nodes that are not among the designated n "home" nodes for a value.

**Hinted handoff**: nodes that temporarilly accepting writes send values to the appropriated "home" nodes once they back online.

- Cons: increasing write availability

#### Multi-datacenter operation


### Detecting Concurrent Writes

Concurrent writes issue in Dynamo-style DB: clients concurrently write to the same key, but nodes recieving write requests in different orders. This could result permanently inconsistent DB.

Solutions:

#### Last write wins (LWW) (Discarding concurrent writes)

Force an **arbitrary oder** on concurrent writes. E.g. attach a timestamp to each write, let the one with biggest timestamp "wins".

- Pros: achieves eventual convergence

- Cons: lost durability -- for several concurrent writes to the same key, even if they were all reported as success to clients, only one of them will persist in the DB.


#### The "happens-before" relationship and concurrency (how to decide if conccurent writes occurs)

**Casual dependency**: An opperation A happens before another operation B, if B knows about A, or depends on A, or builds upon A in some way.



