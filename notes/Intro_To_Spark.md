#Introduction to Spark#

#Spark Context#
* First thing you have to do; tells spark how and where to access cluster
* Use this to create the RDD
* Master parameter = local or host. Can set local for number of cores

#RDDS#
* Immutable once created
* Can create by parallelizing existing Python collection, transforming existing RDD, or from files
* Can specify number of partitions
    - More partitions increases opportunities for parallism
##RDD Operations##
* Transformations and actions
*  Transformations are lazy
*  Can persist into memory or disk
*  Do transformations before actions
*  See spark programming guide and Python API
*  No computation run when parallelize; only does it when needs it
##Spark Transformations##
* Create new datasets from existing ones
* Map - passes each elem through function
* Filter - only returns true values
* Distinct - returns distinct
* flatMap - applies function that returns a flat sequence
    - rdd.flatmap(lambda x: [x, x+5])
        + returns: [1,6,2,7,3,8] when called on [1,2,3]
* No computation until action run 
* Can pass lambda functions or actual functions
    - rdd.filter(isComment)
##SPARK ACTIONS##
* Reduce - aggregates elements using a function
    - Takes two elements and returns 1
* Take - returns array with first n elements
* Collect - return all elements in array
    - Make sure fits in driver's memory
* takeOrdered - takes n elements ordered
* count - number of elements in RDD
##CACHING RDDS##
rdd.cache() - tells spark to cache this RDD. That way only reads from disk the first time.
##Program Lifecycle##
* Create or parallelize RDDs, transform, cache some, perform actions
##Key-Value RDDs##
* [(1,2), (3,4)]
* reduceByKey - aggregates by key using a function
    - takes two values and returns one
    - For example, can sum by key
* sortByKey - sorted by keys in ascending order
* groupByKey - returns new data set of key and iterable value pairs
    - Be careful because can cause a lot of data movement and very large iterables at workers
    - The iterable is all the values for that key
##Closures##
* One closure per worker
* No communication between workers so any changes to global variables at worker not sent to driver
* Closures are sent with EVERY job
* Inefficient to send large data to every worker
* To help with these problems pySpark has shared variables:
    - Broadcast variables - efficiently send large, read-only value to all workers. Saved at workers.
        + Keep read-only variable cached on workers
        + At driver: sc.broadcast([1,2,3])
        + At worker: broadcastVar.value
    - Accumulators - aggregate values from worker back to driver. Write only for tasks and only driver can access the value.
        + Efficiently implement parallel counters and sums
        + Make global in function
        + accumulator.value to access at driver
        + Can be used in actions or transformations
            * Only use accumulators for debugging purposes with transfomations because no guarantees