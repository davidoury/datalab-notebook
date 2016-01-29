## Data Lab notebook = Jupyter notebook with Python, Scala, R, Spark and Mesos 

For now the "Data Lab notebook" is the the docker container called 
`jupyter/all-spark-notebook`. 
In time I hope to add to and modify the container to fit 
our needs at the Data Lab, 
but for now this is exactly what we need. 
It is part of the GitHub repository 
https://github.com/jupyter/docker-stacks. 
Look for the folder `all-spark-notebook`
or follow [this link](https://github.com/jupyter/docker-stacks/tree/master/all-spark-notebook).

To install and then use the notebook follow the directions 
in the section titled [Create the container](#create-the-container) below.

Most of the following text is copied directly from the page linked above.

## What it Gives You

* Jupyter Notebook 4.0.x
* Conda Python 3.x and Python 2.7.x environments
* Conda R 3.2.x environment
* Scala 2.10.x
* pyspark, pandas, matplotlib, scipy, seaborn, scikit-learn pre-installed for Python
* ggplot2, rcurl preinstalled for R
* Spark 1.5.1 for use in local mode or to connect to a cluster of Spark workers
* Mesos client 0.22 binary that can communicate with a Mesos master
* Unprivileged user `jovyan` (uid=1000, configurable, see options) in group `users` (gid=100) with ownership over `/home/jovyan` and `/opt/conda`
* [tini](https://github.com/krallin/tini) as the container entrypoint and [start-notebook.sh](../minimal-notebook/start-notebook.sh) as the default command
* Options for HTTPS, password auth, and passwordless `sudo`

## Create the container

To run this notebook with Docker open a terminal window (on Mac) 
or a command shell (on Windows) and run the remaining commands in this section. 

First, create a docker machine named `datalab`. 
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
Run this command
```
$ docker run -d -p 8888:8888  -e USE_HTTPS=yes -e PASSWORD="YOURPASS"  -v NOTEBOOKDIR:/home/jovyan/work  jupyter/all-spark-notebook
```
which will run the docker container `jupyter/all-spark-notebook` in your Docker/VirtualBox machine named `datalab`.
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
Status: Downloaded newer image for jupyter/all-spark-notebook:latest
8e7d27a3f8e14710a6d8c9dac52c5c20ae6406088ad0ce7c38a783ee1bfca470
```
though the seemingly random letters and numbers above 
won't be the same as yours. 

Finally, point your browser to
```
https://IP_ADDR:8888
```
where `IP_ADDR` is the result of the command
```
$ docker-machine ip datalab
```
then enter the password you specified 
in the previous `docker run` command.

In your browser you should see the Jupyter logo in the upper left corner of the page. 

Continue on to any of the following sections to run sample commands. 

## Run some examples

From the home screen create a Python 3 notebook 

## Debugging

clean all containers: 
```
docker ps -a | sed '1 d' | awk '{print $1}' | xargs -L1 docker rm
```

clean all images: 
```
docker images -a | sed '1 d' | awk '{print $3}' | xargs -L1 docker rmi -f
```

```
https://www.docker.com/products/docker-toolbox
```


## Using Spark Local Mode

This configuration is nice for using Spark on small, local data.

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

### In a Scala Notebook

0. Open a terminal via *New -> Terminal* in the notebook interface.
1. Add information about your cluster to the Scala kernel spec file in `~/.ipython/kernels/scala/kernel.json`. (See below.)
2. Open a Scala notebook.
3. Use the pre-configured `SparkContext` in variable `sc`.

The Scala kernel automatically creates a `SparkContext` when it starts based on configuration information from its command line arguments and environments. Therefore, you must add it to the Scala kernel spec file. You cannot, at present, configure it yourself within a notebook.

For instance, a kernel spec file with information about a Mesos master, Spark binary location in HDFS, and an executor option appears here:

```
{
    "display_name": "Scala 2.10.4",
    "language": "scala",
    "argv": [
        "/opt/sparkkernel/bin/sparkkernel",
        "--profile",
        "{connection_file}",
        "--master=mesos://10.10.10.10:5050"
    ],
    "env": {
        "SPARK_CONFIGURATION": "spark.executor.memory=8g,spark.executor.uri=hdfs://10.10.10.10/spark/spark-1.5.1-bin-hadoop2.6.tgz"
    }
}
```

Note that this is the same information expressed in a notebook in the Python case above. Once the kernel spec has your cluster information, you can test your cluster in a Scala notebook like so:

```
// should print the value of --master in the kernel spec
println(sc.master)

// do something to prove it works
val rdd = sc.parallelize(0 to 99999999)
rdd.sum()
```

## Docker Options

You may customize the execution of the Docker container and the Notebook server it contains with the following optional arguments.

* `-e PASSWORD="YOURPASS"` - Configures Jupyter Notebook to require the given password. Should be conbined with `USE_HTTPS` on untrusted networks.
* `-e USE_HTTPS=yes` - Configures Jupyter Notebook to accept encrypted HTTPS connections. If a `pem` file containing a SSL certificate and key is not found in `/home/jovyan/.ipython/profile_default/security/notebook.pem`, the container will generate a self-signed certificate for you.
* `-e NB_UID=1000` - Specify the uid of the `jovyan` user. Useful to mount host volumes with specific file ownership.
* `-e GRANT_SUDO=yes` - Gives the `jovyan` user passwordless `sudo` capability. Useful for installing OS packages. **You should only enable `sudo` if you trust the user or if the container is running on an isolated host.**
* `-v /some/host/folder/for/work:/home/jovyan/work` - Host mounts the default working directory on the host to preserve work even when the container is destroyed and recreated (e.g., during an upgrade).
* `-v /some/host/folder/for/server.pem:/home/jovyan/.local/share/jupyter/notebook.pem` - Mounts a SSL certificate plus key for `USE_HTTPS`. Useful if you have a real certificate for the domain under which you are running the Notebook server.
* `-p 4040:4040` - Opens the port for the [Spark Monitoring and Instrumentation UI](http://spark.apache.org/docs/latest/monitoring.html). Note every new spark context that is created is put onto an incrementing port (ie. 4040, 4041, 4042, etc.), and it might be necessary to open multiple ports. `docker run -d -p 8888:8888 -p 4040:4040 -p 4041:4041 jupyter/all-spark-notebook`

## Conda Environments

The default Python 3.x [Conda environment](http://conda.pydata.org/docs/using/envs.html) resides in `/opt/conda`. A second Python 2.x Conda environment exists in `/opt/conda/envs/python2`. You can [switch to the python2 environment](http://conda.pydata.org/docs/using/envs.html#change-environments-activate-deactivate) in a shell by entering the following:

```
source activate python2
```

You can return to the default environment with this command:

```
source deactivate
```

The commands `ipython`, `python`, `pip`, `easy_install`, and `conda` (among others) are available in both environments.
