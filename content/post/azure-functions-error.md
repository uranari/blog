---
title: "AzureでFunctionsをデプロイした時に出るreceiverConnectionStringというエラーについて"
author: user
type: post
url: /tech/azure/azure-functions-error
date: 2020-04-16T22:08:41+09:00
draft: false
page_type:
  - content_only_wide
categories:
  - azure
  - azure-functions
---

### Azure Functions の receiverConnectionString エラーについて

Azure で Azure Functions をデプロイした際に下記の画像のようなエラーが出ることがあります。  

{{< figure src="/images/azure-functions-error/azure_deploy.JPG" class="center" width="640" height="1280" >}}


上記のエラーで結構はまってしまったので解決方法を記載しておきます。

解決方法なんですが、自分の場合は EventHub をトリガーにしているので `local.settins.json` に記載されている EventHub の接続文字列を

{{< figure src="/images/azure-functions-error/local_serrings.JPG" class="center" width="640" height="1280" >}}

下記のアプリケーション設定に設定します。

{{< figure src="/images/azure-functions-error/app_config.JPG" class="center" width="640" height="1280" >}}

以上！！