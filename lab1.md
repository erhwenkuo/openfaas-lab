# Lab 1 - Prepare for OpenFaaS

[Chinses version](lab1_zh-tw.md)

<img src="https://github.com/openfaas/media/raw/master/OpenFaaS_Magnet_3_1_png.png" width="500px"></img>

OpenFaaS requires a [Kubernetes](https://kubernetes.io) cluster to operate. You can use a single-node cluster or a multi-node cluster, whether that's on your laptop or in the cloud.

The basic primitive for any OpenFaaS function is a Docker image, which is built using the `faas-cli` tool-chain.

## Pre-requisites:

Let's install Docker, the OpenFaaS CLI and pick Kubernetes to proceed.

## Docker

For Mac

* [Docker CE for Mac Edge Edition](https://store.docker.com/editions/community/docker-ce-desktop-mac)

For Windows 

* Use Windows 10 Pro or Enterprise only
* Install [Docker CE for Windows](https://store.docker.com/editions/community/docker-ce-desktop-windows)

> Please ensure you use the **Linux** containers Docker daemon by using the Docker menu in the Windows task bar notification area.

* Install [Git Bash](https://git-scm.com/downloads)

When you install git bash pick the following options: `install UNIX commands` and `use true-type font`.

> Note: please use *Git Bash* for all steps: do not attempt to use *PowerShell*, *WSL* or *Bash for Windows*.

Linux - Ubuntu or Debian

* Docker CE for Linux

> You can install Docker CE from the [Docker Store](https://store.docker.com).

Note: As a last resort if you have an incompatible PC you can run the workshop on https://labs.play-with-docker.com/.

## OpenFaaS CLI

You can install the OpenFaaS CLI using the official bash script, `brew` is also available but can lag one or two versions behind.

With MacOS or Linux run the following in a Terminal:

```sh
$ curl -sLSf https://cli.openfaas.com | sudo sh
```

For Windows, run this in *Git Bash*:

```sh
$ curl -sLSf https://cli.openfaas.com | sh
```

> If you run into any issues then you can download the latest `faas-cli.exe` manually from the [releases page](https://github.com/openfaas/faas-cli/releases). You can place it in a local directory or in the `C:\Windows\` path so that it's available from a command prompt.

We will use the `faas-cli` to scaffold new functions, build, deploy and invoke functions. You can find out commands available for the cli with `faas-cli --help`.

Test the `faas-cli`. Open a Terminal or Git Bash window and type in:

```sh
$ faas-cli help
$ faas-cli version
```

## Configure a registry - The Docker Hub

Sign up for a Docker Hub account. The [Docker Hub](https://hub.docker.com) allows you to publish your Docker images on the Internet for use on multi-node clusters or to share with the wider community. We will be using the Docker Hub to publish our functions during the workshop.

You can sign up here: [Docker Hub](https://hub.docker.com)

Open a Terminal or Git Bash window and log into the Docker Hub using the username you signed up for above.

```sh
$ docker login
```

> Note: Tip from community - if you get an error while trying to run this command on a Windows machine, then click on the Docker for Windows icon in the taskbar and log into Docker there instead "Sign in / Create Docker ID".

* Set your OpenFaaS prefix for new images

OpenFaaS images are stored in a Docker registry or the Docker Hub, we can set an environment variable so that your username is automatically added to new functions you create. This will save you some time over the course of the workshop.

Edit `~/.bashrc` or `~/.bash_profile` - create the file if it doesn't exist.

Now add the following - changing the URL as per the one you saw above.

```sh
export OPENFAAS_PREFIX="" # Populate with your Docker Hub username
```

## Setup a single-node cluster

OpenFaas can suport to run on different types of container orchestration platform. For this series of lab, only focus on Kubernetes. 

### Install latest `kubectl`

Install `kubectl` for your operating system using the instructions below or the [official documentation](https://kubernetes.io/docs/tasks/tools/install-kubectl/)

* Linux

```sh
export VER=$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)
curl -LO https://storage.googleapis.com/kubernetes-release/release/$VER/bin/linux/amd64/kubectl
chmod +x kubectl
mv kubectl /usr/local/bin/
```

* MacOS

```sh
export VER=$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)
curl -LO https://storage.googleapis.com/kubernetes-release/release/$VER/bin/darwin/amd64/kubectl
chmod +x kubectl
mv kubectl /usr/local/bin/
```

* Windows

```sh
export VER=$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)
curl -LO https://storage.googleapis.com/kubernetes-release/release/$VER/bin/windows/amd64/kubectl.exe
chmod +x kubectl.exe
mkdir -p $HOME/bin/
mv kubectl $HOME/bin/
```

### Install Kubernetes cluster (using k3s/k3d)

If you have Docker on your computer, then you can use `k3d` from Rancher Labs. It installs a lightweight version of Kubernetes called `k3s` and runs it within a Docker container, meaning it will work on any computer that has Docker.

The detail introduction of [Install k3d](https://github.com/rancher/k3d)

Let's start a cluster:

1. `k3d cluster create CLUSTER_NAME` to create a new single-node cluster (= 1 container running k3s + 1 loadbalancer container)
2. `k3d kubeconfig merge CLUSTER_NAME --switch-context` to update your default kubeconfig and switch the current-context to the new one
3. execute some commands like `kubectl get pods --all-namespaces` to test the cluser
4. If you want to delete cluster `k3d cluster delete CLUSTER_NAME`

### Install OpenFaaS

There are three ways to install OpenFaaS and you can pick whatever makes sense for you and your team. In this workshop we will use the official installer `arkade`.

* `arkade app install` - arkade installs OpenFaaS using its official helm chart. It can also offer other software with a user-friendly CLI such as `cert-manager` and `nginx-ingress`. It's the easiest and quickest way to get up and running.

* Helm chart - sane defaults and easy to configure through YAML or CLI flags. Secure options such as `helm template` or `helm 3` also exist for those working within restrictive environments

* Plain YAML files - hard-coded settings/values. Tools like Kustomize can offer custom settings


In this lab, we will use `arkade` to install OpenFaas.

### Install with `arkade`

#### Get arkade

For MacOS / Linux:

```sh
curl -SLsf https://dl.get-arkade.dev/ | sudo sh
```

For Windows:

```sh
curl -SLsf https://dl.get-arkade.dev/ | sh
```

#### Install the OpenFaaS

```sh
arkade install openfaas
```

If you're using a managed cloud Kubernetes service which supplies LoadBalancers, then run the following:

```sh
arkade install openfaas --load-balancer
```

> Note: the `--load-balancer` flag has a default of `false`, so by passing the flag, the installation will request one from your cloud provider.

### Expose OpenFaaS Gateway

* Check the gateway is ready

```sh
kubectl rollout status -n openfaas deploy/gateway
```

If you're using your laptop, a VM, or any other kind of Kubernetes distribution run the following instead:

```sh
kubectl port-forward svc/gateway -n openfaas 8080:8080
```

This command will open a tunnel from your Kubernetes cluster to your local computer so that you can access the OpenFaaS gateway. There are other ways to access OpenFaaS, but that is beyond the scope of this workshop.

Your gateway URL is: `http://127.0.0.1:8080`

### Login OpenFaaS

```sh
export OPENFAAS_URL="http://127.0.0.1:8080" # Populate as above

# This command retrieves your password
PASSWORD=$(kubectl get secret -n openfaas basic-auth -o jsonpath="{.data.basic-auth-password}" | base64 --decode; echo)

# This command logs in and saves a file to ~/.openfaas/config.yml
echo -n $PASSWORD | faas-cli login --username admin --password-stdin
```

Check that `faas-cli list` works:

```sh
faas-cli list
```

### Permanently save your OpenFaaS URL
 
Edit `~/.bashrc` or `~/.bash_profile` - create the file if it doesn't exist.

Now add the following - changing the URL as per the one you saw above.

```sh
export OPENFAAS_URL="http://127.0.0.1:8080" # populate as above
```

Now move onto [Lab 2](lab2.md)