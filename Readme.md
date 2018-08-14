# Docker Bitcoin/Lightning FullNode

This repository has all needed to put up a Bitcoin Full Node on a Docker Container

## Getting Started

These instructions will get you a copy of the project up and running on your local machine for development and testing purposes. See deployment for notes on how to deploy the project on a live system.

### Prerequisites

* Docker
* An unmetered broadband Internet connection with upload speeds of at least 400 kilobits (50 kilobytes) per second.
* It’s common for full nodes on high-speed connections to use 200 gigabytes upload or more a month.
* Download usage is around 20 gigabytes a month, plus around an additional 140 gigabytes the first time you start your node.

### Installing

In order to have a full node we have to follow these instructions.

#### Docker Configuration

All the docker configurations are in the file ./Docker

We have to change the file contained in the ./docker-bitcoind folder. **Specially rpcuser and rpcpassword**

```bash
port=12000
rpcport=12001

server=1

rpcallowip=0.0.0.0/0

rpcuser=changetheuser
rpcpassword=useastrongpassword
```

To build the Docker go to the folder ./docker-ditcoind:

```bash
cd docker-bitcoind
```

And run:

```bash
docker build -t bitcoind .
```

_To build bitcoin ourselves we have to run the supplied ./Docker-Build file._
_Take into account that this takes a loooooooong time to build. Please, consider using the default aproach._

```bash
docker build -t bitcoind . --file Docker-Build
```

## Deployment

Before we start our container we have to create a new volume, after all, we need to save our Bitcoin node state.

```bash
docker volume create bitcoin-node-volume
```

Now we create a container using the created image and volume.

* Sets the name of the container ```--name bitcoind-container```
* Make the container run in detached mode ```-d```
* Allocate a pseudo-tty to keep it running in background ```-t```
* Sets the volume and the folder it must mount to ```-v bitcoin-node-volume:/dev/vol1/node```
* Redirect our port 5000 to cointainer's port 12001 ```-p 5000:12001```

```bash
docker run --name bitcoind-container -d -t -v bitcoin-node-volume:/dev/vol1/node -p 5000:12001 bitcoind
```

You can check our docker is running by using:

```bash
$ docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS                     NAMES
1234567890ab        bitcoind            "bash"              1 second ago        Up 3 seconds        0.0.0.0:5000->12001/tcp   bitcoind-container
```

To start our bitcoin RPC server we run:

```bash
bitcoind -datadir=/dev/vol1/node -daemon
```

And to connect using the client interface we use:

```bash
bitcoin-cli -datadir=/dev/vol1/node [COMMAND]
```

## Built With

* [Docker](https://docker.org/)

## Versioning

We use [SemVer](http://semver.org/) for versioning. For the versions available, see the [tags on this repository](https://github.com/ngoline/medium-domain-proxy/tags).

## Authors

* **Níckolas Goline** - *Initial work* - [nGoline](https://github.com/ngoline)

See also the list of [contributors](https://github.com/ngoline/medium-domain-proxy/contributors) who participated in this project.

## License

This project is licensed under the Apache 2.0 License - see the [LICENSE.md](LICENSE.md) file for details