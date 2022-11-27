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


#### Reference

系统设计面试
系统设计面试是我要着重说的，因为我本身算是MLE，对于系统设计是从零开始准备的。我在三月份大概花了三周的时间把DDIA（Designing Data-intensive Applications）这本书看了一遍，然后四月把grokking（grokking the system design interviews）看了一遍。当时我觉得自己简直无敌，然后去面试Robinhood的第二轮电面（job scheduling system那道题）就挂了。那是我人生第一次系统设计面试，也是我这次这么多轮面试第一次挂在电面上，对我打击很大。特别是我提前刷了面经，知道了他们大概率面这道题目，提前把公司内部的job scheduling那个组的文档和论文都读了几遍，对于这个系统可以说是了如指掌了，但是面试依然挂了。

当时我就意识到一个重要问题：系统设计面试，技巧也很重要。面试技巧只能在实战中学会。于是我幡然醒悟，在一个付费模拟面试的网站(interviewing.io)进行了4次系统设计模拟面试。每次模拟面试完了之后，面试官会立刻跟你反馈，hire还是no hire，问题在哪里。只有这种即时反馈的模拟面试，才能真正让你的面试技巧得到提升。我当时第一次模拟面试就拿了一个weak hire at senior level，面试官给我的反馈就是对面试节奏把握不好，达不到一个senior的要求，这个一会儿我会详细说。

在那个面试官花了十分钟教我如何把握面试节奏之后，我变质了。我像是打开了任督二脉一样，突然意识到了，在花了那么多时间看书看资料练习内功之后，如果把功力在面试的时候表现出来也很重要。于是我给自己定了一个系统设计面试套路，后面的面试都坚持那个套路，同时根据面试官的风格不断调整，做到始终掌握节奏。后面的三个模拟面试，拿了两个hire一个strong hire at senior level，因为我花了那么长时间看DDIA和grokking，我不缺内功，我只缺面试技巧。

下面我来分享这个我自己总结出来的系统设计面试套路，特别是针对senior level。因为senior的系统设计面试，很多公司会要求面试者能主导整个谈话（drive the conversation）。
面试官把题目说出来以后，问问题，搞清楚需求。需求就分两种：functional requirements 和 non-functional requirements。对于functional req，要根据题目适当地问问题，了解需求。对于non-functional req，基本就是在以下的需求里面挑选出合适的：usage pattern（是read heavy还是write heavy），是否需要考虑scalability的问题，是否需要highly available，consistency（strong consistency，还是eventual consistency，是否需要read-your-own-write consistency），latency是否有要求，data durability是否有要求，idempotency（对于付款相关的系统一定强调这个）。以上有那么多non-functional req，我们不可能对于每个问题都把每个需求说一遍，那样太浪费时间。我们应该关注这个系统的重点，比如让你设计monitoring system，那availability就是重中之重，其他的点比如scalability，虽然重要，但不会是考察的重点，可以这时候简单提一下，在面试的后期再回头讲解如何解决这个问题。functional requirements 和 non-functional requirements应该力求在15分钟以内说完。有些点快速略过很重要，有时候你说的不是重点，虽然说了不会扣分，但是会耽误几十秒时间，使得后面没有时间说重点。系统设计面试，时间管理非常重要。
在搞清楚需求之后，对于senior面试者，一般都是希望你从这里开始一直主导对话直到结束的。所以我会直接说"okay now let me list a few topics that we wanna cover"，然后直接写下"API design", "capacity estimation", "high-level architecture design"，"database (schema, sql/nosql, sharding, replication)"和"other topics"。这些就是系统设计可能会讨论的大方面，提前写好可以保证你在主导对话的同时记得涵盖所有的点，不然自己一直说，可能一兴奋忘了说重点。列出这些点之后我会说"now let's start with API design, what do you think?"然后面试官就会回答好或者不好，你就可以继续说下去了。这里要注意的是，我们其实只是在假装"drive the conversation"，最后先说哪个再说哪个其实还是面试官决定的，除非在少数情况下面试官没有偏好，告诉你先说哪个都行，那你就可以自己随便挑。注意，如果面试官让你自己挑顺序，你也要按照合理的顺序，或者让自己最舒服的顺序，来最大化自己的收益。下面具体说说买个方面应该怎么应对。
API design。如果这个系统会暴露出一些API，那么一般这是个考察重点。在说这个主题的时候，一般都是对于每个API，定好名字，定好输入是哪些，输出是哪些就行了。有少数情况面试官会问你是用RPC还是Rest API，有的还会问一些Rest API的知识，这些都准备一下就行了。有的情况下设计synchronized API和asynchronized API会有很大不同，那么就要说清楚你选择哪个，以及理由是什么：比如有的时候一个操作需要执行很久，这时候一般选择设计一个async API先立刻返回一个id给caller，让caller拿着id去查询操作的执行情况，而不应该只设计一个sync API让caller一直阻塞在那里。有时候一些API看起来是读东西的，所以不需要任何输入，这时候需要注意，就算不需要任何输入，也需要一个id来识别caller，因为有时候要做rate limiting，这个在面试的时候随便提一下就可以了，真正如何做rate limiting可以放在最后"other topics"里面说。
Capacity estimation。这个简单，grokking里面讲得不错了。一般就是把QPS (read, write)，network bandwidth(upload, download)，disk storage算一下就行了。注意，算QPS和bandwidth的时候，要把average value和peak value都算一下，一般是先算average，然后你可以说假设peak比average高一个数量级。1M/day换算成QPS就是12/s，这个要记住。算disk storage的时候，直接说“假设我们存5年的数据”，因为5年约等于2000天，好算，同时不要忘记存在disk上的数据都有replication，一般直接说"let's assume the replication factor is 3"，然后乘以3就行了。
High-level architecture design。这个就是画图，没啥好说的。建议提前熟悉一下面试要用的画图软件。
Database (schema, sql/nosql, sharding, replication)。一般讨论数据库的时候就是讨论这些点，说schema的时候一般说一下有哪些表，每张表有哪些column，谁是primary key就行了。对于sql/nosql，要根据场景来说，我遇到的只能用sql的场景很少，只能用nosql的场景也不多，一般都是二者都可以，但是我会跟面试官说虽然二者都可以，但我更偏向于nosql，因为这里写操作比较多。但是大家还是要根据具体情况来，比如订票系统，牵扯到多张表的distributed transaction，那么只能是sql，还有购物相关的系统，user-order-item这些东西是天生relational的，那么sql也是更好的选择。我印象中的其他系统就很少遇到那种只能用sql的。对于sharding，一般都有一个"shard by user id"还是"shard by item id"的trade off，如果是user id，那么会有Hotspotting问题（因为有的user特别活跃），如果是item id，虽然可以解决hotspotting问题，但是如果需要读某个user的数据，就需要访问所有的shard。这个trade off对于大多数题目都适用，如果是你在讲database的时候主动提出，会大大加分。
Other topics（cache/how to scale/push vs. pull/monitoring/rate limiting/failure handling/logging）。如果以上所有东西说完还有时间，一般面试官会让你在这里面挑一两个点讲一讲，就挑最拿手的讲就行了。注意，有时候这里面的某个点会是这个系统的重点，那么就应该把这个点放在前面着重讲。


