---
toc: false
layout: post
description: 11장
categories: [kubernetes]
title: GCP Kubenetes
---



---
# 11. GCP Kubenetes

## 11.1. 환경 설치

1-1) [google cloud sdk 설치](https://cloud.google.com/sdk/docs/install?hl=ko#windows)  
1-2) GCP에서 프로젝트 생성 후, Kubernetes Engine에서 클러스터 생성  
1-3) 윈도우 shell에서 gcloud 초기화 및 연동  
```
PS C:\u01\kubetpl> gcloud init # 초기화
Welcome! This command will take you through the configuration of gcloud.

Settings from your current configuration [default] are:
accessibility:
  screen_reader: 'False'
core:
  account: chwhse@gmail.com
  disable_usage_reporting: 'True'

Pick configuration to use:
 [1] Re-initialize this configuration [default] with new settings
 [2] Create a new configuration
Please enter your numeric choice:  1 # 초기화

Your current configuration has been set to: [default]

You can skip diagnostics next time by using the following flag:
  gcloud init --skip-diagnostics

Network diagnostic detects and fixes local network connection issues.
Checking network connection...done.
Reachability Check passed.
Network diagnostic passed (1/1 checks passed).

Choose the account you would like to use to perform operations for this configuration:
 [1] chwhse@gmail.com
 [2] Log in with a new account
Please enter your numeric choice:  1 # 내 계정 선택

You are logged in as: [chwhse@gmail.com].

Pick cloud project to use:
 [1] affable-beaker-173409
 [2] inner-deck-334800
 [3] my-project-1499687426424
 [4] perceptive-map-173311
 [5] pjt-android
 [6] pjt-shop
 [7] pjt-shop-1502416936516
 [8] Create a new project
Please enter numeric choice or text value (must exactly match list item):  2 # k8s 용 pjt 선택

Your current project has been set to: [inner-deck-334800].

Do you want to configure a default Compute Region and Zone? (Y/n)?  Y

Which Google Compute Engine zone would you like to use as project default?
If you do not specify a zone via a command line flag while working with Compute Engine
resources, the default is assumed.
 [34] asia-northeast1-a
Did not print [39] options.
Too many options [89]. Enter "list" at prompt to print choices fully.
Please enter numeric choice or text value (must exactly match list item):  34 # asia-northeast1-a 존 선택

Your project default Compute Engine zone has been set to [asia-northeast1-a].
You can change it by running [gcloud config set compute/zone NAME].

Your project default Compute Engine region has been set to [asia-northeast1].
You can change it by running [gcloud config set compute/region NAME].

Error creating a default .boto configuration file. Please run [gsutil config -n] if you would like to create this file.
Your Google Cloud SDK is configured and ready to use!

* Commands that require authentication will use chwhse@gmail.com by default
* Commands will reference project `inner-deck-334800` by default
* Compute Engine commands will use region `asia-northeast1` by default
* Compute Engine commands will use zone `asia-northeast1-a` by default

Run `gcloud help config` to learn how to change individual settings

This gcloud configuration is called [default]. You can create additional configurations if you work with multiple accounts and/or projects.
Run `gcloud topic configurations` to learn more.

Some things to try next:

* Run `gcloud --help` to see the Cloud Platform services you can interact with. And run `gcloud help COMMAND` to get help on any gcloud command.
* Run `gcloud topic --help` to learn about advanced features of the SDK like arg files and output formatting
```



<br>
<br>

---
# 11.2. 

```

```



<br>
<br>

---
# 11.3. 
