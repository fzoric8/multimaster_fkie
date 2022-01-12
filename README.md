## This is a new version with daemon instance!

Whats new:

 * Remote access and control of launch and configuration files.
 * Easy remote editing of launch files.
 * Monitoring for ROS nodes and system resources on remote hosts.

> Old version is available on branch: `old_master` and is no longer supported!

# FKIE multimaster for ROS

The ROS stack of *fkie_multimaster* offers a complete solution for using ROS with multicores.
In addition, Node Manager with a daemon provide a GUI-based management environment that is very useful to manage ROS-launch configurations and control running nodes, also in a single-core system.

![multimaster overview](multimaster_overview.png)

## Packages have been renamed

The FKIE multimaster packages used to have an \_fkie suffix. In conformance with [REP-144](http://www.ros.org/reps/rep-0144.html), all packages have been renamed with an fkie\_ prefix, starting from version 1.0.0.
If you have built an older version from source, make sure to remove the build artifacts of the old versions from your build space.

## Install

The communication between the Node Manager GUI and the Daemon is based on Python [gRPC](https://grpc.io/). If you are using Ubuntu 18.10 or later, you can simply run `sudo apt install python-grpcio python-grpc-tools`. For Ubuntu 18.04 LTS, we provide a [PPA backport of the gRPC libraries](https://launchpad.net/~roehling/+archive/ubuntu/grpc). If your Ubuntu version is older than that, you need to install `grpcio-tools` from [PyPI](https://pypi.org/project/grpcio-tools/).

You can run the following commands to setup a build from source:

```
cd catkin_ws/src
git clone https://github.com/fkie/multimaster_fkie.git multimaster
rosdep update
rosdep install -i --as-root pip:false --reinstall --from-paths multimaster
```

Then build all packages:
```
catkin build fkie_multimaster
```

## How to run it? 

In order to run it, use following commands and following order of them. 

Let's say we have 2 PCs, one is PC A, and another one is PC B. 

First we have to configure our `ROS_MASTER_URI` and `ROS_HOSTNAME` **or** `ROS_IP`. 

**If you have added IP adresses of PC A and PC B to /etc/hosts, you can use symbolic adresses. Otherwise, use IP. **

Example of `/etc/hosts`: 

```
127.0.0.1 localhost 
192.168.1.a pc_a
192.168.1.b pc_b
```

Make sure PC A and PC B are on same subnet (e.g 192.168.x.a and 192.168.x.b). 

If you have added names of IP adresses to your `/etc/hosts` add following to `~/.bashrc` and source it. 

On PC A: 

```
export ROS_MASTER_URI=http://pc_a:11311
export ROS_HOSTNAME=pc_a
````

On PC B: 

```
export ROS_MASTER_URI=http://pc_b:11311
export ROS_HOSTNAME=pc_b
````
After that, run following sequence of commands on each PC: 

```
roscore 
rosrun fkie_master_discovery master_discovery _log_level:=DEBUG   # discovers remote master 
rosrun fkie_master_sync master_sync _log_level:=DEBUG             # sync nodes/topics/services
```

If you don't want to configure `/etc/hosts` file you can use `IP adresses directly as follows: 

On PC A: 

```
export ROS_MASTER_URI=http://192.168.x.a:11311
export ROS_IP=192.168.x.a
```

**Bear in mind that ROS_IP and ROS_HOSTNAME exclude each other, so use ROS_HOSTNAME or ROS_IP depending on /etc/hosts configuration!**

You can sync only some topics by running following command on `PC_B` machine: 

```
rosrun fkie_master_sync master_sync _log_level:=DEBUG _sync_topics:=["/topic_namespace/topic1", "/topic_namespace/topic2"]
```

There are also other params which could be configurable, and you can set them in corresponding launch files. 


## Documentation

* [multimaster\_fkie](http://fkie.github.io/multimaster_fkie)
* [discovery](http://fkie.github.io/multimaster_fkie/master_discovery.html) -- `discovery using multicast or zeroconf`
* [synchronization](http://fkie.github.io/multimaster_fkie/master_sync.html) -- `master synchronization`
* [Node Manager GUI](http://fkie.github.io/multimaster_fkie/node_manager.html) -- `A GUI to manage the configuration on local and remote ROS masters`
* [Node Manager daemon](http://fkie.github.io/multimaster_fkie/node_manager_daemon.html) -- `Helper node allows an easy (auto)start of remote nodes and manage remote launch files`

For ROS interfaces and parameterization see the [ROS Wiki](http://www.ros.org/wiki/multimaster_fkie). For configuration details you can find example launch files in each package.

