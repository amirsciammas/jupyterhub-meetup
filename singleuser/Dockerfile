ARG DOCKER_NOTEBOOK_IMAGE
FROM $DOCKER_NOTEBOOK_IMAGE
ARG JUPYTERHUB_VERSION

COPY jupyter_notebook_config.py /etc/jupyter/jupyter_notebook_config.py

RUN python3 -m pip install --no-cache jupyterhub==$JUPYTERHUB_VERSION

RUN conda install --yes virtualenv && \
		conda config --add channels conda-forge --force && \
		conda install -y nb_conda_kernels && \
		conda install --yes -c conda-forge nbpresent && \
		conda install --yes -c conda-forge jupyter_contrib_nbextensions && \
		conda install -c conda-forge -y jupyter_nbextensions_configurator 

# Install ssh client, need to change to root for apt-get
USER root
RUN apt-get update && apt-get install -y openssh-client libspatialindex-dev
RUN apt-get update && apt-get install -y apt-utils graphviz

# Copy template configuration files
RUN mkdir /home/jovyan/conda-envs

# Fix permissions
RUN fix-permissions /home/jovyan/conda-envs

# Switch back to jovjan
USER jovyan

RUN pip install -U pyopenssl==17.5.0

# Install JupyterLab packages
RUN pip install jupyterlab && \
    jupyter serverextension enable --py jupyterlab --sys-prefix

# Install dummy auth
RUN pip install jupyterhub-dummyauthenticator nbgitpuller
RUN jupyter serverextension enable --py nbgitpuller --sys-prefix

# Install hub extention
RUN jupyter labextension install @jupyterlab/hub-extension@0.9.0

# Get workshop repo and install dep
RUN gitpuller https://github.com/jupyter-amsterdam-meetup/meetups master /home/jovyan/work/meetups
#RUN pip install -r /home/jovyan/work/meetups/2019-04-04/workshop-jupyter-meetup/requirements.txt
RUN pip install numpy==1.16.2 pandas==0.24.2 matplotlib==3.0.3 sklearn==0.0 seaborn==0.9.0 graphviz==0.10.1 pydotplus==2.0.2