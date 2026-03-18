FROM nvidia/cuda:13.1.1-cudnn-devel-ubuntu24.04

ENV LANGUAGE=en_US.UTF-8 LC_ALL=en_US.UTF-8 LANG=en_US.UTF-8 TZ=Asia/Seoul

RUN export DEBIAN_FRONTEND=noninteractive; apt-get update && \
    apt-get install -y ca-certificates && update-ca-certificates
RUN export DEBIAN_FRONTEND=noninteractive; /bin/yes | unminimize && \
    apt-get update && apt-get upgrade -y && apt-get autoremove --purge -y && \
    apt-get install -y ubuntu-minimal ubuntu-standard ubuntu-server-minimal ubuntu-server \
    build-essential gcc-multilib gfortran gdb valgrind python3 python3-pip \
    linux-tools-common linux-tools-generic tcsh csh php-cli \
    lm-sensors libboost-all-dev libcgicc3 libgdbm-dev libgoogle-perftools4 \
    libjemalloc2 libjsoncpp25 libomp5 libqt5core5a libswitch-perl libsz2 \
    libglu1-mesa libgl1 net-tools r-base-core zsh gh nvtop

RUN sed -i -e 's/# en_US.UTF-8 UTF-8/en_US.UTF-8 UTF-8/' /etc/locale.gen && \
    dpkg-reconfigure -f noninteractive locales && \
    update-locale LANG=en_US.UTF-8

RUN ln -sf /usr/share/zoneinfo/Asia/Seoul /etc/localtime && \
    dpkg-reconfigure -f noninteractive tzdata && \
    chmod -x /etc/update-motd.d/*reboot*

COPY --from=ghcr.io/astral-sh/uv:latest /uv /uvx /usr/local/bin/

# kubectl
RUN curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl" && \
    install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl && \
    rm -f kubectl

COPY shared/etc/ /etc/
RUN chmod 660 /etc/sudoers.d/*
RUN useradd -u 500 -ms /bin/zsh -p '' seoklab
RUN mkdir -p /home/seoklab/.ssh && \
    printf "Host galaxy3\n\
    HostName cluster.seoklab.org\n\
    Port 1022\n\
    ControlMaster auto\n\
    ControlPath ~/.ssh/control-%%r@%%h:%%p\n\
    ControlPersist 0\n\n\
Host galaxy4\n\
    ProxyJump galaxy3\n\
    ControlMaster auto\n\
    ControlPath ~/.ssh/control-%%r@%%h:%%p\n\
    ControlPersist 10m\n" > /home/seoklab/.ssh/config && \
    chown -R 500:500 /home/seoklab/.ssh && \
    chmod 700 /home/seoklab/.ssh && \
    chmod 600 /home/seoklab/.ssh/config

RUN apt-get update && apt-get upgrade -y && apt-get autoremove --purge -y

USER seoklab
WORKDIR /home/seoklab
ENTRYPOINT [ "/bin/zsh", "-il" ]
