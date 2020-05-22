1. Using function conversion.
```python
pyspark --jar tch.jar

ipAdOp = spark._jvm.x.y.z.b.IpAddress

def conv(column):
  from pyspark.sql.column import Column, _to_java_column, _to_seq
  obj = ipAdOp.string2Long
  return Column(obj(_to_seq()))


```
2. Using UDF Coversion.

~~~python
from pyspark.sql.column import Column
from pyspark.sql.column import _to_java_column
from pyspark.sql.column import _to_seq
from pyspark.sql.functions import col


def getWeek(field):
    _week = sc._jvm._jvm.x.y.z.b.Bv.getBc()
    return Column(_week.apply(_to_seq(sc, [field], _to_java_column)))

df = spark.read.parquet("/x/y/z/b/date=2020-01-01")

from pyspark.sql.functions import avg, udf, substring, col

df.printSchema()

df.select(getWeek(col("c").cast("long"))).show()
~~~