. From 1point 3acres bbs
以上就是我总结出来的套路，但是一定要注意在面试的时候根据面试官风格的不同适当调整。技巧就是每次都先问问面试官是不是ok，比如"let's start with capacity estimation, what do you think?"，有的面试官压根就不希望你做capacity estimation，会告诉你先不要做，后面有时间再做，那么这个技巧就可以帮你节省大量的时间。有时候有的面试官自己有强烈的偏好，会告诉你先画high-level architecture，然后等你画完不给你机会自己说，而是开始问问题，不给你机会drive the conversation。我面了这么多家，各种风格的面试官都遇到过，最后得出来的体会是：这个套路可以应付大多数面试，但是一但发现面试官有自己的偏好，就一定要放弃套路，跟着面试官走，有的面试官完全不需要你来drive conversation，你就让他问问题就好了。有的面试官在你说话的时候绝对不会打断，所以你要不断确认你说的是否合理，不然花很多时间说一些面试官根本不在乎的东西，虽然不扣分，但是浪费了宝贵的时间。对于大多数传统的面试官，根据这个套路就能应付。

面试技巧固然重要，内功也不能忽视。我在准备阶段主要接触了以下资料：
DDIA：这是我一开始准备就开始看的书，我觉得有条件的应该看两遍，开始的时候看一遍，然后进行一些面试练习之后再看一遍加深印象
grokking：这是我看完书之后就看的资料。很多人说这个资料不行，但是我觉得这取决于你如何对待它。它的问题在于讲东西的时候有时候没有章法，很多时候没有列出重要的trade off。但是它的价值在于给我们准备了很多常考的例题，每个例题都踩了一些点。如果我们能合理组织它踩的这些点，还是可以应付面试的。最大的价值还是它提供的例题，基本都是面试中常见的题型。
YouTube上的InfoQ频道：看这个频道里的QCon视频，可以真正了解工业界对某些问题的解法是什么样的。看多了之后会增加你在面试的时候的自信：因为你知道你说的就是工业界常用的解法，绝对不会错。这个建议没事就看看，像看剧一样刷。
YouTube上Scott Shi的频道：这个频道最大的意义也是给大家提供了一些常见的例题（还有一些不常见的例题）。如果纯看视频里讨论的质量，肯定没有那么高，但是关键在于这些例题以及这些讨论能不能引发你的讨论，你能不能把这些题目自己模拟做一遍。

如果以上的资料都看完了，后面的重点就是练习面试技巧，我采取的是付费模拟面试的方法，大家可以采取别的方法。重点是一定要实战练习，让自己习惯面试的环境。
