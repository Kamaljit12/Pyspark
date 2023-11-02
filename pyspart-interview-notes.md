# PySpark Interview  notes

# 1. Question: What are the fundamental concepts that form the basis of Apache Spark?
#### Explanation:
The fundamental concepts of Apache Spark include Resilient
Distributed Datasets (RDD), transformations, actions, Spark SQL, Spark
Streaming, and machine learning libraries (MLlib). RDD is an immutable
distributed collection of objects that can be processed in parallel.
Transformations are operations applied to a dataset to create a new one,
while actions are operations that provide non-RDD values. Spark SQL
provides structured data processing. Spark Streaming enables scalable and
fault-tolerant stream processing of live data. MLlib is Spark's machine learning
library.
# 2. Question: Explain the architecture of Apache Spark.
#### Explanation: 
Spark uses a master/worker architecture. There is a driver node
that runs the main() function of the program and worker nodes that run all
other computations. The driver program splits the Spark application into tasks
and schedules them to run on executors. The driver and worker nodes
communicate through a Cluster Manager, which can be YARN, Mesos, or
Spark's standalone cluster manager.
# 3. Question: What are the different deployment modes in Spark?
#### Explanation: 
Spark supports two deployment modes: client mode and cluster
mode. In client mode, the driver runs on the machine that the job was
submitted from. In cluster mode, the driver runs on a random worker node in
the cluster.
# 4. Question: What is the role of SparkContext and Application Master in a Spark application?
#### Explanation: 
SparkContext is the entry point of any Spark application, and it
establishes a connection to the Spark cluster. The Application Master is
responsible for negotiating resources from the cluster manager and working
with the NodeManager(s) to execute and monitor tasks.
# 5. Question: What are Containers and Executors in Spark?
#### Explanation: 
In Spark, a container is a YARN concept, which is essentially a
slice of a machine's resources (RAM, CPU, etc.) allocated to an application.
An executor is a JVM process launched for a Spark application on a node.
Each executor has a set of cores and a heap space assigned, which it uses to
execute tasks.
# 6. Question: How does resource allocation happen in Spark?
#### Explanation: 
In Spark, resource allocation can be either static or dynamic. In
static allocation, Spark reserves a fixed set of resources on the cluster at the
start of an application and keeps them for its entire duration. Dynamic
Grow Data Skil s
allocation allows Spark to dynamically scale the set of resources it uses
based on the workload, reducing them if some are not used for a long time.
# 7. Question: Explain the difference between transformations and actions in Spark.
#### Explanation: 
Transformations are operations on RDDs that return a new
RDD, like map() and filter(). Actions are operations that return a final value to
the driver program or write data to an external system, like count() and first().
# 8. Question: What is a Job, Stage, and Task in Spark?
#### Explanation: 
A Job is parallel computation consisting of multiple tasks that
get spawned in response to a Spark action (like save, collect). A Stage is a
sequence of transformations on an RDD or DataFrame/Dataset that can be
done in a single pass, i.e., without shuffling all the data around. Tasks are the
smallest unit of work, one task per RDD partition.
# 9. Question: How does Spark achieve parallelism?
#### Explanation: 
Spark achieves parallelism by dividing the data into partitions
and processing them in parallel across different nodes in the cluster. The
number of partitions is configurable and tuning it correctly is essential for
Spark performance.
# 10.Question: What is data skewness in Spark and how to handle it?
#### Explanation: 
Data skewness in Spark is a situation where a single task takes
a lot longer to read its partition of data than the other tasks. It can be handled
by techniques such as splitting skewed data into multiple partitions, using
salting, or replicating the small DataFrame when performing a join operation.
#11. Question: What is the salting technique in Spark?
#### Explanation: 
Salting is a technique to mitigate data skewness. It involves
adding a random key to the data so that instead of one big partition, you have
multiple smaller partitions spread across several workers.
# 12.Question: What is a Lineage Graph in Spark?
#### Explanation: 
A Lineage graph is a sequence of operations on RDDs that can
be reconstructed in case of a data loss. It's a way to achieve fault tolerance in
Spark.
# 13.Question: Can you explain RDD, DataFrames, and Datasets in Spark?
#### Explanation: 
RDD is a fundamental data structure of Spark, an immutable
distributed collection of objects. DataFrames and Datasets are built on top of
RDDs. DataFrame is a distributed collection of data organized into named
columns. It's conceptually equivalent to a table in a relational database.
Datasets provide the benefits of RDDs (strong typing, ability to use powerful
Grow Data Skil s
lambda functions) with the benefits of Spark SQL's optimized execution
engine.
# 14.Question: What is spark-submit used for?
#### Explanation: 
spark-submit is a command-line interface for submitting
standalone applications which you'd like to run on the Spark cluster. It allows
you to specify the application's main class, the Spark master URL, any
additional Spark properties, and the application JAR or Python files to add to
the environment.
# 15.Question: Explain Broadcast and Accumulator variables in Spark.
#### Explanation: 
Broadcast variables are read-only variables that are cached on
each worker node rather than sending a copy of the variable with tasks. They
can be used to give nodes access to a large input dataset efficiently.
Accumulators are variables that can be added through an associative and
commutative operation and are used for counters or sums. Spark natively
supports accumulators of numerical types, and programmers can add support
for new types.
# 16.Question: Can you describe how data shuffling affects Spark's performance, and how can it be managed?
#### Explanation: 
Data shuffling is the process of redistributing data across
partitions, which may cause data to be transferred across the nodes of a
cluster. It's an expensive operation that can cause a significant performance
bottleneck due to high disk I/O, serialization, and network communication.
Techniques to manage data shuffling include minimizing operations that cause
shuffling (like groupByKey) and using operations that can limit shuffling (like
reduceByKey, foldByKey).
# 17.Question: What's the difference between persist() and cache() in Spark?
#### Explanation: 
Both persist() and cache() methods are used to persist an
RDD/Dataset/DataFrame. The cache() method is a synonym for persist() with
the default storage level (MEMORY_AND_DISK). However, persist() allows
the user to specify the storage level (whether to store the data in memory, on
disk, or both, and whether to serialize the data or not).
# 18.Question: What does the coalesce method do in Spark?
#### Explanation: 
Coalesce method in Spark is used to reduce the number of
partitions in an RDD/DataFrame. It's typically used to avoid a full shuffle. If
you're decreasing the number of partitions in your data, use coalesce. If
increasing, use repartition, as it involves a full shuffle.
# 19.Question: How do you handle late arrival of data in Spark Streaming?
#### Explanation: 
Spark Streaming supports windowed computations, where
transformations on RDDs are applied over a sliding window of data. Late data
can be handled with Spark's support for windowed computations and
updateStateByKey operation, which allows you to maintain arbitrary state
while continuously updating it with new information.
# 20.Question: How do you handle data loss in Spark Streaming?
#### Explanation: 
Spark Streaming provides a write-ahead log feature that can be
enabled to save all the received data to fault-tolerant storage systems (like
HDFS). This allows recovery of data from failure by replaying the logs.
# 21.Question: What is the role of the Catalyst framework in Spark?
#### Explanation: 
Catalyst is Spark's query optimization framework. It allows
Spark to automatically transform SQL queries by adding, reordering, and
optimizing steps in the query plan to generate efficient execution plans. It
uses techniques like predicate pushdown, column pruning, and other
rule-based optimizations to optimize queries.
# 22.Question: How does Dynamic Resource Allocation work in Spark?
#### Explanation: 
Dynamic Resource Allocation allows a Spark application to
release resources it doesn't currently need and request them again later when
there's demand. This feature is designed for use with the Shuffle Service,
which allows executors to be removed without losing shuffle data.
# 23.Question: How is fault-tolerance achieved in Spark Streaming?
#### Explanation: 
Fault-tolerance in Spark Streaming is achieved by the
checkpointing mechanism. Checkpointing periodically saves the state of a
streaming application to a storage system, so it can recover from failures. It
updates the metadata information of RDD lineage, RDD operations, and
driver variables to a checkpoint directory.
# 24.Question: How does Spark handle memory management?
#### Explanation: 
Spark uses a combination of on-heap and off-heap memory
management techniques. On-heap memory management uses JVM heap
memory for storage and processing, which could lead to high garbage
collection costs. Off-heap memory management keeps the data outside of the
garbage collector managed area, reducing garbage collection overhead.
# 25.Question: What is the role of the block manager in Spark?
#### Explanation: 
The block manager in Spark is responsible for managing the
storage of data in the memory and disk of the worker nodes. It helps with
storing and retrieving blocks, and it interacts with the driver node to handle
requests.
Grow Data Skil s
# 26.Question: Explain the concept of lineage in Spark.
#### Explanation: 
Lineage in Spark is a sequence of transformations on the data
from the start of the computation to the end. It helps to keep track of the
transformations applied to an RDD and recover lost data without the need for
replication. If any partition of an RDD is lost due to a failure, Spark can
recompute it from the lineage information.
# 27.Question: How does garbage collection impact Spark performance?
#### Explanation: 
Garbage collection (GC) could significantly impact Spark's
performance. Long GC pauses could make Spark tasks slow, lead to
timeouts, and cause failures. GC tuning, including configuring the right GC
algorithm and tuning GC parameters, is often required to optimize Spark
performance.
# 28.Question: Explain the process of how a Spark job is submitted and executed.
#### Explanation: 
When a Spark job is submitted, the driver program's main()
function is run. This driver program contains the job's main control flow and
creates RDDs on the cluster, then applies operations to those RDDs. The
driver program then splits the Spark application into tasks and schedules
them on executors.
# 29.Question: How can broadcast variables improve Spark's performance?
#### Explanation: 
Broadcast variables can improve Spark's performance by
reducing the communication cost. When a large read-only dataset is sent to
each worker node with tasks, it's more efficient to send it once and keep it
there as a broadcast variable rather than send it with every task.
# 30.Question: What is the significance of the SparkSession object?
#### Explanation: 
SparkSession is a combined entry point to any Spark
functionality for a Spark application. It allows programming Spark with the
Dataset and DataFrame API, and also includes the ability to act as a
distributed SQL query engine. It allows the creation of DataFrame objects,
and it's also the entry point for reading data stored in Hive.
# 31.Question: How do you handle unbalanced data or data skew in Spark?
#### Explanation: 
Data skew can lead to scenarios where some tasks take much
longer to complete than others. Strategies to handle this include salting
(adding a random key to the data), using broadcast variables for smaller
skewed data, and partitioning the data better to spread it more evenly across
nodes.
# 32.Question: How can you minimize data shuffling and spill in Spark?
#### Explanation: 
Minimizing data shuffling can be done by choosing
transformations wisely. For instance, using transformations like reduceByKey
Grow Data Skil s
or aggregateByKey, which combine output with a common key on each
partition before shuffling the data, instead of groupByKey. Spill can be
minimized by increasing the amount of memory allocated to Spark or tuning
the data structures used in your job to be more memory-efficient.
# 33.Question: What are narrow and wide transformations in Spark and why do they matter?
#### Explanation: 
Narrow transformations are those where each input partition will
contribute to only one output partition. Examples include map(), filter(), and
union(). Wide transformations, or shuffle transformations, are those where
each input partition can contribute to multiple output partitions. Examples
include groupByKey() and reduceByKey(). Wide transformations involve
shuffling all the data across multiple partitions and hence are more costly.
# 34.Question: Explain Spark's Lazy Evaluation. What's the advantage of it?
#### Explanation: 
Spark uses a technique called lazy evaluation, where the
execution doesn't start until an action is triggered. In Spark, the
transformations are lazy, meaning that they do not compute their results right
away, but they just remember the transformations applied to some base
dataset. The advantage of lazy evaluation is that it saves computation and
makes Spark more efficient.
# 35.Question: How does Spark handle large amounts of data?
#### Explanation: 
Spark can handle large amounts of data using partitioning. Data
in Spark is divided into partitions, which are smaller and more manageable
chunks of data that can be processed in parallel. This allows for distributed
processing of large datasets across a cluster.
# 36.Question: What is the use of the Spark Driver in a Spark Application?
#### Explanation: 
The driver program in a Spark application runs the main()
function and creates a SparkContext. It splits the Spark application into tasks
and schedules them on the executor. It is also responsible for the execution of
the Job and Stage scheduling.
# 37.Question: How does Spark ensure data persistence?
#### Explanation: 
Spark provides two methods for persisting data, cache() and
persist(). These methods allow an RDD to be persisted across operations
after the first time it is computed. They help save the results of RDD
evaluations, storing them in memory or on disk, which can then be reused in
subsequent stages.
# 38.Question: What are some common performance issues in Spark applications?
#### Explanation: 
Some common performance issues in Spark applications
include data skew, overuse of wide transformations like groupByKey,
excessive number of small tasks, extensive shuffling of data, and insufficient
memory causing excessive garbage collection or disk spilling.
# 39.Question: How can we tune Spark Jobs for better performance?
#### Explanation: 
Spark job performance can be improved by a combination of
various methods like minimizing shuffling of data, managing memory properly,
increasing parallelism by adjusting the number of partitions, using broadcast
variables for small data, avoiding the overuse of wide transformations, and
caching intermediate data.
# 40.Question: How do Spark's advanced analytics libraries (MLlib, GraphX) enhance its capabilities?
#### Explanation: 
MLlib is Spark's machine learning library, which makes machine
learning scalable and easy with common learning algorithms and utilities.
GraphX is Spark's API for graph computation, providing graph-parallel
computation. Both of these provide Spark with capabilities to handle complex
analytics tasks.
# 41.Question: How does Spark use DAGs for task scheduling?
#### Explanation: 
Spark uses a directed acyclic graph (DAG) for scheduling tasks.
A DAG is a sequence of computations performed on data. For each action,
Spark creates a DAG and submits it to the DAG Scheduler. The DAG
Scheduler divides the graph into stages of tasks, and tasks are bundled
together into stages.
# 42.Question: What's the difference between Spark's 'repartition' and 'coalesce'?
#### Explanation: 
Both repartition and coalesce are used to modify the number of
partitions in an RDD. repartition can increase or decrease the number of
partitions, but it shuffles all data. coalesce only decreases the number of
partitions. As coalesce avoids full data shuffling, it's more efficient than
repartition when reducing the number of partitions.
# 43.Question: Can you explain how Spark's Machine Learning libraries help with predictive analysis?
#### Explanation: 
Spark's machine learning libraries, MLlib and ML, provide
various machine learning algorithms like classification, regression, clustering,
and collaborative filtering, as well as model evaluation tools. They help build
predictive models, which are key to predictive analysis.
# 44.Question: How can you handle node failures in Spark?
#### Explanation: 
Spark handles node failures by re-computing the lost data.
Since Spark keeps track of each RDD's lineage information, it knows how to
Grow Data Skil s
re-compute lost data. If a task fails, Spark will attempt to re-run it, potentially
on a different node.
# 45.Question: How does 'reduceByKey' work in Spark?
#### Explanation: 
reduceByKey is a transformation in Spark that transforms a pair
of (K, V) RDD into a pair of (K, V) RDD where values for each key are
aggregated using a reduce function. It works by first applying the reduce
function locally to each partition, and then across partitions, allowing it to
scale efficiently.
# 46.Question: How does the 'groupByKey' transformation work in Spark? How is it different from 'reduceByKey'?
#### Explanation: 
groupByKey is a transformation in Spark that groups all the
values of a PairRDD by the key. Unlike reduceByKey, it doesn't perform any
aggregation, which can make it less efficient due to unnecessary shuffling of
data.
# 47.Question: Explain the significance of 'Partitions' in Spark.
#### Explanation: 
Partitions in Spark are chunks of data that can be processed in
parallel. They are the units of parallelism in Spark and can reside on different
nodes in a cluster. By increasing the number of partitions, you can increase
the level of parallelism, improving performance.
# 48.Question: How does Spark SQL relate to the rest of Spark's ecosystem?
#### Explanation: 
Spark SQL provides the ability to query structured and
semi-structured data using SQL, as well as through the DataFrame API. It is
deeply integrated with the rest of Spark's ecosystem, allowing you to use it
alongside other Spark libraries like MLlib and GraphX. It also supports a wide
array of data sources and can work with Hive and Parquet.
# 49.Question: How can memory usage be optimized in Spark?
#### Explanation: 
Memory usage in Spark can be optimized through techniques
like broadcasting large read-only variables, caching RDDs and DataFrames
selectively, and tuning the amount of memory used for shuffling and caching.
Additionally, you can also adjust the fraction of JVM heap space reserved for
Spark, and use off-heap memory.
# 50.Question: What is a 'Shuffle' operation in Spark? How does it affect performance?
#### Explanation: 
Shuffle is the process of redistributing data so that each output
partition receives data from all input partitions. This involves copying data
across the network and disk I/O, which can be expensive and affect
performance. Shuffle operations typically occur during transformations like
groupByKey and reduceByKey.
# 51.Question: What is the 'Stage' concept in Spark?
#### Explanation: 
A Stage in Spark is a sequence of transformations on an RDD
or DataFrame that can be performed in a single pass, i.e., without shuffling
the data. A job gets divided into stages on the basis of transformations.
Stages are essentially tasks that can run in parallel.
# 52.Question: How do Accumulators work in Spark?
#### Explanation: 
Accumulators are variables that are used to accumulate
information across transformations, like counters in MapReduce. They can be
used in transformations, but the results are only guaranteed to be updated
once for each task for transformations.
# 53.Question: How does a 'SparkContext' relate to a 'SparkSession'?
#### Explanation: 
SparkContext represents the connection to a Spark cluster and
is used to create RDDs, accumulators, and broadcast variables. In Spark 2.0
and later, SparkSession provides a single point of entry for DataFrame and
Dataset APIs and is used to create and manage datasets and dataframes.
SparkSession internally has a SparkContext.
# 54.Question: What is 'YARN' in the context of Spark?
#### Explanation: 
YARN (Yet Another Resource Negotiator) is one of the cluster
managers that Spark can run on. YARN allows different data processing
engines like Spark to run and share resources in the same Hadoop cluster.
# 55.Question: Explain what 'executor memory' in spark is. How is it divided?
#### Explanation: 
Executor memory in Spark refers to the amount of memory that
will be allocated to each executor for running tasks. It is divided into Spark
memory and User memory. Spark memory includes storage memory (for
caching and propagating internal data) and execution memory (for
computation in shuffles, joins, sorts, and aggregations). User memory is
reserved for user data structures and computations.
# 56.Question: Explain the role of 'Worker Nodes' in Spark.
#### Explanation: 
Worker nodes refer to any node that can run application code in
a cluster. In Spark, worker nodes run the executors that are responsible for
running the tasks. They communicate with the driver program and are
managed by the cluster manager.
# 57.Question: What are some common reasons for a Spark application to run out of memory?
#### Explanation: 
Some common reasons for a Spark application to run out of
memory include: too much data being processed at once, excessive overhead
Grow Data Skil s
for data structures, large amounts of data being shuffled during operations,
and inefficient use of data structures and transformations.
# 58.Question: How do you handle increasing data volume during a Spark job?
#### Explanation:
When dealing with increasing data volume, you can use a few
strategies: you can increase the level of parallelism by increasing the number
of partitions, you can tune the Spark configuration for better memory
management, you can repartition your data to ensure it's distributed evenly,
and you can ensure you're using transformations that minimize shuffling.
# 59.Question: Explain 'Speculative Execution' in Spark.
#### Explanation: 
Speculative execution in Spark is a feature where Spark runs
multiple copies of the same task concurrently on different worker nodes. This
is to handle situations where a task is running slower than expected (due to
hardware issues or other problems). If one of the tasks finishes before the
others, the result is returned, and the other tasks are killed, potentially saving
time.
# 60.Question: How can you manually partition data in Spark?
#### Explanation: 
You can manually partition data in Spark using transformations
like repartition() and partitionBy(). repartition() can be used with RDDs and
DataFrames to increase or decrease the number of partitions. With PairRDDs,
you can use partitionBy(), which takes a Partitioner.
# 61.Question: What is 'backpressure' in Spark Streaming?
#### Explanation: 
Backpressure is a feature in Spark Streaming which
automatically adjusts the rate of incoming data based on the processing
capacity of the cluster. It helps prevent the system from being overwhelmed
by too much data. Backpressure can be enabled by setting the
'spark.streaming.backpressure.enabled' configuration property to true.
# 62.Question: How can we leverage Spark's GraphX library for graph processing?
#### Explanation: 
GraphX is a graph computation library of Spark. It provides
APIs for expressing graph computation that can model user-defined graphs by
using Pregel abstraction API. It also optimizes the execution in a variety of
graph-specific optimizations.
# 63.Question: What are the differences between persist() and cache() in Spark?
#### Explanation: 
Both persist() and cache() are used to save the RDD results.
The difference between them lies in their default storage level. cache() is a
synonym for persist(), but with the default storage level set to
MEMORY_ONLY, whereas persist() allows you to choose the storage level
(like MEMORY_AND_DISK, DISK_ONLY, etc.).





