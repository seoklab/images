FROM nvidia/cuda:13.1.1-cudnn-devel-ubuntu24.04

ENV LANGUAGE=en_US.UTF-8 LC_ALL=en_US.UTF-8 LANG=en_US.UTF-8 TZ=Asia/Seoul

RUN export DEBIAN_FRONTEND=noninteractive; apt-get update && \
    apt-get install -y ca-certificates && update-ca-certificates
RUN rm /etc/apt/sources.list.d/* && rm -rf /var/lib/apt/lists/*
COPY shared/etc/apt/sources.list.d/ /etc/apt/sources.list.d/
RUN export DEBIAN_FRONTEND=noninteractive; /bin/yes | unminimize && \
    apt-get update && apt-get upgrade -y && apt-get autoremove --purge -y && \
    apt-get install -y ubuntu-minimal ubuntu-standard ubuntu-server-minimal ubuntu-server \
    build-essential gcc-multilib gfortran gdb valgrind python3 python3-pip \
    linux-tools-common linux-tools-generic tcsh csh php-cli \
    lm-sensors libboost-all-dev libcgicc3 libgdbm-dev libgoogle-perftools4 \
    libjemalloc2 libjsoncpp25 libomp5 libqt5core5a libswitch-perl libsz2 \
    libglu1-mesa libgl1 net-tools r-base-core zsh

RUN sed -i -e 's/# en_US.UTF-8 UTF-8/en_US.UTF-8 UTF-8/' /etc/locale.gen && \
    dpkg-reconfigure -f noninteractive locales && \
    update-locale LANG=en_US.UTF-8

RUN ln -sf /usr/share/zoneinfo/Asia/Seoul /etc/localtime && \
    dpkg-reconfigure -f noninteractive tzdata && \
    chmod -x /etc/update-motd.d/*reboot*

COPY --from=ghcr.io/astral-sh/uv:latest /uv /uvx /bin/

# kubectl
RUN curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl" && \
    install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl && \
    rm -f kubectl

COPY shared/etc/ /etc/
RUN chmod 660 /etc/sudoers.d/*
RUN useradd -ms /bin/bash -p '' seoklab
RUN apt-get update && apt-get upgrade -y && apt-get autoremove --purge -y

USER seoklab
WORKDIR /home/seoklab
ENTRYPOINT [ "/bin/zsh", "-il" ]
