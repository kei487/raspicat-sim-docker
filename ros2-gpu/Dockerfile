FROM osrf/ros:humble-desktop-full

SHELL ["/bin/bash", "-c"]

ARG USERNAME="root"

ENV NVIDIA_VISIBLE_DEVICES \
    ${NVIDIA_VISIBLE_DEVICES:-all}
ENV NVIDIA_DRIVER_CAPABILITIES \
    ${NVIDIA_DRIVER_CAPABILITIES:+$NVIDIA_DRIVER_CAPABILITIES,}graphics
ENV USER=$USERNAME
ENV USERNAME=$USERNAME
ENV GIT_PS1="${debian_chroot:+($debian_chroot)}\[\033[01;32m\]\u@\h\[\033[00m\]:\[\033[01;34m\]\w$(__git_ps1)\[\033[00m\](\t)\$ "
ENV NO_GIT_PS1="${debian_chroot:+($debian_chroot)}\u@\h:\w \$ "

RUN groupadd -g 1000 $USERNAME && \
    useradd -m -s /bin/bash -u 1000 -g 1000 -d /home/$USERNAME $USERNAME && \
    echo "$USERNAME ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers && \
    chown -R $USERNAME:$USERNAME /home/$USERNAME

RUN apt update && apt upgrade -y && \
    apt install -y \
    bash-completion \
    eog \
    git \
    terminator \
    vim \
    wget \
    xterm \
    turtlebot3 \
    turtlebot3_simulations \

USER $USERNAME
RUN mkdir -p $HOME/raspicat_ws/src && \
    cd $HOME/raspicat_ws/src && \
    git clone -b ros2 https://github.com/rt-net/raspicat_ros.git && \
    git clone -b ros2 https://github.com/rt-net/raspicat_description.git && \
    git clone -b feat/gnss https://github.com/makotoyoshigoe/raspicat_sim.git && \
    git clone -b ros2 https://github.com/rt-net/raspicat_slam_navigation.git && \
    git clone https://github.com/ryuichiueda/emcl2_ros2.git/ && \
    git clone -b dev https://github.com/kei487/value_iteration2.git && \
    git clone https://github.com/kei487/value_iteration2_exp.git && \
    ./raspicat_sim/raspicat_gazebo/scripts/download_gazebo_models.sh && \
    rosdep update && \
    rosdep install -r -y --from-paths --ignore-src ./

WORKDIR /home/$USERNAME/raspicat_ws/
RUN source /opt/ros/humble/setup.bash && \
    colcon build --symlink-install && \ 
    : "remove cache" && \
    sudo apt-get autoremove -y -qq && \
    sudo rm -rf /var/lib/apt/lists/*

RUN echo "source /home/$USERNAME/raspicat_ws/install/setup.bash" >> $HOME/.bashrc && \
    echo "source /etc/bash_completion" >> $HOME/.bashrc && \
    echo "if [ -f /etc/bash_completion.d/git-prompt ]; then" >> $HOME/.bashrc && \
    echo "    export PS1='${GIT_PS1}'" >> $HOME/.bashrc && \
    echo "else" >> $HOME/.bashrc && \
    echo "    export PS1='${NO_GIT_PS1}'" >> $HOME/.bashrc && \
    echo "fi" >> $HOME/.bashrc && \
    bash <(curl -s https://raw.githubusercontent.com/uhobeike/ros2_humble_install_script/main/ros2_setting.sh)

WORKDIR /home/$USERNAME/raspicat_ws/src
CMD ["/bin/bash"]
