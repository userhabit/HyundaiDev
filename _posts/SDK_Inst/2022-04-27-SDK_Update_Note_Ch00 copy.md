---
title: "SDK Update Note Home"
date: 2022-04-27 00:16:01
layout: article
tag: 
- HyundaiDev
categories: 
- Development
- InstallGuide
- SDK
published: true
#permalink: /HyundaiSDKInst/
sidebar:
  nav: SDK_Sidebar
---

# SDK Update Note History


## DB Server 설치
 - mongodb-org-5.0.5-1.el8.x86_64.rpm 설치
 - openjdk 설치
  
## Load Balancer Server 설치
 - ngnix 설치
 - nginx 관련 보안 설정 : headers-more-nginx-module 빌드
  
## mongodb replicaSet 설정

## Userhabit Solution(polaris) 설치


### mongodb-org-5.0.5-1.el8.x86_64.rpm 설치

아래처럼 실행하면 된다. `rpm -Uvh` 를 `dnf install` 대신 사용해도 된다.
