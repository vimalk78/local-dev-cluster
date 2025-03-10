# local-dev-cluster

![GitHub](https://img.shields.io/github/license/sustainable-computing-io/local-dev-cluster)
[![units-test](https://github.com/sustainable-computing-io/local-dev-cluster/actions/workflows/test.yml/badge.svg)](https://github.com/sustainable-computing-io/local-dev-cluster/actions/workflows/test.yml)

This repo provides the scripts to create a local [kubernetes](kind/kind.sh)/[openshift](microshift/microshift.sh) cluster to be used for development or integration tests. It is also used in [Github action](https://github.com/sustainable-computing-io/kepler-action) for kepler.

## Prerequisites
- Locate your BCC lib and linux header.
- [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/)
- [kind](https://kind.sigs.k8s.io/docs/user/quick-start/#installing-from-release-binaries)

Please install the same version of `kubectl` and `kind` as Github-hosted runner.

Currently Kepler project's Github Action only supports `Ubuntu` based runners.

You can refer to tools list [here](https://github.com/actions/runner-images/blob/main/images/ubuntu/Ubuntu2204-Readme.md#tools)
## Startup
1. Modify kind [config](./kind/manifests/kind.yml) to make sure `extraMounts:` cover the linux header and BCC.
2. Export `CLUSTER_PROVIDER` env variable:
```
export CLUSTER_PROVIDER=kind

or

export CLUSTER_PROVIDER=microshift
```
3. To setup local env run:
```
./main.sh up
```
4. To tear down local env run:
```
./main.sh down
```

Alternatively, use `.env` file to define and override the default configuration
variables. E.g

```sh
#.env

CLUSTER_PROVIDER=microshift
CLUSTER_NAME=microshift
PROMETHEUS_ENABLE=false
GRAFANA_ENABLE=false
```

Start the cluster by running
```sh
./main.sh up
```

## Container registry
There's a container registry available which is exposed at `localhost:5001`.

## Note for kepler contributor
To set up a local cluster for kepler development, we need to make the cluster
connected with a local container registry.

### Bump version step for this repo
1. Check kubectl version.
2. Check k8s cluster provider's version(as KIND).
3. Check prometheus operator version.

## How to contribute to this repo

### A new k8s cluster provider
Please feel free to refer to [kind provider implementation](providers/kind/kind.sh)
to contribute a k8s cluster. Please ensure that these checklist are statisfies
as Kepler requires certain feature to be available.

#### Checklist

- [ ] The provider related script should be placed under `'./provider/<name>/<name.sh>'`
- [ ] The script should have a `<provider>_up` function that sets up the k8s cluster.
- [ ] The script should have a `<provider>_down` function that deletes the cluster.
- [ ] The script should have a `<provider>_kubeconfig` function that prints
      the path to the cluster's kubeconfig that is located on host machine.
      Consider using `tmp/<provider>/kubeconfig` as the path to create/copy the
      kubeconfig file.

- [ ] Ensure cluster can pull from the local specific registry since for local
      development, we expect to push the development image to the local registry
      instead of a public registry.
- [ ] Mount local path of linux kernel and ebpf(BCC) inside kepler pod.
