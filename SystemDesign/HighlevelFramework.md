#### FrameWork

- Gathering requirements:
 - Functional Requirements

 - Non-Functional Requirements
 	- Usage Pattern (Read/Write Heavy)
 	- Realiability
 	- Scalability
 	- Consistency (Strong/Eventual Consistency)
 	- Latency
 	- Data Durability
 	- Idempotency

- Drive the converstion by providing a topic guideline:

 - API design
   - Restful API / RPC
   - Synchronized / Asynchronized
   - Rate Limiting

 - Capacity Estimation (Back of Envelope)
   - QPS (Query Per Second) (Read, Write)
   	 - Average, Peak
   - Network bandwidth (Upload, Download)
   - Disk Storage
     - Data Retention

 - High-level architecture design

 - Database
   - Sql/NoSql
   - Schema
   - Sharding
   - Replication

 - Other Topics
   - Caching
   - Monitoring
   - Throttling
   - Scale Up
   - Failure Handling
   - Logging
   - Push vs Pull
