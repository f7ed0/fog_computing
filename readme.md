# Fog computing

This repository is a direct application of the signature
principle described in [this paper](https://www.sciencedirect.com/science/article/abs/pii/S0167739X23003163),
without identity based authentication.

The goal of the project was to build a edge computing streaming platform using SIS
signature for packet integrity from scratch using MQTT as the main information vector.

This project has no mean to be deployed in a production environment an serve the
purpose of a proof of concept that will be later used for studies.

## Infrastructure

This repository is meant to deploy one fog with one master node and as many as worker nodes as wanted.

Each component rely on the MQTT broker to cummunicate to others.
The master node subscribe to every channel corresponding to `video/stream/*` (* as a wildcard) for video packets to save in the fog then dispatch these packets among the worker nodes.

Attached to the worker nodes a loadbalancer pings them every 2 seconds to know their workload (cpu and memory usage) and publish a policy to the MQTT broker (which worker node should be used first) that the master node will use to send the incoming packets to the correct worker.

When they receive a packet, worker nodes will check if it's a new video being saved and if it is they will send a POST request to the api in order to register this video in the database.
The api is here to tell clients what are the videos registered on the fog.

### Multi-fog infrastructures

You can start multiple fog instances, master nodes will auto-connect between each other as if they were "special" worker nodes.
Like that packets requests received by one master node will be recursively forwarded to all of the other connected master nodes.

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

The majority of the configuration is done through the docker
compose file. It will be precised underneath if any touch in
the code needs to be done to achieve a specific configuration

### Default configuration

You can use the default configuration if you want to run them
project with only one domain. You can find this Configuration
in the compose.yaml of the repository

> All necessary environment variables are exported inside the compose.yaml file

## Deployment

### Getting the repository

This repository is using git submodules, so you need to pull
them (if not already done)

```bash
git submodule update --init
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

## Video uploading

As the uploading to the fog is not part of the infrastructure you will need a third-party script to publish packets to the MQTT broker as mentionned before. This script can be found [here](https://github.com/Miunn/mqtt-streaming-client).

Edit the environment variables in the `.env` file according to your configuration. The structure is :

```
BROKER_URL="10.0.0.1"
BROKER_PORT=1883
BROKER_STREAMING_CLIENT_ID="go-streaming-client"
BROKER_USERNAME="client"
BROKER_PASSWORD="password"
```

Then you can use the script with :

`./main video.mp4`

## Perspectives

