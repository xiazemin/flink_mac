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



