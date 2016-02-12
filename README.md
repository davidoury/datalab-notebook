# Data Lab notebook 

This _Data Lab_ notebook is built from the Docker container
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

To install and configure the notebook follow the directions 
in the [Install and configure](#install-and-configure) section below.

## Documentation links

- [Spark 1.5.1](https://spark.apache.org/docs/1.5.1/)
- [Spark 1.5.1 Quick Start](https://spark.apache.org/docs/1.5.1/quick-start.html) (start here)

## Install and configure 

This section consists of instructions to build a Jupyter notebook 
environment in which you can run Python and Spark. 
This entails many downloads so using an ethernet connection will make 
them quicker and potentially less problematic.

These instructions have been written for Mac and Windows computers. 
If you are running Ubuntu 14.04 then you don't need to create the 
virtual box, but would have to adapt these commands to your system.

Windows machines should have the `Virtualization` BIOS option turned on.

Download and install (in this order):

1. [VirtualBox](https://www.virtualbox.org)
1. [VirtualBox extensions](http://download.virtualbox.org/virtualbox/5.0.14/Oracle_VM_VirtualBox_Extension_Pack-5.0.14-105127.vbox-extpack) (download link)
1. [Vagrant](https://www.vagrantup.com)
1. [GitHub Desktop](https://desktop.github.com) (Windows only)

Open a console/shell:

- Windows: run the _Git Shell_ (there should be an icon on your desktop). 
- Mac: run the terminal program

Clone this GitHub repository:
```
$ git clone https://github.com/davidoury/datalab-notebook
```

Change to the `datalab-notebook` sub-directory (just created) and create the virtual box called `datalab`:
```
$ cd datalab-notebook
$ vagrant up datalab
```

Run a shell/console on the newly created `datalab` virtual box:
```
$ vagrant ssh datalab
```

The prompt is different that the previous one as you are now 
running a shell/console _inside_ the `datalab` virtual box.

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

Point your browser to http://10.10.10.10:8888. 
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

To destroy the `datalab` virtual box and start over again, run the following from a console
with current directory set to `datalab-notebook`:
```
$ vagrant destroy -f datalab
```


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
