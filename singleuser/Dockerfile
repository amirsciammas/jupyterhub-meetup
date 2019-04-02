ARG DOCKER_NOTEBOOK_IMAGE
FROM $DOCKER_NOTEBOOK_IMAGE
ARG JUPYTERHUB_VERSION
ARG PIP_TRUSTED_HOST
ARG PIP_EXTRA_INDEX_URL

COPY jupyter_notebook_config.py /etc/jupyter/jupyter_notebook_config.py

RUN python3 -m pip install --no-cache jupyterhub==$JUPYTERHUB_VERSION

RUN conda install --yes virtualenv && \
		conda install -c defaults --yes azure==1.0.2 && \
		pip install azure-storage && \
		conda config --add channels conda-forge --force && \
		conda install -y nb_conda_kernels && \
		conda install --yes -c conda-forge nbpresent && \
		conda install --yes -c conda-forge jupyter_contrib_nbextensions && \
		conda install -c conda-forge -y jupyter_nbextensions_configurator 

# Install ssh client, need to change to root for apt-get
USER root
RUN apt-get update && apt-get install -y openssh-client libspatialindex-dev

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

RUN pip install jupyterhub-dummyauthenticator nbgitpuller
RUN jupyter serverextension enable --py nbgitpuller --sys-prefix
RUN jupyter labextension install @jupyterlab/hub-extension@0.9.0