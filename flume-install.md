# Flume Installation

1. Download Flume 1.9.0 and move it to hadoop_ecosystem and extract(apache-flume-1.9.0-bin.tar.gz) - https://archive.apache.org/dist/flume/

3. Edit bashrc file.

```#Flume
export FLUME_HOME=/usr/local/hadoop-ecosystem/flume-1.9.0
export PATH=$PATH:$FLUME_HOME/bin
#export CLASSPATH=$CLASSPATH:/FLUME_HOME/lib/*
``` 

```
$ source ~/.bashrc
```

3. Goto conf folder
copy paste flume-conf.properties.template and rename as flume-conf.properties
copy paste flume-env.sh.template and rename as flume-env.sh 

Update the JAVA_HOME of flume-env.sh

```export JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk-amd64```

$ echo $JAVA_HOME //find where is the Java home
/usr/lib/jvm/java-1.8.0-openjdk-amd64


4.Create netcat.conf file and put the below code in:


hadoop@ubuntudev:~$ cd $FLUME_HOME

hadoop@ubuntudev:/usr/local/hadoop-ecosystem/flume-1.9.0$ cd conf

hadoop@ubuntudev:/usr/local/hadoop-ecosystem/flume-1.9.0/conf$ touch netcat.conf

hadoop@ubuntudev:/usr/local/hadoop-ecosystem/flume-1.9.0/conf$ nano netcat.conf

hadoop@ubuntudev:/usr/local/hadoop-ecosystem/flume-1.9.0/conf$ 


```
MyAgent.sources = MySource
MyAgent.sinks = MySinks
MyAgent.channels = MyChannel

#Configure the source
MyAgent.sources.MySource.type = netcat
MyAgent.sources.MySource.bind = localhost
MyAgent.sources.MySource.port = 4444

#Configure the Channel

MyAgent.channels.MyChannel.type = memory
MyAgent.channels.MyChannel.capacity = 1000
MyAgent.channels.MyChannel.transactionCapacity = 100

#Configure the sink.

MyAgent.sinks.MySinks.type = logger

#Bind the source and sink to the channels
MyAgent.sources.MySource.channels = MyChannel
MyAgent.sinks.MySinks.channel = MyChannel
```

5. Execute the flume agent in terminal flume directory
```
hadoop@ubuntudev:/usr/local/hadoop-ecosystem/flume-1.9.0$ bin/flume-ng agent --conf ./conf/ -f conf/netcat.conf -Dflume.root.logger=DEBUG,console -n MyAgent
```
Connect to the port:
```
hadoop@ubuntudev:~$ telnet localhost 4444
```

# Flume connection with Twitter

https://github.com/kyleiwaniec/w205Project/tree/master/flume

wget https://github.com/kyleiwaniec/w205Project/blob/master/flume/flume-sources-1.0-SNAPSHOT.jar

wget https://github.com/kyleiwaniec/w205Project/blob/master/flume/twitter4j-stream-2.2.6.jar

wget https://github.com/kyleiwaniec/w205Project/blob/master/flume/twitter4j-stream-4.0.4.jar

cp flume-sources-1.0-SNAPSHOT.jar $FLUME_HOME/lib

cp twitter4j-stream-2.2.6.jar $FLUME_HOME/lib

cp twitter4j-stream-4.0.4.jar $FLUME_HOME/lib



Create/update conf/flume-env.sh file with the following entries

export JAVA_HOME=/usr/java/jdk1.7.0_67-cloudera
FLUME_CLASSPATH="/usr/local/hadoop_ecosystem/flume-1.9.0/lib/flume-sources-1.0-SNAPSHOT.jar"



Twitter use case: Streaming twitter app log data into HDFS

Steps to be performed in twitter app:

URL: https://apps.twitter.com/


