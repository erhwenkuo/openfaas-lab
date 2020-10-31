# OpenFaaS, K3D Quickstart

[Chinese version](README_zh-tw.md)

This ia a self-paced lab for learning how to build, deploy and run serverless functions with OpenFaas.

## OpenFaaS brief introduciton

![](https://camo.githubusercontent.com/cf01eefb5b6905f3774376d6d1ed55b8f052d211/68747470733a2f2f626c6f672e616c6578656c6c69732e696f2f636f6e74656e742f696d616765732f323031372f30382f666161735f736964652e706e67
)

OpenFaaS makes it easy for developers to deploy event-driven functions and microservices to Kubernetes without repetitive, boiler-plate coding. 

We can package our code or an existing binary in a Docker image to get a highly scalable endpoint with auto-scaling and metrics.

## How to start quickly?

This is a quick and dirty example of how to start [OpenFaas](https://www.openfaas.com/) on [K3S](https://k3s.io/) cluster using [K3D](https://k3d.io/).


### Pre-reqs:

1) Docker
2) [K3d](https://github.com/rancher/k3d/releases)
3) The Openfaas [CLI tool](https://github.com/openfaas/faas-cli#get-started-install-the-cli). 
4) The [arkade](https://github.com/alexellis/arkade) CLI
5) The [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/) CLI

### 1. Install **K3D** cluster

Use below command to create a k3d cluster:

```bash
$ k3d cluster create
```

You should see similar console output.

```bash
INFO[0000] Created network 'k3d-k3s-default'            
INFO[0000] Created volume 'k3d-k3s-default-images'      
INFO[0001] Creating node 'k3d-k3s-default-server-0'     
INFO[0007] Creating LoadBalancer 'k3d-k3s-default-serverlb' 
INFO[0008] (Optional) Trying to get IP of the docker host and inject it into the cluster as 'host.k3d.internal' for easy access 
INFO[0011] Successfully added host record to /etc/hosts in 2/2 nodes and to the CoreDNS ConfigMap 
INFO[0011] Cluster 'k3s-default' created successfully!  
INFO[0011] You can now use it like this:                
kubectl cluster-info
```

Above command will create a k3d cluster named "k3s-default".

### 2. Install **OpenFaas** using **arkade** cli

Using [arkade](https://github.com/alexellis/arkade) is the most easy way to to install OpenFaas in kubernetes.

```bash
$ arkade install openfaas
```

Below information will show once arkade finish install OpenFaas.

```bash
Using kubeconfig: /home/witlab/.kube/config
Node architecture: "amd64"
Client: "x86_64", "Linux"
2020/10/30 07:15:52 User dir established as: /home/witlab/.arkade/
"openfaas" has been added to your repositories

VALUES values.yaml
Command: /home/witlab/.arkade/bin/helm [upgrade --install openfaas openfaas/openfaas --namespace openfaas --values /tmp/charts/openfaas/values.yaml --set gateway.directFunctions=true --set openfaasImagePullPolicy=IfNotPresent --set faasnetes.imagePullPolicy=Always --set gateway.replicas=1 --set ingressOperator.create=false --set queueWorker.maxInflight=1 --set basic_auth=true --set serviceType=NodePort --set clusterRole=false --set operator.create=false --set basicAuthPlugin.replicas=1 --set queueWorker.replicas=1]
Release "openfaas" does not exist. Installing it now.
NAME: openfaas
LAST DEPLOYED: Fri Oct 30 07:15:56 2020
NAMESPACE: openfaas
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
To verify that openfaas has started, run:

  kubectl -n openfaas get deployments -l "release=openfaas, app=openfaas"
=======================================================================
= OpenFaaS has been installed.                                        =
=======================================================================

# Get the faas-cli
curl -SLsf https://cli.openfaas.com | sudo sh

# Forward the gateway to your machine
kubectl rollout status -n openfaas deploy/gateway
kubectl port-forward -n openfaas svc/gateway 8080:8080 &

# If basic auth is enabled, you can now log into your gateway:
PASSWORD=$(kubectl get secret -n openfaas basic-auth -o jsonpath="{.data.basic-auth-password}" | base64 --decode; echo)
echo -n $PASSWORD | faas-cli login --username admin --password-stdin

faas-cli store deploy figlet
faas-cli list

# For Raspberry Pi
faas-cli store list \
 --platform armhf

faas-cli store deploy figlet \
 --platform armhf

# Find out more at:
# https://github.com/openfaas/faas

Thanks for using arkade!

```

### Connect to OpenFaas via UI

The OpenFaaS Gateway can be accessed through its REST API via CLI or UI.

![](https://raw.githubusercontent.com/openfaas/faas/master/docs/of-workflow.png)

#### 1.Proxy OpenFaaS Gateway service

```bash
# Forward the gateway to your machine
$ kubectl port-forward -n openfaas svc/gateway 8080:8080 &
```

#### 2.Retrieve OpenFaas userid & password credential

While arcade CLI install OpenFaaS, it will create a user "admin" with random generated password. Let's export the password as environment variable.

```bash
# If basic auth is enabled, you can now log into your gateway:
$ PASSWORD=$(kubectl get secret -n openfaas basic-auth -o jsonpath="{.data.basic-auth-password}" | base64 --decode; echo)

# Print out the password from environment
$ echo $PASSWORD
{YOUR_PASSWORD}
```

#### 3.Login OpenFaas UI

Open a web browser to `localhost:8080`, and enter the credential:
* User Name: `admin`
* Password: `{YOUR_PASSWORD}`

![OpenFaaS UI](docs/openfaas_ui_01.png)

You can try various functions from the OpenFaaS `store` within the UI. For exmaple:
1) Click the "Deploy New Function" button
2) Search for "Figlet" and click it.
3) Click "Deploy"

