# Data Lab notebook 

The Data Lab notebook is an environment that runs:

- Jupyter notebook, Python and Spark 

This _Data Lab_ notebook is built from the Docker container
`jupyter/pyspark-notebook` 
which is a Jupyter notebook from which you can run Python and Spark. 
The reason behind running this environment in a Docker container is 
that everyone one who is using this setup runs the same environment, 
which makes it easier to work together and share code. 

For details on the container see 
https://github.com/jupyter/docker-stacks. 
Look for the folder `pyspark-notebook`
or follow [this link](https://github.com/jupyter/docker-stacks/tree/master/pyspark-notebook).

There are two ways to install the notebook: 

1. Using Docker natively to run `jupyter/pyspark-notebook`. 
    See section [Native Docker install](#native-docker-install) below. 
1. Using Vagrant to create a virtual machine in which 
    Docker is loaded and which runs `jupyter/pyspark-notebook`
    See section [Vagrant Docker install](#vagrant-docker-install) below. 
    
The reason for taking these two approaches is that certain laptops 
have had trouble with one of these approaches, but so far no laptops 
have had trouble with both. 
    
## Native Docker install

An overview of the procedure is:

1. Install Docker
1. Run the Docker Quickstart terminal
1. Install the `jupyter/pyspark-notebook` container
1. Run the `jupyter/pyspark-notebook` container
1. Open the notebook interface with your browser

Follow the steps below to install the notebook natively with Docker. 

### Step 1 - Downloads and installs

Download and install

- _VirtualBox_ --- https://www.virtualbox.org
- _VirtualBox Extension Pack_ --- https://www.virtualbox.org/wiki/Downloads
- _Docker Toolbox_ --- https://www.docker.com/products/docker-toolbox

### Step 2 - Run _Docker Quickstart Terminal_

Run the _Docker Quickstart Terminal_ either at the end of the install above 
or as an application if Docker is already installed. 

If this is successful you will see the following in a terminal window:

```
docker is configured to use the default machine with IP 192.168.99.100
For help getting started, check out the docs at https://docs.docker.com
```

though your IP address may be different. 

### Step 3 - Get GitHub repository 

In this step you will download the `datalab-notebook` from GitHub 
and determine the location of the `Notebooks` directory on your laptop.
First, type the following command to download the repository.
The dollar sign indicates that this is a shell command and should not be typed.
```
$ git clone https://github.com/davidoury/datalab-notebook
```

The next three commands determine the location of the `Notebooks` sub-directory 
of the GitHub repository you just downloaded in the `datalab-notebook` directory.
Type the following commands.
```
$ cd datalab-notebook
$ cd Notebooks
$ pwd 
```
I'll refer to the output from the `pwd` command as `[YOUR NOTEBOOK DIRECTORY]` in step 5 below.

### Step 4 - Pull (download) the docker image

From the terminal window type the following command.
```
$ docker pull jupyter/pyspark-notebook
```
This command will take several minutes to complete. 

You should see the following if the command was successful. 

```
Status: Downloaded newer image for jupyter/pyspark-notebook:latest
```

### Step 5 - Run the container

Type the following into the terminal window, 
but replace `[YOUR NOTEBOOK DIRECTORY]` below with the 
output from the `pwd` command in step 3 above.
```
$ docker run -d -p 8888:8888 -v [YOUR NOTEBOOK DIRECTORY]:/home/jovyan/work jupyter/pyspark-notebook
```
For instance, the command that I use on my Mac is
```
$ docker run -d -p 8888:8888 -v /Users/david/GitHub/datalab-notebook/Notebooks:/home/jovyan/work jupyter/pyspark-notebook
```
The command that I use with Windows is
```
$ docker run -d -p 8888:8888 -v /c/Users/DOURY/datalab-notebook/Notebooks:/home/jovyan/work jupyter/pyspark-notebook
```
If this command is successful you will see a 64 character string 
```
f907da189c38087329de920c724ead41e2e1faa0bae4291f9f5f8b3dcdd1d234
```
though your individual characters may differ. 

Determine the IP address of your Docker machine by running the following command
```
$ docker-machine ip default
```
and recordng the IP address that it returns. 
For example, my IP address is `192.168.99.100`. 
You will use this in the next step. 
 
### Step 6 - open the notebook interface in your browser

Point your browser to 

- http://192.168.99.100:8888

where you will need to replace the IP `192.168.99.100` with your IP address (as obtained in step 6) if they differ. 

You should now see the notebooks that we have prepared for class. 

The installation is complete and you can now use the Jupyter notebook interface to run Python and Spark.

## Vagrant-Docker install

1. Install Vagrant
1. Create an ubuntu virtualbox with Vagrant which runs 
  the `jupyter/pyspark-notebook` container


### Step 1 - Downloads and installs

Download and install (in this order):

1. [VirtualBox](https://www.virtualbox.org)
1. [Vagrant](https://www.vagrantup.com)
1. [GitHub Desktop](https://desktop.github.com) (Windows only)

### Step 2 - Get GitHub repository

On Windows, open the `Git Shell`. 
On Mac, open the `Terminal` program. 
You can change your current directory in this terminal window using the `cd` command if you like. 
This will determine where the `datalab-notebook` is created. 

Now type the following commands. 
```
$ git clone https://github.com/davidoury/datalab-notebook
$ cd datalab-notebook/Notebooks
$ pwd
```
Save the output of the `pwd` command for step ???.

### Step 3 - Create the virtualbox

The creation of the virtualbox includes instructions to 
install docker and run the `jupyter/pyspark-notebook` container. 
From the terminal window type
```
$ vagrant up datalab
```
This may take up to 15 minutes to complete. 

### Step 4 - Connect to the 

Point your browser to http://10.10.10.10:8888. 
In the upper left corner you should see "jupyter" alongside its logo. 

The installation is complete and you can now use the Jupyter notebook interface to run Python and Spark.

### Stopping and starting the virtual box

There are several options to stop/shutdown the `datalab` virtual box:

1. Shutdown your computer.
1. _VirtuaBox_: safely stop the machine and save its state
1. _Console_: Set the current directory to `datalab-notebook` and run:
```
$ vagrant halt datalab
```

There are two options to start the `datalab` virtual box:

1. _VirtualBox_: start the `datalab` virtual box 
1. _Console_: Set the current directory to `datalab-notebook` and run:
```
$ vagrant up datalab
```
The notebook interface at http://10.10.10.10:8888 will be available 
when the `datalab` virtual box has started. 

## Troubleshooting

To destroy the `datalab` virtual box and start over again, open a console/shell on the host, 
set the current directory to `datalab-notebook` and run the following:
```
$ vagrant destroy -f datalab
```

Download and install the _Docker Toolbox_. 
See https://www.docker.com/products/docker-toolbox. 
Accept all defaults. 

Open a console window by either running _Terminal_ (on Mac) 
or running _Docker Quickstart_ (on Windows).
Now run the remaining commands in this section from that console window. 

First, create a docker machine named `datalab`.
If you have 8GB of ram you might change `2048` below to `4096` and so allocate 4GB of RAM to the docker machine and its containers.

Clean all containers: 
```
docker ps -a | sed '1 d' | awk '{print $1}' | xargs -L1 docker rm
```

Clean all images: 
```
docker images -a | sed '1 d' | awk '{print $3}' | xargs -L1 docker rmi -f
```
