## Hadoop File System Configuration





## Configure core-site.xml



1. sbin directory contains hadoop file system server side shell script
2. bin directory contains hadoop file system client side shell script



### Start up Hadoop File System

```shell
[hadoop@hadoop001 hadoop-2.6.0-cdh5.7.0]$ ./sbin/start-dfs.sh 

Starting namenodes on [hadoop001]
hadoop001: starting namenode, logging to /home/hadoop/install/hadoop-2.6.0-cdh5.7.0/logs/hadoop-hadoop-namenode-hadoop001.out
localhost: starting datanode, logging to /home/hadoop/install/hadoop-2.6.0-cdh5.7.0/logs/hadoop-hadoop-datanode-hadoop001.out
Starting secondary namenodes [0.0.0.0]
0.0.0.0: starting secondarynamenode, logging to /home/hadoop/install/hadoop-2.6.0-cdh5.7.0/logs/hadoop-hadoop-secondarynamenode-hadoop001.out
18/06/27 00:46:37 WARN util.NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable
```



### Check HDFS processes

```shell
[hadoop@hadoop001 hadoop-2.6.0-cdh5.7.0]$ jps
1495 SecondaryNameNode
1335 DataNode
1213 NameNode
1615 Jps
```

### Stop HDFS

```shell
[hadoop@hadoop001 hadoop-2.6.0-cdh5.7.0]$ ./sbin/stop-dfs.sh 
Stopping namenodes on [hadoop001]
hadoop001: stopping namenode
localhost: stopping datanode
Stopping secondary namenodes [0.0.0.0]
0.0.0.0: stopping secondarynamenode
```



```shell
[hadoop@hadoop001 bin]$ hadoop fs -ls /
Found 1 items
drwxr-xr-x   - hadoop supergroup          0 2018-06-27 01:16 /test

[hadoop@hadoop001 bin]$ hadoop fs -mkdir -p /a/b

[hadoop@hadoop001 bin]$ hadoop fs -ls -R /
drwxr-xr-x   - hadoop supergroup          0 2018-06-27 01:17 /a
drwxr-xr-x   - hadoop supergroup          0 2018-06-27 01:17 /a/b
drwxr-xr-x   - hadoop supergroup          0 2018-06-27 01:16 /test

[hadoop@hadoop001 bin]$ hadoop fs -ls -R /a
drwxr-xr-x   - hadoop supergroup          0 2018-06-27 01:17 /a/b
```



```shell
[hadoop@hadoop001 workshop]$ hadoop fs -put u.data /test

[hadoop@hadoop001 workshop]$ hadoop fs -ls -R /
drwxr-xr-x   - hadoop supergroup          0 2018-06-27 01:17 /a
drwxr-xr-x   - hadoop supergroup          0 2018-06-27 01:17 /a/b
drwxr-xr-x   - hadoop supergroup          0 2018-06-27 02:54 /test
-rw-r--r--   1 hadoop supergroup    2079229 2018-06-27 02:54 /test/u.data
```



```shell
[hadoop@hadoop001 workshop]$ hadoop fs -text /test/u.sample.data
18/06/27 03:03:45 WARN util.NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable
293	127	5	888904614
802	333	4	875986155
486	252	3	879875316
699	764	3	886568162
533	8	3	879191938
911	420	4	892840950
760	98	3	875667717
804	294	5	879441099
532	916	3	893115293
737	100	5	884314664
148	127	1	877399351
506	686	3	889874717
57	121	4	883697432
732	269	5	882589593
643	655	4	891448176
```



```shell
[hadoop@hadoop001 workshop]$ hadoop fs -cat /test/u.sample.data
18/06/27 03:05:57 WARN util.NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable
293	127	5	888904614
802	333	4	875986155
486	252	3	879875316
699	764	3	886568162
533	8	3	879191938
911	420	4	892840950
760	98	3	875667717
804	294	5	879441099
532	916	3	893115293
737	100	5	884314664
148	127	1	877399351
506	686	3	889874717
57	121	4	883697432
```



