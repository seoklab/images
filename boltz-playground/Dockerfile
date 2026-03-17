FROM nvidia/cuda:13.0.0-cudnn-devel-ubuntu24.04

ENV DEBIAN_FRONTEND=noninteractive
ENV TZ=Asia/Seoul
ENV LANG=C.UTF-8
ENV LC_ALL=C.UTF-8

RUN apt-get update && apt-get install -y \
    software-properties-common \
    curl \
    wget \
    git \
    vim \
    tmux \
    ca-certificates \
    build-essential \
    pkg-config \
    ninja-build \
    cmake \
    gfortran \
    libssl-dev \
    zlib1g-dev \
    libbz2-dev \
    libreadline-dev \
    libsqlite3-dev \
    libffi-dev \
    liblzma-dev \
    libncursesw5-dev \
    xz-utils \
    tk-dev \
    libxml2-dev \
    libxmlsec1-dev \
    libglib2.0-0 \
    libsm6 \
    libxext6 \
    libxrender1 \
    libaio-dev \
    sudo \
    && rm -rf /var/lib/apt/lists/*

# Python 3.12
RUN add-apt-repository ppa:deadsnakes/ppa -y && \
    apt-get update && apt-get install -y \
    python3.12 \
    python3.12-dev \
    python3.12-venv \
    && rm -rf /var/lib/apt/lists/*

# python / pip

# code-server 설치..?
#RUN curl -fsSL https://code-server.dev/install.sh | sh

# python
RUN ln -sf /usr/bin/python3.12 /usr/bin/python

# venv + pip + uv
RUN python -m venv /opt/venv && \
    /opt/venv/bin/pip install --no-cache-dir --upgrade pip setuptools wheel && \
    /opt/venv/bin/pip install --no-cache-dir uv

ENV PATH="/opt/venv/bin:${PATH}"

# kubectl
RUN curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl" && \
    install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl && \
    rm -f kubectl

# non-root user
ENV NB_USER=bwkim1324

RUN useradd -m -s /bin/bash -U ${NB_USER} && \
    echo "${NB_USER} ALL=(ALL) NOPASSWD:ALL" > /etc/sudoers.d/${NB_USER} && \
    chmod 0440 /etc/sudoers.d/${NB_USER}

USER ${NB_USER}
WORKDIR /home/${NB_USER}/boltz

CMD ["/bin/bash", "-lc", "sleep infinity"]
