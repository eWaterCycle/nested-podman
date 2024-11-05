FROM quay.io/podman/stable

# Change user to podman (any other non-root user is possible)
USER podman
WORKDIR /home/podman

# Install conda
RUN mkdir -p ~/miniconda3
RUN curl -o  ~/miniconda3/miniconda.sh https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
RUN bash ~/miniconda3/miniconda.sh -b -u -p ~/miniconda3
RUN rm ~/miniconda3/miniconda.sh
ENV PATH="/home/podman/miniconda3/bin:$PATH"
RUN conda init bash

# Install eWaterCycle
RUN curl -o conda-lock.yml https://raw.githubusercontent.com/eWaterCycle/ewatercycle/main/conda-lock.yml
RUN conda install mamba conda-lock -n base -c conda-forge -y
RUN conda-lock install --no-dev -n ewatercycle
RUN /home/podman/miniconda3/envs/ewatercycle/bin/python -m pip install ewatercycle

# Alias podman
RUN echo 'alias docker="podman"' >> ~/.bashrc
# do "conda activate ewatercycle when you start container"
