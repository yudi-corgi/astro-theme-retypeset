---
title: Elasticsearch 从来都不是一个数据库
published: 2025-10-10
tags:
  - 技术拾萃
lang: zh
abbrlink: elasticsearch-was-never-a-database
---

## 写在前头

本篇是 [Elasticsearch Was Never a Database](https://www.paradedb.com/blog/elasticsearch-was-never-a-database) 一文的中文翻译，其讲述 ES 并非是一个数据库的种种理由，你的团队正在将 ES 作为数据库使用，那么你可先思考下，面对文章提到的问题是否都有对应的解决方案。

**以下是太长不看版（由 AI 整理）**：

Elasticsearch 是一款卓越的搜索引擎，但绝非一个合格的主数据库。许多团队起初仅用它满足搜索需求，但渐渐地，为了简化数据同步的复杂性，错误地将其推上了主数据库的位置。这种“滥用”最终会导致一系列严重问题，Elasticsearch 的设计初衷是提供极致的搜索体验，而非作为事务性的“记录系统”（System of Record）。当团队试图用它简化架构时，往往会陷入一个“看似捷径，实则荆棘”的困境。

Elasticsearch 在充当数据库角色时存在五大核心缺陷：

1. **事务与一致性缺失**
关系型数据库通过原子事务确保数据操作的完整性，而 Elasticsearch 的保证仅限于单个文档。这意味着在复杂业务场景下（如订单和库存），无法避免数据处于“中间状态”的风险。同时，其“准实时”的特性也可能导致刚写入的数据无法立即被搜到。
2. **僵硬的模式变更**
与关系型数据库灵活的 ALTER TABLE 不同，Elasticsearch 的索引映射（Schema）一旦设定便无法修改。任何字段类型的变更都可能需要创建新索引并进行成本高昂的、高风险的全体数据迁移。
3. **关系查询的短板**
SQL 中最基础的 JOIN 操作在 Elasticsearch 中付之阙如。这迫使开发者通过数据冗余（反范式）、父子文档或在应用层手动拼接数据等方式来模拟关联查询，不仅笨拙，而且效率低下。
4. **可靠性与恢复的鸿沟**
数据库通过预写日志（WAL）等机制确保事务的持久性，即使在崩溃后也能恢复到一致状态。Elasticsearch 的 Translog 虽能保证单文档的持久性，但无法在事务层面提供同等级别的保障，故障可能导致部分操作丢失，破坏数据完整性。
5. **运维的稳定性压力**
作为分布式系统，Elasticsearch 的运维本就复杂。当它成为系统的唯一信源时，分片均衡、JVM 调优、滚动升级等操作带来的风险不再仅仅是“搜索暂时变慢”，而是直接威胁到核心数据的安全与稳定。

滥用工具的代价是高昂的。应当让 Elasticsearch 回归其“搜索引擎”的本质，发挥其在速度和相关性上的巨大优势。对于同时需要强大搜索和事务性保障的场景，文末最后提出了 ParadeDB 这一将两者原生结合的新一代解决方案。

**以下是原文+翻译：**

## Elasticsearch 从来都不是一个数据库 <br>Elasticsearch Was Never a Database

[Elasticsearch](https://www.elastic.co/elasticsearch) was never a database. It was built as a search engine API over [Apache Lucene](https://lucene.apache.org/) (an incredibly powerful full-text search library), but not as a system of record. Even Elastic’s own guidance has long suggested that your source of truth should live somewhere else, with Elasticsearch serving as a secondary index. Yet, over the last decade, many teams have tried to stretch the search engine into being their primary database, usually with unexpected results.  
[Elasticsearch](https://www.elastic.co/elasticsearch) 从来都不是一个数据库。它是基于 [Apache Lucene](https://lucene.apache.org/) （一个极其强大的全文搜索库）构建的搜索引擎 API，而非记录系统。就连 Elastic 自己的指南也早就建议，你的数据来源应该放在其他地方，而 Elasticsearch 应该作为二级索引。然而，在过去十年中，许多团队尝试将搜索引擎扩展为主要数据库，结果往往出乎意料。

## “数据库”是什么意思？ <br>What Do We Mean by “Database”? 

Just to be clear up front, when we say *database* in this context we mean a system you can use as your primary datastore for OLTP transactional workloads: the place where your application’s truth lives. Think Postgres (voted [most loved database](https://survey.stackoverflow.co/2025/technology#most-popular-technologies-database-prof) three years running), MySQL, or even Oracle.
首先要明确一点，我们所说的*数据库*指的是一个可以用作 OLTP 事务性工作负载主数据存储的系统：应用程序真实数据所在的地方。例如 Postgres（连续三年被评为[最受欢迎的数据库 ](https://survey.stackoverflow.co/2025/technology#most-popular-technologies-database-prof)）、MySQL，甚至 Oracle。

## 我们是如何走到这一步的？ <br>How Did We Get Here? 

The story often begins with a simple need: search. A team is already using Postgres or MySQL to store their application data, but the built-in text search features don’t scale. Elasticsearch looks like the perfect solution; it’s fast, flexible, and easy to spin up.
故事通常始于一个简单的需求：搜索。一个团队已经在使用 Postgres 或 MySQL 来存储他们的应用程序数据，但内置的文本搜索功能无法扩展。Elasticsearch 看起来是一个完美的解决方案；它快速、灵活，而且易于启动。

At first, it’s just an index. Documents live in the database, and a copy lives in Elastic for search. But over time the line starts to blur. If documents are already in Elasticsearch, why bother writing them to the database at all? The process to keep the two stores in sync is the most brittle part of the stack, so why not get rid of it? Now the search index is also the database. The system of record has quietly shifted.
起初，它只是一个索引。文档存储在数据库中，副本存储在 Elasticsearch 中用于搜索。但随着时间的推移，这两者之间的界限开始变得模糊。如果文档已经在 Elasticsearch 中，为什么还要费心将它们写入数据库呢？保持两个存储同步的过程是整个技术栈中最脆弱的部分，那么为什么不去掉它呢？现在，搜索索引也变成了数据库。记录系统已经悄然发生了变化。

That’s where the trouble begins. A database isn’t just a place to keep JSON, text documents, and some metadata. It’s the authoritative source of truth, the arbiter that keeps your application data safe. This role carries expectations: atomic transactions, predictable updates, the ability to evolve schema safely, rich queries that let you ask questions beyond retrieval, and reliability under failure. Elasticsearch wasn’t built to solve this set of problems. It’s brilliant as an index, but brittle as a database.
问题就在这里。数据库不仅仅是存储 JSON、文本文档和一些元数据的地方。它是权威的真相来源，是保障应用程序数据安全的仲裁者。这个角色承载着人们的期望：原子事务、可预测的更新、安全地演进模式的能力、允许你提出超越检索问题的丰富查询，以及故障下的可靠性。Elasticsearch 并非为解决这些问题而构建的。它作为索引非常出色，但作为数据库却很脆弱。

## 从未发生过的交易 <br>Transactions That Never Were

The first cracks appear around consistency. In a relational database, transactions guarantee that related writes succeed or fail together. If you insert an order and decrement inventory, those two operations are atomic. Either both happen, or neither does.
第一个漏洞出现在一致性方面。在关系数据库中，事务保证相关的写入操作同时成功或失败。如果你插入订单并减少库存，这两个操作是原子的。要么都发生，要么都不发生。

Elasticsearch can’t make that guarantee beyond a single document. Writes succeed independently, and potentially out of order. If one fails from a logical group, you’re left with half an operation applied. At first, teams add retries or reconciliation jobs, trying to patch over the gaps. But this is the moment Elasticsearch stops behaving like a database. A system of record shouldn’t ever let inconsistencies creep in over time.
Elasticsearch 无法在单个文档之外提供这种保证。写入操作独立成功，并且可能出现乱序。如果逻辑组中的某个操作失败，则操作只剩下一半。起初，团队会添加重试或协调作业，试图弥补差距。但这正是 Elasticsearch 不再像数据库那样运作的时刻。记录系统不应该让不一致的情况随着时间的推移而逐渐出现。

You can see the same problem on the read side. Elasticsearch actually has two kinds of reads: `GET` by ID and `SEARCH`. A `GET` always returns the latest acknowledged version of a document, mirroring how databases work (although under failure-cases [dirty reads are possible](https://www.elastic.co/docs/deploy-manage/distributed-architecture/reading-and-writing-documents)). A `SEARCH`, however, only looks at Lucene segments, which are refreshed asynchronously. That means a recently acknowledged write may not show up until the next refresh.
您可以在读取方面看到同样的问题。Elasticsearch 实际上有两种读取方式：通过 ID 进行 `GET` 和 `SEARCH` 。 `GET` 始终返回文档的最新确认版本，这与数据库的工作方式相似（尽管在故障情况下[可能会出现脏读 ](https://www.elastic.co/docs/deploy-manage/distributed-architecture/reading-and-writing-documents)）。然而， `SEARCH` 仅查看异步刷新的 Lucene 段。这意味着最近确认的写入可能要等到下次刷新才会显示。

Databases solve these issues with transaction boundaries and isolation levels. Elasticsearch has neither, because it doesn’t need them to be an effective search engine.
数据库通过事务边界和隔离级别来解决这些问题。Elasticsearch 则没有，因为它不需要它们就能成为一个高效的搜索引擎。

## 需要重新索引的数据库模式迁移 <br>Schema Migrations That Need Reindexes

Then the application changes. A field that was once an integer now needs decimals. A text field is renamed. In Postgres or MySQL, this would be a straightforward `ALTER TABLE`. In Elasticsearch, [index mappings](https://www.elastic.co/docs/manage-data/data-store/mapping#mapping-manage-update) are immutable once set, so sometimes the [only option](https://www.elastic.co/docs/manage-data/migrate) is to create a new index with the updated mapping and transfer every document into it.
随后，应用程序发生了变化。一个曾经是整数的字段现在需要小数。一个文本字段被重命名。在 Postgres 或 MySQL 中，这只需要一个简单的 `ALTER TABLE` 语句。在 Elasticsearch 中，[ 索引映射](https://www.elastic.co/docs/manage-data/data-store/mapping#mapping-manage-update)一旦设置就不可更改，因此有时[唯一的选择](https://www.elastic.co/docs/manage-data/migrate)是创建一个具有更新映射的新索引，并将所有文档都转移到其中。

When Elasticsearch is downstream of another database this is painful (a full network transfer) but safe, you can replay from the real source of truth. But when Elasticsearch is the only store, schema migrations require moving the entire system of record into a new structure, under load, with no safety net (other than a restore). What should be a routine schema change can become a high-risk operation.
当 Elasticsearch 位于另一个数据库的下游时，这很麻烦（需要完整的网络传输），但很安全，您可以从真实的数据源进行回放。但是，当 Elasticsearch 是唯一的存储时，模式迁移需要将整个记录系统迁移到新的结构中，在负载下，没有任何安全保障（除了恢复之外）。原本应该是常规的模式更改，现在却变成了高风险操作。

## 不带连接的查询 <br>Queries Without Joins 

Once Elasticsearch is the primary store, developers naturally want more than just search. They want to ask questions of the data. This is where you start to hit another wall.
一旦 Elasticsearch 成为主要存储，开发人员自然会想要的不仅仅是搜索。他们想要对数据进行探索。这时，你就会开始遇到另一个障碍。

Elasticsearch’s JSON-based Query DSL is powerful for full-text queries and aggregations, but limited for relational workloads. In Elastic’s [own words](http://elastic.co/docs/explore-analyze/query-filter/languages/querydsl), it “enables complex searching, filtering, and aggregations,” but if you want to move beyond that, the cracks show. Features you’d expect from a system of record (like basic joins) are either missing or only partially supported.
Elasticsearch 基于 JSON 的查询 DSL 对于全文查询和聚合来说非常强大，但对于关系型工作负载来说却有些受限。用 Elastic[ 自己的话](http://elastic.co/docs/explore-analyze/query-filter/languages/querydsl)来说，它“支持复杂的搜索、过滤和聚合”，但如果你想更进一步，就会发现它的不足之处。你期望从记录系统获得的功能（例如基本连接）要么缺失，要么仅部分支持。

Consider the following SQL query:
考虑以下 SQL 查询：

```sql
-- What are the top ten products by average review rating,
-- only considering products with at least 50 reviews

SELECT p.id, p.name, AVG(r.rating) AS avg_rating
FROM products p
JOIN reviews r ON r.product_id = p.id
GROUP BY p.id, p.name
HAVING COUNT(r.id) >= 50
ORDER BY avg_rating DESC
LIMIT 10;
```

In Postgres, this is routine. In Elasticsearch, your options are clumsy: denormalize reviews into each product document (rewriting the product on every new review), embed reviews in products as children, or query both indexes separately and stitch the results back together in application code.
在 Postgres 中，这是常规操作。但在 Elasticsearch 中，你的选择就比较笨拙了：将评论非规范化地放入每个产品文档中（每次新增评论时都重写产品信息），将评论作为子项嵌入到产品中，或者分别查询两个索引，然后在应用程序代码中将结果拼接在一起。

Elastic has been working on this gap. The more recent ES|QL introduces a [similar feature called lookup joins](https://www.elastic.co/blog/esql-lookup-join-elasticsearch), and Elastic SQL provides a more familiar syntax (with [no joins](https://www.elastic.co/docs/reference/query-languages/sql/sql-syntax-select)). But these are still bound by Lucene’s underlying index model. On top of that, developers now face a confusing sprawl of overlapping query syntaxes (currently: [Query DSL](https://www.elastic.co/docs/explore-analyze/query-filter/languages/querydsl),[ES|QL](https://www.elastic.co/docs/explore-analyze/query-filter/languages/esql-kibana),[SQL](https://www.elastic.co/docs/explore-analyze/query-filter/languages/sql),[EQL](https://www.elastic.co/docs/explore-analyze/query-filter/languages/eql),[KQL](https://www.elastic.co/docs/explore-analyze/query-filter/languages/kql)), each suited to different use cases, and with different strengths and weaknesses.
Elastic 一直在努力弥补这一差距。较新的 ES|QL 引入了[类似的功能，称为查找连接 (lookup joins)](https://www.elastic.co/blog/esql-lookup-join-elasticsearch) ，而 Elastic SQL 则提供了更常见的语法（[ 无连接 ](https://www.elastic.co/docs/reference/query-languages/sql/sql-syntax-select)）。但这些语法仍然受限于 Lucene 的底层索引模型。此外，开发人员现在面临着各种相互重叠的查询语法（目前包括：[Query DSL](https://www.elastic.co/docs/explore-analyze/query-filter/languages/querydsl)、[ES|QL](https://www.elastic.co/docs/explore-analyze/query-filter/languages/esql-kibana)、[SQL](https://www.elastic.co/docs/explore-analyze/query-filter/languages/sql)，[EQL](https://www.elastic.co/docs/explore-analyze/query-filter/languages/eql)、[KQL](https://www.elastic.co/docs/explore-analyze/query-filter/languages/kql)）的困扰，每种语法都适用于不同的用例，并且各有优缺点。

It is progress, but not parity with a relational database.
这是进步，但与关系数据库还不相等。

## 可靠性可能不足 <br>Reliability That Can Fall Short 

Eventually every system fails. The difference between an index and a database is how they recover. Databases use write-ahead or redo logs to guarantee that once a transaction is committed, *all* of its changes are durable and will replay cleanly after a crash.
每个系统最终都会失败。索引和数据库的区别在于它们的恢复方式。数据库使用预写日志或重做日志来保证事务提交后， *所有*更改都是持久的，并且在崩溃后能够干净地重放。

Under normal operation Elasticsearch is also durable at the level it was designed for: individual document writes. The [translog](https://www.elastic.co/docs/reference/elasticsearch/index-settings/translog) ensures acknowledged docs are fsynced on the primary shard, can survive crashes, and can be replayed on recovery. But, as we saw with transactions, that durability doesn’t extend beyond a single document. There are no transaction boundaries to guarantee that related writes survive or fail together (because that concept simply doesn’t exist). A failure can leave half-applied operations, and recovery won’t roll them back the way a database would.
在正常运行情况下，Elasticsearch 的持久性也体现在其设计初衷：单个文档写入。[ 事务日志 (translog](https://www.elastic.co/docs/reference/elasticsearch/index-settings/translog) ) 确保已确认的文档在主分片上同步，能够在崩溃后继续执行，并在恢复时重放。但是，正如我们在事务中看到的那样，这种持久性并不扩展到单个文档之外。没有事务边界来保证相关的写入操作能够同时存活或失败（因为这个概念根本不存在）。故障可能会留下未完成的操作，并且恢复时不会像数据库那样回滚它们。

That assumption is fine when Elasticsearch is an index layered on top of a database. If it’s your only store, though, the gap in transactional durability becomes a gap in correctness. Outages don’t just slow down search, they put your system of record at risk.
当 Elasticsearch 是数据库之上的索引层时，这种假设是合理的。但如果它是你唯一的存储，那么事务持久性的缺口就变成了正确性的缺口。宕机不仅会减慢搜索速度，还会危及你的记录系统。

## 破坏稳定的行为 <br>Operations That Strain Stability 

Operating Elasticsearch at scale introduces another reality check. Databases are supposed to be steady foundations: you run them, monitor them, and trust they’ll keep your data safe. Elasticsearch was designed for a different priority: elasticity. Shards can move, clusters can grow and shrink, and data can be reindexed or rebalanced. That flexibility is powerful, but distributed systems come with operational tradeoffs. Shards drift out of balance, JVM heaps demand careful tuning, reindexing consumes cluster capacity, and rolling upgrades can stall traffic.
大规模运行 Elasticsearch 需要面对另一个现实考验。数据库本应是稳固的基础：您运行它们、监控它们，并相信它们会保障数据安全。Elasticsearch 的设计初衷并非如此：弹性。分片可以移动，集群可以扩展或收缩，数据可以重新索引或重新平衡。这种灵活性固然强大，但分布式系统也存在一些运维上的权衡。分片可能会失去平衡，JVM 堆需要仔细调优，重新索引会消耗集群容量，滚动升级可能会造成流量拥堵。

Elastic has added tools to ease these challenges, and many teams do run large clusters successfully. But the baseline expectation is different. A relational database is engineered for stability and correctness because it assumes it will be your source of truth. Elasticsearch is [“optimized for speed and relevance”](https://www.elastic.co/docs/get-started/), and running it also as a system of record means accepting more operational risk than a database would impose.
Elastic 增加了一些工具来缓解这些挑战，许多团队也确实成功运行了大型集群。但基准预期有所不同。关系数据库的设计目标是稳定性和正确性，因为它假设自己将成为您的数据来源。Elasticsearch 则 [“针对速度和相关性进行了优化”](https://www.elastic.co/docs/get-started/) ，将其作为记录系统运行意味着要承担比数据库更大的运营风险。

## 滥用的代价 <br>The Cost of Misuse

Elasticsearch is already complex to operate and heavy on resources. When you try to make it your primary database as well, both of those costs are magnified. Running on a single system feels like a simplification, but it often makes everything harder because you have two different optimization goals.
Elasticsearch 本身就操作复杂，资源占用大。如果尝试将其作为主数据库，则上述成本会进一步放大。在单个系统上运行看似简单，但实际上却往往让一切变得更加困难，因为您有两个不同的优化目标。

Transaction gaps, brittle migrations, limited queries, complex operations, and workarounds all pile up. Instead of reducing complexity, you’ve concentrated it in the most fragile place possible. The result is worse than your original solution: increased engineering effort, higher operational cost, and still none of the guarantees you would expect from a source of truth.
事务缺口、脆弱的迁移、有限的查询、复杂的操作以及各种变通方案层出不穷。您非但没有降低复杂性，反而将其集中在最脆弱的地方。结果比您最初的解决方案更糟糕：工程工作量增加，运营成本上升，而且仍然得不到任何您期望从事实来源获得的保证。

## 如此一来，Elasticsearch 该何去何从？ <br>So Where Does That Leave Elasticsearch? 

Honestly, that leaves it right where it should be, and where it started: a search engine. Elasticsearch (and Apache Lucene under it) is an incredible achievement, bringing world-class search to developers everywhere. As long as you’re not trying to use it as a system of record, it does exactly what it was built for.
说实话，这让它回到了它本该在的位置，回到了它最初的起点：一个搜索引擎。Elasticsearch（以及它旗下的 Apache Lucene）是一项了不起的成就，为世界各地的开发者带来了世界一流的搜索功能。只要你不打算把它当作一个记录系统，它就能完全发挥它的初衷。

Even when used “correctly”, though, the hardest part often isn’t search itself, it’s everything around it. ETL pipelines, sync jobs, and ingest layers quickly become the most fragile parts of the stack.
即使“正确”使用，最难的部分往往不是搜索本身，而是围绕它的一切。ETL 管道、同步作业和采集层很快就会成为堆栈中最脆弱的部分。

That’s where ParadeDB comes in. Run it as your primary database, combining OLTP and full-text search in one system, or keep your existing Postgres database and eliminate ETL by deploying it as a logical follower.
这就是 ParadeDB 的用武之地。将其作为主数据库运行，在一个系统中结合 OLTP 和全文搜索，或者保留现有的 Postgres 数据库并通过将其部署为逻辑跟随者来消除 ETL。

If you want open-source search with correctness, simplicity, and world-class performance, [get started with ParadeDB](https://paradedb.com/).
如果您想要具有正确性、简单性和世界一流性能的开源搜索，[ 请从 ParadeDB 开始 ](https://paradedb.com/)。