```shell
[hadoop@hadoop001 workshop]$ hadoop fs -copyToLocal /test/u.data u.copy.data

[hadoop@hadoop001 workshop]$ ls
u.copy.data  u.data  u.item  u.sample.data

[hadoop@hadoop001 workshop]$ more u.copy.data 
0	50	5	881250949
0	172	5	881250949
0	133	1	881250949
196	242	3	881250949
186	302	3	891717742
22	377	1	878887116
244	51	2	880606923
166	346	1	886397596
298	474	4	884182806
115	265	2	881171488
253	465	5	891628467
305	451	3	886324817
6	86	3	883603013
62	257	2	879372434
278	603	5	891295330
276	796	1	874791932
--More--(0%)

```



```shell
[hadoop@hadoop001 workshop]$ netstat -tulpn

(Not all processes could be identified, non-owned process info
 will not be shown, you would have to be root to see it all.)
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address               Foreign Address             State       PID/Program name   
tcp        0      0 0.0.0.0:50090               0.0.0.0:*                   LISTEN      2324/java           
tcp        0      0 127.0.0.1:8020              0.0.0.0:*                   LISTEN      2043/java           
tcp        0      0 0.0.0.0:50070               0.0.0.0:*                   LISTEN      2043/java           
```



```shell
[root@hadoop001 sbin]# iptables -I INPUT -p tcp --dport 50070 -j ACCEPT
```



```shell
[hadoop@hadoop001 sbin]$ hadoop fs -rm -r /a
18/06/27 03:46:34 WARN util.NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable
Deleted /a
```



# Setup and Test YARN

1. Configure parameters as follows:

etc/hadoop/mapred-site.xml:

```xml
<configuration>
    <property>
        <name>mapreduce.framework.name</name>
        <value>yarn</value>
    </property>
</configuration>
```



etc/hadoop/yarn-site.xml:

```xml
<configuration>
    <property>
        <name>yarn.nodemanager.aux-services</name>
        <value>mapreduce_shuffle</value>
    </property>
</configuration>
```

2. Start ResourceManager daemon and NodeManager daemon:

```shell
   [hadoop@hadoop001 hadoop-2.6.0-cdh5.7.0]$ ./sbin/start-yarn.sh 
   starting yarn daemons
   starting resourcemanager, logging to /home/hadoop/install/hadoop-2.6.0-cdh5.7.0/logs/yarn-hadoop-resourcemanager-hadoop001.out
   localhost: starting nodemanager, logging to /home/hadoop/install/hadoop-2.6.0-cdh5.7.0/logs/yarn-hadoop-nodemanager-hadoop001.out
   
   [hadoop@hadoop001 hadoop-2.6.0-cdh5.7.0]$ jps
   4947 ResourceManager
   4313 DataNode
   4475 SecondaryNameNode
   5036 NodeManager
   5341 Jps
   4191 NameNode
   ```

   3. Browse the web interface for the ResourceManager, by default it is at

- ResourceManager - `http://localhost:8088/`

```shell
[root@hadoop001 hadoop-2.6.0-cdh5.7.0]# iptables -I INPUT -p tcp --dport 8088 -j ACCEPT
[root@hadoop001 hadoop-2.6.0-cdh5.7.0]# iptables-save -c
# Generated by iptables-save v1.4.7 on Wed Jun 27 04:44:37 2018
*filter
:INPUT ACCEPT [0:0]
:FORWARD ACCEPT [0:0]
:OUTPUT ACCEPT [87:9214]
[0:0] -A INPUT -p tcp -m tcp --dport 8088 -j ACCEPT 
[85:12789] -A INPUT -p tcp -m tcp --dport 8088 -j ACCEPT 
[226:24326] -A INPUT -p tcp -m tcp --dport 50070 -j ACCEPT 
[25507:14503741] -A INPUT -m state --state RELATED,ESTABLISHED -j ACCEPT 
[1:84] -A INPUT -p icmp -j ACCEPT 
[286:17160] -A INPUT -i lo -j ACCEPT 
[1:60] -A INPUT -p tcp -m state --state NEW -m tcp --dport 22 -j ACCEPT 
[109:7372] -A INPUT -j REJECT --reject-with icmp-host-prohibited 
[0:0] -A INPUT -p tcp -m tcp --dport 50070 -j ACCEPT 
[0:0] -A INPUT -p tcp -m tcp --dport 80 -j ACCEPT 
[0:0] -A INPUT -p tcp -m tcp --dport 50070 -j ACCEPT 
[0:0] -A INPUT -p tcp -m tcp --dport 50070 -j ACCEPT 
[0:0] -A FORWARD -j REJECT --reject-with icmp-host-prohibited 
COMMIT
# Completed on Wed Jun 27 04:44:37 2018
```



