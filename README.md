[![Build Status](https://travis-ci.com/IllumiDesk/flatiron-stacks.svg?branch=main)](https://travis-ci.com/IllumiDesk/flatiron-stacks)

# Flatiron Stacks

Dockerfiles and related assets for IllumiDesk's workspace images for the `Flatiron School`.

## Pre Requisits

- [Docker](https://docs.docker.com/get-docker/)

## Quickstart

1. Build images

Build images:

```
make build-all
```

2. Run:

Running the image standalone is helpful for testing:

```bash
docker run -p 8888:8888 illumidesk/flatiron-notebook:latest
```

> Refer to [docker's documentation](https://docs.docker.com/engine/reference/run/) for additional `docker run ...` options.

## Image catalogue

| Image | DockerHub Link |
| --- | --- |
| illumidesk/flatiron-base | [![Docker Image](https://img.shields.io/docker/automated/flatiron/flatiron-base)](https://img.shields.io/docker/automated/illumidesk/flatiron-base?label=flatiron-base) |
| illumidesk/flatiron-notebook | [![Docker Image](https://img.shields.io/docker/automated/flatiron/flatiron-notebook)](https://hub.docker.com/repository/docker/illumidesk/flatiron-notebook?label=flatiron-notebook) |
| illumidesk/flatiron-grader | [![Docker Image](https://img.shields.io/docker/automated/flatiron/flatiron-grader)](https://hub.docker.com/repository/docker/illumidesk/flatiron-grader?label=flatiron-grader) |

## Build Mechanism

1. Build and tag the base image or all images at once. Use the `TAG` argument to add your custom tag. The `TAG` argument defaults to `latest` if not specified:

```bash
    make build/flatiron-base TAG=mytag
```

The base image uses the standard `repo2docker` convention to set dependencies. [Refer to this project's documentaiton](https://repo2docker.readthedocs.io/en/latest/) for additional configuration options.


2. (Optional) Use the base image from step 1 above as a base image for an image compatible with the illumidesk stack.

```
FROM jupyter/flatiron-base:latest AS base

FROM illumidesk/flatiron-base:latest

USER root

COPY --from=base /usr/local/bin/start* /usr/local/bin/
COPY --from=base /usr/local/bin/fix-permissions /usr/local/bin/
COPY --from=base /etc/jupyter/jupyter_notebook_config.py /etc/jupyter/

RUN ... do stuff

RUN fix-permissions "${HOME} \
 && fix-permissions "${CONDA_DIR}  # make sure you run fix-permissions after doing stuff

USER "${NB_USER}"

```

2. (Optional) Push images to DockerHub

This step requires creating an Organization account in DockerHub or other docker image compatible registry. The `docker push ...`
command will push the image to the DockerHub registry by default. Please refer to the official Docker documentation if you would
like to push another registry.

For example, assuming the DockerHub organization is `illumidesk` and the image name is built as `flatiron-notebook`, then the full
namespace for the image would be `illumidesk/flatiron-notebook:latest`. Then, you should push the image to the DockerHub registry like so:

```bash
docker push illumidesk/flatiron-notebook:mytag
```

## Development and Testing

1. Create your virtual environment and install dev-requirements:

    make venv

2. Check Dockerfiles with linter:

    make lint-all

Type `make help` for additional commands.

Tests start the docker container(s), runs commands by emulating the  `docker exec ...` command, and asserts the outputs. You can run tests on one image or all images. Use the `TAG` key to specify a docker image tag to test (TAG defaults to `latest`):

```bash
pytest -v
```

## References

These images are based on the `jupyter/docker-stacks` images. [Refer to their documentation](https://jupyter-docker-stacks.readthedocs.io/en/latest/) for the full set of configuration options.

## Attributions

- [JupyterHub repo2docker](https://repo2docker.readthedocs.io/en/latest/)
- [jupyter/docker-stacks images](https://github.com/jupyter/docker-stacks)
