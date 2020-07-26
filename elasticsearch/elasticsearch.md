# Elasticsearch 

Elasticsearch is a **search engine** based on the **Lucene** library. It provides a **distributed**, **multitenant-capable full-text search** engine with an HTTP web interface and schema-free JSON documents. It's a distributed document store that instead of storing information as rows of columnar data; stores complex data structures **that have been serialized as JSON documents**. 

It is indexed and fully searchable in near real-time--within 1 second. Lucene allows new segments to be written and opened, making the documents they contain visible to search ​without performing a full commit. In Elasticsearch, this process of writing and opening a new segment is called a **refresh**. A refresh makes all operations performed on an index since the last refresh available for search. By default, Elasticsearch periodically **refreshes indices every second**, but only on indices that have received one search request or more in the last 30 seconds. This is why we say that Elasticsearch has near real-time search: document changes are not visible to search immediately, but will become visible within this timeframe. With refresh (?refresh) setting in index, update, delete and bulk API you can control when changes made by this request are made visible to search.

By default, Elasticsearch **indexes all data in every field** and each indexed field has a dedicated, optimized data structure.

Elasticsearch **index** is really just a **logical grouping of one or more physical shards**, where each **shard is actually a self-contained index**. Each shard can have zero or more **replicas**.

There are two types of shards: 
- **primaries**:  Each document in an index belongs to one primary shard.
- **replicas**: A replica shard is a copy of a primary shard. Replicas provide redundant copies of your data to protect against hardware failure and increase capacity to serve read requests like searching or retrieving a document.

**The number of primary shards in an index is fixed at the time that an index is created**, but the number of replica shards can be changed at any time, without interrupting indexing or query operations.

There are a number of performance considerations and trade offs with respect to shard size and the number of primary shards configured for an index. The **more shards**, the more overhead there is simply in **maintaining** those indices. The **larger the shard size**, the **longer it takes to move shards** around when Elasticsearch needs to rebalance a cluster.

Querying lots of small shards makes the processing per shard faster, but more queries means more overhead, so querying a smaller number of larger shards might be faster. In short…​it depends.

- Aim to keep the average shard size between a few GB and a few tens of GB. For use cases with time-based data, it is common to see shards in the 20GB to 40GB range.
- Avoid the gazillion shards problem. The number of shards a node can hold is proportional to the available heap space. As a general rule, **the number of shards per GB of heap space should be less than 20**.

Each node hosts one or more shards, and acts as a **coordinator** to delegate operations to the **correct shard(s)**. *Rebalancing* and *routing* are done automatically.

Lucene, the Java libraries on which Elasticsearch is based, introduced the concept of **per-segment search**. A segment is similar to an inverted index, but the word index in Lucene means "a collection of segments plus a commit point". **After a commit, a new segment is added to the commit point and the buffer is cleared**.

- The RAM and Storage should be in the ratio of 1:8 (suggested and implemented in the elastic cloud).
- The shard size should not be more than of the ram that allocated for elasticsearch heap (mostly 50% of total ram allocated for elasticsearch heap) Set Xmx and Xms to no more than 50% of your physical RAM.
- Don’t over allocate the shards, it will reduce the performance.
- Secure Elasticsearch under reverse proxy

About Modelling:
- The basic principle of data modeling in elasticsearch is to reduce the number of shards the elasticsearch looking for the result. shard is nothing but the next bottom level of an index.
- One or more shards forms an index. so giving many numbers of shards for future scalability, may affect the current search and indexing time. So allocating shards in right numbers will gives you certain amount of performance boost.
- It depends on your data. If it is social api data or log, go with time-based index. It will give you lot of flexibility while storing and retrieving.
- If your data are user based, and your search queries will be based on a single user, then it is better to create user based index. But as i said earlier if you need to search on all user data then this modeling will not work. In this case find the next level entity and make it as index point, so that you can search by user id.

## Query
**search**
- **match:** To search for specific terms within a field, you can use a match query.
- **match_phrase:** To perform a phrase search rather than matching individual terms.

# Resources
- [Wikipedia](https://en.wikipedia.org/wiki/Elasticsearch#cite_note-officialsite-4)
- [Elastic docs](https://www.elastic.co/guide/en/elasticsearch/reference/current/elasticsearch-intro.html)