# Nested Podman with eWaterCycle

If for some reason you want to run the [eWaterCycle platform](https://github.com/eWaterCycle/ewatercycle/)
from within a container, this is possible using podman.

There are a few steps you will have to go through.
While `podman` is a drop-in replacement for `docker`, the ewatercycle Python package does
not support it yet.
However, as a technical demonstration, the essential steps below show:

1. starting a container image with both podman and ewatercycle
   - plus forcing data for a model
2. starting a headless container of a grpc4bmi enabled model
   - including mounting the forcing data
3. connecting to that model with `grpc4bmi` and initializing the model

## How to run

First install [podman](https://podman.io/) on your local machine.

Then run the following image (in a rootless configuration):

```sh
podman run -it \
--security-opt label=disable \
--user podman \
--device /dev/fuse \
docker.io/bschilperoort/hbv-podman:latest /bin/bash
```

This container has model forcing data in the `/home/podman/forcing` direcory.

Inside this container you can start another container with podman.
We start this one headless (`-d`) and specify a bind mount for the forcing directory:

```sh
podman run \
-d \
--mount type=bind,source=/home/podman/forcing,target=/home/podman/forcing \
ghcr.io/ewatercycle/hbv-bmi-grpc4bmi:latest
```

After starting the container we can activate the ewatercycle conda environment and start Python:

```sh
conda activate ewatercycle
python
```

Here we can connect to the model and initialize it with the prepared config file:

```python
>>> import grpc
>>> from grpc4bmi.bmi_grpc_client import BmiClient
>>> import numpy as np
>>> 
>>> mymodel = BmiClient(grpc.insecure_channel("localhost:55555"))
>>> mymodel.initialize("/home/podman/forcing/HBV_config.json")
>>> mymodel.get_component_name()
'HBV'

>>> mymodel.update()
>>> mymodel.get_value("Q", np.array([0.]))
array([0.00141273])
```


## Containers

The Dockerfiles to the containers are in the subdirectories in this repository.

The containers are published on Docker Hub ([ewc-podman](https://hub.docker.com/repository/docker/bschilperoort/ewc-podman) and [hbv-podman](https://hub.docker.com/repository/docker/bschilperoort/hbv-podman)).

If the images are rebuilt at some point (and still have tag latest) you will need to clear the podman cache:

```sh
podman system prune --all --force && podman rmi --all
```
