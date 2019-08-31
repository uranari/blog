---
title: "Amazon CloudWatch Logs Insights で SIEM ぽいことをやってみる"
date: 2019-01-03T12:28:36+09:00
draft: false
type: post
url: /tech/security/aws-siem-for-insights/
categories:
 - security
 - tech
 - aws
---

AWS re:Ivent 2018 では AWS Security Hub や KMS Custom Key Store などの新しいセキュリティ関連のマネージドサービスが発表されましたが、個人的に一番熱かったのが Amazon CloudWatch Logs Insights だったので紹介しておきます。  

[Amazon CloudWatch Logs Insights](https://aws.amazon.com/jp/blogs/news/new-amazon-cloudwatch-logs-insights-fast-interactive-log-analytics/)
は従来の CloudWatch Logs のログをインタラクティブに検索できるものですが、概要だけ聞いたときに「それ SIEM じゃん」と思ったので、実際に SIEM として使えるか試してみました。

ここでは例として CloudTrail のログを分析対象とします。  
あらかじめ CloudTrail のログを CloudWatch へ転送する設定を別途実施しておいてください。  

今回は CloudTrail のログから S3 の特定のバケットに オブジェクトが作成された際にバケット名やオブジェクト名を絞る方法を説明します。  

まず始めに CloudTrail のログを分析するときはログを eventSouce のフィールド毎に分けることをおすすめします。  

話が脱線しますが、こういった SIEM のルールを作る際は上記の「S3のバケットにオブジェクトが作成された際のバケット名やオブジェクト名を知りたい」のようにルールの目的やアラートを発出する際の条件とそれが検知された際の調査項目などをあらかじめ整理しておくことをおすすめします。  
典型的なダメダメなパターンは、とりあえず GuardDuty を ON にしたけどアラート飛んできてもこれがだめなものか分からないし、何を調査したらいいか分からないといったパターンです。  
それアラート設定してる意味ないじゃんとなります。  

話を戻して CloudWatch Logs Insight でのログの絞り込み方法です。  
まず eventSource ごとに分けることをおすすめしましたので eventSource だけを表示させます。  


<a href="https://dtiemu0c7gxsz.cloudfront.net/image/aws-siem-for-insight/eventSource.jpg" data-rel="lightbox-gallery-0QZwyEn5" data-rl\_title="" data-rl\_caption="" title=""><img src="https://dtiemu0c7gxsz.cloudfront.net/image/aws-siem-for-insight/eventSource.jpg" alt="" width="1000" height="550" sizes="(max-width: 1270px) 100vw, 1270px" /></a>


ここから S3 のイベントに絞ってさらに特定のバケットのイベントに絞ると以下のようになります。  

<a href="https://dtiemu0c7gxsz.cloudfront.net/image/aws-siem-for-insight/putobject.jpg" data-rel="lightbox-gallery-0QZwyEn5" data-rl\_title="" data-rl\_caption="" title=""><img src="https://dtiemu0c7gxsz.cloudfront.net/image/aws-siem-for-insight/putobject.jpg" alt="" width="1000" height="550" sizes="(max-width: 1270px) 100vw, 1270px" /></a>  

あとはこれを普通の CloudWatch Logs と同様にダッシュボードに追加してアラームの設定をすれば実質 SIEM のできあがりです。