# Fog computing

This repository is a direct application of the signature
principle described in [this paper](https://www.sciencedirect.com/science/article/abs/pii/S0167739X23003163),
without identity based authentication.

The goal of the project was to build a edge computing streaming platform using SIS
signature for packet integrity from scratch using MQTT as the main information vector.

This project has no mean to be deployed in a production environment an serve the
purpose of a proof of concept that will be later used for studies.

## Stack

- Golang
  - GORM
  - Gin
  - [SIS Signature custom package](https://github.com/f7ed0/golang_SIS_SIG)
- msgpack
- Python
  - CPLEX
  - Numpy
  - Scipy
- Vite + react
- Docker
- eclipse mosquitto
- mysql
- FFMPEG

## Configuration

## Deployment

### Getting the repository

This repository is using git submodules, so you need to pull
them (if not already done)

```bash
git submodules update --init
```

### Deploying services using the scripts

If you are deploying multiple instances of the project you will only
need one db for all master-nodes. Please note there will be modification
to be done on the `compose.yaml` to link them.

You can use `wipe-start-mqtt-and-db.sh` to reset everything and launch
both db and MQTT brocker on the device you will use to store the db.

On the other devices, you can use `wipe-start-mqtt.sh` to reset and
launch the MQTT brocker.

!> [!IMPORTANT]
> You need to have the MQTT brocker started before starting anything else.

Once the db and all MQTT brocker launched, you can launch all the services on
all devices using `start-other.sh`. By default the number of worker nodes launched
is 3 (you need to edit the script if you want to change the value)

If it is the first time you launch it, docker will need to pull and build
images in order to work. This may take some time to unfold, but it is a
œone-time task.
