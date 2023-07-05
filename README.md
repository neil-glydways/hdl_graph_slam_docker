# SLAM Testing Docker Tools
The intent of these tools is to allow for testing various SLAM algorithms 
within a docker context.  Most or all of the SLAM algorithms to be tested
can be run within ROS (ROS 1 or ROS 2), but the exact version(s) supported
may vary.  So, docker will be used to allow different versions of ROS to be
run.  

## Datasets
Store datasets somewhere on your local computer.  Map them into the docker
container by editing the `-v <local_folder>:<docker_folder>` path within 
`./run.bash`.

## HDL Graph SLAM
The first algorithm tested here is HDL Graph SLAM: 
https://github.com/koide3/hdl_graph_slam

### Build
Build docker image
```
./build.bash hdl-graph-slam-noetic/
```

Now, you need to run the docker image and build the code.  I have the code
checked out on my machine and mapped into the docker image so that when I
build on the docker image, it actually stores the files on my computer.  
However, I had some read-only file issues that I had to put in a work-around 
for.  

```
./run.bash hdl-graph-slam-noetic/
```
At docker prompt:
```
catkin_make
```

I think I had to throttle this to 2 cores (`-j2`) to prevent my computer 
from running out of memory.  


### Run an example
To run HDL graph SLAM, follow the instructions listed on its README, but
launch each terminal in a different terminal within docker.  Only one docker
container is needed.  Run `./run.bash` for the first instance and 
`.join.bash hdl-graph-slam-noetic` for each additional instance.  Here's 
an example:

Terminal 1 (instantiates the docker container):
```bash
./run.bash hdl-graph-slam-noetic/
```
Within docker shell:
```bash
roscore &
rosparam set use_sim_time true
roslaunch hdl_graph_slam hdl_graph_slam_501.launch
```

Terminal 2 (join first instance):
```bash
./join.bash hdl-graph-slam-noetic
```
Within docker shell: (Launches rviz)
```
source /ros_entrypoint.sh
source devel/setup.bash
roscd hdl_graph_slam/rviz
rviz -d hdl_graph_slam.rviz
```

Terminal 3: (join first instance again):
```bash
./join.bash hdl-graph-slam-noetic
```
Within docker shell:
```
source /ros_entrypoint.sh
source devel/setup.bash
rosbag play --clock <path_to_file>/hdl_501_filtered.bag -r0.25
```
Note: I run the bag slower than full speed (0.25 above) because my computer 
needs a little extra time to think.  This may or may not be necessary depending 
on your computer's capabilities.  They do provide a python player that
keeps the bag from playing back too fast, but I haven't tried this yet.  

Other Note: I had to do some weird recursive runs of chown and chmod to be
able to get things working.  This is probably due to a docker parameter 
that I didn't get right.  Hopefully I'll figure this out someday soon.  
Meanwhile, the files often show up as read-only.  
