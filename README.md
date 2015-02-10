Spark Testing
=============

This is a basic, stand-alone project that builds the KinesisWordCountASL example from the main Apache Spark source.

**Running this requires an Amazon AWS account, and it will incur charges.**

What you Need to Run
---------------------
1. JDK 8
1. Scala 2.10
1. Latest Maven
1. Clone the Apache Spark source from https://github.com/apache/spark/tree/v1.2.1
  1. Build using: mvn -Pkinesis-asl -DskipTests clean package
     Note - this took about 20 minutes on my machine to download all dependencies and build everything.
1. Build this project with: mvn clean package
1. Amazon AWS account, with a user and access key. [More Info](http://docs.aws.amazon.com/AWSSdkDocsJava/latest/DeveloperGuide/credentials.html)
    * Create a ~/.aws/credentials file on your machine
1. Kinesis stream. You can create one with the AWS console. [More Info](http://docs.aws.amazon.com/ElasticMapReduce/latest/DeveloperGuide/kinesis-pig-create-stream.html)

How to Run
----------
1. Start the Master
    * ```./$SPARK_SOURCE_DIR/sbin/start-master.sh```
    * You should be able to see the web console at http://localhost:8080
1. Start a Worker
    * ```./$SPARK_SOURCE_DIR/bin/spark-class org.apache.spark.deploy.worker.Worker spark://$SPARK_CLUSTER_LOCATION```
    * You can find the $SPARK_CLUSTER_LOCATION at the web console
1. Submit the Application
    * ```./bin/spark-submit \
      --class io.archon.spark_testing.KinesisWordCountASL \
      --master spark://$SPARK_CLUSTER_LOCATION \
      spark_testing/target/spark_testing-1.0-SNAPSHOT-jar-with-dependencies.jar \
      sparkTestingStream \
      https://kinesis.us-east-1.amazonaws.com```
1. Start Producing Kinesis Messages
    * ```java -cp spark_testing/target/spark_testing-1.0-SNAPSHOT-jar-with-dependencies.jar:$SPARK_SOURCE_DIR/conf:$SPARK_SOURCE_DIR/assembly/target/scala-2.10/spark-assembly-1.3.0-SNAPSHOT-hadoop1.0.4.jar  io.archon.spark_testing.KinesisWordCountProducerASL sparkTestingStream \
      https://kinesis.us-east-1.amazonaws.com 10 5```
    * If you need help with the classpath, run the following: `$SPARK_SOURCE_DIR/bin/compute-classpath.sh`
1. You should now be able to see the producer put messages on Kinesis, and the consumer application output some word counts.

# !!!WHEN YOU ARE DONE, TURN OFF THE KINESIS STREAM AND THE DYNAMODB TABLE CORRESPONDING TO THE STREAM!!!
