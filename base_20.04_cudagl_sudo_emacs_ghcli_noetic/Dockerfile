FROM nvidia/cudagl:11.4.2-devel-ubuntu20.04

# Use /bin/bash instead of /bin/sh
SHELL ["/bin/bash", "-c"]

# Non-interactive installation mode
ENV DEBIAN_FRONTEND=noninteractive

# Set the locale
RUN apt update && \
    apt install -y -qq locales && \
    locale-gen en_US en_US.UTF-8 && \
    update-locale LC_ALL=en_US.UTF-8 LANG=en_US.UTF-8
ENV LANG=en_US.UTF-8

# Install essentials
RUN apt update && \
    apt install --no-install-recommends -y -qq apt-utils build-essential ca-certificates cmake cmake-curses-gui curl emacs-nox git glmark2 gnupg2 htop iputils-ping jq lsb-release mesa-utils nano psmisc python3-virtualenv sudo unzip vim wget zip && \
    rm -rf /var/lib/apt/lists/*

# Install GitHub cli
RUN curl -fsSL https://cli.github.com/packages/githubcli-archive-keyring.gpg | dd of=/usr/share/keyrings/githubcli-archive-keyring.gpg && \
    chmod go+r /usr/share/keyrings/githubcli-archive-keyring.gpg && \
    echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/githubcli-archive-keyring.gpg] https://cli.github.com/packages stable main" | tee /etc/apt/sources.list.d/github-cli.list > /dev/null && \
    apt update && \
    apt install --no-install-recommends -y -qq gh && \
    rm -rf /var/lib/apt/lists/*

# Install Gazebo
RUN curl -sSL http://get.gazebosim.org | sh

# Install ROS
RUN sh -c 'echo "deb http://packages.ros.org/ros/ubuntu $(lsb_release -sc) main" > /etc/apt/sources.list.d/ros-latest.list' && \
    curl -s https://raw.githubusercontent.com/ros/rosdistro/master/ros.asc | apt-key add - && \
    apt update && \
    apt install --no-install-recommends -y -qq python3-rosdep python3-rosinstall python3-vcstools python3-catkin-tools ros-noetic-jsk-tools ros-noetic-desktop-full && \
    rosdep init && \
    rm -rf /var/lib/apt/lists/*
ENV QT_X11_NO_MITSHM 1

# Create user with passwordless sudo
RUN useradd -l -G sudo -md /home/user -s /bin/bash -p user user && \
    sed -i.bkp -e 's/%sudo\s\+ALL=(ALL\(:ALL\)\?)\s\+ALL/%sudo ALL=NOPASSWD:ALL/g' /etc/sudoers

# Switch to user
USER user

# Configure emacs
RUN echo "(setq-default indent-tabs-mode nil)" >> /home/user/.emacs.el && \
    echo "(setq-default tab-width 4)" >> /home/user/.emacs.el && \
    echo "(setq make-backup-files nil)" >> /home/user/.emacs.el && \
    echo "(setq auto-save-default nil)" >> /home/user/.emacs.el && \
    echo "(setq c-default-style \"linux\"" >> /home/user/.emacs.el && \
    echo "      c-basic-offset 4)" >> /home/user/.emacs.el && \
    echo "(global-subword-mode 1)" >> /home/user/.emacs.el && \
    echo "(add-hook 'before-save-hook 'delete-trailing-whitespace)" >> /home/user/.emacs.el && \
    echo "(custom-set-variables '(custom-enabled-themes '(tango-dark)))" >> /home/user/.emacs.el && \
    echo "(custom-set-faces)" >> /home/user/.emacs.elx

# Setup Gazebo11
RUN echo "source /usr/share/gazebo/setup.sh" >> ~/.bashrc

# Make sure the setup.bash is sourced
RUN echo "source /home/user/ws/devel/setup.bash" >> ~/.bashrc

# Launch bash from /home/user
WORKDIR /home/user
CMD ["bash"]
