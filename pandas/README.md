# Docker Images: Pandas
> For running pandas-based packages

[![pandas ci][pandas-ci-img]][pandas-ci-url]

This is the source for the `bitsofparag/pandas` Docker images.
There are two tags - `${VERSION} and ${VERSION}-jupyter`.

The basic versioned tag provides a small pandas image within a [Micromamba][mmamba-url] environment.
This can be used for running any python app that imports `pandas` as a dependency.

The `-jupyter` tag is based off of [Jupyter Stack images][jupyter-url]
and upon run, will launch a minimal Jupyter notebook server.

See below for usage.

## Installation

Assuming you have Docker installed,

```sh
docker pull bitsofparag/pandas:1.4.1
docker pull bitsofparag/pandas:1.4.1-jupyter
```

> To use the latest images, just replace '1.4.1' with 'latest'

## Usage examples

1. To run a data-science python script, you can launch the pandas container as:

```sh
docker run -it --rm \
  -v my_app:/usr/local/src/apps \
  bitsofparag/pandas:1.4.1 \
  python -m my_app
```

2. To run your data-science notebook, you can launch the pandas jupyter container as:

```sh
docker run -it --rm \
  -v my_notebooks:/usr/local/src/apps \
  -p 8888:8888 \
  bitsofparag/pandas:1.4.1-jupyter
```
Open http://localhost:8888 to access the notebook.


<!-- Markdown link & img badges -->
[pandas-ci-img]: https://github.com/bitsofparag/docker-images/actions/workflows/pandas.yml/badge.svg
[pandas-ci-url]: https://github.com/bitsofparag/docker-images/actions/workflows/pandas.yml
[mmamba-url]: https://mamba.readthedocs.io/en/latest/user_guide/micromamba.html
[jupyter-url]: https://jupyter-docker-stacks.readthedocs.io/en/latest/
