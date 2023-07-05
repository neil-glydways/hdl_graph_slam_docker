# Build off of ros:noetic-robot (if this doesn't work, go back to melodic)
# FROM ros:noetic-robot
FROM osrf/ros:noetic-desktop-full

#### Install packages needed for building HDL-graph-slam:
ENV DEBIAN_FRONTEND noninteractive

RUN apt-get update \
 && apt-get install -y \
    locales \
    ros-noetic-interactive-markers \
    ros-noetic-geodesy \
    ros-noetic-pcl-ros \
    ros-noetic-nmea-msgs \
    ros-noetic-libg2o \
 && sudo apt-get clean -qq
 
 # For xrandr: sudo apt-get install x11-xserver-utils
 
# RUN rosdep update

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

# Make a couple folders for organizing docker volumes
RUN mkdir ~/slam

# When running a container start in the developer's home folder
WORKDIR /home/$USERNAME/slam/hdl_graph_slam/catkin_ws

RUN echo "source /opt/ros/noetic/setup.bash" >> ~/.bashrc \
  && echo "source /home/developer/slam/hdl_graph_slam/catkin_ws/devel/setup.bash" >> ~/.bashrc
