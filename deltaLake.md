1. Write Data.
~~~Scala
    val data = spark.range(0,5)

    // write.
    data.write.format("delta").save("/tmp/delta-table")

    // overwrite.
    data.write.format("delta").mode("overwrite").save("/tmp/delta-table")

    // append
    data.write.format("delta").mode("append").save("/tmp/delta-table")

    // Overwrite using dataframes.
    data.write.format("delta")
    .mode("overwrite")
    .option("replaceWhere","date >= '2017-01-01' AND date <= '2017-01-31'")
    .save("delta/events")

~~~

2. Read Data.

~~~Scala
    val data = spark.read.format("delta").load("/tmp/delta-table")
~~~

3. Update Table Data.

~~~Scala
    val data = spark.range(5,10)
    data.write.format("delta").mode("overwrite").save("/tmp/delta-table")
~~~

4. Conditional Update without overwrite.

~~~Scala
    import io.delta.tables._
    import org.apache.spark.sql.functions._

    val deltaTable = DeltaTable.forPath("/tmp/delta-table")

    // update every even value by adding 100 to it.

    deltaTable.update(
        condition = expr("id%2 == 0"),
        set = Map("id" -> expr("id + 100"))
    ) // More done in sql way.
    // for updation first set condition of on what basis need to be 
    // set what need to be changed.

    // delete every even value.
    deltaTable.delete(condition = expr("id%2 == 0"))

    // Upsert (merge) new data.
    val newData = spark.range(0,20).toDF

    deltaTable.as("oldData")
    .merge(
        newData.as("newData"),
        "oldData.id = newData.id"
    )
    .whenMatched
    .update(Map("id" -> col("newData.id")))
    .whenNotMatched
    .insert(Map("id" -> col("newData.id")))
    .execute()

    deltaTable.toDF.show()

~~~

5. Partition Data.

~~~Scala
df.write.format("delta").partitionBy("date").save("/delta/events")
~~~

6. Query an older version of the delta table.
~~~Scala
df1 = spark.read.format("delta").option("timestampAsOf",timestamp_string).load("/delta/events")
df2 = spark.read.format("delta").option("versionAsOf",version).load("/delta/events")
~~~

7. Clear all data before 2017.
~~~Scala
import io.delta.tables._

val deltaTable = DeltaTable.forPath(spark, "/data/events/")

deltaTable.delete("date < '2017-01-01'")        // predicate using SQL formatted string

import org.apache.spark.sql.functions._
import spark.implicits._

deltaTable.delete(col("date") < "2017-01-01")   
~~~

8. Update Table
~~~Scala
import io.delta.tables._

val deltaTable = DeltaTable.forPath(spark, "/data/events/")

deltaTable.updateExpr(            // predicate and update expressions using SQL formatted string
  "eventType = 'clck'",
  Map("eventType" -> "'click'")

import org.apache.spark.sql.functions._
import spark.implicits._

deltaTable.update(                // predicate using Spark SQL functions and implicits
  col("eventType") === "clck",
  Map("eventType" -> lit("click")));
~~~

10. Upsert Into Table. [Link](https://docs.delta.io/latest/delta-update.html)
~~~Scala

import io.delta.tables._
import org.apache.spark.sql.functions._

val updatesDF = ...  // define the updates DataFrame[date, eventId, data]

DeltaTable.forPath(spark, "/data/events/")
  .as("events")
  .merge(
    updatesDF.as("updates"),
    "events.eventId = updates.eventId")
  .whenMatched
  .updateExpr(
    Map("data" -> "updates.data"))
  .whenNotMatched
  .insertExpr(
    Map(
      "date" -> "updates.date",
      "eventId" -> "updates.eventId",
      "data" -> "updates.data"))
  .execute()

~~~

11. Deduped Insertion
~~~Scala
deltaTable
  .as("logs")
  .merge(
    newDedupedLogs.as("newDedupedLogs"),
    "logs.uniqueId = newDedupedLogs.uniqueId")
  .whenNotMatched()
  .insertAll()
  .execute()
~~~

12. Only looking into specific periods for dedupe.
~~~Scala
deltaTable.as("logs").merge(
    newDedupedLogs.as("newDedupedLogs"),
    "logs.uniqueId = newDedupedLogs.uniqueId AND logs.date > current_date() - INTERVAL 7 DAYS")
  .whenNotMatched("newDedupedLogs.date > current_date() - INTERVAL 7 DAYS")
  .insertAll()
  .execute()
~~~

13. History.
~~~Scala
import io.delta.tables._

val deltaTable = DeltaTable.forPath(spark, pathToTable)

val fullHistoryDF = deltaTable.history()    // get the full history of the table

val lastOperationDF = deltaTable.history(1) // get the last operation
~~~
