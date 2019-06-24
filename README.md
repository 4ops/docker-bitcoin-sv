# Bitcoin SV

Bitcoin SV is the original Bitcoin.

See <https://bitcoinsv.io> for additional info about bitcoin-sv software.

## Minimal hardware requirements for full node setup

### testnet

For initial synchronization:

* 2 CPU cores
* 4 GB RAM
* 35 GB of disk space

When synchronized:

* 2 CPU cores
* 2 GB RAM

### mainnet

For initial synchronization:

* 4 CPU cores
* 8 GB RAM
* 180 GB of disk space

When synchronized:

* 2 CPU cores
* 4 GB RAM

## Deployment

### Deploy with docker-compose

* Install [docker](https://docs.docker.com/install/).
* Install [docker-compose](https://docs.docker.com/compose/install/).
* Copy [example](examples/docker/docker-compose.yml) config to your server and customize it.
* Start service(s) with docker-compose.

Linux CLI example:

```shell
$ curl -fsSL https://get.docker.com -o get-docker.sh # Do not forget to review scripts source code before execution

$ sh get-docker.sh # Install docker
(output omitted)

$ docker --version # Verify docker is installed
Docker version 18.09.6, build 481bc77

$ export BASEFILENAME=docker-compose-$(uname -s)-$(uname -m)

$ export BASEURL=https://github.com/docker/compose/releases/download/1.24.0

$ curl -fsSLO "${BASEURl}/${BASEFILENAME}" && curl -fsSLO "${BASEURl}/${BASEFILENAME}.sha256"

$ sha256sum -c $BASEFILENAME.sha256
docker-compose-Linux-x86_64: OK

$ chmod +x $BASEFILENAME && sudo mv $BASEFILENAME /usr/local/bin/docker-compose && rm -f $BASEFILENAME.sha256 && unset BASEURL && unset BASEFILENAME # Install docker-compose

$ docker-compose --version # Verify docker-compose installed
docker-compose version 1.24.0, build 0aa59064

$ mkdir -p my-bitcoin-sv-service-dir && cd $_ # Customize directory name

$ curl -fsSLO https://raw.githubusercontent.com/4ops/bitcoin-sv/master/examples/docker/docker-compose.yml # Download service config

$ vi docker-compose.yml # Customize configuration

$ docker-compose config --quiet && docker-compose pull --quiet && echo OK # Verify configuration and download required images
OK

$ docker-compose up -d # Start service(s)
Creating network "my-bitcoin-sv-service-dir_default" with the default driver
Creating my-bitcoin-sv-service-dir_testnet_1 ... done
Creating my-bitcoin-sv-service-dir_mainnet_1 ... done
```

### Deploy to existing kubernetes cluster (testnet only example)

* Download [secret](examples/kubernetes/secret.yaml), [service](examples/kubernetes/service.yaml) and [statefulset](examples/kubernetes/statefulset.yaml) examples.
* Customize downloaded manifests.
* Deploy them to kubernetes.

Linux CLI example with kubectl:

```shell
$ mkdir -p my-bitcoin-sv-manifests && cd $_ # Customize directory name

$ export BASEURL=https://raw.githubusercontent.com/4ops/bitcoin-sv/master/examples/kubernetes

$ curl -fsSLO "${BASEURL}/secret.yaml" && curl -fsSLO "${BASEURL}/service.yaml" && curl -fsSLO "${BASEURL}/statefulset.yaml" && unset BASEURL # Download statefulset example

$ vi secret.yaml # Customize passwords

$ vi service.yaml # Customize service configuration

$ vi statefulset.yaml # Customize application and disk storage configuration

$ kubectl -n my-bitcoin-sv-namespace apply -f `pwd` --dry-run # Check syntax
secret/bitcoin-sv-testnet created (dry run)
service/bitcoin-sv-testnet created (dry run)
statefulset.apps/bitcoin-sv-testnet created (dry run)

$ kubectl -n my-bitcoin-sv-namespace apply -f `pwd` # Deploy application (do not forget to change namespace name)
```
