FROM ubuntu:bionic

LABEL maintainer="Lander Usategui <lander at erlerobotics dot com>"

# Setup environment
ENV LANG C.UTF-8
ENV LC_ALL C.UTF-8

ENV ROS2_DISTRO dashing

# Prepare workspace
RUN mkdir -p /root/ros2_mara_ws/src
WORKDIR /root/ros2_mara_ws

RUN \
     echo 'Etc/UTC' > /etc/timezone \
      && ln -s /usr/share/zoneinfo/Etc/UTC /etc/localtime \
      && apt-get update -qq && apt-get install -qq -y tzdata dirmngr gnupg2 lsb-release curl \
      # Setup ROS2 keys
      && curl http://repo.ros2.org/repos.key | apt-key add - \
      # Setup sources.list
      && echo "deb [arch=amd64,arm64] http://packages.ros.org/ros2/ubuntu `lsb_release -cs` main" > /etc/apt/sources.list.d/ros2-latest.list \
      && apt-get update -qq && apt-get install -qq -y \
        alsa-base \
        bash-completion \
        build-essential \
        cmake \
        git \
        python3-pip \
        vim \
        wget \
      && pip3 install \
        tensorflow==1.13.2 \
        transforms3d \
        billiard \
        protobuf==3.19.6 \
        psutil \
      && apt update -qq && apt install -qq -y \
        python3-colcon-common-extensions \
        python3-vcstool \
        python3-numpy \
        python3-sip-dev \
        python3-tk \
        libeigen3-dev \
        libboost-all-dev \
        ros-$ROS2_DISTRO-ros-base \
        ros-$ROS2_DISTRO-action-msgs \
        ros-$ROS2_DISTRO-message-filters \
        ros-$ROS2_DISTRO-yaml-cpp-vendor \
        ros-$ROS2_DISTRO-urdf \
        ros-$ROS2_DISTRO-rttest \
        ros-$ROS2_DISTRO-tf2 \
        ros-$ROS2_DISTRO-tf2-geometry-msgs \
        ros-$ROS2_DISTRO-launch-testing-ament-cmake \
        ros-$ROS2_DISTRO-rclcpp-action \
        ros-$ROS2_DISTRO-rmw-implementation \
        ros-$ROS2_DISTRO-rmw-fastrtps-cpp \
        ros-$ROS2_DISTRO-rmw-opensplice-cpp \
        ros-$ROS2_DISTRO-cv-bridge \
        ros-$ROS2_DISTRO-control-msgs \
        ros-$ROS2_DISTRO-image-transport \
        ros-$ROS2_DISTRO-gazebo-dev \
        ros-$ROS2_DISTRO-gazebo-msgs \
        ros-$ROS2_DISTRO-gazebo-plugins \
        ros-$ROS2_DISTRO-gazebo-ros \
        ros-$ROS2_DISTRO-gazebo-ros-pkgs \
        libopencv-dev \
        && rm -rf /var/lib/apt/lists/* \
      && wget https://raw.githubusercontent.com/AcutronicRobotics/MARA/dashing/mara-ros2.repos && vcs import src < mara-ros2.repos \
      && wget https://raw.githubusercontent.com/ValV/gym-gazebo2/dashing/provision/additional-repos.repos && vcs import src < additional-repos.repos \
      # Generete HRIM packages
      && cd ~/ros2_mara_ws/src/HRIM \
      && pip3 install hrim \
      && hrim generate models/actuator/servo/servo.xml \
      && hrim generate models/actuator/gripper/gripper.xml \
      # Compile the workspace
      && bash -c " cd /root/ros2_mara_ws \
      && source /opt/ros/dashing/setup.bash && colcon build --merge-install --parallel-workers $(nproc) --packages-ignore orocos_kinematics_dynamics individual_trajectories_bridge --allow-overriding orocos_kdl \
      && touch /root/ros2_mara_ws/install/share/orocos_kdl/local_setup.sh /root/ros2_mara_ws/install/share/orocos_kdl/local_setup.bash \
      && cd /root && git clone -b v0.21.0 https://github.com/openai/gym && cd /root/gym && pip3 install -e . \
      # Setup ros2learn
      && cd /root && git clone -b dashing https://github.com/ValV/ros2learn.git \
      && cd /root/ros2learn \
      && git submodule update --init --recursive \
      && git pull --recurse-submodules && git submodule update --recursive --remote \
      && cd /root/ros2learn/environments/gym-gazebo2 && pip3 install -e . \
      && cd /root/ros2learn/algorithms/baselines && pip3 install -e . \
      # Load provisioning script
      && echo 'source /root/ros2learn/environments/gym-gazebo2/provision/mara_setup.sh' >> ~/.bashrc \
      "
WORKDIR /root/ros2learn/
EXPOSE 11597
ENTRYPOINT ["bash"]
