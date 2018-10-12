# Setting Up Pseudo-Distributed Hadoop

## 1. Setup a Linux VM:

**Minimum Requirements:**
- 50GB Disk
- Administrator account
- SSH Keys for login

## 2. Setup SSH Login

1. Create SSH Key using dsa:
```
ssh-keygen -t dsa -P '' -f ~/.ssh/id_dsa
```

2. Copy SSH Key to localhost
```
ssh-copy-id -i ~/.ssh/id_rsa.pub admin@localhost
```

## 3. Install Java JDK 1.7 or above
Installation using yum is not recommended as many functionalities of JDK such as JPS is not included.
Instead, copy the official rpm from oracle to install it.

## 4. Download Hadoop

1. Download from this [site](https://hadoop.apache.org/releases.html). Extract with:
```
tar xvfz hadoop-x.x.x.tar.gz
```

## 5. Set hadoop environment variables in hadoop-env.sh

Add a line in etc/hadoop/hadoop-env.sh
```
export JAVA_HOME=${JAVA_HOME}
export HADOOP_PREFIX=/hadoop/install/location
```

Since hadoop-env.sh takes JAVA_HOME from user environment variables, add the following lines to ~/.bashrc for hadoop user
```
export JAVA_HOME=/usr/java/latest
export PATH=$PATH:$JAVA_HOME/bin
```

## 5. Edit core-site.xml for localhost access

Add the following to etc/hadoop/core-site.xml
```xml
<!-- Under configurations -->
<configuration>
    <property>
        <name>fs.defaultFS</name>
        <value>hdfs://localhost:9000</value>
    </property>
</configuration>
```

## 6. Edit hdfs-site.xml replication factor to 1 in pseudo-distributed mode
Add the following to etc/hadoop/hdfs-site.xml
```xml
<configuration>
  <property>
    <name>dfs.replication</name>
    <value>1</value>
  </property>
</configuration>
```

## 7. Create and edit mapred-site.xml to use Yarn
Add the following to etc/hadoop/mapred-site.xml
```xml
<configuration>
    <property>
        <name>mapreduce.framework.name</name>
        <value>yarn</value>
    </property>
</configuration>
```

## 8. Configure yarn-site.xml
Add the following to etc/hadoop/yarn-site.xml
```xml
<configuration>
<!-- Site specific YARN configuration properties -->
  <property>
    <name>yarn.nodemanager.aux-services</name>
    <value>mapreduce_shuffle</value>
  </property>
</configuration>
```

The following needs to be added to etc/hadoop/yarn-site.xml if Java 8 is being used due to Java 8's excessive memory allocation (https://issues.apache.org/jira/browse/YARN-4714):
```xml
<property>
  <name>yarn.nodemanager.pmem-check-enabled</name>
  <value>false</value>
</property>

<property>
  <name>yarn.nodemanager.vmem-check-enabled</name>
  <value>false</value>
</property>
``` 

## 9. Format the Namenode
```
bin/hdfs namenode -format
```

## 10. Start the master and slave node of distributed filesystem
```
sbin/start-dfs.sh
```
Hadoop might throw warning because native-hadoop was built on 32-bit systems. To suppress warnings, rebuild hadoop source on 64bit machines.

**Gotcha:**
The permissions for ".sh" files in sbin must be set to at least executable by hadoop users to be able to start.
```
chmod u+x sbin/*.sh
```

We should be able to view files using
```
bin/hdfs dfs -ls
```
or we can use a browser to view at http://localhost:50070 (Don't forget to open firewall port).

We can view the resources with `jps`

## 11. Start Yarn
```
sbin/start-yarn.sh
```
We should be able to view cluster resources with http://localhost:8088 (Remember to open firewall port).


