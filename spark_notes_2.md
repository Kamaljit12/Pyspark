# Spark Notes - 2
<img src = 'https://github.com/Kamaljit12/Pyspark/blob/main/images/1_nPcdyVwgcuEZiEZiRqApug.jpg'>

# Persist and Caching in Spark
#### The methods persist() and cache() in Apache Spark are used to save the RDD, DataFrame, or Dataset in memory for faster access during computation. They are effectively ways to optimize the execution of your Spark jobs, especially when you have repeated transformations on the same data. However, they differ in how they handle the storage:
- cache():
  - This method is a shorthand for using persist() with the default storage level. In other words, cache() is 
equivalent to calling persist() without any parameters. The default storage level is MEMORY_AND_DISK in 
PySpark and MEMORY_AND_DISK_SER in Scala Spark. This means that the RDD, DataFrame, or Dataset is 
stored in memory and, if it doesn't fit, the excess partitions are stored on disk.
- persist(storageLevel):
  - This method allows you to control how the data should be stored. You can pass a storage 
level as an argument to the persist() function, which gives you finer control over how the data is persisted. The 
storage level can be one of several options, each of which offers different trade-offs of memory usage and CPU 
efficiency, and can use either memory or disk storage or both.
# Storage Levels in Persist 
- StorageLevel.DISK_ONLY:
  - Store the RDD partitions only on disk.
- StorageLevel.DISK_ONLY_2:
  - Same as the DISK_ONLY, but replicate each partition on two cluster nodes.
- StorageLevel.MEMORY_AND_DISK:
  - Store RDD as deserialized objects in the JVM. If the RDD does not fit in memory, store 
the partitions that don't fit on disk, and read them from there when they're needed.
- StorageLevel.MEMORY_AND_DISK_2:
  - Similar to MEMORY_AND_DISK, but replicate each partition on two cluster nodes.
- StorageLevel.MEMORY_AND_DISK_SER:
  - Store RDD as serialized Java objects (one byte array per partition). This is generally 
more space-efficient than deserialized objects, especially when using a fast serializer, but more CPU-intensive to read.
- StorageLevel.MEMORY_AND_DISK_SER_2:
  - Similar to MEMORY_AND_DISK_SER, but replicate each partition on two cluster 
nodes.
- StorageLevel.MEMORY_ONLY:
  - Store RDD as deserialized Java objects in the JVM. If the RDD does not fit in memory, some 
partitions will not be cached and will be recomputed on the fly each time they're needed.
- StorageLevel.MEMORY_ONLY_2:
  - Similar to MEMORY_ONLY, but replicate each partition on two cluster nodes.
- StorageLevel.MEMORY_ONLY_SER:
  - Store RDD as serialized Java objects (one byte array per partition). This is more 
space-efficient, but more CPU-intensive to read.
- StorageLevel.MEMORY_ONLY_SER_2:
  - Similar to MEMORY_ONLY_SER, but replicate each partition on two cluster nodes.
  
# How does data skewness occur in Spark?
#### Data skewness in Spark occurs when the data is not evenly distributed across partitions. This often happens when certain keys in your data have many more values than others. Consequently, tasks associated with these keys take much longer to run than others, which can lead to inefficient resource utilization and longer overall job execution time.
## Here are a few scenarios where data skewness can occur:
- Join Operations:
  - When you perform a join operation on two datasets based on a key, and some keys have significantly 
more values than others, these keys end up having larger partitions. The tasks processing these larger partitions will take 
longer to complete.
- GroupBy Operations:
  - Similar to join operations, when you perform a groupByKey or reduceByKey operation, and some 
keys have many more values than others, data skewness can occur.
- Data Distribution:
  - If the data distribution is not uniform, such that certain partitions get more data than others, then data 
skewness can occur. This could happen due to the nature of the data itself or the partitioning function not distributing the 
data evenly.

# How to deal with data skewness ?
#### Handling data skewness is a common challenge in distributed computing frameworks like Apache Spark. Here are some popular techniques to mitigate it:
- Salting:
  - Salting involves adding a random component to a skewed key to create additional unique keys. After performing 
the operation (like a join), the extra key can be dropped to get back to the original data.
- Splitting skewed data:
  - Identify the skewed keys and process them separately. For instance, you can filter out the skewed 
keys and perform a separate operation on them.
- Increasing the number of partitions:
  - Increasing the number of partitions can distribute the data more evenly. However, 
this might increase the overhead of managing more partitions.
- Using reduceByKey instead of groupByKey:
  - reduceByKey performs local aggregation before shuffling the data, which 
reduces the data transferred over the network.
- Using Broadcast Variables:
  - When joining a large DataFrame with a small DataFrame, you can use broadcast variables to 
send a copy of the small DataFrame to all nodes. This avoids shuffling of the large DataFrame.

