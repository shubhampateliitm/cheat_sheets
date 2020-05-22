1. Creating DataFrames.

~~~Scala

// toDF version
import spark.implicits._

val someDF = Seq(
  (8, "bat"),
  (64, "mouse"),
  (-27, "horse")
).toDF("number", "word")
 
~~~

The `import spark.implicits._` statement can only be run inside of class definitions when the Spark Session is available. `toDF()` is suitable for local testing, but production grade code that’s checked into master should use a better solution.

~~~Scala
// Using createDataFrame
val someData = Seq(
  Row(8, "bat"),
  Row(64, "mouse"),
  Row(-27, "horse")
)

val someSchema = List(
  StructField("number", IntegerType, true),
  StructField("word", StringType, true)
)

val someDF = spark.createDataFrame(
  spark.sparkContext.parallelize(someData),
  StructType(someSchema)
)
~~~
`createDataFrame()` provides the functionality we need, but the syntax is verbose. Our test files will become cluttered and difficult to read if `createDataFrame()` is used frequently.

~~~Scala
// using createDF
val someDF = spark.createDF(
  List(
    (8, "bat"),
    (64, "mouse"),
    (-27, "horse")
  ), List(
    ("number", IntegerType, true),
    ("word", StringType, true)
  )
)
~~~
createDF() creates readable code like toDF() and allows for full schema customization like createDataFrame(). It’s the best of both worlds.

2. Spark Except function.
   
  Returns a table with the elements from left that are not in right using the built-in spark subtract function.
  

3. Enabling and Disabling of spark UI and specifying quick config [source](https://stackoverflow.com/questions/33774350/how-to-disable-sparkui-programmatically)

~~~scala

val conf = new SparkConf()
      .setAppName(s" The name of app ")
      .set("spark.sql.session.timeZone", "UTC") // Define default time zone.
      .set("spark.cores.max","200") // Total no. of cores.
      .set("spark.executor.memory", "10g") // per executor memory
      .set("spark.ui.enabled", "true") // enable spark ui

val spark: SparkSession = SparkSession.builder
      .config(conf)
      .getOrCreate()
~~~

4. Submit multiple jars.
~~~bash
./bin/spark-submit --class "SparkTest" --master local[*] --jars /fullpath/first.jar,/fullpath/second.jar /fullpath/your-program.jar
~~~

5. Passing external jars with spark-shell
~~~bash
./spark-shell --jars pathOfjarsWithCommaSeprated
./spark-shell --jars *
~~~

6. Get the input file name of the record.

~~~Scala
data.withColumn("inputFileName",input_file_name())
~~~

7. Generic Working Template for Spark Notebook

~~~Scala
import org.apache.spark.sql.functions._
import scala.util.Try
import org.apache.hadoop.conf.Configuration
import org.apache.hadoop.fs.{FileSystem, Path}
import org.apache.spark.SparkConf
import org.apache.spark.sql.functions._
import org.apache.spark.sql.{DataFrame, SaveMode, SparkSession}
import org.apache.hadoop.conf._
import org.apache.hadoop.fs._

val fs: FileSystem = FileSystem.get(new Configuration)


spark.stop
val conf = new SparkConf()
      .setAppName(s" The name of app ")
      .set("spark.sql.session.timeZone", "UTC") // Define default time zone.
      .set("spark.cores.max","20") // Total no. of cores.
      .set("spark.executor.memory", "8g") // per executor memory
      .set("spark.ui.enabled", "true") // enable spark ui

val spark: SparkSession = SparkSession.builder
      .config(conf)
      .getOrCreate()
~~~

1. Spliting string column. [ref](https://stackoverflow.com/questions/39255973/split-1-column-into-3-columns-in-spark-scala)

~~~Scala
data.withColumn("newCol",split($"columnToSplit", "\\.")) // first column.
// will get an array split by `.`

data.withColumn("newCol", split(col("colToSplit"),"regex").getItem(0))
// get the first value.
~~~

9. Overwritting Data.

~~~Scala
data.write.mode("overwrite").csv("path_to_file")
~~~

10. Calculating Median

~~~Scala
data.stat.approxQuantile("colName", Array(0.5), 0.001) //median
        .head
~~~

11. Creating a UDF function.
~~~Scala
import org.apache.spark.sql.expressions.UserDefinedFunction
import org.apache.spark.sql.functions.udf

  val sample_udf: UserDefinedFunction = udf((str: String) => {
    
    str

  })

~~~

12. Get dates in the range.

~~~Scala
import java.time.LocalDate
import java.time.temporal.ChronoUnit

 /**
    * Given startDate and endDate, generate all of the date between them.
    * @param startDate: from Date.
    * @param endDate: to Date
    * @return
    */
  def getDatesInRange(startDate: String,
                      endDate: String): Option[Array[String]] = {

    val from = LocalDate.parse(startDate)
    val to = LocalDate.parse(endDate)

    startDate <= endDate match {
      case true => {
        val numberOfDays = ChronoUnit.DAYS.between(from, to).toInt
        Some(
          (for (f <- 0 to numberOfDays)
            yield from.plusDays(f).toString).toArray
        )
      }
      case false => None
    }

  }

~~~

13. Handling Spark package Conflict. https://stackoverflow.com/questions/25144484/sbt-assembly-deduplication-found-error