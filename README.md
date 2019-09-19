Apache Spark on Docker
==========

[![DockerPulls](https://img.shields.io/docker/pulls/yb19891229/docker-spark.svg)](https://registry.hub.docker.com/u/yb19891229/docker-spark/)
[![DockerStars](https://img.shields.io/docker/stars/yb19891229/docker-spark.svg)](https://registry.hub.docker.com/u/yb19891229/docker-spark/)


This repository contains a Docker file to build a Docker image with Apache Spark. This Docker image depends on our previous [Hadoop Docker](https://github.com/yb19891229/hadoop-docker) image, available at the yb19891229 [GitHub](https://github.com/yb19891229) page.
The base Hadoop Docker image is also available as an official [Docker image](https://registry.hub.docker.com/u/yb19891229/hadoop-docker/).

##Pull the image from Docker Repository
```
docker pull yb19891229/docker-spark:2.4.3
```

## Building the image
```
docker build --rm -t yb19891229/docker-spark:2.4.3 .
```

## Running the image

* if using boot2docker make sure your VM has more than 2GB memory
* in your /etc/hosts file add $(boot2docker ip) as host 'sandbox' to make it easier to access your sandbox UI
* open yarn UI ports when running container
```
docker run -it -p 8088:8088 -p 8042:8042 -p 4040:4040 -h sandbox yb19891229/docker-spark:2.4.3 bash
```
or
```
docker run -d -h sandbox yb19891229/docker-spark:2.4.3 -d
```

## Versions
```
Hadoop 2.6.0 and Apache Spark v2.4.3 on Centos
```

## Testing

There are two deploy modes that can be used to launch Spark applications on YARN.

### YARN-client mode

In yarn-client mode, the driver runs in the client process, and the application master is only used for requesting resources from YARN.

```
# run the spark shell
spark-shell \
--master yarn-client \
--driver-memory 1g \
--executor-memory 1g \
--executor-cores 1

# execute the the following command which should return 1000
scala> sc.parallelize(1 to 1000).count()
```
### YARN-cluster mode

In yarn-cluster mode, the Spark driver runs inside an application master process which is managed by YARN on the cluster, and the client can go away after initiating the application.

Estimating Pi (yarn-cluster mode):

```
# execute the the following command which should write the "Pi is roughly 3.1418" into the logs
# note you must specify --files argument in cluster mode to enable metrics
spark-submit \
--class org.apache.spark.examples.SparkPi \
--files $SPARK_HOME/conf/metrics.properties \
--master yarn-cluster \
--driver-memory 1g \
--executor-memory 1g \
--executor-cores 1 \
$SPARK_HOME/lib/spark-examples-2.4.3-hadoop2.6.0.jar
```

Estimating Pi (yarn-client mode):

```
# execute the the following command which should print the "Pi is roughly 3.1418" to the screen
spark-submit \
--class org.apache.spark.examples.SparkPi \
--master yarn-client \
--driver-memory 1g \
--executor-memory 1g \
--executor-cores 1 \
$SPARK_HOME/lib/spark-examples-2.4.3-hadoop2.6.0.jar
```

### Submitting from the outside of the container
To use Spark from outside of the container it is necessary to set the YARN_CONF_DIR environment variable to directory with a configuration appropriate for the docker. The repository contains such configuration in the yarn-remote-client directory.

```
export YARN_CONF_DIR="`pwd`/yarn-remote-client"
```

Docker's HDFS can be accessed only by root. When submitting Spark applications from outside of the cluster, and from a user different than root, it is necessary to configure the HADOOP_USER_NAME variable so that root user is used.

```
export HADOOP_USER_NAME=root
```
