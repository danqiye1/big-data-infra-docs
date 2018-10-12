# Setting up Hive to Work with Hadoop

## 1. Download the Hive binary

Download link is https://hive.apache.org/downloads.html

After download, unpack the tar file with
```
tar xvfz apache-hive-3.1.0-bin.tar.gz
```

Remember to grant ownership to hadoop user if file is transferred to server via root.

## 2. Add Environment Variables to ~/.bashrc
Add the following lines to ~/.bashrc
```
export HADOOP_HOME='/home/hadoop/hadoop'
export PATH=$PATH:$HADOOP_HOME/bin

export HIVE_HOME='/home/hadoop/hive'
export PATH=$PATH:$HIVE_HOME/bin

export CLASSPATH=$CLASSPATH:$HADOOP_HOME/lib/*:.
export CLASSPATH=$CLASSPATH:$HIVE_HOME/lib/*:
```

## 3. Edit Hive Configurations
1. Copy all contents of $HIVE_HOME/conf/hive-default.xml.template into $HIVE_HOME/conf/hive-site.xml.
2. In $HIVE_HOME/conf/hive-site.xml, replace ${system:java.io.tmpdir} with /tmp/hive_io to specify where to store temporary hive files.
3. Replace ${system:user.name} with 'hadoop' user

## 4. Create Hive directories in HDFS
Execute the following commands:
```
hadoop fs -mkdir /tmp
hadoop fs -mkdir /user
hadoop fs -mkdir /user/hive
hadoop fs -mkdir /user/hive/warehouse
```

The last command will create the directory for data warehouse.

## 5. Give Group Write Permissions to /user/hive/warehouse and /tmp
```
hadoop fs -chmod g+w /tmp
hadoop fs -chmod g+w /user/hive/warehouse
```

## 6. Initialize metastore database
Navigate into HIVE_HOME.
Execute the following to initalize a derby database for Hive metastore
```
schematool -initSchema -dbType derby
```

## 7. Run Hive CLI to verify that Hive is working
```
bash> hive
-------
Bunch of logging info
------
hive> show databases;
```

## 8. Add permanent path to metastore_db
Now hive can only be started within folder where metastore_db was instantiated. To make hive CLI work anywhere, change the following config from:
```xml
<property>
    <name>javax.jdo.option.ConnectionURL</name>
    <value>jdbc:derby:;databaseName=metastore_db;create=true</value>
</property>
```
to:
```xml
<property>
    <name>javax.jdo.option.ConnectionURL</name>
    <value>jdbc:derby:;databaseName=/home/hadoop/apache-hive-3.1.0-bin/metastore_db;create=true</value>
</property>
```

## 9. Use beeline CLI instead of hive CLI
```
beeline -u jdbc:hive2://
```

This is because it has better security and connects to improved hiveserver2.
