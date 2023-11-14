# Spark With YARN Cluster Manager Type

<img src = https://github.com/Kamaljit12/Pyspark/blob/main/images/yarn%20architecture.jpg>


# Spark With YARN Cluster Manager Type
- Resource Manager:
  - It controls the allocation of system resources on all applications. A Scheduler and an
Application Master are included. Applications receive resources from the Scheduler.
- Node Manager:
  - Each job or application needs one or more containers, and the Node Manager monitors these
containers and their usage. Node Manager consists of an Application Master and Container. The Node
Manager monitors the containers and resource usage, and this is reported to the Resource Manager.
- Application Master:
  - The ApplicationMaster (AM) is an instance of a framework-specific library and serves as the 
orchestrating process for an individual application in a distributed environment.
# Deployment Modes Of Spark
### Client Mode:
  - When u start a spark shell, application driver creates the spark session in your local machine which request 
to Resource Manager present in cluster to create Yarn application. YARN Resource Manager start an Application Master 
(AM container). For client mode Application Master acts as the Executor launcher. Application Master will reach to Resource 
Manager and request for further containers. Resource manager will allocate new containers.

#### These executors will directly communicate with Drivers which is present in the system in which you have submitted the spark application.

<img src = 'https://github.com/Kamaljit12/Pyspark/blob/main/images/deployment%20of%20spark.jpg'>

### Cluster Mode:  
 - For cluster mode, there’s a small difference compare to client mode in place of driver. Here Application 
Master will create driver in it and driver will reach to Resource Manager.

<img src = 'https://github.com/Kamaljit12/Pyspark/blob/main/images/deployment%20of%20spark_2.jpg'>

### Local Mode:
 - In local mode, Spark runs on a single machine, using all the cores of the machine. It is the simplest mode of 
deployment and is mostly used for testing and debugging.

<img src = 'https://github.com/Kamaljit12/Pyspark/blob/main/images/deployment%20of%20spark_3.jpg'>

# How Spark Job Runs Internally ?

<img src = 'https://github.com/Kamaljit12/Pyspark/blob/main/images/spark%20job%20run.jpg'>
