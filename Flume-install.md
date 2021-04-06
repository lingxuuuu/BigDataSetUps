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

```hadoop@ubuntudev:~$ cd /usr/local/hadoop-ecosystem/flume-1.9.0/lib

hadoop@ubuntudev:/usr/local/hadoop-ecosystem/flume-1.9.0/lib$ wget https://github.com/kyleiwaniec/w205Project/blob/master/flume/flume-sources-1.0-SNAPSHOT.jar

hadoop@ubuntudev:/usr/local/hadoop-ecosystem/flume-1.9.0/lib$ wget https://github.com/kyleiwaniec/w205Project/blob/master/flume/twitter4j-stream-2.2.6.jar

hadoop@ubuntudev:/usr/local/hadoop-ecosystem/flume-1.9.0/lib$ wget https://github.com/kyleiwaniec/w205Project/blob/master/flume/twitter4j-stream-4.0.4.jar
```



Update conf/flume-env.sh file with the following entries
```
FLUME_CLASSPATH="/usr/local/hadoop-ecosystem/flume-1.9.0/lib/flume-sources-1.0-SNAPSHOT.jar"
```



## Twitter use case: Streaming twitter app log data into HDFS

Steps to be performed in twitter app:

Create an app.

App name: FlumeBDTest1

Desc: This app will be used for testing Flume data ingesting using Twitter API.

Website URL : https://flumetest.com

Allow this app to signin to Twitter.

Callback URL:
https://flumetest.com


#### Keys & tokens fronm Twitter

Consumer Key (API Key): zXRC3ApImqLJyM9DPkITyMdZy
Consumer Secret (API Secret): lGxDOIV4FP9ua2fftiZkxpKZ4y2WcnHo0LGBRnqw2IJgMvq2UE
//Bearer token is not that important
Bearer token: AAAAAAAAAAAAAAAAAAAAANNfOQEAAAAAEOnW6YqsmE287CS9BfsjR1EPCHQ%3DMg0wemicG2VCTLxbyULJYSeGeXkpSCp98u4mt8bUTp5Zr3KUHw

In Twitter App settings, generate the keys and tokens: 
Access token:  1379463248475430918-p0q2VRx8xkYNrk5fSq9EGv03DArjBu
Access token secret:  I5AjT4Wy6hjTyt53XcgwznFUP5VCDsVfzEdqCKpk3qve8



Create a new directory to store all the data fetched from twitter
```
hdfs dfs -mkdir /user/twitter_data
```

Create/update twitter.conf file in $FLUME_HOME/lib/conf directory with details
```
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
```


Execute twitter.conf


// data fetched from twitter will be store into: /user/twitter_data

```
hadoop@ubuntudev:/usr/local/hadoop-ecosystem/flume-1.9.0$ 
bin/flume-ng agent --conf ./conf/ -f conf/twitter.conf -Dflume.root.logger=DEBUG,console -n TwitterAgent
```

```
hadoop@ubuntudev:~$ hdfs dfs -ls /user/twitter_data
Found 4 items
-rw-r--r--   1 hadoop supergroup     594436 2021-04-06 11:28 /user/twitter_data/FlumeData.1617733649852
-rw-r--r--   1 hadoop supergroup     629624 2021-04-06 11:28 /user/twitter_data/FlumeData.1617733682771
-rw-r--r--   1 hadoop supergroup     610482 2021-04-06 11:29 /user/twitter_data/FlumeData.1617733713787
-rw-r--r--   1 hadoop supergroup     398261 2021-04-06 11:29 /user/twitter_data/FlumeData.1617733744813
```

```
#bin/flume-ng agent --conf ./conf/ -f conf/netcat.conf -Dflume.root.logger=DEBUG,console -n MyAgent
```




