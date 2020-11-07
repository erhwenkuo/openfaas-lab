# openfaas-workshop

[English version](lab-introduction.md)

這是一個自主學習的workshop (修改自原始版本 [openfaas-workshop](https://github.com/openfaas/workshop)) 主要用於學習如何使用OpenFaaS來構建，部署和運行serverless functions。

這個workshop將重點關注在kubernes上運行OpenFaaS，並提供更多不同語言（包括Python，Java和Golang）的function開發部署範例。

![](https://github.com/openfaas/media/raw/master/OpenFaaS_Magnet_3_1_png.png)

在workshop中，你首先將OpenFaaS部署到本機電腦或使用Docker for Mac/Windows的遠程集群。然後，你將使用OpenFaaS UI，CLI和Function Store來啟動function的開發。

在使用Python構建，部署並且調用自己的Serverless Function之後，你將繼續學習以下主題：

* 用pip管理依賴項
* 通過secure secrets處理API令牌
* 使用Prometheus來監控己部署的function
* 異步調用function並將function鏈接在一起以創建應用程序

整個workshop最後精彩點在通過讓你創建自己的GitHub機器人(該機器人可以自動響應Githhub project的issue)。通過IFTTT.com連接到線上事件流，代表你可以應用相同的手法來構建不同類型的Bot，自動響應程序以及與社交媒體和IoT設備進行系統集成。

最後，這個labs還涵蓋了一些更進階的主題，並提供了進一步學習的建議。

## Requirements:

我們將逐步介紹如何在[Lab 1](./lab1_zh-tw.md)中安裝這些要求。請在參加講師指導的workshop之前完成[Lab 1](./lab1_zh-tw.md)。至少你應該安裝Docker並預先拉下[OpenFaaS映像檔](./lab1_zh-tw.md#Pre-pull-the-system-images)到本機上。

* 函數將用Python編寫（未來將包含Java與Golang的版本），因此你應具備基本寫程式與一些腳本編寫的經驗和能力
* 安裝推薦的代碼編輯器/ IDE [VSCode](https://code.visualstudio.com/download) / IDE [Pycharm](https://www.jetbrains.com/pycharm/)
* 如果使用Windows作業系統，請安裝[Git Bash](https://git-scm.com/downloads)
* 建議使用的操作系統：MacOS，Windows 10 Pro / Enterprise，Ubuntu Linux

Docker:

* Docker CE for [Mac](https://store.docker.com/editions/community/docker-ce-desktop-mac)/[Windows](https://store.docker.com/editions/community/docker-ce-desktop-windows) 
* Docker CE for Linux

> 注意：如果你有一台不兼容的PC，則萬不得已時，你可以在[play-with-docker](https://labs.play-with-docker.com/)上練習這個workshop。

## [Lab 1 - Prepare for OpenFaaS](./lab1_zh-tw.md)
* 安裝相關必需的軟體或系統
* 設置單節點Kubernetes集群
* 申請Docker Hub帳號
* OpenFaaS CLI安裝與使用
* 佈署OpenFaaS

## [Lab 2 - Test things out](./lab2_zh-tw.md)

* 使用OpenFaas UI
* 使用Function Store來佈署function
* 學習如何使用faas-cli
* 找到監控function的Prometheus指標

## [Lab 3 - Introduction to Functions](./lab3_zh-tw.md)

* 經由function範本或手動地產生新function
* 構建一個`astronaut-finder`的function
 * 用`pip`添加依賴
 * 故障排除：查找容器的日誌
* 故障查找：使用`write_debug`輸出詳細信息
* 使用自定義和第三方語言function開發模板
* 經由OpenFaas的Template Store來發現社群模板

## [Lab 4 - Go deeper with functions](./lab4_zh-tw.md)

* [通過環境變量注入配置](lab4_zh-tw.md#inject-configuration-through-environmental-variables)
  * 使用yaml檔來進行function部署
  * 動態使用HTTP context - querystring / headers 等等
* 安全增強: 唯讀的檔案系統
* [利用日誌記錄](lab4_zh-tw.md#making-use-of-logging)
* [創建工作流](lab4_zh-tw.md#create-workflows)
  * 在客戶端進行function的串連
  * 從另一個調用一個函數

## [Lab 5 - Create a GitHub bot](./lab5_zh-tw.md)

> 構建 `issue-bot` - GitHub問題的自動回複機器人
* 取得GitHub帳號
* 用ngrok建立網路通道(tunneling)
* 創建一個Webhook接收器 `issue-bot`
* 從GitHub接收Webhooks訊號
* 部署情緒分析function
* 通過GitHub API來給予情緒分類標籤
* 完成這個function

## [Lab 6 - HTML for your functions](./lab6_zh-tw.md)

* 從function生成並返回基本HTML網頁
* 從磁盤讀取並返回靜態HTML文件
* 與其他function協作

## [Lab 7 - Asynchronous Functions](./lab7_zh-tw.md)

* 同步與異步調用function
* 查看queue-worker的日誌
* 將`X-Callback-Url`與requestbin和ngrok一起使用

## [Lab 8 - Advanced Feature - Timeouts](./lab8_zh-tw.md)

* 使用`read_timeout`來調整超時設定
* 調適長時間運行的function

## [Lab 9 - Advanced Feature - Auto-scaling](./lab9_zh-tw.md)

* 了解auto-scaling的運作
 * 關於最小和最大副本的一些內部設計
 * 探查本地的Prometheus
 * 執行Prometheus query
 * 通過curl來觸發function
 * 觀察auto-scaling如何啟動

## [Lab 10 - Advanced Feature - Secrets](./lab10_zh-tw.md)

* 調整`issue-bot`以使用secret來進行保護
 * 產生secret
 * function如何查找預設定的secret

## [Lab 11 - Advanced feature - Trust with HMAC](./lab11_zh-tw.md)

* 使用HMAC將安全應用於function


你可以從第一個[Lab 1](lab1_zh-tw.md)開始ＯpenFaas的學習之旅。

## Tear down / Clear up

你可以在[這裡](https://docs.openfaas.com/deployment/troubleshooting/#uninstall-openfaas)找到如何停止和刪除OpenFaaS相關資訊與手法。

## Acknowledgements

這些實驗室的內容源自[openfaas/workshop](https://github.com/openfaas/workshop)，並由許多貢獻者貢獻。

感謝 @alexellis, @iyovcheva, @BurtonR, @johnmccabe, @laurentgrangeau, @stefanprodan, @kenfdev, @templum & @rgee0 提供了這些labs。