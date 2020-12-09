FROM jupyter/scipy-notebook:45bfe5a474fa AS base

USER root

# copy configs
RUN cp /etc/jupyter/jupyter_notebook_config.py /etc/jupyter/jupyter_notebook_config_base.py
COPY jupyter_notebook_config.py /etc/jupyter/
COPY global_nbgrader_config.py /etc/jupyter/nbgrader_config.py

USER "${NB_USER}"

# install packages with mamba to improve build times
WORKDIR /tmp
COPY environment.yml /tmp/environment.yml
COPY requirements.txt /tmp/requirements.txt

# install mamba, then install the rest of the packages with mamba
RUN conda install mamba -c conda-forge \
 && fix-permissions "${CONDA_DIR}" \
 && fix-permissions "${HOME}"

RUN mamba env update --prefix ./env --file environment.yml \
 && fix-permissions "${CONDA_DIR}" \
 && fix-permissions "${HOME}"

RUN pip install -r /tmp/requirements.txt \
 && fix-permissions "${CONDA_DIR}" \
 && fix-permissions "${HOME}"

# update jupyterlab
RUN jupyter serverextension enable --sys-prefix --py jupyter_server_proxy \
 && jupyter labextension install @jupyterlab/server-proxy@^2.1.1 \
 && jupyter lab build -y \
 && jupyter lab clean -y \
 && npm cache clean --force \
 && rm -rf "${HOME}/.cache/yarn" \
 && rm -rf "${HOME}/.node-gyp" \
 && fix-permissions "${CONDA_DIR}" \
 && fix-permissions "${HOME}"

# install nbgrader and then disable all extensions by default
RUN jupyter nbextension install --symlink --sys-prefix --py nbgrader --overwrite \
 && jupyter nbextension disable --sys-prefix --py nbgrader \
 && jupyter serverextension disable --sys-prefix --py nbgrader

# everyone gets the nbgrader validate extension
RUN jupyter nbextension enable --sys-prefix validate_assignment/main --section=notebook \
 && jupyter serverextension enable --sys-prefix nbgrader.server_extensions.validate_assignment

# everyone gets the assignment extension
RUN jupyter serverextension enable --sys-prefix nbgrader.server_extensions.assignment_list \
 && jupyter nbextension enable --sys-prefix assignment_list/main --section=tree

# widgets
RUN jupyter nbextension enable --py widgetsnbextension --sys-prefix \
 && fix-permissions "${CONDA_DIR}" \
 && fix-permissions "${HOME}"

# update permissions as root
USER root
RUN fix-permissions /etc/jupyter/ \
 && fix-permissions "${CONDA_DIR}" \
 && fix-permissions "${HOME}"

USER "${NB_USER}"

WORKDIR "${HOME}"
