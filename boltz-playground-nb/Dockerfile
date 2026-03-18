FROM nvidia/cuda:13.1.1-cudnn-devel-ubuntu24.04

ENV LANGUAGE=en_US.UTF-8 LC_ALL=en_US.UTF-8 LANG=en_US.UTF-8 TZ=Asia/Seoul
ENV NB_USER=seoklab

RUN export DEBIAN_FRONTEND=noninteractive; apt-get update && \
    apt-get install -y ca-certificates curl gnupg && update-ca-certificates

RUN install -m 0755 -d /etc/apt/keyrings && \
    curl -fsSL https://cli.github.com/packages/githubcli-archive-keyring.gpg | tee /etc/apt/keyrings/githubcli-archive-keyring.gpg > /dev/null && \
    chmod go+r /etc/apt/keyrings/githubcli-archive-keyring.gpg && \
    echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/githubcli-archive-keyring.gpg] https://cli.github.com/packages stable main" | tee /etc/apt/sources.list.d/github-cli.list > /dev/null

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

RUN uv pip install --system --break-system-packages jupyterlab notebook

COPY shared/etc/ /etc/
RUN chmod 660 /etc/sudoers.d/*
RUN useradd -u 500 -ms /bin/zsh -p '' ${NB_USER}
RUN mkdir -p /home/${NB_USER}/.ssh && \
    ssh-keygen -t rsa -b 4096 -q -N '' -f /home/${NB_USER}/.ssh/id_rsa && \
    printf "Host galaxy3\n\
    HostName cluster.seoklab.org\n\
    ControlMaster auto\n\
    ControlPath ~/.ssh/control-%%r@%%h:%%p\n\
    ControlPersist 0\n\n\
Host galaxy4\n\
    ProxyJump galaxy3\n\
    ControlMaster auto\n\
    ControlPath ~/.ssh/control-%%r@%%h:%%p\n\
    ControlPersist 10m" > /home/${NB_USER}/.ssh/config && \
    chown -R 500:500 /home/${NB_USER}/.ssh && \
    chmod 700 /home/${NB_USER}/.ssh && \
    chmod 600 /home/${NB_USER}/.ssh/config /home/${NB_USER}/.ssh/id_rsa && \
    chmod 644 /home/${NB_USER}/.ssh/id_rsa.pub

RUN apt-get update && apt-get upgrade -y && apt-get autoremove --purge -y

USER ${NB_USER}
WORKDIR /home/${NB_USER}

ENTRYPOINT [ "/bin/zsh", "-il", "-c" ]
CMD ["jupyter lab --notebook-dir=/home/${NB_USER} --ip=0.0.0.0 --no-browser --allow-root --port=8888 --LabApp.token='' --LabApp.password='' --LabApp.allow_origin='*' --LabApp.base_url=${NB_PREFIX}"]
