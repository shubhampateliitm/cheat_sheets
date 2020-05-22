1. Testing file exits in hdfs or not.

~~~bash
hdfs dfs -test -e "/a/b/c/file.txt"

if [ $? == 0 ] ; then
  echo "file exits"
fi

if [ $? == 1 ] ; then
    echo "file doesn't exists"
fi

~~~