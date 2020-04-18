---
title: "CloudTrail Monitoring -IAM-"
author: user
type: post
date: 2019-10-26T11:20:49+09:00
url: /tech/aws/CloudTrail-Monitoring-IAM/
page_type:
  - content_only_wide
update_level:
  - high
categories:
  - aws
  - security
---

### CloudTrail 見てますか？ 

AWS を利用するうえでのセキュリティ対策として CloudTrail を有効にすることが色んな記事で紹介されていますが、皆さんちゃんと見ていますか？ 

ふだん自分がセキュリティ対策として CloudTrail のログでどういったリアルタイム監視をしているかをリソース別に紹介したいと思います。 
すべてのリソースのログを一つの記事で紹介すると量が多くなってしまうため、今回はログインと IAM、AssumeRole 周りのログの監視について紹介したいと思います。  

### 紹介するイベントについてとCloudTrailのログについて  
本記事と今後も CloudTrail のログ監視について紹介する記事を書くことがあった場合、基本的に `Event name` の値別で紹介していきます。
また、CloudTrail のログでは、そのイベントが失敗したか成功したかについてはログに `errorcode` や `errormessage` のフィールドに値が入っているかどうかで判断できます。  
これらのフィールドに値が入っている場合はそのイベントは失敗しており、その理由がそれらのフィールドに記録されます。  

{{< figure src="/images/CloudTrail-Monitoring-IAM/trail.png" class="center" width="1280" height="2560" caption="cloudtrailの画面 View event を開くと生ログが見れる" >}}

### ConsoleLogin  
名前の通り、AWS の Web コンソール にログインを試みた際に記録されるイベント。  
あくまで `Webコンソール` にログインを試みたイベントなので、AWS CLI などを利用した場合はこのイベントは記録されません。  
もし急に海外からのログインとかがあったり、本人が身に覚えのない場合は認証情報が漏洩している可能性があるかもしれません。  

### CreateUser  
新しい IAM User が作成された際に記録されるイベント。  
勝手に変なユーザーが作成されてないか確認することができます。  

### CreatePolicy  
IAM で新しい Policy を作成した際に記録されるイベント。  
付与される権限も CloudTrail のログに記録されているので、余計な権限が付与されてないか確認することができます。

### CreatePolicyVersion  
既存の Policy を編集する際に記録されるイベントです。  
こちらもログにインラインポリシーが記録されているので余計な権限が付与されてないか確認することができます。

### CreateRole  
新しい Role が作成されたときに記録されるイベント。  
AWS のサービスの中にはサービスに伴って自動的に Role が作成されるものもある(WAF のログを Kinesis に流すときとか)ので色々とややこしかったりするんですが、こちらも同様に変な Role が作成されてないか監視できます。  

### AttachRolePolicy  
Role に Policy を付与する際に記録されるイベント。  
Role に対して適切な Policy が付与されているか確認できます。  

### PutRolePolicy  
Role に 直接インラインポリシーを書き込む際に記録されるイベント。
こちらも適切な Policy が付与されているか確認できます。  

### AssumeRole  
AssumeRole が実行された際に記録されるイベントです。  
ユーザーやサービスが適切な Role に AssumeRole しているか確認できます。  
割と色んな AWS サービスや連携しているサードパーティー製のツールが実行しているので、ノイズが多いという人は userIdentity,type で絞り込むといいかもしれません。  

### UpdateAssumeRolePolicy  
AssumeRole で Role を引き受けることが可能な ARN を編集した際に記録されるイベントです。  
適切な ARN が引き受け先になっているか確認することができます。  

### まとめ  
CloudTrail でひとまず監視していればいいログイン、IAM、AssumeRole 関係のイベントは以上かなーと思います。  
組織によっては他にも Detach や Delete のイベントも監視対象にしてもいいかなと思いますけど、上記は最低限見ておいたほうがいいと思います。  
もし、AWS の監視をセキュリティの部署でやっているけど実際のオペレーションは各現場で勝手にやっているから監視していてもそれが不審なものかどうかなんて分からないよなんて人がいましたら、監視を始める前にワークフローを整備することをおすすめします。