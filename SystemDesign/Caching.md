#### Articles:
- Cache strategies: https://codeahoy.com/2017/08/11/caching-strategies-and-how-to-choose-the-right-one/

#### Topics

- Application Server cache
- Content Delivery Network (CDN)
- Cache Invalidation
- Cache eviction policies

**? Difference between Invalidation and Eviction?**

#### Application Server Cache

Cache level: in memory; on disk

Problem with multiple nodes: request randomization introduced by load balancer -- increasing cache misses

Solution:
 - Global Caches
 - Distributed Caches

**? what are they? What are the common implementation/Tech stack for them?**

A: Memcached & Redis

##### cache strategies

Cache write strategies: https://www.baeldung.com/cs/cache-write-policy


###### Cache-aside cache
- Similar to read-through, but application takes responsibility of maintaining the cache
- Can use a different data model in cache, from the one in DB

###### Write-through cache

data write into cache + DB simultaneously.

Pros:
- complete data consistency between cache & DB
- Prevent data loss during a crash

Cons:
- higher latency for write operations (as it needs write twice ,1 in cache, 1 in DB)

###### Write-around cache

During write, data directly write into DB, bypassing the cache.

**?How to invalidate the data in cache then?**
- TTL (Time to live): set TTL as a contract: continue serve stale data untill TTL expires
- immediate invalidate the cache entry

Pros:
- Prevent write flood to cache for data that will not have frequent re-read (e.g. a batch write of infrequent read items)

Cons:
- has "cache miss" & higher latency in such scenario
  - ? how to invalidate the data in cache (if no write to cache?)

###### Write-back cache

Data first write to cache alone. Then populate to DB after specified intervals / certain conditions.

Pros:
- low-latency & high-throughput for write-intensive applications

Cons:
- Risk of data loss during a crash
 

##### Tech Stacks

Choosing between Memcached & Redis: https://docs.aws.amazon.com/AmazonElastiCache/latest/mem-ug/SelectEngine.html

Short Summary:

- Choose Memcached if:
 - simplest model possible
 - large cache nodes with multi-core/threads
 - flexible scale out and in (remove/add nodes)
 - ?**cache objects**


- Choose Redis if:
 - Rich/Complex data types: e/g/ strings, hashes, lists, sets, sorted sets, bitmaps
 - Need pub/sub capabilities
 - Transcational cache behavior (lua script)
 - Cache streaming (consume new produced items in cache in non-blocking fasion)  ?**example**
 - rolse-based auth at cache level
 - native support of encrpytion at rest, in transition


###### Memcached

###### Redis
- https://aws.amazon.com/elasticache/redis/

###### DAX (read & write through cache for DynamoDB)
- data consistency model: https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/DAX.consistency.html

##### Cache Use Case Examples:

- AWS: build chat application with Redis: https://aws.amazon.com/blogs/database/how-to-build-a-chat-application-with-amazon-elasticache-for-redis/
	- Leverage Redis Pub/Sub pattern to scale chat app to multi-webSocket servers with a global pubSub cache
	- Redis SortedSet (Z-index on time) for presenting messages chronologically

- AWS: Redis application in Hudls news feed: https://aws.amazon.com/blogs/database/caching-hudls-news-feed-with-amazon-elasticache-for-redis/
	- Leverage Redis rich data types:
		- Lists for a native timeline cache support for their news feed timeline presentation
		- Hashes for a quick display of # of followers for a user: (new follower is just a https://redis.io/commands/hincrby)
		- Sets for prevent double executions (e.g. prevent post same news twice for a user in a retry use case)
	- Write-through strategy so that no cache-invalidation needed


#### Content Delivery Network (CDN)

For serving large amounts of **static media**.

Request -> CDN: asking static media -> return if cached -> query backend, cache it, then return

Incremental approach (when CDN is an overkill): prepare a separated subdomain (e.g. static.yourapp.com) for serving static media.
- You can easily cut-over the static DNS from your http server to a CDN for a scale up.


#### Cache Eviction Policies

- Chronologically:
	- FIFO (First In First Out)
	- LIFO (Last In First Out) **?why would ppl want this?**

- Frequence+Chrono based:
  - LRU (Least Recently Used)
  - MRU (Most Recently Used): **?Why would ppl want this?**

- Frequency based:
  - LFU (Least Frequently Used): Counts on how often an item is needed. Discard first for those used least often

- RR (Random Replacement): Randomly select a candidate to discard

#### Additional Reads

- https://lethain.com/introduction-to-architecting-systems-for-scale/
