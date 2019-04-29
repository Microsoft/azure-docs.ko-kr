---
title: Azure virtual machines (Vm)에서 IBM Db2 HADR 설정 | Microsoft Docs
description: Azure virtual machines (Vm)에서 IBM Db2 LUW의 고가용성을 설정 합니다.
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: SAP
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/10/2019
ms.author: juergent
ms.openlocfilehash: 3c1d0e252b5c658ab6da2b3932918f05ba651d52
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60835983"
---
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[1612105]:https://launchpad.support.sap.com/#/notes/1612105

[sles-for-sap-bp]:https://www.suse.com/documentation/sles-for-sap-12/
[db2-hadr-11.1]:https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html
[db2-hadr-10.5]:https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html
[dbms-db2]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_ibm
[sles-pacemaker]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker
[sap-instfind]:https://help.sap.com/viewer/9e41ead9f54e44c1ae1a1094b0f80712/ALL/en-US/576f5c1808de4d1abecbd6e503c9ba42.html
[nfs-ha]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs
[sles-ha-guide]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP3/
[ascs-ha]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md
[azr-sap-plancheck]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist



# <a name="high-availability-of-ibm-db2-luw-on-azure-vms-on-suse-linux-enterprise-server-with-pacemaker"></a>Pacemaker 사용 하 여 SUSE Linux Enterprise Server의 Azure Vm에서 IBM Db2 LUW의 고가용성

IBM Db2 LUW (Linux, Unix 및 Windows)에서 [HADR 구성](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html) 주 데이터베이스 인스턴스를 실행 하는 노드 하나 및 보조 데이터베이스 인스턴스를 실행 하는 노드가 하나 이상 구성 합니다. 변경 내용은 기본 데이터베이스 인스턴스에 복제 보조 데이터베이스 인스턴스에 동기적 또는 비동기적으로 구성에 따라 달라 집니다. 

이 문서에서는 배포 및 가상 컴퓨터를 구성, 클러스터 프레임 워크 및 설치 하 고 IBM Db2 LUW HADR 구성에서 구성 하는 방법을 설명 합니다. 문서는 설치 및 IBM Db2 LUW HADR 또는 SAP 소프트웨어 설치 구성 하는 방법을 설명 하지 않습니다. 이러한 작업을 수행 하는 IBM 및 SAP 설치 설명서에 대 한 참조가 제공 됩니다. Azure 환경에 관련 된 파트에 포커스가 있습니다. 

지원 되는 IBM Db2 버전 10.5 되며 SAP 참고 #에 설명 된 대로 더 높은[1928533]합니다.

다음 SAP note 및 설치에 도달 하기 전에 먼저 설명서를 읽어보세요.

| SAP Note | 설명 |
| --- | --- |
| [1928533] | Azure의 SAP 애플리케이션: 지원되는 제품 및 Azure VM 유형 |
| [2015553] | Microsoft Azure의 SAP: 필수 구성 요소 지원 |
| [2178632] | Microsoft Azure의 SAP용 주요 모니터링 메트릭 |
| [2191498] | Azure 포함 Linux의 SAP: 고급 모니터링 |
| [2243692] | Microsoft Azure(IaaS) VM의 Linux: SAP 라이선스 문제 |
| [1984787] |SUSE LINUX Enterprise Server 12: 설치 참고 사항 |
| [1999351] |SAP용 고급 Azure 모니터링 문제 해결 |
| [2233094] |DB6: IBM Db2를 사용 하 여 Linux, UNIX 및 Windows-추가 정보에 대 한 Azure의 SAP 응용 프로그램 |
| [1612105] |DB6: Db2 고가용성 재해 복구 (HADR)에 대 한 FAQ |


| 문서화 | 
| --- |
| [SAP Community WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) Linux에 모든 필요한 SAP Note |
| [Azure Virtual Machines 계획 및 Linux에서 SAP에 대 한 구현을] [ planning-guide] 가이드 |
| [Linux에서 SAP 용 azure Virtual Machines 배포] [ deployment-guide] (이 문서) |
| [Linux에서 SAP 용 azure Virtual Machines DBMS 배포] [ dbms-guide] 가이드 |
| [SAP 워크 로드를 Azure의 계획 및 배포 검사 목록][azr-sap-plancheck] |
| [SAP 애플리케이션 12 SP3용 SUSE Linux Enterprise Server 모범 사례 가이드][sles-for-sap-bp] |
| [SUSE Linux Enterprise 고가용성 확장 12 SP3][sles-ha-guide] |
| [SAP 워크 로드 용 IBM Db2 Azure Virtual Machines DBMS 배포][dbms-db2] |
| [IBM Db2 높은 가용성 재해 복구 11.1][db2-hadr-11.1] |
| [IBM Db2 높은 가용성 재해 복구 R 10.5][db2-hadr-10.5] |

## <a name="overview"></a>개요
HADR 사용 하 여 IBM Db2 LUW 두 개 이상의 Azure virtual machines에서 배포 된에 설치 되어 고가용성을 달성 하는 [Azure 가용성 집합](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets) 또는 걸쳐 [Azure 가용성 영역](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ha-availability-zones)합니다. 아래 그래픽에서는 두 데이터베이스 서버 Azure Vm의 설치를 보여 줍니다. 두 데이터베이스 서버 Azure Vm 연결 자체 저장소가 있고 실행 중 이며 있습니다. HADR, Azure Vm 중 하나에 단일 데이터베이스 인스턴스가는 기본 인스턴스의 역할입니다. 모든 클라이언트는이 기본 인스턴스로 연결 됩니다. Db2 트랜잭션 로그에 데이터베이스 트랜잭션의 모든 변경 내용은 로컬로 유지 됩니다. 트랜잭션 로그 레코드를 로컬로 유지 하는 대로 레코드를 전송할지 TCP/IP를 통해 데이터베이스 인스턴스에 두 번째 데이터베이스 서버, 대기 중인 서버 또는 대기 인스턴스. 대기 인스턴스 롤포워드 전송 된 트랜잭션 로그 레코드에서 로컬 데이터베이스를 업데이트 합니다. 따라서 대기 서버는 주 서버와 동기화 유지 됩니다.

