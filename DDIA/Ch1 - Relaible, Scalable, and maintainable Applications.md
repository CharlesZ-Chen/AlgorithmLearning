### Reliability


### Scalability

#### Describing Load

Identify **key load parameters**. E.g. reads per sec to database, write per sec to database, fan-out rate for a write request...

- It depends on the architecture/characteristic of your system

E.g. a **key load parameters** for Twitter would be to support user home timeline request efficiently. (300 k read requests / sec on aggregating other users tweets posted by the people a user follows)


#### Describing Performance

- when a load parameter increased, and keep system resources (CPU, memory, network bandwidth etc) unchanged, how is the performance of your system affected?

- when a load parameter increased, how much do you need to increase the resources if you want to keep performance unchanged?


- For batch processing system: usually cares about **throughput** (# of records processed per sec)

- For online system: usually cares about **service's response time**

### Maintainability


