# Build off of ros:noetic-robot (if this doesn't work, go back to melodic)
# FROM ros:noetic-robot
FROM osrf/ros:melodic-desktop-full

#### Install packages needed for building HDL-graph-slam:
ENV DEBIAN_FRONTEND noninteractive

RUN apt-get update \
 && apt-get install -y git \
 && sudo apt-get clean -qq


#### Done installing ros packages 
# Add a user with the same user_id as the user outside the container
# Requires a docker build argument `user_id`
ARG user_id
ENV USERNAME developer
RUN useradd -U --uid ${user_id} -ms /bin/bash $USERNAME \
 && echo "$USERNAME:$USERNAME" | chpasswd \
 && adduser $USERNAME sudo \
 && echo "$USERNAME ALL=NOPASSWD: ALL" >> /etc/sudoers.d/$USERNAME

# Commands below run as the developer user
USER $USERNAME

# Build newer version of cmake:
RUN cd /home/$USERNAME \
 && git clone https://github.com/Kitware/CMake/; cd CMake \
 && ./bootstrap && make && sudo make install

# When running a container start in the developer's home folder
WORKDIR /home/$USERNAME/slam_tools/ct_icp

# Do the superbuild part of the build:
RUN cd $WORKDIR \
 && mkdir .cmake-build-superbuild \
 && cd .cmake-build-superbuild \
 && cmake ../superbuild \
 && cmake --build . --config Release 

RUN echo "source /opt/ros/melodic/setup.bash" >> ~/.bashrc 
# \
#  && echo "source /home/developer/slam/hdl_graph_slam/catkin_ws/devel/setup.bash" >> ~/.bashrc
