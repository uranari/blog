---
title: "Windowsのおすすめ監査設定"
author: user
type: post
url: /tech/windows/audit-config/
date: 2019-12-22T22:08:41+09:00
draft: true
page_type:
  - content_only_wide
categories:
  - windows
  - security
---

### Windows でおすすめの監査設定

Windows の Event Log をセキュリティ監視でリアルタイム監視していると標準の監査設定では意外と必要なログを取ってくれていなかったりします。

今回は Windows でセキュリティ監視をする上で有効にしておきたい監査項目を紹介します。

#### グループポリシーエディタ

監査設定はグループポリシーエディタを使って設定します。  
グループポリシーエディタは Windows の検索窓に `gpedit` と入力すれば立ち上がります。  

{{< figure src="/images/windows-audit-config/gpedit.PNG" class="center" width="1280" height="2560" caption="グループポリシーエディタ" >}}