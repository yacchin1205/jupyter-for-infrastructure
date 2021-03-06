FROM debian:jessie

MAINTAINER https://twitter.com/yacchin1205/

ENV DEBIAN_FRONTEND noninteractive
RUN REPO=http://cdn-fastly.deb.debian.org \
 && echo "deb $REPO/debian jessie main\ndeb $REPO/debian-security jessie/updates main" > /etc/apt/sources.list \
 && apt-get update && apt-get -yq dist-upgrade \
 && apt-get install -yq --no-install-recommends \
    wget \
    bzip2 \
    ca-certificates \
    sudo \
    locales \
 && apt-get clean \
 && rm -rf /var/lib/apt/lists/*

RUN echo "en_US.UTF-8 UTF-8" > /etc/locale.gen && \
    locale-gen


# Install Tini
RUN wget --quiet https://github.com/krallin/tini/releases/download/v0.10.0/tini && \
    echo "1361527f39190a7338a0b434bd8c88ff7233ce7b9a4876f3315c22fce7eca1b0 *tini" | sha256sum -c - && \
    mv tini /usr/local/bin/tini && \
    chmod +x /usr/local/bin/tini

ENV LC_ALL en_US.UTF-8
ENV LANG en_US.UTF-8
ENV LANGUAGE en_US.UTF-8

# Create 'bit_kun' user
ENV NB_USER bit_kun
ENV NB_UID 1000
RUN useradd -m -s /bin/bash -N -u $NB_UID $NB_USER && \
    mkdir /home/$NB_USER/.jupyter && \
    chown -R $NB_USER:users /home/$NB_USER/.jupyter && \
    echo "$NB_USER ALL=(ALL) NOPASSWD: ALL" > /etc/sudoers.d/$NB_USER

# jupyter
RUN bash -c 'apt-get update && apt-get install -yq --no-install-recommends \
             curl python2.7-dev python2.7 build-essential && \
             curl -L https://bootstrap.pypa.io/get-pip.py | python2.7 && \
             apt-get clean && rm -rf /var/lib/apt/lists/*'

RUN pip install jupyter

RUN apt-get update && apt-get install -yq --no-install-recommends \
    git \
    vim \
    jed \
    emacs \
    unzip \
    libsm6 \
    pandoc \
    texlive-latex-base \
    texlive-latex-extra \
    texlive-fonts-extra \
    texlive-fonts-recommended \
    texlive-generic-recommended \
    libxrender1 \
    inkscape \
    wget \
    curl \
    && apt-get clean && \
    rm -rf /var/lib/apt/lists/*

ADD conf /tmp/
USER $NB_USER
RUN mkdir -p $HOME/.jupyter && \
    cp -f /tmp/jupyter_notebook_config.py \
       $HOME/.jupyter/jupyter_notebook_config.py

USER root
RUN pip install pandas matplotlib numpy \
                seaborn scipy scikit-learn scikit-image \
                sympy cython patsy \
                statsmodels cloudpickle dill bokeh h5py

# ansible
RUN apt-get update && \
    apt-get -y install sshpass openssl ipmitool libssl-dev build-essential && \
    apt-get clean && rm -rf /var/lib/apt/lists/* && \
    pip install requests paramiko ansible && \
    cat /tmp/sitecustomize.py >> \
        /usr/lib/python2.7/sitecustomize.py
RUN mkdir -p /etc/ansible && cp /tmp/ansible.cfg /etc/ansible/ansible.cfg

# extensions for jupyter
## nbextensions_configurator
RUN pip install jupyter_nbextensions_configurator
RUN mkdir -p $HOME/.local/share && \
    pip install six \
    https://github.com/ipython-contrib/jupyter_contrib_nbextensions/tarball/master
USER $NB_USER
RUN jupyter contrib nbextension install --user

## Jupyter-multi_outputs (NII) - https://github.com/NII-cloud-operation/Jupyter-multi_outputs
RUN curl -L https://github.com/NII-cloud-operation/Jupyter-multi_outputs/archive/feature/merge_code_cell_status_extension.zip > /tmp/archive.zip && \
    cd /tmp && unzip /tmp/archive.zip && \
    mkdir -p $HOME/.ipython/nbextensions/multi_outputs/ && \
    cp -f /tmp/Jupyter-multi_outputs-feature-merge_code_cell_status_extension/* $HOME/.ipython/nbextensions/multi_outputs/
RUN mkdir -p $HOME/.jupyter/nbconfig && \
    cp /tmp/notebook.json $HOME/.jupyter/nbconfig/notebook.json

## nbextension_i18n_cells (NII) - https://github.com/NII-cloud-operation/Jupyter-i18n_cells
USER root
RUN pip install git+https://github.com/NII-cloud-operation/Jupyter-i18n_cells.git
USER $NB_USER
RUN jupyter nbextension install --py nbextension_i18n_cells --user && \
    jupyter nbextension enable --py nbextension_i18n_cells --user

# Theme for jupyter
RUN mkdir -p $HOME/.jupyter/custom/ && \
    cp /tmp/custom.css $HOME/.jupyter/custom/custom.css

RUN mkdir -p $HOME/notebooks
WORKDIR $HOME/notebooks
ENTRYPOINT ["tini", "--"]
CMD ["jupyter", "notebook"]
