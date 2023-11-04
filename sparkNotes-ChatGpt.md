# most important notes

#### can give you an example of using UDF with PySpark. UDF stands for User Defined Function, which is a feature that allows you to define your own custom functions to operate on PySpark DataFrame or RDD. You can use the udf() function from pyspark.sql.functions module to create a UDF and specify the return type of the function. You can then use the UDF with DataFrame select(), withColumn(), or SQL expressions.

#### Here is an example of creating and using a UDF to calculate the length of a string column in a DataFrame:

## Python

#### Import the udf function and the IntegerType
    from pyspark.sql.functions import udf
    from pyspark.sql.types import IntegerType

##### Create a sample DataFrame
    df = spark.createDataFrame([("Hello",), ("World",), ("Spark",)], ["word"])

### Define a Python function to calculate the length of a string
    def string_length(s):
      return len(s)

## Convert the Python function to a UDF
    string_length_udf = udf(string_length, IntegerType())

### Use the UDF with DataFrame select()
    df.select("word", string_length_udf("word").alias("length")).show()

### Output
    +-----+------+
    | word|length|
    +-----+------+
    |Hello|     5|
    |World|     5|
    |Spark|     5|
    +-----+------+

# What is the difference between cache() and persist() in PySpark?

#### The difference between cache() and persist() in PySpark is that cache() only stores data in memory, while persist() allows you to choose where to store the data. Additionally, persist() allows you to choose the level of persistence, from MEMORY_ONLY to MEMORY_AND_DISK_SER_2³.

#### cache() is a synonym of persist(MEMORY_ONLY), which means it stores the data in memory only and does not spill to disk if there is not enough space². cache() is useful for fast and frequent access to the data, but it can cause memory pressure and garbage collection issues if the data is too large⁴⁵.

#### persist() is a more general method that can take an optional argument storageLevel to specify where and how the data will be stored⁴. The storageLevel can be one of the following¹:

- MEMORY_ONLY: Store data in memory as deserialized objects. This is the same as cache().
- MEMORY_ONLY_SER: Store data in memory as serialized objects. This can save space but increase the cost of deserialization.
- MEMORY_AND_DISK: Store data in memory as deserialized objects. If there is not enough space, spill to disk.
- MEMORY_AND_DISK_SER: Store data in memory as serialized objects. If there is not enough space, spill to disk.
- DISK_ONLY: Store data on disk only.
- MEMORY_ONLY_2, MEMORY_ONLY_SER_2, etc.: Same as the previous levels, but replicate each partition on two nodes for fault tolerance.

#### persist() is useful for controlling the trade-off between memory, disk, and CPU usage. It can also help to avoid recomputing the data if it is lost or evicted⁴⁵.

#### To summarize, cache() and persist() are both optimization techniques for storing intermediate results of PySpark computations, but cache() is a special case of persist() with a default storage level of MEMORY_ONLY. You should use cache() when you want to store the data in memory only and you have enough space, and use persist() when you want to customize the storage level according to your needs.


# How do I check if a DataFrame is cached or persisted?

To check if a DataFrame is cached or persisted, you can use the following methods:

- Use the is_cached attribute of the DataFrame object. This will return True if the DataFrame is cached or persisted, and False otherwise. For example:

```python
# Create a sample DataFrame
df = spark.createDataFrame([(1, "a"), (2, "b"), (3, "c")], ["id", "value"])

# Check if the DataFrame is cached
print(df.is_cached) # False

# Cache the DataFrame
df.cache()

# Check if the DataFrame is cached
print(df.is_cached) # True
```

- Use the storageLevel attribute of the DataFrame object. This will return the StorageLevel object that represents the level of persistence of the DataFrame. For example:

```python
# Create a sample DataFrame
df = spark.createDataFrame([(1, "a"), (2, "b"), (3, "c")], ["id", "value"])

# Check the storage level of the DataFrame
print(df.storageLevel) # StorageLevel (False, False, False, False, 1)

# Persist the DataFrame with memory and disk
df.persist(pyspark.StorageLevel.MEMORY_AND_DISK)

# Check the storage level of the DataFrame
print(df.storageLevel) # StorageLevel (True, True, False, False, 1)
```

- Use the catalog object of the SparkSession to access the Catalog API. This will allow you to list and query the cached tables and views in the current session. For example:

```python
# Create a sample DataFrame
df = spark.createDataFrame([(1, "a"), (2, "b"), (3, "c")], ["id", "value"])

# Create a temporary view from the DataFrame
df.createOrReplaceTempView("df_view")

# Get the catalog object
catalog = spark.catalog

# List the cached tables and views
catalog.cacheTable("df_view") # Cache the table
catalog.listTables() # List the tables
# [Table (name = 'df_view', database = None, description = None, tableType = 'TEMPORARY', isTemporary = True)]

# Check if a table or view is cached
catalog.isCached("df_view") # True
```

# How do I unpersist a DataFrame in PySpark?

To unpersist a DataFrame in PySpark, you can use the unpersist() method of the DataFrame object. This method will mark the DataFrame as non-persistent and remove all blocks for it from memory and disk. You can optionally pass a boolean argument to the unpersist() method to indicate whether you want to block until all blocks are deleted or not. The default value is False, which means the unpersist() method will return immediately without waiting for the deletion to complete¹².

Here is an example of how to unpersist a DataFrame in PySpark:

```python
# Create a sample DataFrame
df = spark.createDataFrame([(1, "a"), (2, "b"), (3, "c")], ["id", "value"])

# Persist the DataFrame with memory and disk
df.persist(pyspark.StorageLevel.MEMORY_AND_DISK)

# Check the storage level of the DataFrame
print(df.storageLevel) # StorageLevel (True, True, False, False, 1)

# Unpersist the DataFrame
df.unpersist()

# Check the storage level of the DataFrame
print(df.storageLevel) # StorageLevel (False, False, False, False, 1)
```

# Can I cache or persist multiple DataFrames at once?

Yes, you can cache or persist multiple DataFrames at once in PySpark. There are two ways to do this:

- You can use the cache() or persist() methods on each DataFrame individually. For example:

```python
# Create some sample DataFrames
df1 = spark.createDataFrame([(1, "a"), (2, "b"), (3, "c")], ["id", "value"])
df2 = spark.createDataFrame([(4, "d"), (5, "e"), (6, "f")], ["id", "value"])
df3 = spark.createDataFrame([(7, "g"), (8, "h"), (9, "i")], ["id", "value"])

# Cache or persist each DataFrame
df1.cache() # Cache with default storage level (MEMORY_ONLY)
df2.persist(pyspark.StorageLevel.MEMORY_AND_DISK) # Persist with custom storage level
df3.cache() # Cache with default storage level
```

- You can use the spark.catalog.cacheTable() or spark.catalog.persistTable() methods to cache or persist multiple DataFrames at once by registering them as temporary views. For example:

```python
# Create some sample DataFrames
df1 = spark.createDataFrame([(1, "a"), (2, "b"), (3, "c")], ["id", "value"])
df2 = spark.createDataFrame([(4, "d"), (5, "e"), (6, "f")], ["id", "value"])
df3 = spark.createDataFrame([(7, "g"), (8, "h"), (9, "i")], ["id", "value"])

# Register each DataFrame as a temporary view
df1.createOrReplaceTempView("df1_view")
df2.createOrReplaceTempView("df2_view")
df3.createOrReplaceTempView("df3_view")

# Cache or persist multiple DataFrames at once
spark.catalog.cacheTable("df1_view, df2_view, df3_view") # Cache with default storage level (MEMORY_ONLY)
spark.catalog.persistTable("df1_view, df2_view, df3_view", pyspark.StorageLevel.MEMORY_AND_DISK) # Persist with custom storage level
```

# How do I check the storage level of a DataFrame?

To check the storage level of a DataFrame in PySpark, you can use the storageLevel attribute of the DataFrame object. This attribute will return a StorageLevel object that represents the level of persistence of the DataFrame. The StorageLevel object has five properties: useDisk, useMemory, useOffHeap, deserialized, and replication¹⁴.

For example, you can create a sample DataFrame and check its storage level as follows:

```python
# Create a sample DataFrame
df = spark.createDataFrame([(1, "a"), (2, "b"), (3, "c")], ["id", "value"])

# Check the storage level of the DataFrame
print(df.storageLevel)
# Output: StorageLevel (False, False, False, False, 1)
```

This means that the DataFrame is not persisted and has the default storage level, which is equivalent to StorageLevel.NONE¹⁴.

You can also cache or persist the DataFrame with different storage levels and check the storage level again. For example:

```python
# Cache the DataFrame with the default storage level (MEMORY_ONLY)
df.cache()

# Check the storage level of the DataFrame
print(df.storageLevel)
# Output: StorageLevel (True, True, False, True, 1)
```

This means that the DataFrame is cached in memory as deserialized objects and has a replication factor of 1¹⁴.

```python
# Persist the DataFrame with a custom storage level (MEMORY_AND_DISK_SER_2)
df.persist(pyspark.StorageLevel.MEMORY_AND_DISK_SER_2)

# Check the storage level of the DataFrame
print(df.storageLevel)
# Output: StorageLevel (True, True, False, False, 2)
```

This means that the DataFrame is persisted in memory and disk as serialized objects and has a replication factor of 2¹⁴.

# What is the difference between cache() and persist()?

The difference between cache() and persist() in PySpark is that cache() only stores data in memory, while persist() allows you to choose where to store the data. Additionally, persist() allows you to choose the level of persistence, from MEMORY_ONLY to MEMORY_AND_DISK_SER_2³.

cache() is a synonym of persist(MEMORY_ONLY), which means it stores the data in memory only and does not spill to disk if there is not enough space². cache() is useful for fast and frequent access to the data, but it can cause memory pressure and garbage collection issues if the data is too large⁴ .

persist() is a more general method that can take an optional argument storageLevel to specify where and how the data will be stored⁴. The storageLevel can be one of the following¹:

- MEMORY_ONLY: Store data in memory as deserialized objects. This is the same as cache().
- MEMORY_ONLY_SER: Store data in memory as serialized objects. This can save space but increase the cost of deserialization.
- MEMORY_AND_DISK: Store data in memory as deserialized objects. If there is not enough space, spill to disk.
- MEMORY_AND_DISK_SER: Store data in memory as serialized objects. If there is not enough space, spill to disk.
- DISK_ONLY: Store data on disk only.
- MEMORY_ONLY_2, MEMORY_ONLY_SER_2, etc.: Same as the previous levels, but replicate each partition on two nodes for fault tolerance.

persist() is useful for controlling the trade-off between memory, disk, and CPU usage. It can also help to avoid recomputing the data if it is lost or evicted⁴ .


# What are some best practices for caching and persisting in PySpark?

