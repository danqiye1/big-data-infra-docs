# Setting up Jupyter Notebook for Spark2 and Hadoop

## 1. Download and install Anaconda in hadoop user.

Download link: https://repo.anaconda.com/archive/Anaconda3-5.2.0-Linux-x86_64.sh

Just run the .sh file to install.

## 2. Configure Jupyter

1. Generate the configuration for jupyter.
```bash
jupyter notebook --generate-config
```
This should generate the config in ~/.jupyter/jupyter_notebook_config.py

2. Create password for access to jupyter in Anaconda's python
```python
from notebook.auth import passwd
passwd()
```

3. Copy the string to jupyter config
```
##The string should be of the form type:salt:hashed-password.
c.NotebookApp.password = u'hash_from_above_step'
```

4. Configure jupyter ip, port and notebook directories.

Listen to all incoming connections
```
## The IP address the notebook server will listen on.
c.NotebookApp.ip = '0.0.0.0'
```

```
## The directory to use for notebooks and kernels.
c.NotebookApp.notebook_dir = u'/< Path-to-Notebook_dir> '
```

```
## The port the notebook server will listen on.
c.NotebookApp.port = 8888
```

Open the firewall port to allow access
```
firewall-cmd --add-port=8888/tcp --permanent
firewall-cmd --reload
```

5. Run Jupyter notebook
```
jupyter notebook
```
Note book should be available at http://ip_address:8888

## 3. Install Spark2

Download spark from official website and unroll the tarball
```
tar xvfz spark-2.3.2-bin-hadoop2.7.tar
```

## 4. Integrate Spark with YARN
1. Add the following environment variables in ~/.bashrc
```
export HADOOP_CONF_DIR=/home/hadoop/hadoop/etc/hadoop
export LD_LIBRARY_PATH=/home/hadoop/hadoop/lib/native:$LD_LIBRARY_PATH
```

2. Create a spark-defaults.conf
```
cp $SPARK_HOME/conf/spark-defaults.conf.template $SPARK_HOME/conf/spark-defaults.conf
```

3. Edit the following in spark-defaults.conf
```
spark.master yarn
```

## 5. Setup Spark2 to launch from jupyter notebook

Edit to ~/.bashrc
```
# Spark environment variables
export SPARK_HOME='/home/hadoop/spark'
export PATH=$SPARK_HOME/bin:$PATH

# Jupyter with Pyspark
export PYSPARK_SUBMIT_ARGS='pyspark-shell'
export PYSPARK_DRIVER_PYTHON=ipython
export PYSPARK_DRIVER_PYTHON_OPTS='notebook' pyspark
```

Now we can run a jupyter notebook with pyspark kernel using
```
pyspark
```

## 6. [Optional] Turn logging level down to WARN for spark-submit
Create a log4j.properties file
```
cp $SPARK_HOME/conf/log4j.properties.template $SPARK_HOME/conf/log4j.properties
```

Edit the following:
```
log4j.rootCategory=WARN, console
```


