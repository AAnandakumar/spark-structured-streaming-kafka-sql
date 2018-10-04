# spark-structured-streaming-kafka-sql
Structured Streaming is built upon the Spark SQL engine, and improves upon the constructs from Spark SQL Data Frames and Datasets so you can write streaming queries in the same way you would write batch queries.

spark-sql-kafka supports to run SQL query over the topics read and write.

### Application setup for Development.

-  java 1.8
-   Maven 3 +
-   Recommended IDE (intellij/Scala IDE)

### Application setup for deployment.

-   java 1.8
-   Spark 2.2.0
-   Kafka 0.10
-   Scala 2.11

##### Read from kafka

`val ksDf = spark
         .readStream
         .format("kafka")
         .options(kafkaConsumerMap)
         .load()`
##### Convert JSON string to DF with typed JSON schema

Once data has been projected then when can apply our SQL operation( filter condition, aggregation) ()

`val stDF=ksDf
         .selectExpr("CAST(value AS STRING)")
         .select(from_json($"value", schema) as "data")
         .select("data.nameSpace","data.deviceName","data.location","data.id")`
         
##### Streaming write Df to - [console/kafka/file]

We apply SQL select query and form the key-value pair for kafka

`val stdfk=stDF.selectExpr("CAST(id AS STRING) AS key", "to_json(struct(*)) AS value")
        .writeStream
        .format("kafka")
        .options(kafkaProducerMap)
        .trigger(Trigger.ProcessingTime(1.seconds))
        .option("checkpointLocation", checkpt_loc)
        .queryName("df-kafka-topics")
        .start()
      stdfk.awaitTermination()`
      
