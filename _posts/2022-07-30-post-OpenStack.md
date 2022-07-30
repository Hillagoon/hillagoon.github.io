---
title: "OpenStack"
last_modified_at: 2022-07-30T16:40:00-00:00
categories:
  - Linux
tags:
  - Linux
  - OpenStack
  - Cloud
---

# 오픈스택 소개
# 1. 서비스
![OpenStack Platform](https://rol.redhat.com/rol/static/static_file_cache/cl110-16.1/intro/CL110-Service-Overview.png "오픈스택 서비스")  
OpenStack은 수 많은 서비스들이 연동하여 가상화 환경을 제공한다. 

핵심 기능을 정리하자면  
- 가상화
- 스토리지
- 네트워크
- 인증
위와 같이 4가지 기능들이 서로 얽히고 얽혀서  
클라우드 환경을 제공한다.  

OpenStack의 각각의 컴포넌트는 이후 이어지는 내용을 참고.  

## 1.1. Cinder
블록 스토리지 서비스를 담당한다.  
쉽게 정의하면 하드디스크 제공을 한다고 생각하면 된다.  

AWS의 EBS (Elastic Block Storage)에 해당한다.  

## 1.2. Glance
이미지 서비스를 담당한다.  
이후 설명할 Nova에서 새로운 인스턴스를 실행하면,  
Glance를 통해 OS 이미지를 제공받는다.  

Glance에서는 이미지 버전 관리, 제공한다.  

AWS의 EC2 인스턴스 생성하는 단계에서 이미지선택하는 화면이 나오는데  
이는 Glance에 해당하는 서비스가 연동되어 있다.  

## 1.3. Heat
오케스트레이션 서비스를 담당한다.  
오케스트레이션이란 확장/축소를 조절한다는 의미로서 사용된다.  
OpenStack 뿐만아니라 다른 플랫폼(AWS,GCP,Kubernetes)에서도  
리소스 사용량에 따른 스케일인/아웃을 조정하는데 사용된다.  

OpenStack 에서는 인프라 전반에 걸친 그룹 배포가 가능하다.
Heat는 yaml코드로 관리된다.

AWS의 AutoScale에 해당한다.  

## 1.4. Horizon
웹 기반의 GUI 인터페이스를 담당한다.  

AWS의 EC2 Management Console에 해당한다.  

## 1.5. Ironic
베어 메탈 프로비저닝 서비스를 담당한다.  

베어 메탈이란 물리 서버를 의미한다.  
Ironic을 통해 하드웨어를 컨트롤 할 수 있다.  

AWS의 EC2 Bare Metal 에 해당한다.  

## 1.6. Keystone
ID서비스를 담당한다.  

모든 OpenStack 서비스에 인증 및 권한 부여/확인을 하며,  
사용자, 도메인, 역할, 프로젝트를 관리하는 역할을 한다.  

LDAP 연동을 통해 조직의 IDM을 통합할 수 있다.  

AWS의 iam,Organizations 에 해당한다.  

## 1.7. Neutron
네트워크 서비스를 담당한다.  
네트워크, 서브넷, 유동IP를 만들 수 있으며  
이는 곳 OpenStack에서 SDN 역할을 한다.  

OpenvSwitch, OVN이 함께 사용된다.  

AWS의 VPC 에 해당한다.  

## 1.8. Nova
컴퓨팅 서비스를 담당한다.  
Nova를 통해 가상환경을 제공 받을수 있으며,  
하이퍼바이저에 libvirtd, qemu, kvm을 사용한다.  

NoVNC와 연동하여 웹을 통한 인스턴스 연결이 가능하다.  

AWS의 EC2 에 해당한다.  

## 1.8. Swift
오브젝트 스토리지를 제공한다.  
데이터 백업에 유용하다.  

AWS의 S3에 해당한다.  

## 1.9. Ceph
분산 데이터 오브젝트 저장소 역할을 한다.  
Ceph 클러스터링을 통해 데이터 손실을 최소화 하고 용량 확장이 쉽다.  

AWS의 S3에 해당한다.  

## 1.10. Manila
공유 파일 시스템 서비스로서 파일 공유 서버 역할을 한다.  
쉽게 말하면 공유 서버로서 이용 가능하다.  

Manila - Ceph 를 연동하면 기존 Nova 인스턴스를 재활용하여  
생성/삭제 하더라도 기존 데이터를 불러와서 사용 가능하다.  

AWS의 EFS(Elastic File System) 에 해당한다.  

## 1.11. TripleO
OpenStack의 배포를 담당한다.  
배포 툴로 Ansible,Puppet을 사용하며,
기존에 짜여진 yaml코드를 재활용하여
새로운 베어 메탈에 OpenStack을 쉽게 설치/배포를 해준다.  

AWS에서 비슷하게는 Terraform에 해당한다.  

# 2. 프로젝트 관리
## 2.1. 프로젝트 관리
**프로젝트 생성**  
```
[user@workstation ~(admin)]$ openstack project create \
> --description "Demo Org Project" --domain demo-org demo-project
```

부모-자식 구조로 관리할 수도 있다.  
```
[user@workstation ~(admin)]$ openstack project create \
> --description "Demo Org Project" --domain demo-org-children --parent demo-org
```

demo-org
ㄴdemo-org-children

**프로젝트 삭제**

```
[user@workstation ~(admin)]$ openstack project delete \
> --domain demo-org demo-project
```

**프로젝트 리스트출력**
```
[user@master ~(admin)]$ openstack project list
```

## 2.2. 도메인 관리
**도메인 생성**
```
[student@workstation ~(admin)]$ openstack domain create \
> --description "My Corp Domain" MyCorp
+-------------+----------------------------------+
| Field       | Value                            |
+-------------+----------------------------------+
| description | My Corp Domain                   |
| enabled     | True                             |
| id          | 8350ad8d9e7d4f3aacfdd9a2a7d9a45e |
| name        | MyCorp                           |
| options     | {}                               |
| tags        | []                               |
+-------------+----------------------------------+
```

**도메인 삭제**
```
[student@workstation ~(admin)]$ openstack domain delete MyCorp
[student@workstation ~(admin)]$
```

**도메인 리스트출력**
```
[student@workstation ~(admin)]$ openstack domain list
+----------------------------------+------------+---------+--------------------+
| ID                               | Name       | Enabled | Description        |
+----------------------------------+------------+---------+--------------------+
| 8350ad8d9e7d4f3aacfdd9a2a7d9a45e | MyCorp     | True    | My Corp Domain     |
+----------------------------------+------------+---------+--------------------+
```

## 2.3. 유저관리
**유저 생성**
```
[student@workstation ~(admin)]$ openstack user create \
> --domain MyCorp \
> --password redhat \
> developer1
+---------------------+----------------------------------+
| Field               | Value                            |
+---------------------+----------------------------------+
| domain_id           | 8350ad8d9e7d4f3aacfdd9a2a7d9a45e |
| enabled             | True                             |
| id                  | 3c348c3a943346eba2a810776f31cd02 |
| name                | developer1                       |
| options             | {}                               |
| password_expires_at | None                             |
+---------------------+----------------------------------+
```

**유저 삭제**
```
[student@workstation ~(admin)]$ openstack user delete \
> developer1 --domain MyCorp
+----------------------------------+------------+
| ID                               | Name       |
+----------------------------------+------------+
| 3c348c3a943346eba2a810776f31cd02 | developer1 |
+----------------------------------+------------+
```

**유저 리스트출력**
```
[student@workstation ~(admin)]$ openstack user list --domain MyCorp
+----------------------------------+------------+
| ID                               | Name       |
+----------------------------------+------------+
| 3c348c3a943346eba2a810776f31cd02 | developer1 |
+----------------------------------+------------+
```

## 2.4. 역할관리
**역할 할당**  
```
[student@workstation ~(admin)]$ openstack role add \
> --user-domain MyCorp \
> --user developer1 \
> --project-domain MyCorp \
> --project sales \
> member
```

프로젝트가 부모-자식 구조일때, 하위 프로젝트(자식) 포함 할당하기  
MyTest (부모)  
ㄴDevelopment (자식)  

```
[student@workstation ~(operator5-finance)]$ openstack role add \
> --project-domain Example --project MyTest \
> --user-domain Example --user developer1 --inherited admin
```
**--inherited** 옵션을 잊지말자.  

```
[student@workstation ~(operator5-finance)]$ openstack role assignment list \
> --project Development --names --effective -f json
[
  {
    "Role": "admin",
    "User": "developer1@Example",
    "Group": "",
    "Project": "Development@Example",
    "Domain": "",
    "System": "",
    "Inherited": true
  },
  {
    "Role": "member",
    "User": "developer1@Example",
    "Group": "",
    "Project": "Development@Example",
    "Domain": "",
    "System": "",
    "Inherited": true
  },
  {
    "Role": "reader",
    "User": "developer1@Example",
    "Group": "",
    "Project": "Development@Example",
    "Domain": "",
    "System": "",
    "Inherited": true
  }
]
```

옵션 참고
--names : id를 name으로 변환하여 출력해준다.  
--effective : 부모-자식구조에서 할당내용이 안보일수있다. 생각보다 중요하니 비교해보는것을 추천  


**역할 삭제**
```
[student@workstation ~(admin)]$ openstack role delete \
> --user-domain MyCorp \
> --user developer1 \
> --project-domain MyCorp \
> --project sales \
> member
```

**역할 리스트출력**
```
[student@workstation ~(admin)]$ openstack role list
+----------------------------------+---------------+
| ID                               | Name          |
+----------------------------------+---------------+
| 71ccc37d41c8491c975ae72676db687f | Member        |
| 149f50a1fe684bfa88dae76a48d26ef7 | ResellerAdmin |
| 9fe2ff9ee4384b1894a90878d3e92bab | _member_      |
| 6ecf391421604da985db2f141e46a7c8 | admin         |
| deb4fffd123c4d02a907c2c74559dccf | anotherrole   |
+----------------------------------+---------------+
```

**할당된 역할출력**
```
[student@workstation ~(admin)]$ openstack role assignment list \
> --effective \
> --names \
> --user-domain MyCorp \
> --user developer1
+----------+-------------------+-------+----------------+--------+-----------+
| Role     | User              | Group | Project        | Domain | Inherited |
+----------+-------------------+-------+----------------+--------+-----------+
| member   | developer1@MyCorp |       | sales@MyCorp   |        | False     |
| reader   | developer1@MyCorp |       | sales@MyCorp   |        | False     |
+----------+-------------------+-------+----------------+--------+-----------+
```
## 2.5. 할당량 관리
**프로젝트 할당량보기**
```
[student@workstation ~(operator1-research)]$ openstack quota show \
> -c cores -c ram -c instances research
+-----------+-------+
| Field     | Value |
+-----------+-------+
| cores     | 20    |
| instances | 10    |
| ram       | 51200 |
+-----------+-------+
```

**프로젝트 할당량 설정**
```
[student@workstation ~(operator1-research)]$ openstack quota set \
> --cores 5 \
> --instances 1 \
> --ram 8192 \
> research
[student@workstation ~(operator1-research)]$ 
```

# 3. 네트워크 관리
# 4. 리소스 관리
# 5. 디스크 구성
# 6. 스토리지 구성
# 7. 배치 관리
# 참고자료
- RHEL CL110, CL210
