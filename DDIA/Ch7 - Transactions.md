## The Slippery Concept of a Transcation

### The Meaning of ACID

ACID: Atomicity, Consistency, Isolation, Durability

#### Atomicity

ACID Atomicity is about safe guarantees when a client wants to make several writes. (It is **not** about concurrency, i.e. one thread ensure an atomic action etc.)

If the writes were grouped into transaction, then this group of writes either be all successfully completed (committed), or be all aborted.

#### Consistency

ACID Consistency is a property of application. It describes an application-specific notion of the database is guaranteed to always hold data invariants
(as long as application submit transactions that holds these invariants).

#### Isolation

Isolation described that concurrently execucting transcations are **isolated** to each other, i.e. they don't have dependencies to each other.

A formal term of Isolation is **Serializability**, means concurrent transcations will have the same result to database as if they were executed serially.

In practice, Serializability is a database property with high performance cost, and multiple weaker **isolation levels** are being applied.

#### Durability

**Durability** promise that once a transaction has commmitted successfully, any data it has written will not be forgotten, even if database crashed.

