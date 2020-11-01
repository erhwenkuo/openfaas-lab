# Lab 2 - Test things out

<img src="https://github.com/openfaas/media/raw/master/OpenFaaS_Magnet_3_1_png.png" width="500px"></img>

Before starting this lab, create a new folder for your files:

```sh
$ mkdir -p lab2 \
   && cd lab2
```

## Use the UI Portal

You can now test out the OpenFaaS UI:

If you have set an `$OPENFAAS_URL` then get the URL and then click on it:

```sh
echo $OPENFAAS_URL
http://localhost:8080
```

If you haven't set an `$OPENFAAS_URL` then the default is: [http://127.0.0.1:8080](http://127.0.0.1:8080).

We can deploy some sample functions and then use them to test things out:

```sh
$ faas-cli deploy -f https://raw.githubusercontent.com/openfaas/faas/master/stack.yml
```

You should see many functions deployed on left function list.

![](docs/lab2/deploy_store_funcs.png)

You can try them out in the UI such as the Markdown function which converts Markdown code into HTML.

Type the below into the *Request* field:

```sh
## The **OpenFaaS** _workshop_
```

Now click *Invoke* and see the response appear in the bottom half of the screen.

I.e.

```sh
<h2>The <strong>OpenFaaS</strong> <em>workshop</em></h2>
```

You will see the following fields displayed:

* Status - whether the function is ready to run. You will not be able to invoke the function from the UI until the status shows Ready.
* Replicas - the amount of replicas of your function running in the cluster
* Image - the Docker image name and version as published to the Docker Hub or Docker repository
* Invocation count - this shows how many times the function has been invoked and is updated every 5 seconds

Click *Invoke* a number of times and see the *Invocation count* increase.

![](docs/lab2/invoke_markdown_func.png)

## Deploy via the Function Store

You can deploy a function from the OpenFaaS store. The store is a free collection of functions curated by the community.

* Click *Deploy New Function*
* Click *From Store*
* Click *Figlet* or enter *figlet* into the search bar and then click *Deploy*

![](docs/lab2/deploy_figlet_func.png)

The `Figlet` function will now appear in your left-hand list of functions. Give this a few moments to be downloaded from the Docker Hub and then type in some text (ex. 'hello') and click Invoke like we did for the Markdown function.

You'll see an ASCII logo generated like this:

```sh
 _          _ _       
| |__   ___| | | ___  
| '_ \ / _ \ | |/ _ \ 
| | | |  __/ | | (_) |
|_| |_|\___|_|_|\___/ 
                      
```

![](docs/lab2/test_figlet_func.png)

## Learn about the CLI

You can now test out the CLI, but first a note on alternate gateways URLs:

If your *gateway is not* deployed at http://127.0.0.1:8080 then you will need to specify the alternative location. There are several ways to accomplish this:

1. Set the environment variable `OPENFAAS_URL` and the `faas-cli` will point to that endpoint in your current shell session. For example: `export OPENFAAS_URL=http://localhost:8080`. This is already set in [Lab 1](./lab1.md) if you are following the Kubernetes instructions.
2. Specify the correct endpoint inline with the `-g` or `--gateway` flag: `faas-cli deploy --gateway http://openfaas.endpoint.com:8080`
3. In your deployment YAML file, change the value specified by the `gateway:` object under `provider:`.

### List the deployed functions

This will show the functions, how many replicas you have and the invocation count.

```sh
$ faas-cli list

Function                      	Invocations    	Replicas
base64                        	0              	1    
wordcount                     	0              	1    
echoit                        	0              	1    
markdown                      	0              	1    
nodeinfo                      	0              	1    
hubstats                      	0              	1    
figlet                        	1              	1  
```

You should see the *markdown* function as `markdown` and the *figlet* function listed too along with how many times you've invoked them.

Now try the verbose flag

```sh
$ faas-cli list --verbose

Function                      	Image                                   	Invocations    Replicas
base64                        	functions/alpine:latest                 	0              1    
wordcount                     	functions/alpine:latest                 	0              1    
echoit                        	functions/alpine:latest                 	0              1    
markdown                      	functions/markdown-render:latest        	0              1    
nodeinfo                      	functions/nodeinfo:latest               	0              1    
hubstats                      	functions/hubstats:latest               	0              1    
figlet                        	functions/figlet:0.13.0                 	1              1    
```
or

```sh
$ faas-cli list -v

Function                      	Image                                   	Invocations    	Replicas
base64                        	functions/alpine:latest                 	0              	1    
wordcount                     	functions/alpine:latest                 	0              	1    
echoit                        	functions/alpine:latest                 	0              	1    
markdown                      	functions/markdown-render:latest        	0              	1    
nodeinfo                      	functions/nodeinfo:latest               	0              	1    
hubstats                      	functions/hubstats:latest               	0              	1    
figlet                        	functions/figlet:0.13.0                 	1              	1    
```

You can now see the Docker image along with the names of the functions.

### Invoke a function

Pick one of the functions you saw appear on `faas-cli list` such as `markdown`:

```sh
$ faas-cli invoke markdown
```

You'll now be asked to type in some text. Hit `Control + D` when you're done.

![](docs/lab2/cli_invoke_markdown_func.png)

Alternatively you can use a command such as `echo` or `uname -a` as input to the `invoke` command which works through the use of pipes.

```sh
$ echo Hi | faas-cli invoke markdown

$ uname -a | faas-cli invoke markdown
```

![](docs/lab2/cli_invoke_markdown2.png)

You can even generate a HTML file from this lab's markdown file with the following:

```sh
$ git clone https://github.com/openfaas/workshop \
   && cd workshop

$ cat lab2.md | faas-cli invoke markdown
```

![](docs/lab2/cli_invoke_markdown3.png)

## Monitoring dashboard

OpenFaaS tracks metrics on your functions automatically using Prometheus. The metrics can be turned into a useful dashboard with free and Open Source tools like [Grafana](https://grafana.com).

Deploy OpenFaaS Grafana with _Kubernetes_:

Run Grafana in OpenFaaS Kubernetes namespace:

```sh
kubectl -n openfaas run \
--image=stefanprodan/faas-grafana:4.6.3 \
--port=3000 \
grafana
```

Expose Grafana with a NodePort:

```sh
kubectl -n openfaas expose pod grafana \
--type=NodePort \
--name=grafana
```

Find Grafana node port address:

```sh
$ GRAFANA_PORT=$(kubectl -n openfaas get svc grafana -o jsonpath="{.spec.ports[0].nodePort}")
$ GRAFANA_URL=http://IP_ADDRESS:$GRAFANA_PORT/dashboard/db/openfaas
```
where `IP_ADDRESS` is your corresponding IP for Kubernetes.

Or you may run this port-forwarding command in order to be able to access Grafana on `http://127.0.0.1:3000`:

```sh
$ kubectl port-forward pod/grafana 3000:3000 -n openfaas
```

![](docs/lab2/grafana_port_forward.png)

After the service has been created open Grafana in your browser, login with username `admin` password `admin` and navigate to the pre-made OpenFaaS dashboard at:

![](docs/lab2/grafana_login.png)

Find _OpenFaaS_ dashborad from top-left search dropdown `Home -> OpenFaaS`.

![](docs/lab2/grafana_openfaas_dashboard.png)

*Pictured: example of an OpenFaaS dashboard with Grafana*

Now move onto [Lab 3](./lab3.md)