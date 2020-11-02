# Lab 1 - Prepare for OpenFaaS

[English version](lab1.md)

<img src="https://github.com/openfaas/media/raw/master/OpenFaaS_Magnet_3_1_png.png" width="500px"></img>

OpenFaaS需要Kubernetes(https://kubernetes.io)集群才能運行。無論是在本機電腦上還是在雲中，你都可以使用單節點群集或多節點群集來安裝OpenFaaS。

OpenFaaS對於一個function的基本載體是Docker映像，你可使用`faas-cli`工具來幫助構建一個要部署在OpenFaas上的一個function服務。

## Pre-requisites:

讓我們安裝Docker，OpenFaaS CLI並選擇Kubernetes來啟動這個lab之旅。

## Docker

For Mac

* [Docker CE for Mac 版本](https://store.docker.com/editions/community/docker-ce-desktop-mac)

For Windows 

* 使用Windows 10 Pro或Enterprise版本
* 安裝 [Docker CE for Windows](https://store.docker.com/editions/community/docker-ce-desktop-windows)

> Please ensure you use the **Linux** containers Docker daemon by using the Docker menu in the Windows task bar notification area.

* 安裝 [Git Bash](https://git-scm.com/downloads)

安裝git bash時，請選擇以下選項：
`install UNIX commands` 與 `use true-type font`。

> 注意: 請使用　*Git Bash*　來完成所有安裝的步驟, 不要在Windows的環境下使用　*PowerShell*, *WSL* 或 *Bash

Linux - Ubuntu or Debian

* Docker CE for Linux

> 你可以從[Docker Store](https://store.docker.com)安裝Docker CE。

注意：如果你有一台不兼容的PC，不得已的情況下你可以在[play-with-docker](https://labs.play-with-docker.com/)上運行這個lab。

## OpenFaaS CLI

你可以使用官方的bash腳本安裝OpenFaaS CLI，也可以使用`brew` ，但它跟主流的版本可能會落後一到兩個版本號。

使用MacOS或Linux在終端中運行以下命令：

```sh
$ curl -sLSf https://cli.openfaas.com | sudo sh
```

在Windows的環境，請在*Git Bash*中運行:

```sh
$ curl -sLSf https://cli.openfaas.com | sh
```

> 如果遇到任何問題，則可以從OpenFaas的[releases page](https://github.com/openfaas/faas-cli/releases)上手動下載最新的`faas-cli.exe`。你可以將其放置在本地目錄或`C:\Windows\`路徑中，以便從命令提示符下使用它。

我們將使用`faas-cli`來幫忙創建新的function的程式結構，同時也會用它來構建，部署和調用function。你可以使用`faas-cli --help`來找出可用於cli相關命令解說。

測試`faas-cli`。打開終端或Git Bash視窗並輸入：

```sh
$ faas-cli help
$ faas-cli version
```

## Configure a registry - The Docker Hub

註冊一個Docker Hub帳戶。[Docker Hub](https://hub.docker.com)允許你將Docker映像發佈到Internet上，便於多節點群集或與更廣泛的社群共享。我們將在lab的過程中使用Docker Hub來發布我們的functions。

你可以在這裡註冊: [Docker Hub](https://hub.docker.com)

打開終端或Git Bash窗口，並使用你在上面註冊的用戶名登錄Docker Hub。

```sh
$ docker login
```

> 注意：來自社群的小提示 - 如果在嘗試在Windows環境上運行此命令時遇到錯誤，請點擊任務欄中的Docker for Windows圖標，然後在此處登錄Docker，而不是在"Sign in / Create Docker ID"。

### 針對佈署到OpenFaaS的Docker映像設置一個OpenFaaS prefix

OpenFaaS映像會存儲在Docker registry或Docker Hub中，我們可以設置環境變量，以便將用戶名自動添加到你所創建的新function映像的tag上。這將為你節省一些手動設定的動作與時間。

編輯 `~/.bashrc`或`~/.bash_profile`-創建相關檔案（如果不存在）。

現在添加以下內容-根據你申請Docker到的username來修改。

```sh
export OPENFAAS_PREFIX="xxxxxx" # Populate with your Docker Hub username
```

## Setup a single-node cluster

OpenFaas可以支持在不同類型的容器編排平台上運行。對於本系列lab，僅關注使用Kubernetes。

### Install latest `kubectl`

使用以下說明或[官方文檔](https://kubernetes.io/docs/tasks/tools/install-kubectl/)為你的操作系統安裝`kubectl`:

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

如果你的本機電腦上裝有Docker，則可以使用Rancher Labs的`k3d`。它安裝了名為`k3s'的輕量級Kubernetes，並在Docker容器中運行它，這意味著它將在具有Docker的任何電腦上運行的能力。

詳細介紹[Install k3d](https://github.com/rancher/k3d)

讓我們創建一個Kubernetes集群:

1. `k3d cluster create CLUSTER_NAME` 創建一個新的單節點集群 (= 1個容器運行 k3s + 1個容器運行 loadbalancer )
2. `k3d kubeconfig merge CLUSTER_NAME --switch-context` 更新你預設的kubeconfig並將當前上下文切換到新的集群
3. 執行一些命令，例如 `kubectl get pods --all-namespaces` 來測試cluser
4. 如果要刪除群集 `k3d cluster delete CLUSTER_NAME`

### Install OpenFaaS

有三種安裝OpenFaaS的方法，你可以選擇對你和你的團隊合適的方法。在本workshop中，我們將使用官方建議的安裝手法`arkade`。

* `arkade app install` - arkade使用其官方helm chart來安裝OpenFaaS。它還可以簡單地安裝其他常用的元件，例如`cert-manager`和`nginx-ingress`。這是啟動和運行OpenFaaS的最簡單，最快的方法。

* Helm chart - 提供許多合理的預設值，並且易於通過YAML或CLI旗標來進行配置。

* Plain YAML files - 手動地設定相關的設定值。Kustomize之類的工具可以幫助簡化設置工作。

在本workshop中，我們將使用`arkade`來安裝OpenFaas。

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

如果你使用的是己提供LoadBalancers的託管雲Kubernetes服務，請運行以下命令：

```sh
arkade install openfaas --load-balancer
```

> 注意：`--load-balancer`旗標的默認值為`false`，因此通過傳遞該旗標，安裝工具將向你的雲提供商請求一個。

### Expose OpenFaaS Gateway

* 檢查OpenFaaS網關是否準備好

```sh
kubectl rollout status -n openfaas deploy/gateway
```

如果你使用的是本機電腦，VM或任何其他類型的Kubernetes發行版，請運行以下命令：

```sh
kubectl port-forward svc/gateway -n openfaas 8080:8080
```

此命令將打開從Kubernetes群集到本地計算機的網路連線通道，以便你可以訪問OpenFaaS網關。還有其他訪問OpenFaaS的方法，但這超出了本workshop的範圍。

你的網關網址將會是: `http://127.0.0.1:8080`

### Login OpenFaaS

```sh
export OPENFAAS_URL="http://127.0.0.1:8080" # Populate as above

# This command retrieves your password
PASSWORD=$(kubectl get secret -n openfaas basic-auth -o jsonpath="{.data.basic-auth-password}" | base64 --decode; echo)

# This command logs in and saves a file to ~/.openfaas/config.yml
echo -n $PASSWORD | faas-cli login --username admin --password-stdin
```

檢查`faas-cli list`是否順利運行:

```sh
faas-cli list
```

### Permanently save your OpenFaaS URL
 
修改 `~/.bashrc` 或 `~/.bash_profile` - 創建檔案（如果不存在）。

現在添加以下內容-根據上面看到的內容更改URL。

```sh
export OPENFAAS_URL="http://127.0.0.1:8080" # populate as above
```

下一步 >>  [Lab 2](lab2_zh-tw.md)