4. Run a map reduce job on yarn

```shell
[hadoop@hadoop001 mapreduce]$ hadoop jar hadoop-mapreduce-examples-2.6.0-cdh5.7.0.jar wordcount /input/war_peace.txt /output/wc
18/06/27 04:54:01 WARN util.NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable
18/06/27 04:54:02 INFO client.RMProxy: Connecting to ResourceManager at /0.0.0.0:8032
18/06/27 04:54:04 INFO input.FileInputFormat: Total input paths to process : 1
18/06/27 04:54:04 INFO mapreduce.JobSubmitter: number of splits:1
18/06/27 04:54:04 INFO mapreduce.JobSubmitter: Submitting tokens for job: job_1530067192170_0002
18/06/27 04:54:04 INFO impl.YarnClientImpl: Submitted application application_1530067192170_0002
18/06/27 04:54:04 INFO mapreduce.Job: The url to track the job: http://localhost:8088/proxy/application_1530067192170_0002/
18/06/27 04:54:04 INFO mapreduce.Job: Running job: job_1530067192170_0002
18/06/27 04:54:13 INFO mapreduce.Job: Job job_1530067192170_0002 running in uber mode : false
18/06/27 04:54:13 INFO mapreduce.Job:  map 0% reduce 0%
18/06/27 04:54:21 INFO mapreduce.Job:  map 100% reduce 0%
18/06/27 04:54:30 INFO mapreduce.Job:  map 100% reduce 100%
18/06/27 04:54:30 INFO mapreduce.Job: Job job_1530067192170_0002 completed successfully
18/06/27 04:54:31 INFO mapreduce.Job: Counters: 49
	File System Counters
		FILE: Number of bytes read=649694
		FILE: Number of bytes written=1522423
		FILE: Number of read operations=0
		FILE: Number of large read operations=0
		FILE: Number of write operations=0
		HDFS: Number of bytes read=3359656
		HDFS: Number of bytes written=487290
		HDFS: Number of read operations=6
		HDFS: Number of large read operations=0
		HDFS: Number of write operations=2
	Job Counters 
		Launched map tasks=1
		Launched reduce tasks=1
		Data-local map tasks=1
		Total time spent by all maps in occupied slots (ms)=6395
		Total time spent by all reduces in occupied slots (ms)=5401
		Total time spent by all map tasks (ms)=6395
		Total time spent by all reduce tasks (ms)=5401
		Total vcore-seconds taken by all map tasks=6395
		Total vcore-seconds taken by all reduce tasks=5401
		Total megabyte-seconds taken by all map tasks=6548480
		Total megabyte-seconds taken by all reduce tasks=5530624
	Map-Reduce Framework
		Map input records=66055
		Map output records=566308
		Map output bytes=5541955
		Map output materialized bytes=649694
		Input split bytes=106
		Combine input records=566308
		Combine output records=41991
		Reduce input groups=41991
		Reduce shuffle bytes=649694
		Reduce input records=41991
		Reduce output records=41991
		Spilled Records=83982
		Shuffled Maps =1
		Failed Shuffles=0
		Merged Map outputs=1
		GC time elapsed (ms)=183
		CPU time spent (ms)=3230
		Physical memory (bytes) snapshot=289292288
		Virtual memory (bytes) snapshot=6093733888
		Total committed heap usage (bytes)=139317248
	Shuffle Errors
		BAD_ID=0
		CONNECTION=0
		IO_ERROR=0
		WRONG_LENGTH=0
		WRONG_MAP=0
		WRONG_REDUCE=0
	File Input Format Counters 
		Bytes Read=3359550
	File Output Format Counters 
		Bytes Written=487290

```

