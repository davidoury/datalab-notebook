## Data Lab notebook 

This _Data Lab_ notebook is based on the Docker container
`jupyter/pyspark-notebook` 
which is a Jupyter notebook from which you can run Python and Spark. 
For details on the container see 
https://github.com/jupyter/docker-stacks. 
Look for the folder `pyspark-notebook`
or follow [this link](https://github.com/jupyter/docker-stacks/tree/master/pyspark-notebook).

In order that this notebook works on as many OSes as possible, I've taken the approach of:

1. Creating an Ubuntu virtual box with Vagrant
1. Running Docker from within this virtual box 

The primary reason for this setup is that the host operating system (Mac, Windows 7/8/10) 
needs only to be able to complete the first step and allow the user to log into the virtual box. 
In addition, the configuration and management of the virtual box is (should be?) identical
regardless of host operating system.
I hope to avoid many headaches previously encountered.

To install and then use the notebook follow the directions 
in the [Create](#create) section below.

## Documentation links

- [Spark 1.5.1](https://spark.apache.org/docs/1.5.1/)
- [Spark 1.5.1 Quick Start](https://spark.apache.org/docs/1.5.1/quick-start.html) (start here)

## Create 

This section includes instructions to build a Jupyter notebook environment 
in which you can run Python and Spark. 
This entails many downloads so using an ethernet connection will make them quicker and potentially less problematic.

Windows machines should ensure that the `Virtualization` BIOS option is turned on.

Download and install (in this order):

1. [VirtualBox](https://www.virtualbox.org)
1. [VirtualBox extensions](http://download.virtualbox.org/virtualbox/5.0.14/Oracle_VM_VirtualBox_Extension_Pack-5.0.14-105127.vbox-extpack) (download link)
1. [Vagrant](https://www.vagrantup.com)
1. [GitHub Desktop](https://desktop.github.com) (Windows only)

Open a console/shell:

- Windows: run the _Git Shell_ (there should be an icon on your desktop). 
- Mac: run the terminal program
- Linux: create a console/shell window

Clone this GitHub repository:
```
$ git clone https://github.com/davidoury/datalab-notebook
```

Change to the `datalab-notebook` sub-directory (just created) and create the virtual box called `datalab`:
```
$ cd datalab-notebook
$ vagrant up datalab
```

Now run a shell/console on the newly created `datalab` virtual box:
```
$ vagrant ssh datalab
```

The prompt will change as you are now running a shell/console 
_inside_ the `datalab` virtual box.

Become the root (super) user:
```
sudo bash
```

Update all programs installed in the virtual box: 
```
apt-get update
```

Download and install the Docker program in the virtual box: 
```
curl -fsSL https://get.docker.com/ | sh
```

Add the `vagrant` user to the `docker` group so the `vagrant` user can run docker commands:
```
usermod -aG docker vagrant
```

Test your setup:
```
$ docker run hello-world
```
If you see the text `Hello from Docker` somewhere then all is well. 

Create some useful files so you can stop and start your virtual box 
and its notebook interface.
```
$ tar Pxvf /vagrant/cron.d.tar.gz
```

Download and run the Jupyter container:
```
$ /etc/cron.d/docker.sh
```
This will take a little while. 
[](If it does not complete you an simply rerun the command. 
To rerun this command in another console you will need to change the working directory 
to `datalab-notebook` then run)

Point your browser to http://10.10.10.10:8888. 

When you are done working shutdown the `datalab` virtual box from _VirtuaBox_
by safely stopping the machine and saving its state. 
Alternately, you can use a console/shell where `datalab-notebook` is the working directory 
and run:
```
vagrant halt datalab
```



## PLEASE IGNORE THIS SECTION --- IT CONTAINS OLD STUFF

Download and install the _Docker Toolbox_. 
See https://www.docker.com/products/docker-toolbox. 
Accept all defaults. 

Open a console window by either running _Terminal_ (on Mac) 
or running _Docker Quickstart_ (on Windows).
Now run the remaining commands in this section from that console window. 

First, create a docker machine named `datalab`.
If you have 8GB of ram you might change `2048` below to `4096` and so allocate 4GB of RAM to the docker machine and its containers.
```
$ docker-machine create --driver=virtualbox --virtualbox-memory=2048 datalab
```

Second, setup the Docker environment in your terminal/shell.
```
$ eval "$(docker-machine env datalab)"
```

Third, in the command below, 
change `YOURPASS` to some password of your choosing, and 
change `NOTEBOOKDIR` to an existing directory where you 
plan to save your notebooks. 
On Windows, the directory `C:\Users\DOURY\Notebooks` is 
specified as `/c/Users/DOURY/Notebooks` in the command below. 
Notice the leading forward slash "/", the lower case "c" and 
the replacement of all back slashes with forward slashes. 
After making all these changes, run this command
```
$ docker run -d -p 8888:8888  -e PASSWORD="YOURPASS"  -v NOTEBOOKDIR:/home/jovyan/work  jupyter/pyspark-notebook
```
which will run the docker container `jupyter/pyspark-notebook` in your Docker/VirtualBox machine named `datalab`.
It will take some time to create the container. 
If the output ends with 
```
... no space left on device
```
then run
```
$ docker-machine rm datalab
```
answer `yes` to the prompt and rerun the `docker-machine create` command above, but use `4096` in place of `2048`. 

When the container has been created successfully you should see something like this: 
```
Digest: sha256:e12307fc1f339eacab758088359aa6c2b84f8d0dee9fe617de6845af56f091f9
Status: Downloaded newer image for jupyter/pyspark-notebook:latest
8e7d27a3f8e14710a6d8c9dac52c5c20ae6406088ad0ce7c38a783ee1bfca470
```
though the seemingly random letters and numbers above 
won't be the same as yours. 

Finally, point your browser to
```
http://IP_ADDR:8888
```
where `IP_ADDR` is the result of the command
```
$ docker-machine ip datalab
```
then enter the password you specified 
in the previous `docker run` command.

In your browser you should see the Jupyter logo in the 
upper left corner of the page. 

Continue with any of the following sections to run sample commands. 

## Run some examples

From the home screen create a Python 3 notebook 

### In a Python Notebook

0. Run the container as shown above.
1. Open a Python 2 or 3 notebook.
2. Create a `SparkContext` configured for local mode.

For example, the first few cells in a Python 3 notebook might read:

```python
import pyspark
sc = pyspark.SparkContext('local[*]')

# do something to prove it works
rdd = sc.parallelize(range(1000))
rdd.takeSample(False, 5)
```

In a Python 2 notebook, prefix the above with the following code to ensure the local workers use Python 2 as well.

```python
import os
os.environ['PYSPARK_PYTHON'] = 'python2'

# include pyspark cells from above here ...
```

### In a R Notebook

0. Run the container as shown above.
1. Open a R notebook.
2. Initialize `sparkR` for local mode.
3. Initialize `sparkRSQL`.

For example, the first few cells in a R notebook might read:

```
library(SparkR)

sc <- sparkR.init("local[*]")
sqlContext <- sparkRSQL.init(sc)

# do something to prove it works
data(iris)
df <- createDataFrame(sqlContext, iris)
head(filter(df, df$Petal_Width > 0.2))
```

### In a Scala Notebook

0. Run the container as shown above.
1. Open a Scala notebook.
2. Use the pre-configured `SparkContext` in variable `sc`.

For example:

```
val rdd = sc.parallelize(0 to 999)
rdd.takeSample(false, 5)
```

## Connecting to a Spark Cluster on Mesos

This configuration allows your compute cluster to scale with your data.

0. [Deploy Spark on Mesos](http://spark.apache.org/docs/latest/running-on-mesos.html).
1. Configure each slave with [the `--no-switch_user` flag](https://open.mesosphere.com/reference/mesos-slave/) or create the `jovyan` user on every slave node.
2. Run the Docker container with `--net=host` in a location that is network addressable by all of your Spark workers. (This is a [Spark networking requirement](http://spark.apache.org/docs/latest/cluster-overview.html#components).)
    * NOTE: When using `--net=host`, you must also use the flags `--pid=host -e TINI_SUBREAPER=true`. See https://github.com/jupyter/docker-stacks/issues/64 for details.
3. Follow the language specific instructions below.

### In a Python Notebook

0. Open a Python 2 or 3 notebook.
1. Create a `SparkConf` instance in a new notebook pointing to your Mesos master node (or Zookeeper instance) and Spark binary package location.
2. Create a `SparkContext` using this configuration. 

For example, the first few cells in a Python 3 notebook might read:

```python
import os
# make sure pyspark tells workers to use python3 not 2 if both are installed
os.environ['PYSPARK_PYTHON'] = '/usr/bin/python3'

import pyspark
conf = pyspark.SparkConf()

# point to mesos master or zookeeper entry (e.g., zk://10.10.10.10:2181/mesos)
conf.setMaster("mesos://10.10.10.10:5050")
# point to spark binary package in HDFS or on local filesystem on all slave
# nodes (e.g., file:///opt/spark/spark-1.5.1-bin-hadoop2.6.tgz) 
conf.set("spark.executor.uri", "hdfs://10.10.10.10/spark/spark-1.5.1-bin-hadoop2.6.tgz")
# set other options as desired
conf.set("spark.executor.memory", "8g")
conf.set("spark.core.connection.ack.wait.timeout", "1200")

# create the context
sc = pyspark.SparkContext(conf=conf)

# do something to prove it works
rdd = sc.parallelize(range(100000000))
rdd.sumApprox(3)
```

To use Python 2 in the notebook and on the workers, change the `PYSPARK_PYTHON` environment variable to point to the location of the Python 2.x interpreter binary. If you leave this environment variable unset, it defaults to `python`.

Of course, all of this can be hidden in an [IPython kernel startup script](http://ipython.org/ipython-doc/stable/development/config.html?highlight=startup#startup-files), but "explicit is better than implicit." :)

### In a R Notebook

0. Run the container as shown above.
1. Open a R notebook.
2. Initialize `sparkR` Mesos master node (or Zookeeper instance) and Spark binary package location.
3. Initialize `sparkRSQL`.

For example, the first few cells in a R notebook might read:

```
library(SparkR)

# point to mesos master or zookeeper entry (e.g., zk://10.10.10.10:2181/mesos)\
# as the first argument
# point to spark binary package in HDFS or on local filesystem on all slave
# nodes (e.g., file:///opt/spark/spark-1.5.1-bin-hadoop2.6.tgz) in sparkEnvir
# set other options in sparkEnvir
sc <- sparkR.init("mesos://10.10.10.10:5050", sparkEnvir=list(
    spark.executor.uri="hdfs://10.10.10.10/spark/spark-1.5.1-bin-hadoop2.6.tgz",
    spark.executor.memory="8g"
    )
)
sqlContext <- sparkRSQL.init(sc)

# do something to prove it works
data(iris)
df <- createDataFrame(sqlContext, iris)
head(filter(df, df$Petal_Width > 0.2))
```

## Debugging

We may need these commands. 
Just ignore this section and these commands for now. 

Clean all containers: 
```
docker ps -a | sed '1 d' | awk '{print $1}' | xargs -L1 docker rm
```

Clean all images: 
```
docker images -a | sed '1 d' | awk '{print $3}' | xargs -L1 docker rmi -f
```
