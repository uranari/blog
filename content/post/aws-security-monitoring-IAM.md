---
title: "AWS Security Monitoring -IAM-"
author: user
type: post
date: 2019-05-01T11:20:49+09:00
url: /tech/aws/AWS-Security-Monitoring-IAM/
amazonS3_cache:
  - 'a:1:{s:33:"//hackers-champloo.org/about.html";a:1:{s:9:"timestamp";i:1531926481;}}'
page_type:
  - content_only_wide
update_level:
  - high
categories:
  - aws
  - security
---

AWS Security Monitoring, that was introduced some checkpoint on some web sites.  
But i hava not seen so much "how check", i will introduce example on use Cloudwatch Logs Insight and CloudTrail to check IAM rule.

## Narrow down IAM event

First step, narrow down only IAM event.  
sample query  
```
fields eventSource, @timestamp
| filter eventSource = "iam.amazonaws.com"
```

<a href="https://dtiemu0c7gxsz.cloudfront.net/image/AWS-Security-Monitoring-IAM/eventSource.png" data-rel="lightbox-gallery-0QZwyEn5" data-rl\_title="" data-rl\_caption="" title=""><img src="https://dtiemu0c7gxsz.cloudfront.net/image/AWS-Security-Monitoring-IAM/eventSource.png" alt="" width="1000" height="550" sizes="(max-width: 1270px) 100vw, 1270px" /></a>  

## Create New User  
example case, if you were stolen aws credential, attacker may create new user in use that credential.  
I shown below sample query to search `CreateUser` event.  
This query is you can know when created new user, where from to accessed, how accessed, who created, what the created new user name, whether it is enabled MFA.  
This sample result is `example` user create `test` user and MFA is unenabled.  
```
filter eventSource = "iam.amazonaws.com" and eventName = "CreateUser"
| fields eventSource, eventName, responseElements.user.createDate as createDate,
 sourceIPAddress, userAgent, userIdentity.userName as requestUser,
  requestParameters.userName as createdUser,
  userIdentity.sessionContext.attributes.mfaAuthenticated as mfaAuthenticated
```
<a href="https://dtiemu0c7gxsz.cloudfront.net/image/AWS-Security-Monitoring-IAM/createuser.jpg" data-rel="lightbox-gallery-0QZwyEn5" data-rl\_title="" data-rl\_caption="" title=""><img src="https://dtiemu0c7gxsz.cloudfront.net/image/AWS-Security-Monitoring-IAM/createuser.jpg" alt="" width="1000" height="400" sizes="(max-width: 1270px) 100vw, 1270px" /></a>  

## DeleteUser  
If some user was deleted.  
you can find this event.  
```
filter eventSource = "iam.amazonaws.com" and eventName = "DeleteUser"
| fields eventSource, eventName, eventTime, sourceIPAddress, userAgent,
requestParameters.userName as DeletedUser, userIdentity.userName as requestUser,
userIdentity.sessionContext.attributes.mfaAuthenticated as mfaAuthenticated
```
<a href="https://dtiemu0c7gxsz.cloudfront.net/image/AWS-Security-Monitoring-IAM/DeleteUser.jpg" data-rel="lightbox-gallery-0QZwyEn5" data-rl\_title="" data-rl\_caption="" title=""><img src="https://dtiemu0c7gxsz.cloudfront.net/image/AWS-Security-Monitoring-IAM/DeleteUser.jpg" alt="" width="1000" height="400" sizes="(max-width: 1270px) 100vw, 1270px" /></a> 
## AddUserToGroup  
AddUserToGroup, this event is some user added some group.  
This events in the same say monitoring, check to the have not undesirable permission for user and who requested, where from to accessed, added user, what added group.  
sample query  
```
filter eventSource = "iam.amazonaws.com" and eventName = "AddUserToGroup"
| fields eventSource, eventName, eventTime, sourceIPAddress, userAgent,
userIdentity.userName as requestUser, requestParameters.userName as addedUser,
requestParameters.groupName as groupName,
userIdentity.sessionContext.attributes.mfaAuthenticated as mfaAuthenticated
```
<a href="https://dtiemu0c7gxsz.cloudfront.net/image/AWS-Security-Monitoring-IAM/addUsertoGroup.jpg" data-rel="lightbox-gallery-0QZwyEn5" data-rl\_title="" data-rl\_caption="" title=""><img src="https://dtiemu0c7gxsz.cloudfront.net/image/AWS-Security-Monitoring-IAM/addUsertoGroup.jpg" alt="" width="1200" height="400" sizes="(max-width: 1270px) 100vw, 1270px" /></a>  

## RemoveUserFromGroup
This event and AddUserToGroup event different is add user to some group or remove from some group.  
Replace it with the opposite meaning.  
```
filter eventSource = "iam.amazonaws.com" and eventName = "RemoveUserFromGroup"
| fields eventSource, eventName, eventTime, sourceIPAddress, userAgent,
requestParameters.groupName as removedGroup, requestParameters.userName as user,
userIdentity.userName as requestUser
```
<a href="https://dtiemu0c7gxsz.cloudfront.net/image/AWS-Security-Monitoring-IAM/RemoveUserFromGroup.jpg
" data-rel="lightbox-gallery-0QZwyEn5" data-rl\_title="" data-rl\_caption="" title=""><img src="https://dtiemu0c7gxsz.cloudfront.net/image/AWS-Security-Monitoring-IAM/RemoveUserFromGroup.jpg" alt="" width="1000" height="550" sizes="(max-width: 1270px) 100vw, 1270px" /></a> 