# Difference between Repartition & Coalesce in Spark repartition():
- This method is used to increase or decrease the number of partitions in an RDD or DataFrame.
- It can cause a full shuffle of the data, i.e., all the data is reshuffled across the network to create new partitions.
- This operation is expensive due to the full shuffle. However, the resulting partitions can be balanced, i.e., they 
have roughly equal amounts of data.
- It can be used when you want to increase the number of partitions to allow for more concurrent tasks and increase 
parallelism when the cluster has more resources.
- In certain scenarios, you may want to partition based on a specific key to optimize your job. For example, if you 
frequently filter by a certain key, you might want all records with the same key to be on the same partition to 
minimize data shuffling. In such cases, you can use repartition() with a column name.
coalesce():
- This method is used to reduce the number of partitions in an RDD or DataFrame.
- It avoids a full shuffle. If you're decreasing the number of partitions, it will try to minimize the amount of data that's 
shuffled. Some partitions will be combined together, and the data within these partitions will not need to be moved.
- This operation is less expensive than repartition() because it minimizes data shuffling.
- However, it can lead to data skew if you have fewer partitions than before, because it combines existing partitions 
to reduce the total number.

# Example for Salting
#### Imagine we have two DataFrames, df1 and df2, that we want to join on a column named 'id'. Assume that the 'id' column is highly skewed. Firstly, without any handling of skewness, the join might look something like this:
    result = df1.join(df2, on='id', how='inner')
    
Now, let's implement salting to handle the skewness:

    import pyspark.sql.functions as F

## Define the number of keys you'll use for salting
    num_salting_keys = 100
## Add a new column to df1 for salting
    df1 = df1.withColumn('salted_key', (F.rand()*num_salting_keys).cast('int'))
## Explode df2 into multiple rows by creating new rows with salted keys
    df2_exploded = df2.crossJoin(F.spark.range(num_salting_keys).withColumnRenamed('id', 'salted_key'))
## Now perform the join using both 'id' and 'salted_key'
    result = df1.join(df2_exploded, on=['id', 'salted_key'], how='inner')
## If you wish, you can drop the 'salted_key' column after the join
    result = result.drop('salted_key')
    
#### In this code, we've added a "salt" to the 'id' column in df1 and created new rows in df2 for each salt value. We then perform the join operation 
on both 'id' and the salted key. This helps to distribute the computation for the skewed keys more evenly across the cluster.

# Difference between RDD, Dataframe and Dataset

<img src = 'https://github.com/Kamaljit12/Pyspark/blob/main/images/rdd_dataframe.jpeg'>
<img src = 'https://github.com/Kamaljit12/Pyspark/blob/main/images/rdd-and-dataframe-comparision.png'>
<img src = 'https://github.com/Kamaljit12/Pyspark/blob/main/images/rdd_dataframe_2.jpeg'>
<img src = 'https://github.com/Kamaljit12/Pyspark/blob/main/images/rdd_dataframe_3.jpeg'>
<img src = 'https://github.com/Kamaljit12/Pyspark/blob/main/images/rdd_dataframe_4.jpeg'>

# Understand Spark-Submit
#### spark-submit is a command-line interface to submit your Spark applications to run on a cluster. It can use a number of supported master URL's to distribute your application across a cluster, or can run the application locally.
## Here is the general structure of the spark-submit command:
    spark-submit \
     --class <main-class> \
     --master <master-url> \
     --deploy-mode <deploy-mode> \
     --conf <key>=<value> \
     <application-jar> \
     [application-arguments]
- class: This is the entry point for your application, i.e., where your main method runs. For Java and Scala, this would be 
a fully qualified class name.
- master: This is the master URL for the cluster. It can be a URL for any Spark-supported cluster manager. For example, 
local for local mode, spark://HOST:PORT for standalone mode, mesos://HOST:PORT for Mesos, or yarn for YARN.
Reference for list of configuration properties available in Spark 3.X - <br>
'https://spark.apache.org/docs/latest/configuration.html' 
# Understand Spark-Submit
- deploy-mode: This can be either client (default) or cluster. In client mode, the driver runs on the machine from which 
the job is submitted. In cluster mode, the framework launches the driver inside the cluster.
- conf: This is used to set any Spark property. For example, you can set Spark properties like spark.executor.memory, 
spark.driver.memory, etc.
- <application-jar>: This is a path to your compiled Spark application.
[application-arguments]: These are arguments that you need to pass to your Spark application.
#### For example, if you have a Spark job written in Python and you want to run it on a local machine, your spark-submit  command might look like this:
    spark-submit \
     --master local[4] \
     --py-files /path/to/other/python/files \
     /path/to/your/python/application.py \
     arg1 arg2
#### In this example, --master local[4] means the job will run locally with 4 worker threads (essentially, 4 cores). --py-files is used to add .py, .zip or .egg files to be distributed with your application. Finally, arg1 and arg2 are arguments that will be passed to your Spark application.

# Understand Spark-Submit



