create new app in the twitter -> -> create an app -> What is your primary reason for using Twitter developer tools? Student
-> Add a valid phone number
-> Activate it
-> Will use it for learning and practicing bigdata tools. I would use  Apache flume to ingest the twitter data into Hadoop-HDFS and and move it to Hive for practicing the near real time streaming data using Apache Flume. 
-> Will pull the data from twitter and analyze it using big data tools.  I would use  Apache flume to ingest the twitter data into Hadoop-HDFS and and move it to Hive for practicing the near real time streaming using Apache Flume. 
-> Yes, the app will use Tweet, Retweets and use this data for analysis in the hadoop ecosystem. I would use  Apache flume to ingest the twitter data into Hadoop-HDFS and and move it to Hive for practicing the near real time streaming using Apache Flume. 
-> I would use  Apache flume to ingest the twitter data into Hadoop-HDFS and and move it to Hive for practicing the aggregation of the near real time streaming data using Apache Flume. 
-> No, the derived information will not be available to a government entity.


Create an app.

App name: FlumeTweetsApp

Desc: This app will be used for testing Flume data ingesting using Twitter API.

Website URL : https://flumetest.com

Allow this app to signin to Twitter.

Callback URL:
https://flumetest.com



get the following values

Consumer Key (API Key): nR7U6HWxy0UFcZCIelM67X4A8
Consumer Secret (API Secret): JcLAq5SfyryDysklDl9NUhPCZWgJGVR4KLjIiL9kiOcEu4O9fE
Access Token:   1097509651111911426-ZK10URSlYoiUn7YFYDGcNSuLqLi6jA
Access Token Secret:  is5CdY2tsNL8sWOC3g4iqkiH3OdI7fA6kIZQfyo2r90om



Consumer API keys
-----------------

API key:
fivRfXBqJfhXG2I5eH1IitJyl

API secret key:
IIUfw8cDg1oLRcln557wS2pYxcFgWrArK0K7VpbLgs1wody5P4


Access token : 1097509651111911426-JNzrDWUWFyAYEmmejJabYcWDWWk4p4

Access token secret : 0CyHfuBF5rjo8GWwoRXMBC42Jkx6x8xsv4qqvuMgzQTMG



hdfs dfs -mkdir /user/twitter_data


Create/update twitter.conf file in   $FLUME_HOME/lib/conf  directory with details

### Naming the components on the current agent. 
TwitterAgent.sources = Twitter 
TwitterAgent.channels = MemChannel 
TwitterAgent.sinks = HDFS
  
### Describing/Configuring the source 
TwitterAgent.sources.Twitter.type = org.apache.flume.source.twitter.TwitterSource
TwitterAgent.sources.Twitter.consumerKey = fivRfXBqJfhXG2I5eH1IitJyl
TwitterAgent.sources.Twitter.consumerSecret = IIUfw8cDg1oLRcln557wS2pYxcFgWrArK0K7VpbLgs1wody5P4 
TwitterAgent.sources.Twitter.accessToken = 1097509651111911426-ciVl9ptmoRV3yPjDSVK6ViGy1Pd7Tq 
TwitterAgent.sources.Twitter.accessTokenSecret = rSmZZx192VL8IwlruyR200nADt30w1Dq3KoR1LZDnU77R
TwitterAgent.sources.Twitter.keywords = bigdata, mapreduce, spark, sqoop, hive
  
### Describing/Configuring the sink 

TwitterAgent.sinks.HDFS.type = hdfs 
TwitterAgent.sinks.HDFS.hdfs.path = hdfs://localhost:9000/user/twitter_data/
TwitterAgent.sinks.HDFS.hdfs.fileType = DataStream 
TwitterAgent.sinks.HDFS.hdfs.writeFormat = Text 
TwitterAgent.sinks.HDFS.hdfs.batchSize = 1000
TwitterAgent.sinks.HDFS.hdfs.rollSize = 0 
TwitterAgent.sinks.HDFS.hdfs.rollCount = 10000 
 
### Describing/Configuring the channel 
TwitterAgent.channels.MemChannel.type = memory 
TwitterAgent.channels.MemChannel.capacity = 10000 
TwitterAgent.channels.MemChannel.transactionCapacity = 1000
  
### Binding the source and sink to the channel 
TwitterAgent.sources.Twitter.channels = MemChannel
TwitterAgent.sinks.HDFS.channel = MemChannel


Execute twitter.conf


bin/flume-ng agent --conf ./conf/ -f conf/twitter.conf -Dflume.root.logger=DEBUG,console -n TwitterAgent

#bin/flume-ng agent --conf ./conf/ -f conf/netcat.conf -Dflume.root.logger=DEBUG,console -n MyAgent




