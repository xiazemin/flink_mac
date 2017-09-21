# section4

Samza使用入门



本文基于Samza-0.10



Samza及相关环境



Samza依赖于kafka、zookeeper、Hadoop。运行samza之前需要先配置好相关环境。



如果对他们不熟悉的话也不要害怕，Hello Samza中集成了这三者，对于新手及其友好。本文依据于此进行介绍的。但熟悉之后强烈建议自己配置相关环境，管理相关环境的版本。



本文针对已经完整执行Hello Samza提供的所有例子后，想自己编写代码并运行的情况。



Samza运行



Samza运行前首先要将kafka、zookeeper、Yarn（分布式运行时）启动。



运行代码为：



samza/bin/run-job.sh \

--config-factory=org.apache.samza.config.factories.PropertiesConfigFactory \

--config-path=file://$PWD/samza/config/config.properties

1

2

3

运行时需要配置好config.properties。



Samza Job配置文件



一个简单的运行配置文件为：



\#Job

\#指定运行环境，本地测试使用ThreadJobFactory或ProcessJobFactory

\#job.factory.class=org.apache.samza.job.yarn.YarnJobFactory

job.factory.class=org.apache.samza.job.local.ThreadJobFactory

job.name=CountLatancy



\#YARN

\#本地运行时可以省略

yarn.package.path=file://{tmp}/filename.tar.gz

\#Task

\#主类名，包名.主类名

task.class=samza.CountLatancy

\#使用kafka作为输入流时,字段含义为kafka.topic

task.inputs=kafka.clickevent

\#windowable job 必须配置，单位为ms

task.window.ms=60000



\#Serializers

\#定义序列化与反序列化类

serializers.registry.json.class=org.apache.samza.serializers.JsonSerdeFactory

serializers.registry.string.class=org.apache.samza.serializers.StringSerdeFactory

serializers.registry.integer.class=org.apache.samza.serializers.IntegerSerdeFactory



\# Kafka System

systems.kafka.samza.factory=org.apache.samza.system.kafka.KafkaSystemFactory

\#输入流的键值序列化时使用哪一个序列化类。

systems.kafka.samza.key.serde=string

systems.kafka.samza.msg.serde=string

systems.kafka.consumer.zookeeper.connect=localhost:2181

systems.kafka.producer.bootstrap.servers=localhost:9092





\# Job Coordinator

job.coordinator.system=kafka

\# Normally, this would be 3, but we have only one broker.

job.coordinator.replication.factor=1

1

2

3

4

5

6

7

8

9

10

11

12

13

14

15

16

17

18

19

20

21

22

23

24

25

26

27

28

29

30

31

32

33

34

35

36

Samza Demo代码



package samza;



import java.util.HashMap;

import java.util.Map;

import java.util.Map.Entry;



import org.apache.samza.config.Config;

import org.apache.samza.system.IncomingMessageEnvelope;

import org.apache.samza.system.OutgoingMessageEnvelope;

import org.apache.samza.system.SystemStream;

import org.apache.samza.task.InitableTask;

import org.apache.samza.task.MessageCollector;

import org.apache.samza.task.StreamTask;

import org.apache.samza.task.TaskContext;

import org.apache.samza.task.TaskCoordinator;

import org.apache.samza.task.WindowableTask;

import org.json.JSONObject;



public class CountLatancy implements StreamTask,WindowableTask,InitableTask{

    Map&lt;String,Integer&gt; clickEvent = new HashMap&lt;String,Integer&gt;\(\);

    Map&lt;String,Integer&gt; clickCount = new HashMap&lt;String,Integer&gt;\(\);

    int total = 0;

    private static String TOPIC\_NAME = "pageviews";



    private static SystemStream stream = new SystemStream\("kafka",TOPIC\_NAME\);



    public void window\(MessageCollector collector, TaskCoordinator coodinator\)

            throws Exception {



        //int total = 0;

        for\(Entry&lt;String,Integer&gt; entry:clickEvent.entrySet\(\)\){

            String name = entry.getKey\(\);

            int latancy = entry.getValue\(\);

            int count = clickCount.get\(name\);

            String json = "{\"name\" : \""+name+"\""+

                    ",\"total\_latancyMs\" : "+latancy+

                    ",\"clickNum\" : "+count+

                    ",\"aver\_lantancyMs\" : "+\(latancy+0.0\)/count+

                    "}";

            total+=count;

            collector.send\(new OutgoingMessageEnvelope \(stream,json\)\);

        }

        collector.send\(new OutgoingMessageEnvelope \(stream,"Total messages per window : "+total+" ."\)\);

        clickEvent = new HashMap&lt;String,Integer&gt;\(\);

        clickCount = new HashMap&lt;String,Integer&gt;\(\);

    }



    public void process\(IncomingMessageEnvelope envelope, MessageCollector collector,

            TaskCoordinator coodinator\) throws Exception {

        JSONObject json = new JSONObject\(envelope.getMessage\(\).toString\(\)\);

        String name = json.getString\("name"\);

        int latancy = json.getInt\("latancyMs"\);

        if\(clickEvent.containsKey\(json.get\("name"\).toString\(\)\)\){

            clickEvent.put\(name, clickEvent.get\(name\)+latancy\);

            clickCount.put\(name, clickCount.get\(name\)+1\);

        }else{

            clickEvent.put\(name,latancy\);

            clickCount.put\(name,1\);

        }

    }



    public void init\(Config config, TaskContext task\) throws Exception {

        System.out.println\("----------------------------------------------------------------"\);

        System.out.println\(config.toString\(\)\);

        System.out.println\("----------------------------------------------------------------"\);



    }

}

代码是统计一下固定时间段内，每个人点击总点击次数和总时延。输入流demo为：



{"timestamp":"2015-09-01T01:35:19Z","url":"/foo","user":"bob","latencyMs":66}

1

Samza文件夹结构



在运行Samza程序之前，首先要了解一下Samza文件夹的组织结构。



Samza主文件夹下存在三个重要文件夹，bin、lib、config.



bin文件夹下存放各种启动代码（run-job.sh等）。



lib文件夹下存放以jar包形式存放的Samza运行环境，包括需要执行的Samza Job类。



config文件夹下存放各种Samza运行配置文件（test.properties）。



代码放在哪



代码需要使用maven工具进行打包，打包成jar包（不需要将运行环境打包进去）。放置于samza/lib/下。



如果本地运行，准备工作就结束了，可以直接运行了。



如果是Yarn job，就需要将打包后的jar包放置于samza/lib/文件夹下后，将samza/\*压缩为一个gz文件。这个压缩文件就是配置文件中的第9行中我们指定的那个运行包。



YARN job 运行流程



cd {src\_home}

mvn clean package

cp target/target.jar {samza\_home}/lib/

cd {samza\_home}

tar -zcvf filename.tar.gz \*

mv filename.tar.gz {tmp}/

{samza\_home}/bin/run-job.sh \

--config-factory=org.apache.samza.config.factories.PropertiesConfigFactory \

--config-path=file://{samza\_home}/config/config.properties





