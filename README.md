# Introduction

介绍文档：

[http://flink.apache.org/introduction.html](http://flink.apache.org/introduction.html)

下载

[http://www.apache.org/dyn/closer.lua/flink/flink-1.3.2/flink-1.3.2-bin-hadoop26-scala\_2.11.tgz](http://www.apache.org/dyn/closer.lua/flink/flink-1.3.2/flink-1.3.2-bin-hadoop26-scala_2.11.tgz)

安装说明

[https://ci.apache.org/projects/flink/flink-docs-release-1.3/quickstart/setup\_quickstart.html](https://ci.apache.org/projects/flink/flink-docs-release-1.3/quickstart/setup_quickstart.html)

安装

方法－：

```
$ brew install apache-flink
```

方法二：

$tar -zxvf flink-1.3.2-bin-hadoop26-scala\_2.11.tgz

$cd flink-1.3.2

$./bin/start-local.sh

[http://localhost:8081/\#/overview](http://localhost:8081/#/overview)

![](/assets/flink.png)$ tail log/flink-\*-jobmanager-\*.log

2017-09-21 14:18:51,954 INFO  org.apache.flink.runtime.taskmanager.TaskManager              - TaskManager has 1 task slot\(s\).

2017-09-21 14:18:51,955 INFO  org.apache.flink.runtime.clusterframework.standalone.StandaloneResourceManager  - Trying to associate with JobManager leader akka.tcp://flink@localhost:6123/user/jobmanager

2017-09-21 14:18:51,959 INFO  org.apache.flink.runtime.taskmanager.TaskManager

运行实例：

$nc -l 9000

$./bin/flink run examples/streaming/SocketWindowWordCount.jar --port 9000

Cluster configuration: Standalone cluster with JobManager at localhost/127.0.0.1:6123

Using address localhost:6123 to connect to JobManager.

JobManager web interface address [http://localhost:8081](http://localhost:8081)

Starting execution of program

Submitting job with JobID: c6295dd2db9235ed251771b15970a009. Waiting for job completion.

Connected to JobManager at Actor\[akka.tcp://flink@localhost:6123/user/jobmanager\#342654186\] with leader session id 00000000-0000-0000-0000-000000000000.

09/21/2017 14:24:05    Job execution switched to status RUNNING.

09/21/2017 14:24:05    Source: Socket Stream -&gt; Flat Map\(1/1\) switched to SCHEDULED

09/21/2017 14:24:05    TriggerWindow\(TumblingProcessingTimeWindows\(5000\)

![](/assets/flinktask.png)

