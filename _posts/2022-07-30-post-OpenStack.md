---
title: "OpenStack"
last_modified_at: 2022-07-31T13:30:00-00:00
categories:
  - Linux
tags:
  - Linux
  - OpenStack
  - Cloud
---

# 들어가며
![OpenStack Logo](https://upload.wikimedia.org/wikipedia/commons/thumb/e/e6/OpenStack%C2%AE_Logo_2016.svg/1200px-OpenStack%C2%AE_Logo_2016.svg.png "오픈스택 로고")

오픈스택은 클라우드 환경 구축에 필요한 다양한 기술을 제공하는 강력한 플렛폼 중 하나이다.  
이번 포스트에서는 RHLS - CL110 과정에서 배운 것을 빠르게 복습할 수 있도록 자료정리를 해보고자 한다.  

# 1. 서비스
![OpenStack Platform](https://rol.redhat.com/rol/static/static_file_cache/cl110-16.1/intro/CL110-Service-Overview.png "오픈스택 서비스")  
OpenStack은 수 많은 서비스들이 연동하여 가상화 환경을 제공한다. 

핵심 기능을 정리하자면  
- 가상화
- 스토리지
- 네트워크
- 인증

위와 같이 4가지 기능들이 서로 얽히고 얽혀서 클라우드 환경을 제공한다. 오픈스택의 주요 서비스는 아래와 같다.  

## 1.1. 핵심 서비스
![OpenStack Services](https://docs.openstack.org/security-guide/_images/marketecture-diagram.png "오픈스택 핵심 서비스")

**Cinder**  
블록 스토리지 서비스를 담당한다.  
쉽게 정의하면 하드디스크 제공을 한다고 생각하면 된다.  

상세 설명은 [블록 스토리지 서비스 개요](https://docs.openstack.org/mitaka/ko_KR/install-guide-rdo/common/get_started_block_storage.html)를 참조.  

**Glance**  
이미지 서비스를 담당한다.  
이후 설명할 Nova에서 새로운 인스턴스를 실행하면,  
Glance를 통해 OS 이미지를 제공받는다.  

Glance에서는 이미지 버전 관리, 제공한다.  

상세 설명은 [이미지 서비스 개요](https://docs.openstack.org/mitaka/ko_KR/install-guide-rdo/common/get_started_image_service.html)를 참조.  

**Horizon**  
웹 기반의 GUI 인터페이스를 담당한다.  

AWS의 EC2 Management Console에 해당한다.  

**Keystone**  
ID서비스를 담당한다.  

모든 OpenStack 서비스에 인증 및 권한 부여/확인을 하며,  
사용자, 도메인, 역할, 프로젝트를 관리하는 역할을 한다.  

LDAP 연동을 통해 조직의 IDM을 통합할 수 있다.  

상세 설명은 [Identity 서비스 개요](https://docs.openstack.org/mitaka/ko_KR/install-guide-rdo/common/get_started_identity.html)

**Neutron**  
네트워크 서비스를 담당한다.  
네트워크, 서브넷, 유동IP를 만들 수 있으며  
이는 곳 OpenStack에서 SDN 역할을 한다.  

OpenvSwitch, OVN이 함께 사용된다.  

상세 설명은 [네트워킹 서비스 개요](https://docs.openstack.org/mitaka/ko_KR/install-guide-rdo/common/get_started_networking.html)를 참조.  

**Nova**  
컴퓨팅 서비스를 담당한다.  
Nova를 통해 가상환경을 제공 받을수 있으며,  
하이퍼바이저에 libvirtd, qemu, kvm을 사용한다.  

NoVNC와 연동하여 웹을 통한 인스턴스 연결이 가능하다.  

상세 설명은 [Compute 서비스 개요](https://docs.openstack.org/mitaka/ko_KR/install-guide-rdo/common/get_started_compute.html)를 참조.  

**Swift**  
![Swift 아키텍쳐](https://cdn.ttgtmedia.com/rms/onlineImages/storage-openstack_object_storage_mobile.png "Swift 아키텍쳐")
오브젝트 스토리지를 제공한다. 오브젝트 는 데이터로서 저장 할 수 있는 모든 형태의 파일을 의미한다.  

상세 설명은 [오브젝트 스토리지 서비스개요](https://docs.openstack.org/mitaka/ko_KR/install-guide-rdo/common/get_started_object_storage.html)를 참조

## 1.2. 옵션 서비스

**Heat**  
오케스트레이션 서비스를 담당한다.  
오케스트레이션이란 확장/축소를 조절한다는 의미로서 사용된다.  
OpenStack 뿐만아니라 다른 플랫폼(AWS,GCP,Kubernetes)에서도  
리소스 사용량에 따른 스케일인/아웃을 조정하는데 사용된다.  

OpenStack 에서는 인프라 전반에 걸친 그룹 배포가 가능하다.
Heat는 yaml코드로 관리된다.

상세 설명은 [Orchestration 서비스 개요](https://docs.openstack.org/mitaka/ko_KR/install-guide-rdo/common/get_started_orchestration.html)를 참조.

**Ironic**  
베어 메탈 프로비저닝 서비스를 담당한다.  

베어 메탈이란 물리 서버를 의미한다.  
Ironic을 통해 하드웨어를 컨트롤 할 수 있다.  

**Ceph**  
분산 데이터 오브젝트 저장소 역할을 한다.  
Ceph 클러스터링을 통해 용량 확장, 장애 복구가 쉽다는 장점이 있다.  

파일, 블록, 오브젝트 와 같이 다양한 형태로 데이터 저장, 이용을 할 수 있다.  

**Manila**  
공유 파일 시스템 서비스로서 파일 공유 서버 역할을 한다.  
쉽게 말하면 공유 서버로서 이용 가능하다.  

Manila - Ceph 를 연동하면 기존 Nova 인스턴스를 재활용하여  
생성/삭제 하더라도 기존 데이터를 불러와서 사용 가능하다.  

AWS의 EFS(Elastic File System) 에 해당한다.  

**TripleO**  
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
  
**역할 오버라이팅**  
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
--effective : 부모-자식구조에서 지정해줘야 할당내용이 보인다. 생각보다 중요하니 비교해보는것을 추천  


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
## 3.1. 네트워크 기술개요  
![Neutron 아키텍쳐](https://docs.openstack.org/security-guide/_images/sdn-connections.png "Neutron 아키텍쳐")
![OpenStack 구성예시](https://docs.openstack.org/security-guide/_images/1aa-network-domains-diagram.png "OpenStack 구성예시")
## 3.2. 네트워크 관리
### 3.2.1. 역할에 따른 출력차이
**Member 역할일때**  
```
[student@workstation ~(developer1-finance)]$ openstack network show \
> finance-network1 --max-width 80
+---------------------------+--------------------------------------------------+
| Field                     | Value                                            |
+---------------------------+--------------------------------------------------+
...output omitted...
| name                      | finance-network1                                 |
| port_security_enabled     | True                                             |
| project_id                | c0cbb4890bcd45828bf31dc1d64fe5cd                 |
| provider:network_type     | None                                             |
| provider:physical_network | None                                             |
| provider:segmentation_id  | None                                             |
| qos_policy_id             | None                                             |
| revision_number           | 2                                                |
| router:external           | Internal                                         |
| segments                  | None                                             |
| shared                    | False                                            |
...output omitted...
```
  
**Admin 역할일때**  
```
[student@workstation ~(operator1-finance)]$ openstack network show \
> finance-network1 --max-width 80
+---------------------------+--------------------------------------------------+
| Field                     | Value                                            |
+---------------------------+--------------------------------------------------+
...output omitted...
| name                      | finance-network1                                 |
| port_security_enabled     | True                                             |
| project_id                | c0cbb4890bcd45828bf31dc1d64fe5cd                 |
| provider:network_type     | geneve                                           |
| provider:physical_network | None                                             |
| provider:segmentation_id  | 95                                               |
| qos_policy_id             | None                                             |
| revision_number           | 2                                                |
| router:external           | Internal                                         |
| segments                  | None                                             |
| shared                    | False                                            |
| status                    | ACTIVE                                           |
| subnets                   | 66b8efce-51d5-48ca-8e5f-bbef8b5ef58f             |
...output omitted...
```
역할에 따라 정보가 제한되는것을 알 수 있다.  

### 3.2.2. 네트워크 
**서브넷 정보출력**  
```
[student@workstation ~(operator1-finance)]$ openstack subnet show \
> 66b8efce-51d5-48ca-8e5f-bbef8b5ef58f --max-width 80
+-------------------+----------------------------------------------------------+
| field            | value                                                    |
+-------------------+----------------------------------------------------------+
| allocation_pools  | 192.168.1.2-192.168.1.254                                |
| cidr              | 192.168.1.0/24                                           |
| created_at        | 2020-06-10T14:46:52Z                                     |
| description       |                                                          |
| dns_nameservers   | 172.25.250.254                                           |
| enable_dhcp       | True                                                     |
| gateway_ip        | 192.168.1.1                                              |
| host_routes       |                                                          |
| id                | 66b8efce-51d5-48ca-8e5f-bbef8b5ef58f                     |
| ip_version        | 4                                                        |
| ipv6_address_mode | None                                                     |
| ipv6_ra_mode      | None                                                     |
| location          | cloud='', project.domain_id=,                            |
|                   | project.domain_name='Example',                           |
|                   | project.id='c0cbb4890bcd45828bf31dc1d64fe5cd',           |
|                   | project.name='finance', region_name='regionOne', zone=   |
| name              | finance-subnet1                                          |
| network_id        | bcf96725-88f5-4a5e-a2c9-9e89fb7eb255                     |
| prefix_length     | None                                                     |
| project_id        | c0cbb4890bcd45828bf31dc1d64fe5cd                         |
| revision_number   | 0                                                        |
| segment_id        | None                                                     |
| service_types     |                                                          |
| subnetpool_id     | None                                                     |
| tags              |                                                          |
| updated_at        | 2020-06-10T14:46:52Z                                     |
+-------------------+----------------------------------------------------------+
```
  
**네트워크 정보검색**  
```
[student@workstation ~(operator1-finance)]$ openstack network show \
> provider-datacentre --max-width 80
+---------------------------+--------------------------------------------------+
| Field                     | Value                                            |
+---------------------------+--------------------------------------------------+
...output omitted...
| name                      | provider-datacentre                              |
| port_security_enabled     | True                                             |
| project_id                | b04181074c884a89acc6469595599083                 |
| provider:network_type     | flat                                             |
| provider:physical_network | datacentre                                       |
| provider:segmentation_id  | None                                             |
| qos_policy_id             | None                                             |
| revision_number           | 2                                                |
| router:external           | External                                         |
| segments                  | None                                             |
| shared                    | True                                             |
| status                    | ACTIVE                                           |
| subnets                   | 655df137-b2e3-4e3d-9b52-98221b7abf24             |
| id                        | ef95203b-7c9f-46c0-b328-e51aa7729798             |
...output omitted...
```

provider-datacentre 네트워크의 정보  
```
provider:network_type     | flat        
provider:physical_network | datacentre  
router:external           | External   
shared                    | True 
subnets                   | 655df137-b2e3-4e3d-9b52-98221b7abf24
id                        | ef95203b-7c9f-46c0-b328-e51aa7729798
```

**서브넷 정보검색**  
```
[student@workstation ~(operator1-finance)]$ openstack subnet show \
> 655df137-b2e3-4e3d-9b52-98221b7abf24 --max-width 80
+-------------------+----------------------------------------------------------+
| Field             | Value                                                    |
+-------------------+----------------------------------------------------------+
| allocation_pools  | 172.25.250.101-172.25.250.189                            |
| cidr              | 172.25.250.0/24                                          |
| created_at        | 2020-04-19T11:05:12Z                                     |
| description       |                                                          |
| dns_nameservers   | 172.25.250.254                                           |
| enable_dhcp       | False                                                    |
| gateway_ip        | 172.25.250.254                                           |
| host_routes       |                                                          |
| id                | 655df137-b2e3-4e3d-9b52-98221b7abf24                     |
...output omitted...
```
 [655df137-b2e3-4e3d-9b52-98221b7abf24] 서브넷 정보 확인  
 ```
 allocation_pools  | 172.25.250.101-172.25.250.189
 dns_nameservers   | 172.25.250.254
 gateway_ip        | 172.25.250.254   
 ```
   
controller에서 확인  
```
[root@controller0 ~]# ovs-vsctl show
Bridge br-ex
    fail_mode: standalone
    Port br-ex
         Interface br-ex
             type: internal
    Port "eth2"
         Interface "eth2"
    Port "patch-provnet-ef95203b-7c9f-46c0-b328-e51aa7729798-to-br-int"
         Interface "patch-provnet-ef95203b-7c9f-46c0-b328-e51aa7729798-to-br-int"
             type: patch
             options: {peer="patch-br-int-to-provnet-ef95203b-7c9f-46c0-b328-e51aa7729798"}
```
  
브릿지 br-ex에 소속된 포트 3개  
- br-ex, Internal 타입
- eth2, Physical 타입
- patch-provnet-ef95203b-7c9f-46c0-b328-e51aa7729798-to-br-int, patch타입

patch 인터페이스는 아래와 같은 명명규칙으로 관리된다.  
patch-provnet-"network id"-to-"connected port"  

```
Bridge br-int
    fail_mode: secure
    Port "ovn-0102a3-0"
        Interface "ovn-0102a3-0"
             type: geneve
             options: {csum="true", key=flow, remote_ip="172.24.2.12"}
    Port "patch-br-int-to-provnet-ef95203b-7c9f-46c0-b328-e51aa7729798"
        Interface "patch-br-int-to-provnet-ef95203b-7c9f-46c0-b328-e51aa7729798"
             type: patch
             options: {peer="patch-provnet-ef95203b-7c9f-46c0-b328-e51aa7729798-to-br-int"}
    Port "ovn-1af225-0"
        Interface "ovn-1af225-0"
             type: geneve
             options: {csum="true", key=flow, remote_ip="172.24.2.6"}
    Port "o-hm0"
        Interface "o-hm0"
             type: internal
    Port "ovn-a643d0-0"
        Interface "ovn-a643d0-0"
             type: geneve
             options: {csum="true", key=flow, remote_ip="172.24.2.2"}
...output omitted...
```
브릿지 br-int에 소속된 포트 5개  
- ovn-0102a3-0, geneve타입
- patch-br-int-to-provnet-ef95203b-7c9f-46c0-b328-e51aa7729798, patch타입
- ovn-1af225-0, geneve타입
- o-hm0, Internal타입
- ovn-a643d0-0, geneve타입

patch 인터페이스는 아래와 같은 명명규칙으로 관리된다.  
patch-provnet-"network id"-to-"connected port"  
  
이를 통해 알 수 있는 점  
```
+----+----+
| real-net| 
+----+----+
     |  <- ???
     |
-----|----------    <- SDN과 물리 네트워크의 경계선
     |
     |  <- eth2
+----+----+
| br-ex   | 
+----+----+
     |  <- patch-provnet-ef95203b-7c9f-46c0-b328-e51aa7729798-to-br-int
     |     - network :   ef95203b-7c9f-46c0-b328-e51aa7729798 (provider-datacentre)
     |     - subnet  :   655df137-b2e3-4e3d-9b52-98221b7abf24 (DHCP)
     |
     |
     |  <- patch-br-int-to-provnet-ef95203b-7c9f-46c0-b328-e51aa7729798
+----+----+
| br-int  | 
+---------+
```

### 3.2.3. 네트워크 관리하기
**네트워크 생성**
```
[student@workstation ~(developer1-finance)]$ openstack network create \
> MyNet
...output omitted...
```

**서브넷 생성**
```
[student@workstation ~(developer1-finance)]$ openstack subnet create \
> --subnet-range 192.168.4.0/24 \
> --network MyNet \
> MyNet-Sub192_168_4_0
...output omitted...
```

**라우터 생성**
```
[student@workstation ~(developer1-finance)]$ openstack router create \
> finance-router1
+-------------------------+------------------------------------------------------+
| Field                   | Value                                                |
+-------------------------+------------------------------------------------------+
| admin_state_up          | UP                                                   |
| availability_zone_hints | None                                                 |
| availability_zones      | None                                                 |
| created_at              | 2020-07-14T16:00:45Z                                 |
| description             |                                                      |
| external_gateway_info   | null                                                 |
| flavor_id               | None                                                 |
| id                      | 6ea98144-c243-41cc-8064-c8281821c6d0                 |
| location                | cloud='', project.domain_id=,                        |
|                         | project.domain_name='Example',                       |
|                         | project.id='f76fd09fa0b14a678b5b61f9e3ec3c87',       |
|                         | project.name='finance', region_name='regionOne',     |
|                         | zone=                                                |
| name                    | finance-router1                                      |
| project_id              | f76fd09fa0b14a678b5b61f9e3ec3c87                     |
| revision_number         | 0                                                    |
| routes                  |                                                      |
| status                  | ACTIVE                                               |
| tags                    |                                                      |
| updated_at              | 2020-07-14T16:00:45Z                                 |
+-------------------------+------------------------------------------------------+
```

**라우터 리스트출력**
```
[student@workstation ~(developer1-finance)]$ openstack router list
+--------------------+-----------------+--------+-------+---------------------+
| ID                 | Name            | Status | State | Project             |
+--------------------+-----------------+--------+-------+---------------------+
| 6ea98144-c243-41cc | finance-router1 | ACTIVE | UP    | f76fd09fa0b14a678b5 |
| -8064-c8281821c6d0 |                 |        |       | b61f9e3ec3c87       |
+--------------------+-----------------+--------+-------+---------------------+
```

**서브넷 연결**
```
[student@workstation ~(developer1-finance)]$ openstack router add subnet \
> finance-router1 finance-subnet3
```

**게이트웨이 설정**
```
[student@workstation ~(developer1-finance)]$ openstack router set \
> --external-gateway provider-datacentre \
> finance-router1
```

**유동IP 생성**
```
[student@workstation ~(developer1-finance)]$ openstack floating ip create \
> provider-datacentre
+---------------------+----------------------------------------------------------+
| Field               | Value                                                    |
+---------------------+----------------------------------------------------------+
| created_at          | 2020-07-14T16:48:40Z                                     |
| description         |                                                          |
| dns_domain          |                                                          |
| dns_name            |                                                          |
| fixed_ip_address    | None                                                     |
| floating_ip_address | 172.25.250.125                                           |
| floating_network_id | ef95203b-7c9f-46c0-b328-e51aa7729798                     |
| id                  | cda15c6c-3325-49f5-a05a-740761aec953                     |
| location            | Munch({'cloud': '', 'region_name': 'regionOne', 'zone':  |
|                     | None, 'project': Munch({'id':                            |
|                     | 'f76fd09fa0b14a678b5b61f9e3ec3c87', 'name': 'finance',   |
|                     | 'domain_id': None, 'domain_name': 'Example'})})          |
| name                | 172.25.250.125                                           |
| port_details        | None                                                     |
| port_id             | None                                                     |
| project_id          | f76fd09fa0b14a678b5b61f9e3ec3c87                         |
| qos_policy_id       | None                                                     |
| revision_number     | 0                                                        |
| router_id           | None                                                     |
| status              | DOWN                                                     |
| subnet_id           | None                                                     |
| tags                | []                                                       |
| updated_at          | 2020-07-14T16:48:40Z                                     |
+---------------------+----------------------------------------------------------+
```

**유동IP 할당**
```
[student@workstation ~(developer1-finance)]$ openstack server add floating ip \
> finance-server1 172.25.250.125
```

이를 통해 알 수 있는 점  
```
+----+----+
| real-net| 
+----+----+
     |  <- ???
     |
-----|----------    <- SDN과 물리 네트워크의 경계선
     |
     |  <- ??? neutron 설정에 정의된 물리 인터페이스
+----+----+-----------+
| provider-datacentre | 
+----+----------------+
     ^
     | 
     | 
     | 
     |  external-gateway 
+----+----+---------+
| finance-router1   | 
+----+--------------+
     |  subnet
     |
     |
     |
     v
+----+----+--------+
| finance-subnet3  | 
+---------+--------+
```

# 4. 리소스 관리
## 4.1. 이미지 
**이미지 등록**  
```
[student@workstation ~(developer1-finance)]$ openstack image create \ 
> --disk-format qcow2 \
> --file ~/Downloads/osp-small.qcow2 \
> rhel8-dbsmall
...output omitted...
```
  
**이미지 정보출력**  
```
[student@workstation ~(developer1-finance)]$ openstack image show \
> rhel8-dbsmall --max-width 80
+------------------+-----------------------------------------------------------+
| Field            | Value                                                     |
+------------------+-----------------------------------------------------------+
| checksum         | 34c6c63549f342af9c95b52d23372fda                          |
| container_format | bare                                                      |
| created_at       | 2020-05-12T12:19:00Z                                      |
| disk_format      | qcow2                                                     |
| file             | /v2/images/5ad936aa-8384-4b31-b7ab-2f0daee8168a/file      |
| id               | 5ad936aa-8384-4b31-b7ab-2f0daee8168a                      |
| min_disk         | 0                                                         |
| min_ram          | 0                                                         |
| name             | rhel8-dbsmall                                             |
| owner            | 4a08af8fda6a4609ad293613e0e637e3                          |
| properties       | direct_url='rbd://63e5c992-81fb-11ea-bc11-52540001fac8/im |
|                  | ages/5ad936aa-8384-4b31-b7ab-2f0daee8168a/snap',          |
|                  | locations='[{'url': 'rbd://63e5c992-81fb-11ea-bc11-525400 |
|                  | 01fac8/images/5ad936aa-8384-4b31-b7ab-2f0daee8168a/snap', |
|                  | 'metadata': { }}]', os_hash_algo='sha512', os_hash_value=' |
|                  | 189b477467b2d7e2c7b34bfc7a0713f2f1ec802659ab48d9dba3e13d3 |
|                  | e8aae56ce622a48f7d3176ec86d6a928cb75e17db2eb76d3117baf677 |
|                  | 47f3853a905809', os_hidden='False'                        |
| protected        | False                                                     |
| schema           | /v2/schemas/image                                         |
| size             | 1086128128                                                |
| status           | active                                                    |
| tags             |                                                           |
| updated_at       | 2020-05-12T12:19:13Z                                      |
| virtual_size     | None                                                      |
| visibility       | shared                                                    |
+------------------+-----------------------------------------------------------+
```

**이미지 설정변경**  
```
[student@workstation ~(developer1-finance)]$ openstack image set \
> --protected \
> --min-disk 10 \
> --min-ram 1024 \  <- M단위
> rhel8-dbsmall
[student@workstation ~(developer1-finance)]$ openstack image show \
> rhel8-dbsmall --max-width 80
+------------------+-----------------------------------------------------------+
| Field            | Value                                                     |
+------------------+-----------------------------------------------------------+
| checksum         | 34c6c63549f342af9c95b52d23372fda                          |
| container_format | bare                                                      |
| created_at       | 2020-05-12T12:19:00Z                                      |
| disk_format      | qcow2                                                     |
| file             | /v2/images/5ad936aa-8384-4b31-b7ab-2f0daee8168a/file      |
| id               | 5ad936aa-8384-4b31-b7ab-2f0daee8168a                      |
| min_disk         | 10                                                        |
| min_ram          | 1024                                                         |
| name             | rhel8-dbsmall                                             |
| owner            | 4a08af8fda6a4609ad293613e0e637e3                          |
| properties       | direct_url='rbd://63e5c992-81fb-11ea-bc11-52540001fac8/im |
|                  | ages/5ad936aa-8384-4b31-b7ab-2f0daee8168a/snap',          |
|                  | locations='[{'url': 'rbd://63e5c992-81fb-11ea-bc11-525400 |
|                  | 01fac8/images/5ad936aa-8384-4b31-b7ab-2f0daee8168a/snap', |
|                  | 'metadata': { }}]', os_hash_algo='sha512', os_hash_value=' |
|                  | 189b477467b2d7e2c7b34bfc7a0713f2f1ec802659ab48d9dba3e13d3 |
|                  | e8aae56ce622a48f7d3176ec86d6a928cb75e17db2eb76d3117baf677 |
|                  | 47f3853a905809', os_hidden='False'                        |
| protected        | True                                                      |
| schema           | /v2/schemas/image                                         |
| size             | 1086128128                                                |
| status           | active                                                    |
| tags             |                                                           |
| updated_at       | 2020-05-12T12:27:08Z                                      |
| virtual_size     | None                                                      |
| visibility       | shared                                                    |
+------------------+-----------------------------------------------------------+
```

**protect모드에서 삭제시도**  
```
[student@workstation ~(developer1-finance)]$ openstack image delete rhel8-dbsmall
Failed to delete image with name or ID 'rhel8-dbsmall': HTTP 403 Forbidden: Image 5ad936aa-8384-4b31-b7ab-2f0daee8168a is protected and cannot be deleted.
Failed to delete 1 of 1 images.
```
**--unprotected** 옵션으로 보호모드를 해제해야 삭제가 가능하다.  

## 4.2. 플레이버
```
[student@workstation ~(operator1-finance)]$ openstack flavor create \
> --vcpus 2 \
> --ram 1024 \
> --disk 10 \
> --ephemeral 2 \
> --swap 1024 \
> db-flavor
+----------------------------+--------------------------------------+
| Field                      | Value                                |
+----------------------------+--------------------------------------+
| OS-FLV-DISABLED:disabled   | False                                |
| OS-FLV-EXT-DATA:ephemeral  | 2                                    |
| disk                       | 10                                   |
| id                         | 74434d48-41b4-45c7-987a-8d332f3086db |
| name                       | db-flavor                           |
| os-flavor-access:is_public | True                                 |
| properties                 |                                      |
| ram                        | 1024                                 |
| rxtx_factor                | 1.0                                  |
| swap                       | 1024                                 |
| vcpus                      | 2                                    |
+----------------------------+--------------------------------------+
```
옵션  
- vcpus     : vcpu 할당 개수
- ram       : ram 메모리 할당 용량
- disk      : root disk(메인디스크) 할당 용량 *예시: Windows C:\ 드라이버*
- ephemeral : ephemeral disk(임시 디스크) 할당 용량 *예시: Windows D:\ 드라이버*
- swap      : 스왑 메모리 할당 용량

**인스턴스 시작**  
```
[student@workstation ~(developer1-finance)]$ openstack server create \
> --image rhel8 \
> --flavor default \
> --nic net-id=MyNet \
> --wait Test-Instance
+-----------------------------+----------------------------------------+
| Field                       | Value                                  |
+-----------------------------+----------------------------------------+
| OS-DCF:diskConfig           | MANUAL                                 |
| OS-EXT-AZ:availability_zone | nova                                   |
| OS-EXT-STS:power_state      | Running                                |
| OS-EXT-STS:task_state       | None                                   |
| OS-EXT-STS:vm_state         | active                                 |
| OS-SRV-USG:launched_at      | 2020-05-12T09:01:31.000000             |
| OS-SRV-USG:terminated_at    | None                                   |
| accessIPv4                  |                                        |
| accessIPv6                  |                                        |
| addresses                   | MyNet=192.168.2.140                    |
| adminPass                   | 7KDuUWwXKJae                           |
| config_drive                |                                        |
| created                     | 2020-05-12T09:00:55Z                   |
| flavor                      | default (8ff1...8c7)                   |
| hostId                      | 353d7...381fa5aa8a3bc0ebac0a1b         |
| id                          | 39b1...7e6                             |
| image                       | rhel8 (2b4d...c71)                     |
| key_name                    | None                                   |
| name                        | Test-Instance                          |
| progress                    | 0                                      |
| project_id                  | 4a08af8fda6a4609ad293613e0e637e3       |
| properties                  |                                        |
| security_groups             | name='default'                         |
| status                      | ACTIVE                                 |
| updated                     | 2020-05-12T09:01:32Z                   |
| user_id                     | b3bf7...fd45d45a1470bce0adcce1a5c3570  |
| volumes_attached            |                                        |
+-----------------------------+----------------------------------------+
```

**인스턴스 콘솔접속**  
```
[student@workstation ~(developer1-finance)]$ openstack console url \
> show Test-Instance
+-------+-------------------------------------------------+
| Field | Value                                           |
+-------+-------------------------------------------------+
| type  | novnc                                           |
| url   | http://172.25.250.50:6080/vnc_auto.html?path=...|
+-------+-------------------------------------------------+
```

# 5. 디스크 구성
## 5.1 임시 디스크
**flavor - ephemeral**  
```
[student@workstation ~(operator1-finance)]$ openstack flavor create \
> --ram 2048 \
> --disk 10 \
> --ephemeral 5 \
> --vcpus 2 \
> --public default-with-ephemeral -f json
{
  "OS-FLV-DISABLED:disabled": false,
  "OS-FLV-EXT-DATA:ephemeral": 5,
  "disk": 10,
  "id": "ae1de0aa-1ae6-45ec-9a56-d22cfc82174a",
  "name": "default-with-ephemeral",
  "os-flavor-access:is_public": true,
  "properties": "",
  "ram": 2048,
  "rxtx_factor": 1.0,
  "swap": "",
  "vcpus": 2
}
```
## 5.2 영구 디스크
**이미지 볼륨 생성**  
```
[student@workstation ~(developer1-finance)]$ openstack volume create \
> --size 10 \
> --image rhel8 \
> finance-vol-rhel8
+---------------------+---------------------------------------+
| Field               | Value                                 |
+---------------------+---------------------------------------+
| attachments         | []                                    |
| availability_zone   | nova                                  |
| bootable            | false                                 |
| consistencygroup_id | None                                  |
| created_at          | 2020-05-29T05:23:12.000000            |
| description         | None                                  |
| encrypted           | False                                 |
| id                  | b013db6e-78b1-4378-81aa-ca36203cef79  |
| multiattach         | False                                 |
| name                | finance-vol-rhel8                     |
| properties          |                                       |
| replication_status  | None                                  |
| size                | 10                                    |
| snapshot_id         | None                                  |
| source_volid        | None                                  |
| status              | creating                              |
| type                | tripleo                               |
| updated_at          | None                                  |
| user_id             | b3bf...77638121e0abd2a5e89fa85f663570 |
+---------------------+---------------------------------------+
```

**데이터 볼륨생성**  
```
[student@workstation ~ (developer1-finance)]$ openstack volume create \ 
> --size 1 finance-volume1
+---------------------+---------------------------------------------+
| Field               | Value                                       |
+---------------------+---------------------------------------------+
| attachments         | []                                          |
| availability_zone   | nova                                        |
| bootable            | false                                       |
| consistencygroup_id | None                                        |
| created_at          | 2020-05-28T07:58:34.000000                  |
| description         | None                                        |
| encrypted           | False                                       |
| id                  | b017a769-9dbf-484d-8d04-74723445ffc7        |
| multiattach         | False                                       |
| name                | finance-volume1                             |
| properties          |                                             |
| replication_status  | None                                        |
| size                | 1                                           |
| snapshot_id         | None                                        |
| source_volid        | None                                        |
| status              | creating                                    |
| type                | tripleo                                     |
| updated_at          | None                                        |
| user_id             | b3bf...6649e76fd45d45a1470bce0adcce1a5c3570 |
+---------------------+---------------------------------------------+
```

**볼륨 상태**  
```
[student@workstation ~(developer1-finance)]$ openstack volume list \
> -c Name -c Status
+-------------------+-----------+
| Name              | Status    |
+-------------------+-----------+
| finance-vol-rhel8 | available |
+-------------------+-----------+
```

**이미지 볼륨으로 인스턴스 시작하기**  
```
[student@workstation ~(developer1-finance)]$ openstack server create \
> --flavor default \
> --nic net-id=finance-network1 \
> --volume finance-vol-rhel8 \
> finance-server10
+-----------------------------+--------------------------------------+
| Field                       | Value                                |
+-----------------------------+--------------------------------------+
| OS-DCF:diskConfig           | MANUAL                               |
| OS-EXT-AZ:availability_zone |                                      |
| OS-EXT-STS:power_state      | NOSTATE                              |
| OS-EXT-STS:task_state       | scheduling                           |
| OS-EXT-STS:vm_state         | building                             |
| OS-SRV-USG:launched_at      | None                                 |
| OS-SRV-USG:terminated_at    | None                                 |
| accessIPv4                  |                                      |
| accessIPv6                  |                                      |
| addresses                   |                                      |
| adminPass                   | rmcovAvLK8QK                         |
| config_drive                |                                      |
| created                     | 2020-05-29T05:30:26Z                 |
| flavor                      | default (8ff1eecb...ec999a78c7)      |
| hostId                      |                                      |
| id                          | a992dd57-3b36-40ea-8b2f-8d2c3286e48b |
| image                       |                                      |
| key_name                    | None                                 |
| name                        | finance-server10                     |
| progress                    | 0                                    |
| project_id                  | 4a08af8fda6a4609ad293613e0e637e3     |
| properties                  |                                      |
| security_groups             | name='default'                       |
| status                      | BUILD                                |
| updated                     | 2020-05-29T05:30:26Z                 |
| user_id                     | b3bf...e89fa85f6649e76fd45d45a1470bce|
| volumes_attached            |                                      |
+-----------------------------+--------------------------------------+
```

**할당 확인**  
```
[student@workstation ~(developer1-finance)]$ openstack volume list \
> -c Name -c Status
+-------------------+-----------+
| Name              | Status    |
+-------------------+-----------+
| finance-volume1   | available |
| finance-vol-rhel8 | in-use    |
+-------------------+-----------+
```

**데이터 볼륨 할당**  
```
[student@workstation ~ (developer1-finance)]$ openstack server add volume \
> finance-server10 finance-volume1
[student@workstation ~(developer1-finance)]$ 
```

**스냅샷 이미지 생성**  
```
[student@workstation ~(developer1-finance)]$ openstack server image create \
> --name finance-server10-snapshot \
> --wait finance-server10

+------------------+-------------------------------------------------------------+
| Field            | Value                                                       |
+------------------+-------------------------------------------------------------+
| checksum         | d41d8cd98f00b204e9800998ecf8427e                            |
| container_format | bare                                                        |
| created_at       | 2020-06-22T02:53:24Z                                        |
| disk_format      | qcow2                                                       |
| file             | /v2/images/205b421d-8497-45e9-8155-9c7165e0b115/file        |
| id               | 205b421d-8497-45e9-8155-9c7165e0b115                        |
| min_disk         | 10                                                          |
| min_ram          | 2048                                                        |
| name             | finance-server10-snapshot                                   |
...output omitted...
| status           | active                                                      |
| tags             |                                                             |
| updated_at       | 2020-06-22T02:53:25Z                                        |
| virtual_size     | None                                                        |
| visibility       | private                                                     |
+------------------+-------------------------------------------------------------+
```

**스냅샷 이미지로 인스턴스 시작**  
```
[student@workstation ~(developer1-finance)]$ openstack server create \
> --image finance-server10-snapshot \
> --flavor default \
> --nic net-id=finance-network3 \
> --wait \
> finance-server2

+-----------------------------+--------------------------------------------------+
| Field                       | Value                                            |
+-----------------------------+--------------------------------------------------+
| OS-DCF:diskConfig           | MANUAL                                           |
| OS-EXT-AZ:availability_zone | nova                                             |
| OS-EXT-STS:power_state      | Running                                          |
| OS-EXT-STS:task_state       | None                                             |
| OS-EXT-STS:vm_state         | active                                           |
| OS-SRV-USG:launched_at      | 2020-06-22T03:26:36.000000                       |
| OS-SRV-USG:terminated_at    | None                                             |
| accessIPv4                  |                                                  |
| accessIPv6                  |                                                  |
| addresses                   | finance-network3=192.168.3.167                   |
| adminPass                   | uvR2QtcT4ZuC                                     |
| config_drive                |                                                  |
| created                     | 2020-06-22T03:26:26Z                             |
| flavor                      | default (8ff1eecb-15b4-4f32-bb99-4dec999a78c7)   |
| hostId                      | 270d29f8ba5e76c1fd80c56d592882a1138b5d12ad0f35a1 |
|                             | 7eac7546                                         |
| id                          | 253ff506-7d33-4269-8d29-49d897c2602c             |
| image                       | finance-server1-snapshot                         |
|                             | (205b421d-8497-45e9-8155-9c7165e0b115)           |
| key_name                    | None                                             |
| name                        | finance-server2                                  |
| progress                    | 0                                                |
| project_id                  | 3fe4ec06f5654a4d906a640d7f1a0457                 |
| properties                  |                                                  |
| security_groups             | name='default'                                   |
| status                      | ACTIVE                                           |
| updated                     | 2020-06-22T03:26:37Z                             |
| user_id                     | b3bf77638121e0abd2a5e89fa85f6649e76fd45d45a1470b |
|                             | ce0adcce1a5c3570                                 |
| volumes_attached            | id='4053e1de-62bb-470a-8fc3-b6bbdaaa2590'        |
+-----------------------------+--------------------------------------------------+
```

**볼륨 스냅샷 제거**  
```
[student@workstation ~(developer1-finance)]$ openstack volume snapshot list
+-----------------------+-----------------------+-------------+-----------+------+
| ID                    | Name                  | Description | Status    | Size |
+-----------------------+-----------------------+-------------+-----------+------+
| 53b88d27-2c10-4578-bf | snapshot for finance- |             | available |   10 |
| ba-94f59bc04bb0       | server10-snapshot     |             |           |      |
+-----------------------+-----------------------+-------------+-----------+------+

[student@workstation ~(developer1-finance)]$ openstack volume snapshot delete \
> 53b88d27-2c10-4578-bfba-94f59bc04bb0
```

# 6. 스토리지 구성
## 6.1 오브젝트 스토리지
### 6.1.1. SWIFT
**컨테이너 생성**  
```
[student@workstation ~(operator1-finance)]$ openstack container create \
> finance-container1
+-------------------+--------------------+-------------+
| account           | container          | x-trans-id  |
+-------------------+--------------------+-------------+
| AUTH_c0cbb...e5cd | finance-container1 | tx41...4d72 |
+-------------------+--------------------+-------------+
```

**컨테이너 리스트**  
```
[student@workstation ~(operator1-finance)]$ openstack container list
+--------------------+
| Name               |
+--------------------+
| finance-cdr        |
| finance-container1 |
+--------------------+
```

**파일 업로드**  
```
[student@workstation ~(operator1-finance)]$ openstack object create \
> finance-container1/finance-data finance-rules
+---------------+---------------------------------+-------------+
| object        | container                       | etag        |
+---------------+---------------------------------+-------------+
| finance-rules | finance-container1/finance-data | d41d...427e |
+---------------+---------------------------------+-------------+
```

**finance-cdr 컨테이너 오브젝트 리스트**  
```
[student@workstation ~(operator1-finance)]$ openstack object list \
> finance-cdr
+----------------------+
| Name                 |
+----------------------+
| cdr//tmp/cdr.19Jun20 |
+----------------------+

```

**오브젝트 세부정보**  
```
[student@workstation ~(operator1-finance)]$ openstack object show \
> finance-cdr cdr//tmp/cdr.19Jun20
+----------------+----------------------------------------+
| Field          | Value                                  |
+----------------+----------------------------------------+
| account        | AUTH_c0cbb4890bcd45828bf31dc1d64fe5cd  |
| container      | finance-cdr                            |
| content-length | 10610                                  |
| content-type   | application/octet-stream               |
| etag           | 598d1e6b4f0a3583244e1b4e09b49fe5       |
| last-modified  | Fri, 19 Jun 2020 08:44:54 GMT          |
| object         | cdr//tmp/cdr.19Jun20                   |
+----------------+----------------------------------------+
```

**오브젝트 다운로드**  
```
[student@workstation ~(operator1-finance)]$ openstack object save \
> --file /tmp/cdr.txt finance-cdr cdr//tmp/cdr.19Jun20
[student@workstation ~(operator1-finance)]$ cat /tmp/cdr.txt
725bed67-0293-41c7-bc5a-eff9115108f6|7805589022|4115767947|2016-03-04T02:06:49.795+05:30|2016-03-04T02:09:08.950+05:30|VOICE|0.78949857|ANSWERED
...output omitted...
```

### 6.1.2. Manila 
**NFS 공유 스토리지 - 타입생성**  
```
[student@workstation ~(operator1-finance)]$ manila type-create cephfstype false
+----------------------+--------------------------------------+
| Property             | Value                                |
+----------------------+--------------------------------------+
| ID                   | 6cdeb359-a353-4d7d-b50a-a3b55b713338 |
| Name                 | cephfstype                           |
| Visibility           | public                               |
| is_default           | -                                    |
| required_extra_specs | driver_handles_share_servers : False |
| optional_extra_specs |                                      |
| Description          | None                                 |
+----------------------+--------------------------------------+
```

**NFS 공유 스토리지 - 공유생성**  
```
[student@workstation ~(developer1-finance)]$ manila create \
> --name finance-share1 --share-type cephfstype cephfs 1
+----------------------+--------------------------------------+
| Property             | Value                                |
+----------------------+--------------------------------------+
| id                   | d2ad3b20-d0a2-4734-91de-f425f9964189 |
| size                 | 1                                    |
| availability_zone    | None                                 |
| created_at           | 2020-07-02T09:07:11.000000           |
| status               | creating                             |
| name                 | finance-share1                       |
...output omitted...
[student@workstation ~(developer1-finance)]$ manila list \
> --columns Name,'Share Proto',Status,'Share Type Name'
+----------------+-------------+-----------+-----------------+
| Name           | Share Proto | Status    | Share Type Name |
+----------------+-------------+-----------+-----------------+
| finance-share1 | CEPHFS      | available | cephfstype      |
+----------------+-------------+-----------+-----------------+
```

**cephx 유저 키 파일 생성**  
```
[root@controller0 ~]# podman exec -t \
> ceph-mon-controller0 ceph --name=client.manila \
> --keyring=/etc/ceph/ceph.client.manila.keyring \
> auth get-or-create client.cloud-user > /root/cloud-user.keyring
```

**인스턴스로 파일 복사**  
```
[student@workstation manila(developer1-finance)]$ scp \
> {cloud-user.keyring,ceph.conf} cloud-user@172.25.250.122:
Warning: Permanently added '172.25.250.122' (ECDSA) to the list of known hosts.
cloud-user.keyring    100%   70    43.5KB/s   00:00
ceph.conf             100%  941   729.7KB/s   00:00
```

**공유 접근 권한 할당**  
```
[student@workstation ~(developer1-finance)]$ manila access-allow \
> finance-share1 cephx cloud-user
+--------------+--------------------------------------+
| Property     | Value                                |
+--------------+--------------------------------------+
| id           | cb7c11f1-b710-45b9-8762-a34ae334fc2b |
| share_id     | 4e099421-8733-4cf2-9af6-7159b7d24a37 |
| access_level | rw                                   |
| access_to    | cloud-user                           |
| access_type  | cephx                                |
| state        | queued_to_apply                      |
| access_key   | None                                 |
| created_at   | 2020-07-02T12:05:19.000000           |
| updated_at   | None                                 |
| metadata     | {}                                   |
+--------------+--------------------------------------+
[student@workstation ~(developer1-finance)]$ manila access-list \
> finance-share1 --columns access_to,access_level,state
+------------+--------------+--------+
| Access_To  | Access_Level | State  |
+------------+--------------+--------+
| cloud-user | rw           | active |
+------------+--------------+--------+
```

**공유 액세스 포인트**  
```
[student@workstation ~]$ source ~/developer1-finance-rc
[student@workstation ~(developer1-finance)]$ manila share-export-location-list \ 
> finance-share1 --columns Path
+------------------------------------------------------------------------+
| Path                                                                   |
+------------------------------------------------------------------------+
| 172.24.3.1:6789:/volumes/_nogroup/019cc044-1f71-43d5-bd6d-2b30817b1e57 |
+------------------------------------------------------------------------+
```

**인스턴스 설정**  
```
[root@finance-server6 ~]# curl -s -f \
> -o /etc/yum.repos.d/ceph.repo http://materials.example.com/ceph.repo
[root@finance-server6 ~]# yum install ceph-fuse
...output omitted...
Is this ok [y/d/N]: y
...output omitted...
Complete!

[root@finance-server6 ~]# ceph-fuse /mnt/ceph/ \
> --id=cloud-user --conf=/home/cloud-user/ceph.conf \
> --keyring=/home/cloud-user/cloud-user.keyring \
> --client-mountpoint=/volumes/_nogroup/cea022a9-c00c-4003-b6f3-8fea2a49bd5f
 2020-07-02 08:27:39.600 7f22c17d7040 -1 init, newargv = 0x555b64a4e460 newargc=7
 ceph-fuse[11810]: starting ceph client
 ceph-fuse[11810]: starting fuse
[root@finance-server6 ~]# df -Th
...output omitted...
ceph-fuse      fuse.ceph-fuse  1.0G     0  1.0G   0% /mnt/ceph
```
# 마치며
정리하면서 느끼는거지만 오픈스택 정말 어려운것 같다.  
어떻게 시험 합격했는지 모르겠다...  
관리자 관점에서도 어려운데 솔루션은 얼마나 어려울지...  
가능하면 퍼블릭 클라우드 사용하는게 정신건강에 좋을것 같다.  

**참고사항 - CL210 과정에 추가되는것들**  
- RC파일관리
- IDM통합
- 컨트롤 플레인 백업
- 베어메탈 관리
- 토큰관리
- 보안관리
- 네트워크 관리
- 이미지 커스터마이징 및 배포
- 모니터링
- HEAT 배포
- 트러블슈팅

기회가 된다면 다뤄보도록 하겠습니다.  
기회가 된다면...  

# 참고자료
- RHLS CL110
