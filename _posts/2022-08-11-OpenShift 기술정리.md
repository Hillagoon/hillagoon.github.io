---
title: "Container/OpenShift 기술정리"
last_modified_at: 2022-08-11T17:30:00-00:00
categories:
  - OpenShift
tags:
  - OpenShift/Kubernetes
  - Virtualization
  - OSS
  - RHLS_DO280
---

# 개요
이번에는 컨테이너 오케스트레이션의 동작원리와 기술에 대해 정리를 해보고자 한다.  

# OpenShift
## RHOCP - 소개
RedHat OpenShift Container Platform, 줄여서 RHOCP는 Kubernetes 오픈소스 프로젝트를 기반으로 제작된 컨테이너 오케스트레이션이다.  

**Red Hat OpenShift Container Platform**  
베어 메탈 서버를 포함하여 퍼블릭/온프레미스 데이터센터에서 컨테이너 기반 App을 구축/배포/관리 기능을 
엔터프라이즈급 Kubernetes 환경으로서 제공한다.

**Red Hat OpenShift 전용**  
AWS/GCP/Azure/IBM Cloud와 같은 퍼블릭 클라우드에서 관리형 OpenShift 환경을 제공한다. RHOCP에서 제공하는 모든 기능을 이용할 수 있으며
RedHat에서 클러스터를 관리한다.

**Red Hat OpenShift Online**  
클라우드 환경에서 App을 개발/빌드/배포/호스팅 하는 솔루션을 제공하는 호스팅된 공용 컨테이너 오케스트레이션 플랫폼을 제공한다.
RedHat에서 클러스터를 관리한다.


**Red Hat OpenShift Kubernetes 엔진**  
RHOCP 에 있는 하위 기능 집합(RHEL CoreOS/CRI-O/Kubernetes Platform/Core Cluster Service)를 제공한다.  

**Red Hat Code Ready Containers**  
로컬 개발/시험을 위해 랩탑에서 실행할 수 있는 테스트용 Minimal 환경을 제공한다.  

## Architecture
![RHOCP Architecture](https://raw.githubusercontent.com/Hillagoon/hillagoon.github.io/8c6eb4d74fcf3dc6652e18f3dbb66b0961cf4356/assets/post_image/arch-features-openshift.svg)  

**RHOCP에서 통합하는 요소**  
- RHEL CoreOS
- CRI-O, OCI(Open Container Initiative) 컨테이너 런타임 엔진으로 컨테이너를 실행시키기 위해 고안된 매우 가벼운 엔진이다.
- Kubernetes, 오픈소스 컨테이너 오케스트레이션 플랫폼
- GUI환경, 셀프 서비스 웹 콘솔.
- 내부 컨테이너 이미지 레지스트리 및 모니터링 프레임워크와 같은 사전 설치된 App 서비스
- 프로그래밍 언어 런타임, 데이터베이스, 기타 소프트웨어 패키지의 공인 컨테이너 이미지

##OpenShift 기능
![architecture-openshift-kubernetes-engine](https://raw.githubusercontent.com/Hillagoon/hillagoon.github.io/6e4c843dd074d7abb4b7004c1dd0e0b4701c41e1/assets/post_image/architecture-openshift-kubernetes-engine.svg)  
OpenShift에서는 App을 자동화,확장 및 유지 관리하는 기능을 제공한다.
BYO(Build-Your-Own) Kubernetes 설정에서 추가하고 구성해야 하는 추가 구성 요소가 필요하다.

**고가용성**  
- Kubernetes는 내부 구성 요소 및 사용자 App 모두의 고가용성을 염두에 두고 설계됬다.
- etcd 클러스터에서 OpenShift 클러스터 및 해당 App의 상태를 저장한다.
- etcd에 저장된 배포 구성 등의 리소스에서는 컨테이너를 종료하기 위해 컨테이너를 자동으로 다시 시작하는 기능을 제공한다.
- App뿐만 아니라 웹 콘솔과 내부 이미지 레지스트리와 같이 클러스터를 구성하는 컨테이너화된 서비스에도 적용된다.

**경량 운영체제**  
- 민첩성, 이식성, 보안에 중점을 둔 Red Hat의 경량 운영 체제인 Red Hat Enterprise Linux CoreOS에서 실행된다.

**부하분산**  
클러스터에서 3가지 유형의 로드 밸런서를 제공한다.  
- 외부 로드 밸런서, OpenShift API에 대한 액세스를 관리한다.
- HAProxy 로드 밸런서, 외부에서 내부 App에 액세스하는데 사용되는 HAProxy 기반 로드 밸런서이다.
- 내부 로드 밸런서, 내부에서 App과 서비스에 액세스하는데 Netfilter 규칙을 사용하는 내부 로드 밸런서이다.

**확장 자동화**  
OpenShift 클러스터는 새 컨테이너를 자동 시작/종료하여 App 트래픽에 맞게 자동 조절 할 수 있다.  

**로깅 및 모니터링**  
RHOCP에는 클러스터에 관한 지표를 Prometheus를 기반으로 모니터링하는 기능이 포함되어 있다.  

**서비스 검색**  
내부 DNS 서비스를 통해 컨테이너를 이용할 수 있도록 구성되어 있다.  

**스토리지**  
Kubernetes에서는 블록/파일 스토리지를 사용할 수 있다.  

**App 관리**  
RHOCP를 사용하면 개발자가 APP 개발 및 배포를 자동화 할 수 있다.
OpenShift S2I(Source-to-Image) 기능을 사용하여 소스 코드를 기반으로 컨테이너를 자동 빌드 및 배포한다.
Python,Ruby,Java,Nodejs 와 같은 런타임 언어와 DB/메시징 서버를 지원한다.
App 및 운영자 설치를 통한 카탈로그 확장이 가능한다.

**클러스터 확장성**  
- 확장 API 및 사용자 지정 리소스 정의와 같은 Kubernetes의 표준 확장 메커니즘을 사용하여 업스트림 Kubernetes에서 제공하지않는 기능이 추가할 수 있다.
- OLM(Operator Lifecycle Manager)을 통해 운영자로 패키징된 App 및 인프라 구성 요소 검색, 설치, 업데이트 및 관리를 쉽게할 수 있다.
- operatorhub.io, 공용 리포지토리 및 마켓플레이스로서 OpenShift/OLM을 포함한다.
- Marketplace, OpenShift클러스터에 배포할 수 있도록 Kubernetes 운영자로 패키징된 소프트웨어에 액세스 할 수 있는 플랫폼이다.


##
