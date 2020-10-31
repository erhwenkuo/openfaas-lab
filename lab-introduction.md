# openfaas-workshop

[Chinses version](lab-introduction_zh-tw.md)

This is a self-paced workshop (modifed version of orgin [openfaas-workshop](https://github.com/openfaas/workshop)) for learning how to build, deploy and run serverless functions with OpenFaaS.

The workshop will focus on running OpenFaaS on kubernes and also providing more examples of different languages (including Python, Java & Golang).

![](https://github.com/openfaas/media/raw/master/OpenFaaS_Magnet_3_1_png.png)

In this workshop you begin by deploying OpenFaaS to your laptop or a remote cluster with Docker for Mac or Windows. You will then kick the tires with the OpenFaaS UI, CLI and Function Store. 

After building, deploying an invoking your own Serverless Functions in Python you'll go on to cover topics such as: 

* managing dependencies with pip
* dealing with API tokens through secure secrets
* monitoring functions with Prometheus
* invoking functions asynchronously and chaining functions together to create applications

The labs culminate by having you create your very own GitHub bot which can respond to issues automatically. The same method could be applied by connecting to online event-streams through IFTTT.com - this will enable you to build bots, auto-responders and integrations with social media and IoT devices.

Finally the labs cover more advanced topics and give suggestions for further learning.

## Requirements:

We walk through how to install these requirements in [Lab 1](./lab1.md). Please do [Lab 1](./lab1.md) before you attend an instructor-led workshop.  At the very least you should [install Docker](./lab1.md#docker) and [pre-pull the OpenFaaS images](./lab1.md#Pre-pull-the-system-images).

* Functions will be written in Python(later on Java, Golang might be included), so prior programming or scripting experience is preferred 
* Install the recommended code-editor / IDE [VSCode](https://code.visualstudio.com/download) / IDE [Pycharm](https://www.jetbrains.com/pycharm/)
* For Windows install [Git Bash](https://git-scm.com/downloads)
* Preferred OS: MacOS, Windows 10 Pro/Enterprise, Ubuntu Linux

Docker:

* Docker CE for [Mac](https://store.docker.com/editions/community/docker-ce-desktop-mac)/[Windows](https://store.docker.com/editions/community/docker-ce-desktop-windows) **Edge edition**
* Docker CE for Linux

> Note: As a last resort if you have an incompatible PC you can run the workshop on https://labs.play-with-docker.com/.

## [Lab 1 - Prepare for OpenFaaS](./lab1.md)

* Install pre-requisites
* Set up a single-node cluster with Kubernetes
* Docker Hub account
* OpenFaaS CLI
* Deploy OpenFaaS

## [Lab 2 - Test things out](./lab2.md)

* Use the UI Portal
* Deploy via the Function Store
* Learn about the CLI
* Find metrics with Prometheus

## [Lab 3 - Introduction to Functions](./lab3.md)

* Scaffold or generate a new function
* Build the astronaut-finder function
 * Add dependencies with `pip`
 * Troubleshooting: find the container's logs
* Troubleshooting: verbose output with `write_debug`
* Use custom and third-party language templates
* Discover community templates using the Template Store

## [Lab 4 - Go deeper with functions](./lab4.md)

* [Inject configuration through environmental variables](lab4.md#inject-configuration-through-environmental-variables)
  * At deployment using yaml
  * Dynamically using HTTP context - querystring / headers etc
* Security: read-only filesystems
* [Making use of logging](lab4.md#making-use-of-logging)
* [Create Workflows](lab4.md#create-workflows)
  * Chaining functions on the client-side
  * Call one function from another

## [Lab 5 - Create a GitHub bot](./lab5.md)

> Build `issue-bot` - an auto-responder for GitHub Issues

* Get a GitHub account
* Set up a tunnel with ngrok
* Create an webhook receiver `issue-bot`
* Receive webhooks from GitHub
* Deploy SentimentAnalysis function
* Apply labels via the GitHub API
* Complete the function

## [Lab 6 - HTML for your functions](./lab6.md)

* Generate and return basic HTML from a function
* Read and return a static HTML file from disk
* Collaborate with other functions

## [Lab 7 - Asynchronous Functions](./lab7.md)

* Call a function synchronously vs asynchronously
* View the queue-worker's logs
* Use an `X-Callback-Url` with requestbin and ngrok

## [Lab 8 - Advanced Feature - Timeouts](./lab8.md)

* Adjust timeouts with `read_timeout`
* Accommodate longer running functions

## [Lab 9 - Advanced Feature - Auto-scaling](./lab9.md)

* See auto-scaling in action
  * Some insights on min and max replicas
  * Discover and visit local Prometheus
  * Execute and Prometheus query
  * Invoke a function using curl
  * Observe auto-scaling kicking in


## [Lab 10 - Advanced Feature - Secrets](./lab10.md)

* Adapt issue-bot to use a secret
  * Create a secret
  * Access the secret within the function

## [Lab 11 - Advanced feature - Trust with HMAC](./lab11.md)

* Apply trust to functions using `HMAC`

You can start with the first lab [Lab 1](lab1.md).

## Tear down / Clear up

You can find how to stop and remove OpenFaaS [here](https://docs.openfaas.com/deployment/troubleshooting/#uninstall-openfaas)

## Acknowledgements

The content of these labs origined from [openfaas/workshop](https://github.com/openfaas/workshop) and contributed by many contributers.

Thanks to @alexellis, @iyovcheva, @BurtonR, @johnmccabe, @laurentgrangeau, @stefanprodan, @kenfdev, @templum & @rgee0 for these labs.