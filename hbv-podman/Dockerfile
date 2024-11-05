FROM bschilperoort/ewc-podman:latest

COPY forcing forcing
COPY HBV_config.json HBV_config.json

RUN /home/podman/miniconda3/envs/ewatercycle/bin/python -m pip install https://github.com/eWaterCycle/ewatercycle-hbv/archive/refs/heads/main.zip
RUN /home/podman/miniconda3/envs/ewatercycle/bin/python -m pip install HBV
