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

### Step 1 - downloads and installs

Download and install

- _VirtualBox_ --- https://www.virtualbox.org
- _VirtualBox Extension Pack_ --- https://www.virtualbox.org/wiki/Downloads
- _Docker Toolbox_ --- https://www.docker.com/products/docker-toolbox

### Step 2 - run _Docker Quickstart Terminal_

Run the _Docker Quickstart Terminal_ either at the end of the install above 
or as an application if Docker is already installed. 

If this is successful you will see the following in a terminal window:

```
docker is configured to use the default machine with IP 192.168.99.100
For help getting started, check out the docs at https://docs.docker.com
```

though your IP address may be different. 

### Step 3 - Pull (download) the docker image

From the terminal window (opened by the _Docker Quickstart Terminal_) type the following command.
```
$ docker pull jupyter/pyspark-notebook
```
The dollar sign indicates that this is a shell command and should not be typed.
This command will take several minutes to complete. 

You should see the following if the command is successful. 

```
Status: Downloaded newer image for jupyter/pyspark-notebook:latest
```

### Step 4 - Setup a notebook directory

Create a directory called `Notebooks` in your filesystem.
The easiest way to do this is to type (from the _Quickstart Terminal_) the following commands. 
```
$ mkdir Notebooks
$ cd Notebooks
$ pwd
```
Save the output from the `pwd` command for the next step. 
This is the directory where your notebooks will be saved. 

### Step 5 - Run the container

Type the following into the terminal window, 
but replace "[YOUR NOTEBOOK DIRECTORY]" with the output from the `pwd` command above.
```
$ docker run -d -p 8888:8888 -v [YOUR NOTEBOOK DIRECTORY]:/home/jovyan/work jupyter/pyspark-notebook
```
For instance, the command that I use is on my Mac is
```
$ docker run -d -p 8888:8888 -v /Users/david/Notebooks:/home/jovyan/work jupyter/pyspark-notebook
```
The command that I use is on Windows is
```
$ docker run -d -p 8888:8888 -v /c/Users/DOURY/Notebooks:/home/jovyan/work jupyter/pyspark-notebook
```
If this command is successful you should see a 64 character string 
```
f907da189c38087329de920c724ead41e2e1faa0bae4291f9f5f8b3dcdd1d234
```
though your individual characters may differ. 

Windows users may need to create a directory 

Run the command 
```
$ docker-machine ip default
```
and record the IP address that it returns. 
For example, my IP address is `192.168.99.100`. 
You will use this in the next step. 
 
### Step 6 - open notebook in browser

Point your browser to 

- http://192.168.99.100:8888

if that is the IP address from previous step. 
Replace this IP `192.168.99.100` with your address if they differ. 

## Vagrant Docker Install

1. Install Vagrant
1. Creating an Ubuntu virtual box with Vagrant
1. Install and run Docker inside this virtual machine
1. Install the `jupyter/pyspark-notebook` container (in the virtual machine)
1. Run the `jupyter/pyspark-notebook` container (in the virtual machine)


### Step 1 - 

Download and install (in this order):

1. [Vagrant](https://www.vagrantup.com)
1. [GitHub Desktop](https://desktop.github.com) (Windows only)

### Step 1 - 

```
$ vagrant up datalab
```

### Step 1 - 

Open a console/shell:

- Windows: run the _Git Shell_ (there should be an icon on your desktop). 
- Mac: run the terminal program

Clone this GitHub repository:
```
$ git clone https://github.com/davidoury/datalab-notebook
```

### Step 1 - 

Change to the `datalab-notebook` sub-directory (just created) and create the virtual box called `datalab`:
```
$ cd datalab-notebook
$ vagrant up datalab
```

### Step 1 - 

Run a shell/console on the newly created `datalab` virtual box:
```
$ vagrant ssh datalab
```

The prompt is different that the previous one as you are now 
running a shell/console _inside_ the `datalab` virtual box.

### Step 1 - 

Become the root (super) user:
```
sudo bash
```

Update all programs installed on the virtual box: 
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

Point your browser to http://10.10.10.10:8888. The password is "hi".
In the upper left corner you should see "jupyter" alongside its logo. 

To shutdown or start the virtual box see the next section. 

## Stopping and starting the virtual box

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

Then you can start from the first instruction in the 
[Install and configure](#install-and-configure) section above.

INCOMPLETE --- I'LL FIX THIS
- remove old datalab-notebook folder
- stop all running virtual boxes 

- git pull the folder containing the datalab-notebook directory
- or
- download text from FB
- place in datalab-notebook/Notebooks directory

## IGNORE THE REST PLEASE

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

## Docker debugging

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
