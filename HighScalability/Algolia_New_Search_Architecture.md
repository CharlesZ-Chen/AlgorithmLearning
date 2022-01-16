## Article

- Part 1: http://highscalability.com/blog/2021/8/2/evolution-of-search-engines-architecture-algolia-new-search.html
- Part 2: http://highscalability.com/blog/2021/10/11/scaling-indexing-and-search-algolia-new-search-architecture.html



## Reading Notes

### The Inverted Index -- The Early Days Of Search

- First hand learn on what is an inverted Index: https://nlp.stanford.edu/IR-book/html/htmledition/a-first-take-at-building-an-inverted-index-1.html

Early Days search engine architecture:

- one **indexing process** taking the list of records to compute and producing a binary file (represents the inverted index)
- one **query processing" search process intepreting the binary file to compute the intersection of inverted lists for a particular query

### Introducion Of Sharding -- Parallelization to Scale the Indexing

The idea:

- split documents to N smaller set of documents => each set is called a **shard**

- indexing in parallel on those shards => producing N smaller inverted indices

- search in each shard in parallel => producing N queries & aggregating the results to the unified result set

The shard-based search engine architecture:

- A fixed number of shards, distributed across different servers

- A distributed computation of the inverted index (indexing, one thread per shard)

- A distributed computation of the search results, across the different shards (searching, one thread per shard), finalized by a fast merge of the N results

Additionally:

- Indexing and searching need to be on the same machine for the same shard (inverted indices are on local storage)

- lack of mechanisms allowing an indexing operation to be visible on all shards at the same time
  **?? What does this meant?**

- New types of merging problems ariased (e.g. collapsing results based on field values)


### High Availability and Scaling Of Search -- The Age Of Maturity -- Replication to Scale the Querying

Introduce Primary/Replica architecture to scale on the querying -- achieve scalability of handling large volume of querying and high availability.

Architecturally characteristics is same with sharding, additionally:

- scale on replications can handles larger volume of queries, but it takes time to  set up replications.
  - Good solution only when the increase of traffic can be **predicated**.
  
- Multi-provider context: replicate indexing operations instead of binary files -- as the inter-provider bandwidth can be the bottleneck
  ?? **what is Multi-provider?**

### High Availability Of Indexing And Elasticity -- The Business Critical Area


### Next Steps -- The Current Challenges Of Search Engines


## Part 2

### Anatomy Of An Index
  