HADR 사용 되는 복제 기능에만 합니다. 없음 오류 감지 및 자동 없는 인수 또는 장애 조치 기능 있습니다. 인수 또는 전송 대기 데이터베이스 관리자가 수동으로 시작 해야 합니다. 자동 인수 및 오류 감지를 위해 Linux Pacemaker 클러스터링 기능을 사용할 수 있습니다. Pacemaker는 두 개의 데이터베이스 서버/인스턴스를 모니터링합니다. 때 주 데이터베이스 서버/인스턴스 충돌할 때 Pacemaker 시작을 **자동** 대기 서버에서 HADR 인수 또한 새 주 서버에 가상 IP 주소가 할당 되어 있는지를 확인 합니다.

![IBM Db2 고가용성 개요](./media/dbms-guide-ha-ibm/ha-db2-hadr-lb.png)

SAP에 대 한 기본 데이터베이스에 연결 하는 응용 프로그램 서버 가상 호스트 이름 및 가상 IP가 필요 합니다. 장애 조치의 경우에서 SAP 응용 프로그램 서버는 새 주 데이터베이스 인스턴스에 연결 됩니다. Azure 환경에는 [Azure Load Balancer](https://microsoft.sharepoint.com/teams/WAG/AzureNetworking/Wiki/Load%20Balancing.aspx) IBM Db2 HADR에 필요한 방식으로 가상 IP 주소를 사용 해야 하는 합니다. 

다음 그림을 완전히 이해, HADR 및 Pacemaker를 사용 하 여 IBM Db2 LUW 고가용성 SAP 시스템 설치에 적용 하는 방법에 IBM Db2 데이터베이스를 기반으로 SAP 시스템의 항상 사용 가능한 설정의 개요를 제공 합니다. 이 문서에서는 IBM Db2만을 설명 하 고 SAP 시스템의 다른 구성 요소를 설정 하는 방법의 다른 문서에 대 한 참조입니다.

![IBM DB2 HA 전체 환경 개요](.//media/dbms-guide-ha-ibm/end-2-end-ha.png)


### <a name="high-level-overview-of-steps-needed"></a>필요한 단계에 대 한 개략적인 개요
IBM Db2 구성에 배포 하려면 다음이 단계를 설명 해야 합니다.

  + 환경 계획
  + Vm 배포
  + SUSE Linux를 업데이트 하 고 파일 시스템 구성
  + 설치 및 Pacemaker를 구성 합니다.
  + 설치 [항상 사용 가능한 NFS][nfs-ha]
  + 설치 [별도 클러스터에 ASCS/ERS][ascs-ha] 
  + 분산 및 고가용성 옵션 (SWPM)를 사용 하 여 IBM Db2 데이터베이스를 설치 합니다.
  + 보조 데이터베이스 노드와 인스턴스 설치/만들기 및 HADR 구성
  + HADR이 작동 하는지 확인 합니다.
  + Pacemaker 구성을 IBM Db2 컨트롤에 적용
  + Azure Load Balancer 구성 
  + 주 + 대화 설치 응용 프로그램 서버
  + SAP 응용 프로그램 서버에 대 한 구성을 확인/조정
  + 장애 조치를 수행 / 인수 테스트



## <a name="planning-azure-infrastructure-for-hosting-ibm-db2-luw-with-hadr"></a>HADR 사용 하 여 IBM Db2 LUW 호스팅을 위한 Azure 인프라를 계획 합니다.

배포를 실행 하기 전에 계획을 통해 이동 합니다. Azure에서 hadr Db2의 구성을 배포 하는 것에 대 한 기초를 작성 합니다. IMB Db2 LUW (SAP 환경의 데이터베이스 일부)에 대 한 계획의 일부가 되도록 해야 하는 주요 요소입니다.

| 항목 | 간단한 설명 |
| --- | --- |
| Azure 리소스 그룹을 정의 합니다. | 리소스 그룹을 배포할 VM, VNet, Azure Load Balancer 및 기타 리소스입니다. 기존 또는 새 수 있습니다. |
| 가상 네트워크 / 서브넷 정의 | 여기서 IBM Db2 및 Azure Load Balancer에 대 한 Vm 배포 하기는 합니다. 기존 또는 새롭게 만든 수 있습니다. |
| IBM Db2 LUW 호스팅 virtual machines | VM 크기, 저장소, 네트워킹, IP 주소 |
| 가상 호스트 이름 및 IBM Db2 데이터베이스에 대 한 가상 IP| 가상 IP/호스트 이름 SAP 응용 프로그램 서버의 연결에 사용 되는입니다. **db-virt-hostname**, **db-virt-ip** |
| Azure 펜스 | Azure 펜스 하거나 SBD 펜싱 (권장). 분할 브레인 상황을 방지 하려면 메서드는 수 없습니다. |
| SBD VM | SBD 가상 머신 크기, 저장소, 네트워크 |
| Azure Load Balancer | Basic 또는 Standard (권장), Db2 데이터베이스 (권장 사항은 62500)에 대 한 포트 검색 **프로브 포트** |
| 이름 확인| 이름 확인 환경의 작동 방식 DNS 서비스를 사용 하는 것이 좋습니다. 로컬 호스트 파일을 사용할 수 있습니다. |
    
Azure에서 Linux Pacemaker 사용에 대 한 자세한 내용은 다음이 문서에서 찾을 수 있습니다.

- [Azure의 SUSE Linux Enterprise Server에서 Pacemaker 설정](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker)



## <a name="deployment-on-suse-linux"></a>SUSE linux 배포

IBM Db2 LUW 용 리소스 에이전트는 SAP 응용 프로그램용 SUSE Linux Enterprise Server에 포함 됩니다. 이 문서에 설명 된 설정, SAP 응용 프로그램용 SUSE Linux 서버를 사용 하는 필수입니다. Azure Marketplace는 새 Azure 가상 머신을 배포 하는 데 사용할 수 있는 SAP Applications 12에 대 한 SUSE Enterprise Server에 대 한 이미지를 포함 합니다. 다른 지원/서비스 모델에서 제공 하는 SUSE는 Azure 갤러리를 통해 Azure VM 갤러리에서 VM 이미지를 선택할 때 고려해 야 합니다. 

### <a name="hosts---dns-updates"></a>호스트-DNS 업데이트
가상 호스트 이름을 포함 하는 모든 호스트 이름 목록을 확인 하 고 호스트 이름 확인에 적절 한 IP 주소를 사용 하도록 설정 하려면 DNS 서버를 업데이트 합니다. 경우에는 DNS 서버가 존재 하지 않습니다 또는 DNS 항목을 만들고 업데이트할 수 없는이 시나리오에 참여 하는 개별 Vm의 로컬 호스트 파일을 활용 하 여 해야 합니다. 호스트 파일 항목을 사용 하는 경우 항목은 SAP 시스템 환경에서 모든 Vm에 적용 되었는지 확인 해야 합니다. 그러나가 이상적으로 Azure로 확장 되는 DNS를 사용 하는 것이 좋습니다.


### <a name="manual-deployment"></a>수동 배포

선택한 OS의 IBM Db2 LUW 용 IBM/SAP에서 지원 되는지 확인 합니다. Azure Vm 및 Db2 릴리스에 대해 지원 되는 OS 버전의 목록은 SAP Note에서 사용할 수 [1928533]합니다. 개별 Db2 릴리스에서 OS 릴리스 목록을 SAP 제품 가용성 매트릭스에서 사용할 수 있습니다. SLES 12 SP3 이상 버전에서이 Azure 관련 된 성능 향상으로 인해 최소 좋습니다 SUSE Linux 버전.

1. 리소스 그룹 만들기/선택
2. 만들기/선택 가상 네트워크 및 서브넷
3. Azure 가용성 집합을 만들거나 가용성 영역에서 배포
    + 가용성 집합-2로 최대 업데이트 도메인 설정
4. 1 가상 컴퓨터를 만듭니다.
    + Azure 갤러리의 SAP 이미지에 대 한 SLES 사용
    + Azure 가용성 집합 만든 단계 3 개 선택 가용성 영역에서 선택
5.  가상 머신 2를 만듭니다.
    + Azure 갤러리의 SAP 이미지에 대 한 SLES 사용
    + Azure 가용성 집합에서 만든된 3 단계에서 선택 합니다. 또는 3 단계와 같이 동일한 영역이 아니라-가용성 영역을 선택 합니다.
6. Vm에 데이터 디스크 추가-문서의 파일 시스템 설정의 권장 사항을 확인 [SAP 워크 로드 용 IBM Db2 Azure Virtual Machines DBMS 배포][dbms-db2]

## <a name="create-the-pacemaker-cluster"></a>Pacemaker 클러스터 만들기
    
단계를 따릅니다 [Azure의 SUSE Linux Enterprise Server에서 Pacemaker 설정][sles-pacemaker] 이 IBM Db2 서버에 대 한 기본 Pacemaker 클러스터를 만들려고 합니다. 

## <a name="install-ibm-db2-luw-and-sap-environment"></a>Db2 luw 용 IBM 및 SAP 환경 설치

IBM Db2 LUW에 따라 SAP 환경 설치를 시작 하기 전에 (문서의 시작 부분에 제공 된 링크)를 검토 합니다.

+ Azure 설명서
+ SAP 설명서
+ IBM 설명서

IBM Db2 LUW NetWeaver 기반 응용 프로그램을 설치 하는 방법은 sap에서 설치 설명서를 확인 합니다.

SAP 포털 사용 하 여 도움말에 대 한 지침을 찾을 수 있습니다는 [SAP 설치 가이드 찾기][sap-instfind]

필터를 설정 하는 가이드가 횟수를 줄이는 검색을 필터링 할 수 있습니다.

+ 하고 싶어요: "새 시스템 설치"
+ 데이터베이스: "Linux, Unix 및 Windows 용 IBM Db2"
+ SAP Netweaver 버전, 스택 구성 또는 운영 체제에 대 한 필터를 추가 합니다.

### <a name="installation-hints-for-setting-up-ibm-db2-luw-with-hadr"></a>HADR 사용 하 여 IBM Db2 LUW 설정에 대 한 설치 힌트

기본 IBM Db2 LUW 데이터베이스 인스턴스를 설정 합니다.

- 사용 하 여 고가용성을 지원 하거나 분산된 옵션
- 데이터베이스 및 SAP ASCS/ERS 인스턴스 설치
- 새로 설치 된 데이터베이스의 백업을 수행합니다


> [!IMPORTANT] 
> "데이터베이스 통신 설정 된 포트" 설치 하는 동안 적어 둡니다. 데이터베이스 인스턴스 모두에 대해 동일한 포트 번호 여야 합니다.

SAP의 동종 시스템 복사 절차를 사용 하 여 대기 데이터베이스 서버를 설정 하기 위한 다음이 단계를 실행 합니다.

  - 시스템 복사 옵션을 사용 하 여--배포-시스템 데이터베이스 인스턴스를 대상입니다.
  - 대기 서버/인스턴스에서 백업을 복원 하려면 backup을 사용할 수 있도록 복사 메서드로 동종 시스템 복사를 선택
  - 동종 시스템 복사본에 대 한 데이터베이스를 복원 하는 종료 단계에 도달 하면 설치 관리자를 종료 합니다. 기본 호스트의 백업에서 데이터베이스를 복원 합니다. 주 데이터베이스 서버에서 이미 실행 한 모든 후속 설치 단계
- HADR IBM Db2에 대 한 설정

> [!NOTE]
> 설치/구성 Azure 및 Pacemaker에 특정 합니다. SAP Software Provisioning Manager를 통해 설치 절차 중 IBM Db2 LUW 용 고가용성에는 명시적 질문 포함 되어 있습니다.
>+ IBM Db2 pureScale 선택 하지 마십시오
>+ "설치 IBM Tivoli 시스템 자동화에 대 한 Multiplatforms 선택 하지 마십시오
>+ "클러스터 구성 파일 생성"을 선택 하지 마십시오

> [!NOTE]
>Linux pacemaker SBD 장치를 사용 하는 경우 Db2 HADR 매개 변수를 설정
>+ HADR 피어 기간 (초) (HADR_PEER_WINDOW) = 300  
>+ HADR 시간 제한 값 (HADR_TIMEOUT) = 60

> [!NOTE]
>Pacemaker Azure 펜스 에이전트를 사용 하 여:
>+ HADR 피어 기간 (초) (HADR_PEER_WINDOW) = 900  
>+ HADR 시간 제한 값 (HADR_TIMEOUT) = 60

매개 변수는 초기 인수 장애 조치/테스트에 따른 것이 좋습니다. 인수 및 이러한 매개 변수 설정 사용 하 여 장애 조치의 적절 한 기능을 테스트 하는 것이 반드시 합니다. 개별 구성은 다양할 수 있으므로 이러한 매개 변수는 조정이 필요할 수 있습니다. 

> [!IMPORTANT]
> HADR 구성 정상 모드-보조/대기 데이터베이스 인스턴스를 사용 하 여 IBM Db2에 대 한 특정 주 데이터베이스 인스턴스를 시작할 수 전에 실행 되어야 합니다.

이 문서에 설명 된 절차와 데모 목적에 대 한 SID 데이터베이스가 **PTR**합니다.

##### <a name="ibm-db2-hadr-check"></a>IBM Db2 HADR Check
HADR 구성 하는 한 번 이며, 상태 피어와 연결 됨 주 및 대기 노드.

<pre><code>
Execute command as db2&lt;sid&gt; db2pd -hadr -db &lt;SID&gt;

#Primary output:
# Database Member 0 -- Database PTR -- Active -- Up 1 days 01:51:38 -- Date 2019-02-06-15.35.28.505451
# 
#                             <b>HADR_ROLE = PRIMARY
#                           REPLAY_TYPE = PHYSICAL
#                         HADR_SYNCMODE = NEARSYNC
#                            STANDBY_ID = 1
#                         LOG_STREAM_ID = 0
#                            HADR_STATE = PEER
#                            HADR_FLAGS = TCP_PROTOCOL
#                   PRIMARY_MEMBER_HOST = azibmdb02
#                      PRIMARY_INSTANCE = db2ptr
#                        PRIMARY_MEMBER = 0
#                   STANDBY_MEMBER_HOST = azibmdb01
#                      STANDBY_INSTANCE = db2ptr
#                        STANDBY_MEMBER = 0
#                   HADR_CONNECT_STATUS = CONNECTED</b>
#              HADR_CONNECT_STATUS_TIME = 02/05/2019 13:51:47.170561 (1549374707)
#           HEARTBEAT_INTERVAL(seconds) = 15
#                      HEARTBEAT_MISSED = 0
#                    HEARTBEAT_EXPECTED = 6137
#                 HADR_TIMEOUT(seconds) = 60
#         TIME_SINCE_LAST_RECV(seconds) = 13
#              PEER_WAIT_LIMIT(seconds) = 0
#            LOG_HADR_WAIT_CUR(seconds) = 0.000
#     LOG_HADR_WAIT_RECENT_AVG(seconds) = 0.000025
#    LOG_HADR_WAIT_ACCUMULATED(seconds) = 434.595
#                   LOG_HADR_WAIT_COUNT = 223713
# SOCK_SEND_BUF_REQUESTED,ACTUAL(bytes) = 0, 46080
# SOCK_RECV_BUF_REQUESTED,ACTUAL(bytes) = 0, 374400
#             PRIMARY_LOG_FILE,PAGE,POS = S0000280.LOG, 15571, 27902548040
#             STANDBY_LOG_FILE,PAGE,POS = S0000280.LOG, 15571, 27902548040
#                   HADR_LOG_GAP(bytes) = 0
#      STANDBY_REPLAY_LOG_FILE,PAGE,POS = S0000280.LOG, 15571, 27902548040
#        STANDBY_RECV_REPLAY_GAP(bytes) = 0
#                      PRIMARY_LOG_TIME = 02/06/2019 15:34:39.000000 (1549467279)
#                      STANDBY_LOG_TIME = 02/06/2019 15:34:39.000000 (1549467279)
#               STANDBY_REPLAY_LOG_TIME = 02/06/2019 15:34:39.000000 (1549467279)
#          STANDBY_RECV_BUF_SIZE(pages) = 2048
#              STANDBY_RECV_BUF_PERCENT = 0
#            STANDBY_SPOOL_LIMIT(pages) = 0
#                 STANDBY_SPOOL_PERCENT = NULL
#                    STANDBY_ERROR_TIME = NULL
#                  PEER_WINDOW(seconds) = 300
#                       PEER_WINDOW_END = 02/06/2019 15:40:25.000000 (1549467625)
#              READS_ON_STANDBY_ENABLED = N

#Secondary output:
# Database Member 0 -- Database PTR -- Standby -- Up 1 days 01:46:43 -- Date 2019-02-06-15.38.25.644168
# 
#                             <b>HADR_ROLE = STANDBY
#                           REPLAY_TYPE = PHYSICAL
#                         HADR_SYNCMODE = NEARSYNC
#                            STANDBY_ID = 0
#                         LOG_STREAM_ID = 0
#                            HADR_STATE = PEER
#                            HADR_FLAGS = TCP_PROTOCOL
#                   PRIMARY_MEMBER_HOST = azibmdb02
#                      PRIMARY_INSTANCE = db2ptr
#                        PRIMARY_MEMBER = 0
#                   STANDBY_MEMBER_HOST = azibmdb01
#                      STANDBY_INSTANCE = db2ptr
#                        STANDBY_MEMBER = 0
#                   HADR_CONNECT_STATUS = CONNECTED</b>
#              HADR_CONNECT_STATUS_TIME = 02/05/2019 13:51:47.205067 (1549374707)
#           HEARTBEAT_INTERVAL(seconds) = 15
#                      HEARTBEAT_MISSED = 0
#                    HEARTBEAT_EXPECTED = 6186
#                 HADR_TIMEOUT(seconds) = 60
#         TIME_SINCE_LAST_RECV(seconds) = 5
#              PEER_WAIT_LIMIT(seconds) = 0
#            LOG_HADR_WAIT_CUR(seconds) = 0.000
#     LOG_HADR_WAIT_RECENT_AVG(seconds) = 0.000023
#    LOG_HADR_WAIT_ACCUMULATED(seconds) = 434.595
#                   LOG_HADR_WAIT_COUNT = 223725
# SOCK_SEND_BUF_REQUESTED,ACTUAL(bytes) = 0, 46080
# SOCK_RECV_BUF_REQUESTED,ACTUAL(bytes) = 0, 372480
#             PRIMARY_LOG_FILE,PAGE,POS = S0000280.LOG, 15574, 27902562173
#             STANDBY_LOG_FILE,PAGE,POS = S0000280.LOG, 15574, 27902562173
#                   HADR_LOG_GAP(bytes) = 0
#      STANDBY_REPLAY_LOG_FILE,PAGE,POS = S0000280.LOG, 15574, 27902562173
#        STANDBY_RECV_REPLAY_GAP(bytes) = 155
#                      PRIMARY_LOG_TIME = 02/06/2019 15:37:34.000000 (1549467454)
#                      STANDBY_LOG_TIME = 02/06/2019 15:37:34.000000 (1549467454)
#               STANDBY_REPLAY_LOG_TIME = 02/06/2019 15:37:34.000000 (1549467454)
#          STANDBY_RECV_BUF_SIZE(pages) = 2048
#              STANDBY_RECV_BUF_PERCENT = 0
#            STANDBY_SPOOL_LIMIT(pages) = 0
#                 STANDBY_SPOOL_PERCENT = NULL
#                    STANDBY_ERROR_TIME = NULL
#                  PEER_WINDOW(seconds) = 300
#                       PEER_WINDOW_END = 02/06/2019 15:43:19.000000 (1549467799)
#              READS_ON_STANDBY_ENABLED = N
</code></pre>



## <a name="db2-pacemaker-configuration"></a>Db2 Pacemaker 구성

Pacemaker를 사용 하 여 노드 오류가 발생할 경우 자동 장애 조치 하는 경우 Db2 인스턴스 및 Pacemaker를 적절 하 게 구성 해야 합니다. 이 섹션에서는이 유형의 구성 설명 합니다.

다음 항목 중 하나를 사용 하 여 붙습니다.

- **[A]**  -모든 노드에 적용
- **[1]**  -노드 1에만 적용 
- **[2]**  -노드 2에만 적용 됩니다.

<!-- Yast is a fixed term in Linux and not a spelling error -->

**[A]**  Pacemaker 구성에 대 한 필수 구성 요소:
1. 사용자 db2 사용 하 여 두 데이터베이스 서버를 종료할\<sid > db2stop를 사용 하 여
2. Db2에 대 한 변경 셸 환경\<sid > "/ bin/ksh"-사용자 Yast 도구를 사용 하는 것이 좋습니다 


### <a name="pacemaker-configuration"></a>Pacemaker 구성:

**[1]**  IBM Db2 HADR 특정 Pacemaker 구성
<pre><code># Put Pacemaker into maintenance mode
sudo crm configure property maintenance-mode=true
</code></pre>

**[1]**  IBM Db2 만들 리소스
<pre><code># Replace **bold strings** with your instance name db2sid, database SID and virtual IP address/Azure Load Balancer

sudo crm configure primitive rsc_Db2_db2ptr_<b>PTR</b> db2 \
        params instance="<b>db2ptr</b>" dblist="<b>PTR</b>" \
        op start interval="0" timeout="130" \
        op stop interval="0" timeout="120" \
        op promote interval="0" timeout="120" \
        op demote interval="0" timeout="120" \
        op monitor interval="30" timeout="60" \
        op monitor interval="31" role="Master" timeout="60"

# Configure virutal IP - same as Azure Load Balancer IP
sudo crm configure primitive rsc_ip_db2ptr_<b>PTR</b> IPaddr2 \
        op monitor interval="10s" timeout="20s" \
        params ip="<b>10.100.0.10</b>"

# Configure probe port for Azure load Balancer
sudo crm configure primitive rsc_nc_db2ptr_<b>PTR</b> anything \
        params binfile="/usr/bin/nc" cmdline_options="-l -k <b>62500</b>" \
        op monitor timeout="20s" interval="10" depth="0"

sudo crm configure group g_ip_db2ptr_<b>PTR</b> rsc_ip_db2ptr_<b>PTR</b> rsc_nc_db2ptr_<b>PTR</b>

sudo crm configure ms msl_Db2_db2ptr_<b>PTR</b> rsc_Db2_db2ptr_<b>PTR</b> \
        meta target-role="Started" notify="true"

sudo crm configure colocation col_db2_db2ptr_<b>PTR</b> inf: g_ip_db2ptr_<b>PTR</b>:Started msl_Db2_db2ptr_<b>PTR</b>:Master

sudo crm configure order ord_db2_ip_db2ptr_<b>PTR</b> inf: msl_Db2_db2ptr_<b>PTR</b>:promote g_ip_db2ptr_<b>PTR</b>:start

sudo crm configure rsc_defaults resource-stickiness=1000
sudo crm configure rsc_defaults migration-threshold=5000
</code></pre>

**[1]**  IBM Db2 시작 리소스-유지 관리 모드에서 Pacemaker 배치
<pre><code># Put Pacemaker out of maintenance-mode - that start IBM Db2
sudo crm configure property maintenance-mode=false</pre></code>

**[1]**  있는지 클러스터 상태가 정상 인지와 모든 리소스가 시작 됩니다. 리소스가 실행되는 노드는 중요하지 않습니다.
<pre><code>sudo crm status</code>

# <a name="2-nodes-configured"></a>2 개 노드 구성
# <a name="5-resources-configured"></a>5 개의 리소스 구성

# <a name="online--azibmdb01-azibmdb02-"></a>온라인: [azibmdb01 azibmdb02]

# <a name="full-list-of-resources"></a>리소스의 전체 목록:

#  <a name="stonith-sbd----stonithexternalsbd-started-azibmdb02"></a>stonith sbd (stonith:external / sbd): 시작된 azibmdb02
#  <a name="resource-group-gipdb2ptrptr"></a>Resource Group: g_ip_db2ptr_PTR
#      <a name="rscipdb2ptrptr--ocfheartbeatipaddr2-------started-azibmdb02"></a>rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       시작된 azibmdb02
#      <a name="rscncdb2ptrptr--ocfheartbeatanything------started-azibmdb02"></a>rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      시작된 azibmdb02
#  <a name="masterslave-set-msldb2db2ptrptr-rscdb2db2ptrptr"></a>마스터/슬레이브 집합: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
#      <a name="masters--azibmdb02-"></a>마스터: [azibmdb02]
#      <a name="slaves--azibmdb01-"></a>슬레이브: [azibmdb01]
</pre>

> [!IMPORTANT]
> 관리 Pacemaker 클러스터 Db2 인스턴스 Pacemaker 도구를 통해 수행 해야 합니다. Db2 명령 (예: db2stop)를 사용 하 여가 검색 Pacemaker 리소스의 오류로 합니다. 유지 관리의 경우 노드 또는 리소스를 유지 관리 모드에 넣을 수 있습니다 하 고 Pacemaker 리소스 모니터링을 일시 중단 됩니다 일반 db2 관리 명령을 사용할 수 있습니다.


### <a name="configure-azure-load-balancer"></a>Azure Load Balancer 구성
사용 하는 것이 좋습니다.는 [Azure 표준 Load Balancer SKU](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview)합니다.

1. (Azure portal)를 통해 Azure Load Balancer를 구성 합니다. 먼저 프런트 엔드 IP 풀을 만듭니다.

   1. Azure portal에서 Azure 부하 분산 장치를 열고, 선택 **프런트 엔드 IP 풀**, 선택한 **추가**합니다.
   2. 새 프런트 엔드 IP 풀의 이름을 입력 합니다 (예를 들어 **Db2 연결**).
   3. 설정 합니다 **할당** 하 **정적** IP 주소를 입력 하 고 **가상 IP** 시작 부분에 정의 합니다.
   4. **확인**을 선택합니다.
   5. 새 프런트 엔드 IP 풀을 만든 후, 풀 IP 주소를 적어 둡니다.

2. 백 엔드 풀을 만들려면 다음 단계는입니다.

   1. Azure portal에서 Azure 부하 분산 장치를 열고, 선택 **백 엔드 풀**, 선택한 **추가**합니다.
   2. 새 백 엔드 풀의 이름을 입력 합니다 (예를 들어 **Db2-백 엔드**).
   3. **가상 머신 추가**를 선택합니다.
   4. 3 단계에서 만든 IBM Db2 데이터베이스를 호스팅하는 가용성 집합/가상 컴퓨터를 선택 합니다.
   5. IBM Db2 클러스터의 가상 컴퓨터를 선택 합니다.
   6. **확인**을 선택합니다.

3. 세 번째 단계는 상태 프로브를 만듭니다.

   1. Azure portal에서 Azure 부하 분산 장치를 열고, 선택 **상태 프로브**, 선택한 **추가**합니다.
   2. 새 상태 프로브의 이름 입력 (예를 들어 **Db2 hp**).
   3. 선택 **TCP** 프로토콜 및 포트 **62500**합니다. 5로 설정된 **간격** 값, 2로 설정된 **비정상 임계값** 값을 유지합니다.
   4. **확인**을 선택합니다.

4. 부하 분산 규칙을 만듭니다.

   1. Azure portal에서 Azure Load Balancer를 열고 선택 **부하 분산 규칙**, 선택한 **추가**합니다.
   2. 새 부하 분산 장치 규칙의 이름을 입력 합니다 (예를 들어 **Db2 SID**).
   3. 프런트 엔드 IP 주소, 백 엔드 풀 및 이전에 만든 상태 프로브 선택 (예를 들어 **Db2 프런트 엔드**).
   4. 유지 된 **프로토콜** 로 설정 **TCP**, 포트를 입력 하 고 *데이터베이스 통신 포트*합니다.
   5. **유휴 상태 시간 제한**을 30분으로 증가시킵니다.
   6. **부동 IP를 사용하도록 설정**했는지 확인합니다.
   7. **확인**을 선택합니다.


### <a name="make-changes-to-sap-profiles-to-use-virtual-ip-for-connection"></a>연결에 대 한 가상 IP를 사용 하는 SAP 프로필 변경
SAP 응용 프로그램 계층 정의 및 HADR 구성의 기본 인스턴스에 연결 하려면 Azure Load Balancer에 대해 구성 된 가상 IP 주소를 사용 해야 합니다. 다음 변경이 필요 합니다.

/sapmnt/\<SID>/profile/DEFAULT.PFL
<pre><code>SAPDBHOST = db-virt-hostname
j2ee/dbhost = db-virt-hostname
</code></pre>

/sapmnt/\<SID>/global/db6/db2cli.ini
<pre><code>Hostname=db-virt-hostname
</code></pre>



## <a name="install-primary-and-dialog-application-servers"></a>기본 설치 및 대화 상자 응용 프로그램 서버

설치 주 서버와 Db2 HADR 구성에 대 한 대화 응용 프로그램 서버를 해야 사용 하는 경우 선택한 가상 호스트 이름을 구성 합니다. 

Db2 HADR 구성을 만들기 전에 설치를 수행한 경우 이전 단락에서와 같이 SAP Java 스택에 대 한 설명 된 대로 변경할 수 있도록 해야 합니다.

### <a name="abapjava-or-java-stack-systems-jdbc-url-check"></a>ABAP + Java 또는 Java 스택 시스템 JDBC URL 확인

J2EE 구성 도구를 사용 하 여 확인 하거나 JDBC URL을 업데이트 합니다. J2EE 구성 도구는 그래픽 도구, 해야 결과적 **X 서버** 설치:
 
1. J2EE 인스턴스의 기본 응용 프로그램 서버에 로그인 하 고 실행 합니다.
    <pre><code>sudo /usr/sap/*SID*/*Instance*/j2ee/configtool/configtool.sh</code></pre>
2. 왼쪽된 프레임에서 보안 저장소를 선택 합니다.
2. 오른쪽 프레임 선택 키jdbc/풀/<SAPSID>/url 합니다.
2. 가상 호스트 이름 JDBC URL에 호스트 이름 변경 <pre><code>jdbc:db2://db-virt-hostname:5912/TSP:deferPrepares=0</code></pre>
5. 선택 추가 합니다.
5. 변경 내용을 저장 하려면 왼쪽된 위 모퉁이에 있는 디스크 아이콘을 클릭 합니다.
5. 구성 도구를 닫습니다.
5. Java 인스턴스를 다시 시작 합니다.

## <a name="configuration-of-log-archiving-for-hadr-setup"></a>HADR 설치에 대 한 로그 보관 구성
HADR 설치에 대 한 보관 Db2 로그를 구성 하려면 주 및 대기 데이터베이스에 모든 로그 보관 위치에서 자동 로그 검색 기능을 구성 하는 것이 좋습니다. 주 및 대기 데이터베이스 인스턴스 수 로그 파일을 보관 하는 데는 데이터베이스 중 하나에 모든 로그 보관 위치에서 로그 보관 파일을 검색할 수 있어야 합니다. 

보관 로그만 주 데이터베이스에서 수행 됩니다. HADR 데이터베이스 서버의 역할을 변경 하는 경우, 오류가 발생 하는 경우 새 주 데이터베이스는 로그를 보관 하는 일을 담당 합니다. 다른 로그 보관 위치를 설정한 경우 로그를 두 번 보관할 수 있습니다 및 로컬 또는 원격 후속 작업의 경우 이전 주 서버에서 새 주 서버의 활성 로그 위치에 보관 된 로그를 수동으로 복사 해야 할 수도 있습니다.

두 노드 모두에서 로그 기록 되는 일반적인 NFS 공유를 구성 하는 것이 좋습니다. NFS를 항상 사용 가능 해야 합니다. 

전송의 경우 프로필 디렉터리가 사용 되는 기존 항상 사용 가능한 NFS를 사용할 수 있습니다. 읽기:

- [SUSE Linux Enterprise Server의 Azure VM에 있는 NFS의 고가용성][nfs-ha] 
- [SAP 응용 프로그램에 대 한 Azure NetApp 파일을 사용 하 여 SUSE Linux Enterprise Server의 Azure Vm에서 SAP NetWeaver에 대 한 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files) 사용 하는 방법은 [Azure NetApp 파일](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction) NFS 공유를 만들려면


## <a name="test-the-cluster-setup"></a>클러스터 설정 테스트

이 섹션에서는 Db2 HADR 설치 프로그램을 테스트 하는 방법을 설명 합니다. **모든 테스트 root 사용자로 로그인 하는 것으로 가정** 에서 실행 되 고 IBM Db2 주 **azibmdb01** 가상 머신.

모든 테스트 사례에 대 한 초기 상태는 여기에서 설명한: (crm_mon-r 또는 crm 상태)

- **상태를 crm** 실행 시 스냅숏 Pacemaker 상태가 
- **crm_mon-r** 은 Pacemaker 상태의 연속 출력

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Stopped
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Stopped
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     rsc_Db2_db2ptr_PTR      (ocf::heartbeat:db2):   Promoting azibmdb01
     Slaves: [ azibmdb02 ]
</code></pre>

SAP 시스템의 원래 상태는 트랜잭션 DBACOCKPIT에 설명 되어 있습니다 구성-->와 같은 개요-->:

![DBACockpit - Pre Migration](./media/dbms-guide-ha-ibm/hadr-sap-mgr-org.png)




### <a name="test-takeover-of-ibm-db2"></a>IBM Db2의 인수를 테스트 합니다.


> [!IMPORTANT] 
> 테스트를 시작 하기 전에 Pacemaker 모든 실패 한 작업 (crm 상태) 되지 않은 제약 조건이 없는 위치 (leftovers 마이그레이션 테스트) 및 IBM Db2 HADR 동기화가 작동 하는지 확인 합니다. 사용자 db2를 사용 하 여 확인\<sid > <pre><code>db2pd -hadr -db \<DBSID></code></pre>


다음 명령을 실행 하 여 기본 Db2 데이터베이스를 실행 하는 노드 마이그레이션:
<pre><code>crm resource migrate msl_<b>Db2_db2ptr_PTR</b> azibmdb02</code></pre>

마이그레이션이 완료 되 면 crm 상태 출력은 다음과 같습니다.
<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Slaves: [ azibmdb01 ]
</code></pre>

SAP 시스템의 원래 상태는 트랜잭션 DBACOCKPIT에 설명 되어 있습니다 구성-->와 같은 개요-->: ![DBACockpit - Post Migration](./media/dbms-guide-ha-ibm/hadr-sap-mgr-post.png)

리소스 마이그레이션을 사용 하 여 crm 리소스 "마이그레이션"에 위치 제약 조건을 만듭니다. 위치 제약 조건은 삭제 해야 합니다. 위치 제약 조건 삭제 되는 경우 다음 리소스 장애 복구할 수 또는 원치 않는 takeovers 발생할 수 있습니다. 

리소스를 마이그레이션 돌아갑니다 **azibmdb01** 위치 제약 조건의 선택을 취소 하 고
<pre><code>crm resource migrate msl_<b>Db2_db2ptr_PTR</b> azibmdb01
crm resource clear msl_<b>Db2_db2ptr_PTR</b>
</code></pre>


- crm 리소스 마이그레이션 < res_name > <host> -위치 제약 조건을 만들고 인수를 사용 하 여 문제가 발생할 수 있습니다
- crm 리소스 지우기 < res_name >-지웁니다 위치 제약 조건
- crm 리소스 정리 < res_name >-리소스의 모든 오류를 지웁니다.


### <a name="test-the-fencing-agent"></a>펜싱 에이전트를 테스트 합니다.

이 경우 SBD 펜싱 SUSE Linux를 사용 하 여 사용을 위한 권장 테스트 합니다.

<pre><code>
azibmdb01:~ # ps -ef|grep sbd
root       2374      1  0 Feb05 ?        00:00:17 sbd: inquisitor
root       2378   2374  0 Feb05 ?        00:00:40 sbd: watcher: /dev/disk/by-id/scsi-36001405fbbaab35ee77412dacb77ae36 - slot: 0 - uuid: 27cad13a-0bce-4115-891f-43b22cfabe65
root       2379   2374  0 Feb05 ?        00:01:51 sbd: watcher: Pacemaker
root       2380   2374  0 Feb05 ?        00:00:18 sbd: watcher: Cluster

azibmdb01:~ # kill -9 2374
</code></pre>

클러스터 노드 **azibmdb01** 를 다시 부팅 해야 합니다. IBM Db2 주 HADR 역할로 이동할 것 **azibmdb02**합니다. 때 **azibmdb01** 인스턴스는 인스턴스를 보조 데이터베이스의 역할을 이동 하 고 Db2 온라인으로 백업 합니다. 

Pacemaker 서비스를 다시 부팅된 이전 주에 자동으로 시작 하지 않는 경우 사용 하 여 수동으로 시작 하도록 확인 합니다.

<code><pre>sudo service pacemaker start</code></pre>

### <a name="test-a-manual-takeover"></a>인수를 수동 테스트

수동 인수에서 Pacemaker 서비스를 중지 하 여 테스트할 수 있습니다 **azibmdb01** 노드:
<pre><code>service pacemaker stop</code></pre>

status on **azibmdb02**
<pre><code>
2 nodes configured
5 resources configured

Online: [ azibmdb02 ]
OFFLINE: [ azibmdb01 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Stopped: [ azibmdb01 ]
</code></pre>

장애 조치 후 다시 시작할 수 있습니다 서비스에 **azibmdb01**합니다.
<pre><code>service pacemaker start</code></pre>


### <a name="kill-db2-process-on-the-node-running-the-hadr-primary-database"></a>Db2 HADR 주 데이터베이스를 실행 하는 노드 프로세스를 종료 합니다.

<pre><code>#Kill main db2 process - db2sysc
azibmdb01:~ # ps -ef|grep db2s
db2ptr    34598  34596  8 14:21 ?        00:00:07 db2sysc 0

azibmdb01:~ # kill -9 34598
</code></pre>

Db2 인스턴스는 실패 하 고 Pacemaker는 다음 상태 보고:

<pre><code>
2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Stopped
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Stopped
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Slaves: [ azibmdb02 ]
     Stopped: [ azibmdb01 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_demote_0 on azibmdb01 'unknown error' (1): call=157, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:28:19 2019', queued=40ms, exec=223ms


</code></pre>

Pacemaker 같은 노드 또는 인스턴스화된 보조 데이터베이스를 실행 하는 노드에 대 한 장애 조치에서 Db2 기본 데이터베이스 인스턴스를 다시 시작 하 고 오류가 보고 됩니다.

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_demote_0 on azibmdb01 'unknown error' (1): call=157, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:28:19 2019', queued=40ms, exec=223ms
</code></pre>


### <a name="kill-db2-process-on-node-that-runs-the-secondary-database-instance"></a>보조 데이터베이스 인스턴스를 실행 하는 노드에서 Db2 프로세스 중지

<pre><code>azibmdb02:~ # ps -ef|grep db2s
db2ptr    65250  65248  0 Feb11 ?        00:09:27 db2sysc 0

azibmdb02:~ # kill -9</code></pre>

노드 가져옵니다를 명시 하지 못했으며 오류 보고
<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     rsc_Db2_db2ptr_PTR      (ocf::heartbeat:db2):   FAILED azibmdb02
     Masters: [ azibmdb01 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_monitor_30000 on azibmdb02 'not running' (7): call=144, status=complete, exitreason='',
last-rc-change='Tue Feb 12 14:36:59 2019', queued=0ms, exec=0ms</code></pre>

Db2 인스턴스 가져오기 전에 할당 해야 보조 역할로 다시 시작

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_monitor_30000 on azibmdb02 'not running' (7): call=144, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:36:59 2019', queued=0ms, exec=0ms</code></pre>



### <a name="stop-db-via-db2stop-force-on-node-running-the-hadr-primary-database-instance"></a>HADR 주 데이터베이스 인스턴스를 실행 하는 노드에서 db2stop 강제를 통해 DB 중지

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]</code></pre>

사용자 db2로\<sid > 명령을 db2stop 강제 실행 합니다.
<pre><code>azibmdb01:~ # su - db2ptr
azibmdb01:db2ptr> db2stop force</code></pre>

오류를 발견 했습니다
<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Stopped
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Stopped
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     rsc_Db2_db2ptr_PTR      (ocf::heartbeat:db2):   FAILED azibmdb01
     Slaves: [ azibmdb02 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_demote_0 on azibmdb01 'unknown error' (1): call=201, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:45:25 2019', queued=1ms, exec=150ms</code></pre>

Db2 HADR 보조 데이터베이스 인스턴스를 주 역할로 승격
<pre><code> nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Stopped: [ azibmdb01 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_start_0 on azibmdb01 'unknown error' (1): call=205, stat
us=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:45:27 2019', queued=0ms, exec=865ms</pre></code>


### <a name="crash-vm-with-restart-on-node-running-the-hadr-primary-database-instance"></a>HADR 주 데이터베이스 인스턴스를 실행 하는 노드에서 다시 시작 하 여 VM 충돌

<pre><code>#Linux kernel panic - with OS restart
azibmdb01:~ # echo b > /proc/sysrq-trigger</code></pre>

Pacemaker는 주 인스턴스 역할에 보조 인스턴스를 승격 합니다. Vm 보조 역할에 이전 주 인스턴스로 이동 하 고 모든 서비스는 VM 다시 부팅 후에 완전히 복원 됩니다.

<pre><code> nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]</code></pre>



### <a name="crash-the-vm-running-the-hadr-primary-database-instance-with-halt"></a>"중지"를 사용 하 여 HADR 주 데이터베이스 인스턴스를 실행 하는 VM 충돌

<pre><code>#Linux kernel panic - halts OS
azibmdb01:~ # echo b > /proc/sysrq-trigger</code></pre>

이러한 경우 Pacemaker는 기본 데이터베이스 인스턴스를 실행 하는 노드 응답 하지 않음을 감지 합니다.

<pre><code>2 nodes configured
5 resources configured

Node azibmdb01: UNCLEAN (online)
Online: [ azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]</code></pre>

다음 단계에 대 한 확인 하는 것을 **분할 브레인** 상황입니다. 리소스의 장애 조치를 실행할 것 남아 있는 노드를 기본 데이터베이스 인스턴스를 마지막으로 실행 하는 노드에 다운 인지
<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb02 ]
OFFLINE: [ azibmdb01 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Stopped: [ azibmdb01 ] </code></pre>


노드를 "중지"의 경우 실패 한 노드를 Azure 관리 도구 (PowerShel, 포털, azure Cli,...)를 통해 다시 시작 해야 합니다. 실패 한 노드는 다시 온라인 상태가 되 면 보조 역할로 Db2 인스턴스를 시작 하려고 합니다.

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Slaves: [ azibmdb01 ]</code></pre>

## <a name="next-steps"></a>다음 단계
이 설명서를 참조 하세요.

- [고가용성 아키텍처 및 SAP NetWeaver에 대 한 시나리오](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios)
- [SUSE Linux 입력에서 Pacemaker 설정
- pri
- Azure에서 서버 se] (https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker)

     