![Deploy Figlet](docs/deploy_figlet.png)

The function should appear on the menu on the left, click it, and wait for the status to change to “Ready”. Then enter "hello" in the “Request Body” field and click Invoke. 

![Invoke Figlet](docs/invoke_figlet.png)

You should able to see the result in "Response body":

```bash
 _          _ _       
| |__   ___| | | ___  
| '_ \ / _ \ | |/ _ \ 
| | | |  __/ | | (_) |
|_| |_|\___|_|_|\___/ 
                      

```

### Connect to OpenFaas via CLI

Make sure you install `faas-cli` below below practice. If you're on MacOS and already have `homebrew` installed then installation is as simple as:

```bash
brew install faas-cli
```

For manual installation, you can use the following command:

```bash
curl -sSL https://cli.openfaas.com | sudo sh
```

#### Configure faas-cli

Configure the faas-cli to use your local OpenFaaS cluster by using the faas-cli login command. There are two environment variables needed for faas-cli:
* OPENFAAS_UR
* PASSWORD

```bash
$ export OPENFAAS_URL=http://localhost:8080
$ echo $PASSWORD | faas-cli login --password-stdin
```

#### List deployed function

To list current deployed functions in OpenFaas:

```bash
$ faas-cli list

Function                      	Invocations    	Replicas
figlet                        	0              	1  
```

#### Invoke deployed function via CLI

```bash
$ echo "hello" | faas-cli invoke figlet

 _          _ _       
| |__   ___| | | ___  
| '_ \ / _ \ | |/ _ \ 
| | | |  __/ | | (_) |
|_| |_|\___|_|_|\___/ 
```

### Destroy & clean up

To clean up, execute below commands:

```bash
$ k3d cluster delete
```

That's it! You have a runing OpenFaaS to experiement.

### Hey, I am interested

To learn more how to use OpenFaas for designing/deploying serverless functions, you can continue with [labs introduction](lab-introduction.md) and each well-craft labs:

* [Lab 1 - Prepare for OpenFaaS](./lab1.md)
* [Lab 2 - Test things out](./lab2.md)
* [Lab 3 - Introduction to Functions](./lab3.md)
* [Lab 4 - Go deeper with functions](./lab4.md)
* [Lab 5 - Create a GitHub bot](./lab5.md)
* [Lab 6 - HTML for your functions](./lab6.md)
* [Lab 7 - Asynchronous Functions](./lab7.md)
* [Lab 8 - Advanced Feature - Timeouts](./lab8.md)
* [Lab 9 - Advanced Feature - Auto-scaling](./lab9.md)
* [Lab 10 - Advanced Feature - Secrets](./lab10.md)
* [Lab 11 - Advanced feature - Trust with HMAC](./lab11.md) 

