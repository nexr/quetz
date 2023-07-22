FROM ubuntu:20.04

RUN apt-get update && apt-get install -y \
    bzip2 \
    ca-certificates \
    curl \
    postgresql-client \
    git \
    && rm -rf /var/lib/{apt,dpkg,cache,log}
RUN curl -L https://micro.mamba.pm/api/micromamba/linux-64/latest | \
    tar -xj -C /tmp bin/micromamba
RUN cp /tmp/bin/micromamba /bin/micromamba

ENV MAMBA_ROOT_PREFIX=/opt/conda


RUN mkdir -p $(dirname $MAMBA_ROOT_PREFIX) && \
    /bin/micromamba shell init -s bash -p $MAMBA_ROOT_PREFIX && \
    echo "micromamba activate base" >> ~/.bashrc

ENV PATH="/opt/conda/bin:${PATH}"

RUN mkdir /code
WORKDIR /code
COPY environment.yml /code/

RUN micromamba install -v -y -n base -c conda-forge -f environment.yml

COPY . /code/
RUN pip install -e .

# install plugin
RUN cd plugins && \
    pip install -e quetz_runexports -e quetz_conda_suggest \
    -e quetz_current_repodata -e quetz_mamba_solve \
    -e quetz_repodata_patching -e quetz_repodata_zchunk

RUN useradd quetz --no-log-init -u 1000 -p "$(openssl passwd -1 mamba)" 

# install quetz-frontend
RUN mamba install --yes --no-deps -c conda-forge quetz-frontend=0.4.0

# Add user for PAM
RUN useradd nexr --no-log-init -u 1001 -p "$(openssl passwd -1 nexr1234~)"
