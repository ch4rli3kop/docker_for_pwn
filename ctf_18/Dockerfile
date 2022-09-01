FROM --platform=linux/amd64 ubuntu:18.04
LABEL maintainer="ch4rli3kop <pch2180@gmail.com>" version="1.0"

# pass user info
ARG USERNAME
ARG PASSWD
ARG CONT_PATH

# set language
ENV LANG C.UTF-8
ENV DEBIAN_FRONTEND=noninteractive 

# change apt source server
RUN sed -i 's/archive.ubuntu.com/mirror.kakao.com/g' /etc/apt/sources.list

# install basic utilties and libraries
RUN dpkg --add-architecture i386 && \
    apt update && \
    apt upgrade -y && \
    apt install -y gcc gcc-multilib g++ g++-multilib lib32stdc++6 build-essential clang make cmake gdb sudo coreutils \
                   python3 python3-dev python3-pip \
                   libssl-dev libffi-dev git net-tools curl wget ninja-build \
                   software-properties-common vim unzip file tmux patchelf \
                   ltrace strace ruby ruby-dev zsh gnupg

# install cmake latest version
RUN wget -O - https://apt.kitware.com/keys/kitware-archive-latest.asc 2>/dev/null | gpg --dearmor - | tee /etc/apt/trusted.gpg.d/kitware.gpg >/dev/null && \
    apt-add-repository 'deb https://apt.kitware.com/ubuntu/ bionic main' && \
    apt update && \
    apt install -y cmake

# install gcc-11 g++-11 version
RUN add-apt-repository ppa:ubuntu-toolchain-r/test && \
    apt install gcc-11 g++-11 -y && \
    update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-11 110 --slave /usr/bin/g++ g++ /usr/bin/g++-11

# add user account using sudo without password
RUN adduser --disabled-password --gecos '' $USERNAME && echo "${USERNAME}:${PASSWD}" | chpasswd && adduser ${USERNAME} sudo && echo "${USERNAME} ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers
USER ${USERNAME}
WORKDIR /home/${USERNAME}

# install zsh with xiong-chiamiov theme
RUN sudo chsh -s $(which zsh) && \
    sh -c "$(wget -O- https://github.com/deluan/zsh-in-docker/releases/download/v1.1.2/zsh-in-docker.sh)" -- -t xiong-chiamiov

# install pwntools
RUN python3 -m pip install --upgrade pip
RUN python3 -m pip install --upgrade pwntools
RUN pip3 install ropper z3-solver capstone angr

# install gef pwndbg peda with https://github.com/apogiatzis/gdb-peda-pwndbg-gef.git
RUN cd ~ && git clone https://github.com/apogiatzis/gdb-peda-pwndbg-gef.git && \
    cd ~/gdb-peda-pwndbg-gef && ./install.sh

# install one_gadget seccomp-tools
RUN sudo gem install one_gadget seccomp-tools

# install rp++
RUN cd ~ && git clone https://github.com/0vercl0k/rp.git && \
   cd ~/rp/src/build && chmod u+x ./build-release.sh && ./build-release.sh && \
   sudo cp ~/rp/src/build/rp-lin-x86_64 /usr/local/bin/rp-lin-x86_x64

RUN cd ~ && mkdir ${CONT_PATH}
RUN echo "set-option -g default-shell /bin/zsh" >> ~/.tmux.conf

ENTRYPOINT [ "tmux" ]
