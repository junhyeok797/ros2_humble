FROM ubuntu:22.04
LABEL maintainer="Junhyeok Seo <jun2342@chungbuk.ac.kr>"

# Set noninteractive installation
ENV DEBIAN_FRONTEND=noninteractive
ENV ROS_DISTRO=humble

# Install basic dependencies
RUN apt-get update && apt-get install -y \
    locales \
    curl \
    gnupg2 \
    lsb-release \
    build-essential \
    nano \
    sudo \
    wget \
    git \
    gosu \
    software-properties-common \
    && apt-get -y autoremove \
    && apt-get clean autoclean \
    && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

# Locale settings
RUN locale-gen en_US en_US.UTF-8 \
    && update-locale LC_ALL=en_US.UTF-8 LANG=en_US.UTF-8
ENV LANG=en_US.UTF-8

# Setup ROS2 sources and keys
RUN curl -sSL https://raw.githubusercontent.com/ros/rosdistro/master/ros.key \
    | gpg --dearmor -o /usr/share/keyrings/ros-archive-keyring.gpg

RUN echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/ros-archive-keyring.gpg] \
    http://packages.ros.org/ros2/ubuntu $(lsb_release -cs) main" \
    > /etc/apt/sources.list.d/ros2.list

# Install ROS2 Humble
RUN apt-get update && apt-get install -y \
    ros-${ROS_DISTRO}-desktop \
    python3-rosdep \
    python3-colcon-common-extensions \
    python3-argcomplete \
    && apt-get -y autoremove \
    && apt-get clean autoclean \
    && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

# Initialize rosdep
RUN rosdep init && rosdep update

# Create user (uid 1001)
RUN useradd --shell /bin/bash -u 1001 -c "" -m user \
    && usermod -a -G dialout user

# Add user to sudoers
RUN echo user ALL=\(root\) NOPASSWD:ALL > /etc/sudoers.d/user \
    && chmod 0440 /etc/sudoers.d/user

# Make ROS2 available by default
SHELL ["/bin/bash", "-c"]
RUN echo "source /opt/ros/${ROS_DISTRO}/setup.bash" >> /root/.bashrc \
    && echo "source /opt/ros/${ROS_DISTRO}/setup.bash" >> /home/user/.bashrc

# Entrypoint
COPY scripts/entrypoint.sh /usr/local/bin/entrypoint.sh
ENTRYPOINT ["/usr/local/bin/entrypoint.sh"]

# Set working directory
WORKDIR /home/user

CMD ["/bin/bash"]
