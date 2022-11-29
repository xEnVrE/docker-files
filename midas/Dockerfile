FROM nvidia/cudagl:11.3.0-devel-ubuntu20.04

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
    apt install --no-install-recommends -y -qq apt-utils build-essential ca-certificates cmake cmake-curses-gui curl emacs-nox git glmark2 gnupg2 htop iputils-ping jq libopenblas-dev lsb-release mesa-utils nano psmisc python3-dev python3-virtualenv sudo unzip vim wget zip libxrender1 && \
    rm -rf /var/lib/apt/lists/*

# Install GitHub cli
RUN curl -fsSL https://cli.github.com/packages/githubcli-archive-keyring.gpg | dd of=/usr/share/keyrings/githubcli-archive-keyring.gpg && \
    chmod go+r /usr/share/keyrings/githubcli-archive-keyring.gpg && \
    echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/githubcli-archive-keyring.gpg] https://cli.github.com/packages stable main" | tee /etc/apt/sources.list.d/github-cli.list > /dev/null && \
    apt update && \
    apt install --no-install-recommends -y -qq gh && \
    rm -rf /var/lib/apt/lists/*

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

# virtualenv
WORKDIR /home/user
RUN virtualenv env && \
    . env/bin/activate && \
    pip install --upgrade pip && \
    pip install dill ffmpeg gdown gitpython GPUtil hydra-core matplotlib ninja numpy omegaconf opencv-python open3d potpourri3d pyvista scipy scikit-learn tensorboard theseus && \
# torch
    pip install torch==1.12.0+cu113 torchvision==0.13.0+cu113 torchaudio==0.12.0 --extra-index-url https://download.pytorch.org/whl/cu113

# MinkowskiEngine
# WORKDIR /home/user
# RUN . env/bin/activate && \
#     git clone https://github.com/NVIDIA/MinkowskiEngine && cd MinkowskiEngine && \
#     export CXX=c++ && export CUDA_HOME=/usr/local/cuda-11.3 && python setup.py install --blas=openblas --force_cuda && \
#     cd /home/user && git clone https://github.com/facebookresearch/MidasTouch && cd MidasTouch && \
#     git submodule update --init --recursive && \
#     # cd YCB-Slide && chmod +x download_dataset.sh && ./download_dataset.sh && \
#     # cd /home/user/MidasTouch && \
#     # chmod +x download_assets.sh && ./download_assets.sh && \
#     pip install -e .

# TACTO fork
# WORKDIR /home/user
# RUN . env/bin/activate && \
#     git clone https://github.com/suddhu/tacto.git && cd tacto && \
#     pip install -e .

# Launch bash from /home/user
WORKDIR /home/user
CMD ["bash"]
