## Configuration Part

### Set the JAVA_HOME environment variable for Hadoop

Open the `hadoop-env.sh` file under `etc/hadoop` folder. At the bottom of the file, expose the JAVA_HOME to point at the installation path of Java.

All the below configuration files, if not specified, meaning locate under `etc/hadoop` folder.

```
export JAVA_HOME=/Library/Java/JavaVirtualMachines/jdk-10.0.1.jdk/Contents/Home
```

Please note that if you in Mac, can run `/usr/libexec/java_home` to get the Java installation path.

### Set up the HADOOP_PREFIX environment variable to point to your hadoop distribution

```
export HADOOP_PREFIX=/Users/ant/hadoop-3.1.0
```

### Configure the `core-site.xml` setting

This `core-site.xml` file, is used to configure the overall hadoop, not component specific. 

##### Make hadoop to use HDFS

The below config is to make hadoop use HDFS as the storage. 

```
<configuration>
	<property>
		<name>fs.defaultFS</name>
		<value>hdfs://localhost:9000</value>
	</property>
</configuration>
``` 

### Configure `hdfs-site.xml` to customize HDFS

##### Set the replication factor to be 1

Replication is a way by which fault tolerance and recovery is built in hadoop. A psedo-distributed set up has only a single node so the replication factor has to be 1 

```
<configuraiton>
	<property>
		<name>dfs.replication</name>
		<value>1</value>
	</property>
</configuration>
```

### Configure `mapred-site.xml` to customize MapReduce

This file can be not existed by default. Create this file manually if the file does not exist.

##### Set `Yarn` as the runtime framework to execute MapReduce jobs

Here We choose to use `yarn` but other available options are `local` and `classic`. Specifically, `classic` is only used for the first version of MapReduce, and `local` is used for running MapReduce locally. 

```
<configuration>
	<property>
		<name>mapreduce.framework.name</name>
		<value>yarn</value>
	</property>
</configuration>
```

### Configure `yarn-site.xml` to customize Yarn

##### Allow shuffle and sort capabilities for the MapReduce

```
<configuration>
	<property>
		<name>yarn.nodemanager.aux-services</name>
		<value>mapreduce_shuffle</value>
	</property>
</configuration>
```

## Start Hadoop

### Start the HDFS

##### Format the NameNode

The NameNode is the master node in your cluster, it keeps track of all the other nodes on which processes run. Format this so that we can have a fresh start.

```
> bin/hdfs namenode -format
```

##### Start the master and slave nodes of the distributed file system

After this successful execution of this command, name node and data nodes have been started. 

```
> sbin/start-dfs.sh
```

##### Open the web interface in browser to verify name node

Hadoop provides a special web interface at the port number 50070, especially for the name node.

```
http://localhost:50070/
```

##### Create home directory on HDFS

```
> bin/hdfs dfs -mkdir /users
> bin/hdfs dfs -mkdir /users/ant
```

##### Confirm the HDFS is up and running

Run the `jps` command, which is a standard command line utility that comes along with the Java JDK, and it shows the list of java processes that are running on your system. Here we should see `NameNode`, `DataNode`, `SecondaryNameNode`. These three process being up indicate that HDFS is up and running.

### Start Yarn

```
> sbin/start-yarn.sh 
```

Running the jps again, and should be able see two additional processes, `NodeManager` and `ResourceManager`. 

## Monitor the cluster

### Cluster view using resource manager in Yarn

Open `http://localhost:8088` in the browser to see the entire cluster view.

### Run the example MapReduce 

##### Create the input directory in HDFS

```
> bin/hdfs dfs -mkdir input
```

##### Copy over the `etc/hadoop` folder to input folder

```
> bin/hdfs dfs -put etc/hadoop/* input/
> bin/hdfs dfs -ls input
```

##### Run the MapReduce job

```
> bin/hadoop jar share/hadoop/mapreudce/hadoop-mapreduce-examples-3.1.0.jar grep input output 'dfs[a-z.]+'
> bin/hdfs dfs -cat output/*
```