## PutUserPermissionsBoundary
PutUserPermissionsBoundary is user added new policy.  
monitoring this event mean is same say AdduserToGroup, you check user whether should not have suspicious permission.  
permissionsBoundary field is user added permission, this sample result is test user was added in Administrator policy.
```
filter eventSource = "iam.amazonaws.com" and eventName = "PutUserPermissionsBoundary"
| fields eventSource, eventName, eventTime, sourceIPAddress, userAgent,
requestParameters.permissionsBoundary as permissionsBoundary, requestParameters.userName as addedUser,
userIdentity.userName as requestUser
```

<a href="https://dtiemu0c7gxsz.cloudfront.net/image/AWS-Security-Monitoring-IAM/PutUserPermissionsBoundary.jpg" data-rel="lightbox-gallery-0QZwyEn5" data-rl\_title="" data-rl\_caption="" title=""><img src="https://dtiemu0c7gxsz.cloudfront.net/image/AWS-Security-Monitoring-IAM/PutUserPermissionsBoundary.jpg" alt="" width="1000" height="550" sizes="(max-width: 1270px) 100vw, 1270px" /></a>  

## DeleteUserPermissionsBoundary
DeleteUserPermissionsBoundary is delete permissons boundary from user.  
```
filter eventSource = "iam.amazonaws.com" and eventName = "DeleteUserPermissionsBoundary"
| fields eventSource, eventName, eventTime, sourceIPAddress, userAgent,
requestParameters.userName as user, userIdentity.userName as requestUser
```
<a href="https://dtiemu0c7gxsz.cloudfront.net/image/AWS-Security-Monitoring-IAM/DeleteUserPermissionsBoundary.jpg" data-rel="lightbox-gallery-0QZwyEn5" data-rl\_title="" data-rl\_caption="" title=""><img src="https://dtiemu0c7gxsz.cloudfront.net/image/AWS-Security-Monitoring-IAM/DeleteUserPermissionsBoundary.jpg" alt="" width="1000" height="550" sizes="(max-width: 1270px) 100vw, 1270px" /></a> 

## PutUserPolicy
User have some permissions in inline policy.  
If user added inline policy detected this event.  
what can user action and where resource able that there were written in policy Documents.
```
filter eventSource = "iam.amazonaws.com" and eventName = "PutUserPolicy"
| fields eventSource, eventName, eventTime, sourceIPAddress, userAgent,
requestParameters.userName as user, requestParameters.policyName as policyName,
requestParameters.policyDocument as policyDocument, userIdentity.userName as requestUser
```
<a href="https://dtiemu0c7gxsz.cloudfront.net/image/AWS-Security-Monitoring-IAM/PutUserPolicy.jpg" data-rel="lightbox-gallery-0QZwyEn5" data-rl\_title="" data-rl\_caption="" title=""><img src="https://dtiemu0c7gxsz.cloudfront.net/image/AWS-Security-Monitoring-IAM/PutUserPolicy.jpg" alt="" width="1000" height="550" sizes="(max-width: 1270px) 100vw, 1270px" /></a> 

## DetachUserPolicy
Detached policy from user.  
that case query.  
```
filter eventSource = "iam.amazonaws.com" and eventName = "DetachUserPolicy"
| fields eventSource, eventName, eventTime, sourceIPAddress, userAgent,
requestParameters.policyArn as DetachedPolicy, requestParameters.userName as DetahedUser,
userIdentity.userName as requestUser
```
<a href="https://dtiemu0c7gxsz.cloudfront.net/image/AWS-Security-Monitoring-IAM/DetachUserPolicy.jpg" data-rel="lightbox-gallery-0QZwyEn5" data-rl\_title="" data-rl\_caption="" title=""><img src="https://dtiemu0c7gxsz.cloudfront.net/image/AWS-Security-Monitoring-IAM/DetachUserPolicy.jpg" alt="" width="1000" height="550" sizes="(max-width: 1270px) 100vw, 1270px" /></a> 

## EnableMFADevicex
## CreateVirtualMFADevice
## CreateAccessKey
## UploadSSHPublicKey
## EnableMFADevice



## AssumeRole
AssuleRole is not exactly IAM, but it is convenient monitoring together IAM.  
this result can know who switched to what role.
```
filter eventSource = "signin.amazonaws.com" and eventName = "SwitchRole"
| fields eventSource, eventName, eventTime, sourceIPAddress, userAgent, 
responseElements.SwitchRole as result, additionalEventData.SwitchTo as SwitchTo, userIdentity.arn as SwitchFrom
```

<a href="https://dtiemu0c7gxsz.cloudfront.net/image/AWS-Security-Monitoring-IAM/SwitchRole.jpg" data-rel="lightbox-gallery-0QZwyEn5" data-rl\_title="" data-rl\_caption="" title=""><img src="https://dtiemu0c7gxsz.cloudfront.net/image/AWS-Security-Monitoring-IAM/SwitchRole.jpg" alt="" width="1000" height="550" sizes="(max-width: 1270px) 100vw, 1270px" /></a> 