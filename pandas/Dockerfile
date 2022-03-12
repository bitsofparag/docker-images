# -----------------------
# Dev build
FROM condaforge/mambaforge:4.11.0-4 as build

LABEL author="Parag M."
LABEL description="Dev image for working with pandas"

ARG PLATFORM=linux-64

ENV LANG=en_US.UTF-8
ENV CRYPTOGRAPHY_DONT_BUILD_RUST=1
ENV PYTHONDONTWRITEBYTECODE=1

RUN mamba install --yes conda-lock

COPY environment.yml environment.yml
COPY conda-lock.yml conda-lock.yml

RUN conda-lock render -k explicit conda-lock.yml \
    && mamba create \
       -p /opt/conda/envs/dev \
       --file conda-${PLATFORM}.lock

RUN mamba uninstall --yes conda-lock \
    && mv conda-${PLATFORM}.lock /opt/conda/envs/dev/conda-meta/conda-meta.lock \
    && /opt/conda/bin/conda clean -afy \
    && find /opt/conda/ -follow -type f -name '*.a' -delete \
    && find /opt/conda/ -follow -type f -name '*.pyc' -delete \
    && find /opt/conda/ -follow -type f -name '*.pyo' -delete \
    && find /opt/conda/ -name '*~' -exec rm -f {} + \
	  && find /opt/conda/ -name '__pycache__' -exec rm -fr {} + \
    && find /opt/conda/ -type d -name 'tests' -exec rm -fr {} + \
    && find /opt/conda/ -follow -type f -name '*.js.map' -delete \
    && cd /opt/conda/envs/dev \
    && rm -rf bin/x86_64-conda-linux-gnu-ld \
       bin/sqlite3 \
       bin/openssl \
       share/terminfo \
       lib/libpython3.9.so.1.0

RUN echo ". /opt/conda/etc/profile.d/conda.sh && conda activate dev" > ~/.bashrc


# -----------------------
# Publish build
FROM mambaorg/micromamba:0.22.0 as dist

LABEL author="Parag M."
LABEL description="Minimal image for running pandas-based packages"

ARG MAMBA_USER=mambauser

COPY --from=build --chown=$MAMBA_USER:$MAMBA_USER /opt/conda/envs/dev /opt/conda

WORKDIR /usr/local/src
ENTRYPOINT ["/usr/local/bin/_entrypoint.sh"]
SHELL ["micromamba", "run", "-n", "base", "/bin/bash", "-c"]


# -----------------------
# Jupyter notebook build
FROM jupyter/base-notebook:python-3.9.10 as jupyter
LABEL author="Parag M."
LABEL description="Minimal Jupyter image for running pandas-based data-science notebooks"
COPY --from=build /usr/local/src/environment.yml .
RUN conda-env update -n base --file environment.yml
WORKDIR /usr/local/src
