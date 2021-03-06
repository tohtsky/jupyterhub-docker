FROM ubuntu:16.04

MAINTAINER CodeLibs Project

ENV DEBIAN_FRONTEND noninteractive

ADD res/clean-layer.sh /tmp/clean-layer.sh

RUN apt update && apt upgrade -y && apt install -y locales && rm -rf /var/lib/apt/lists/*

RUN locale-gen en_US.UTF-8
ENV LANG en_US.UTF-8
ENV LANGUAGE en_US:en
ENV LC_ALL en_US.UTF-8
ENV PIP_DEFAULT_TIMEOUT 180

RUN apt-get update && apt install -y build-essential curl git gcc make openssl libssl-dev libbz2-dev \
    libreadline-dev libsqlite3-dev cmake libxml2-dev wget bzip2 sudo vim unzip libcurl4-openssl-dev \
    && /tmp/clean-layer.sh

RUN wget --quiet https://github.com/krallin/tini/releases/download/v0.18.0/tini && \
    echo "12d20136605531b09a2c2dac02ccee85e1b874eb322ef6baf7561cd93f93c855 *tini" | sha256sum -c - && \
    mv tini /usr/local/bin/tini && \
    chmod +x /usr/local/bin/tini

ENV PATH=/opt/conda/bin:$PATH

RUN wget -q https://repo.continuum.io/miniconda/Miniconda3-4.5.11-Linux-x86_64.sh -O /tmp/miniconda.sh \
    && echo 'e1045ee415162f944b6aebfe560b8fee */tmp/miniconda.sh' | md5sum -c - \
    && bash /tmp/miniconda.sh -f -b -p /opt/conda \
    && /opt/conda/bin/conda install --yes -c conda-forge \
    python=3.6 sqlalchemy tornado jinja2 traitlets requests pip nodejs configurable-http-proxy ncurses \
    && /opt/conda/bin/pip install --upgrade pip \
    && /tmp/clean-layer.sh

RUN pip install notebook \
    && pip install jupyter \
    && pip install jupyterhub \
    && pip install jupyterlab \
    && pip install oauthenticator \
    && pip install jupyterhub-ldapauthenticator \
    && pip install jupyter_contrib_nbextensions \
    && pip install autopep8 \
    && pip install yapf \
    && pip install pycurl \
    && /tmp/clean-layer.sh

RUN jupyter contrib nbextension install --system \
    && jupyter labextension install @jupyterlab/hub-extension

RUN mkdir -p /opt/oauthenticator
WORKDIR /opt/oauthenticator
ENV OAUTHENTICATOR_DIR /opt/oauthenticator
ADD res/addusers.sh /opt/oauthenticator/addusers.sh
ADD res/userlist /opt/oauthenticator/userlist
RUN ["sh", "/opt/oauthenticator/addusers.sh"]
RUN chmod 700 /opt/oauthenticator

RUN mkdir -p /opt/jupyterhub/
WORKDIR /opt/jupyterhub/
ADD res/jupyterhub_config.py jupyterhub_config.py
# LDAP
#ADD res/server.key server.key
#ADD res/server.crt server.crt

EXPOSE 8000

LABEL org.jupyter.service="jupyterhub"

ENTRYPOINT ["tini", "--"]
CMD ["jupyterhub"]

