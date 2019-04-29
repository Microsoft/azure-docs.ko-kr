---
title: SAP 애플리케이션용 SUSE Linux Enterprise Server의 SAP NetWeaver에 대한 Azure Virtual Machines 고가용성 | Microsoft Docs
description: SAP 애플리케이션용 SUSE Linux Enterprise Server의 SAP NetWeaver에 대한 고가용성 가이드
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: mssedusch
manager: jeconnoc
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/15/2019
ms.author: sedusch
ms.openlocfilehash: 90ec7cf4964440d39b3f69eb9ae9708eaafe3748
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60710701"
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms-on-suse-linux-enterprise-server-for-sap-applications"></a>SAP 애플리케이션용 SUSE Linux Enterprise Server의 Azure VM에 있는 SAP NetWeaver에 대한 고가용성

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[2205917]:https://launchpad.support.sap.com/#/notes/2205917
[1944799]:https://launchpad.support.sap.com/#/notes/1944799
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[suse-ha-guide]:https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
[suse-drbd-guide]:https://www.suse.com/documentation/sle-ha-12/singlehtml/book_sleha_techguides/book_sleha_techguides.html
[suse-ha-12sp3-relnotes]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP3/

[template-multisid-xscs]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[template-converged]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-converged-md%2Fazuredeploy.json
[template-file-server]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-file-server-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability.md
[nfs-ha]:high-availability-guide-suse-nfs.md

이 문서에서는 가상 머신을 배포 및 구성하고 클러스터 프레임워크 및 고가용성 SAP NetWeaver 7.50 시스템을 설치하는 방법을 설명합니다.
그리고 예제 구성, 설치 명령 등을 소개합니다. 00 ASCS 인스턴스 번호, 02 ERS 인스턴스 번호 및 NW1 SAP 시스템 ID를 사용합니다. 예제에 포함된 리소스(예: 가상 머신, 가상 네트워크)의 이름은 SAP 시스템 ID가 NW1인 [수렴형 템플릿][template-converged]을 사용하여 리소스를 만들었다고 가정합니다.

먼저 다음 SAP 참고와 문서 읽기

* SAP Note [1928533], 다음 항목을 포함합니다.
  * SAP 소프트웨어 배포에 지원되는 Azure VM 크기 목록
  * Azure VM 크기에 대한 중요한 용량 정보
  * 지원되는 SAP 소프트웨어 및 운영 체제(OS)와 데이터베이스 조합
  * Microsoft Azure에서 Windows 및 Linux에 필요한 SAP 커널 버전

* SAP Note [2015553]는 Azure에서 SAP을 지원하는 SAP 소프트웨어 배포에 대한 필수 구성 요소를 나열합니다.
* SAP Note [2205917]에는 SAP 애플리케이션용 SUSE Linux Enterprise Server에 권장되는 OS 설정이 나와 있습니다.
* SAP Note [1944799]에는 SAP 애플리케이션용 SUSE Linux Enterprise Server에 대한 SAP HANA 지침이 나와 있습니다.
* SAP Note [2178632]는 Azure에서 SAP에 대해 보고된 모든 모니터링 메트릭에 대한 자세한 정보를 포함하고 있습니다.
* SAP Note [2191498]는 Azure에서 Linux에 필요한 SAP Host Agent 버전을 포함하고 있습니다.
* SAP Note [2243692]는 Azure에서 Linux의 SAP 라이선스에 대한 정보를 포함하고 있습니다.
* SAP Note [1984787]은 SUSE LINUX Enterprise Server 12에 대한 일반 정보를 포함하고 있습니다.
* SAP Note [1999351]은 SAP용 Azure 고급 모니터링 확장을 위한 추가 문제 해결 정보를 포함하고 있습니다.
* [SAP Community WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes)는 Linux에 필요한 모든 SAP Note를 포함하고 있습니다.
* [Linux에서 SAP용 Azure Virtual Machines 계획 및 구현][planning-guide]
* [Linux에서 SAP용 Azure Virtual Machines 배포][deployment-guide]
* [Linux에서 SAP용 Azure Virtual Machines DBMS 배포][dbms-guide]
* [SUSE SAP HA 모범 사례 가이드][suse-ha-guide] 이 가이드에는 Netweaver HA 및 SAP HANA 시스템 복제 온-프레미스를 설정하는 데 필요한 모든 정보가 포함됩니다. 이 가이드를 일반 기준으로 사용하세요. 여기서 훨씬 더 자세한 정보를 제공합니다.
* [SUSE High Availability Extension 12 SP3 릴리스 정보][suse-ha-12sp3-relnotes]

## <a name="overview"></a>개요

SAP NetWeaver의 가용성을 높이려면 NFS 서버가 필요합니다. NFS 서버는 별도의 클러스터에서 구성되며, 여러 SAP 시스템에서 사용할 수 있습니다.

![SAP NetWeaver 고가용성 개요](./media/high-availability-guide-suse/ha-suse.png)

NFS 서버, SAP NetWeaver ASCS, SAP NetWeaver SCS, SAP NetWeaver ERS 및 SAP HANA 데이터베이스는 가상 호스트 이름 및 가상 IP 주소를 사용합니다. Azure에서는 가상 IP 주소를 사용하려면 부하 분산 장치가 필요합니다. 아래 목록에서는(A)SCS 및 ERS 부하 분산 장치에 대한 구성이 나와 있습니다.

### <a name="ascs"></a>(A)SCS

* 프런트 엔드 구성
  * IP 주소: 10.0.0.7
* 백 엔드 구성
  * (A)SCS/ERS 클러스터의 일부분이어야 하는 모든 가상 머신의 주 네트워크 인터페이스에 연결됨
* 프로브 포트
  * 포트 620<strong>&lt;nr&gt;</strong>
* 로드 
* 분산 규칙
  * 32<strong>&lt;nr&gt;</strong> TCP
  * 36<strong>&lt;nr&gt;</strong> TCP
  * 39<strong>&lt;nr&gt;</strong> TCP
  * 81<strong>&lt;nr&gt;</strong> TCP
  * 5<strong>&lt;nr&gt;</strong>13 TCP
  * 5<strong>&lt;nr&gt;</strong>14 TCP
  * 5<strong>&lt;nr&gt;</strong>16 TCP

### <a name="ers"></a>ERS

* 프런트 엔드 구성
  * IP 주소: 10.0.0.8
* 백 엔드 구성
  * (A)SCS/ERS 클러스터의 일부분이어야 하는 모든 가상 머신의 주 네트워크 인터페이스에 연결됨
* 프로브 포트
  * 포트 621<strong>&lt;nr&gt;</strong>
* 부하 분산 규칙
  * 33<strong>&lt;nr&gt;</strong> TCP
  * 5<strong>&lt;nr&gt;</strong>13 TCP
  * 5<strong>&lt;nr&gt;</strong>14 TCP
  * 5<strong>&lt;nr&gt;</strong>16 TCP

## <a name="setting-up-a-highly-available-nfs-server"></a>고가용성 NFS 서버 설정

SAP NetWeaver에는 전송 및 프로필 디렉터리에 대한 공유 저장소가 필요합니다. SAP NetWeaver용 NFS 서버를 설정하는 방법은 [SUSE Linux Enterprise Server의 Azure VM에 있는 NFS의 고가용성][nfs-ha]을 참조하세요.

## <a name="setting-up-ascs"></a>(A)SCS 설정

GitHub의 Azure 템플릿을 사용하여 필요한 Azure 리소스(가상 머신, 가용성 집합 및 부하 분산 장치 등)를 모두 배포하거나 수동으로 리소스를 배포할 수 있습니다.

### <a name="deploy-linux-via-azure-template"></a>Azure 템플릿을 통해 Linux 배포

Azure Marketplace에는 새 가상 머신을 배포하는 데 사용할 수 있는 SAP Applications 12용 SUSE Linux Enterprise Server의 이미지가 포함되어 있습니다. Marketplace 이미지는 SAP NetWeaver용 리소스 에이전트를 포함합니다.

GitHub에서 빠른 시작 템플릿 중 하나를 사용하여 필요한 모든 리소스를 배포할 수 있습니다. 템플릿에서 가상 머신, 부하 분산 장치, 가용성 집합 등을 배포합니다. 다음 단계를 따라 템플릿을 배포합니다.

1. 엽니다는 [ASCS/SCS 다중 SID 템플릿을] [ template-multisid-xscs] 또는 [수렴 형 템플릿] [ template-converged] Azure portal에서 합니다. 
   ASCS/SCS 템플릿은 부하 분산 규칙만 만드는 SAP NetWeaver ASCS/SCS 및 ERS (Linux 전용) 인스턴스의 반면 수렴 형된 템플릿은 데이터베이스 (예: Microsoft SQL Server 또는 SAP HANA)에 대 한 부하 분산 규칙도 만듭니다. SAP NetWeaver 기반 시스템을 설치할 계획이며 동일한 컴퓨터에 데이터베이스도 설치하려는 경우에는 [수렴형 템플릿][template-converged]을 사용합니다.
1. 다음 매개 변수를 입력합니다.
   1. 리소스 접두사(ASCS/SCS 다중 SID 템플릿에만 해당)  
      사용할 접두사를 입력합니다. 이 값은 배포되는 리소스의 접두사로 사용됩니다.
   3. SAP 시스템 ID(수렴형 템플릿에만 해당)  
      설치하려는 SAP 시스템의 SAP 시스템 ID를 입력합니다. 이 ID는 배포되는 리소스의 접두사로 사용됩니다.
   4. 스택 유형 -  
      SAP NetWeaver 스택 유형 선택
   5. OS 종류 -  
      Linux 배포판 중 하나를 선택합니다. 이 예에서는 SLES 12 BYOS 선택
   6. Db 형식  
      HANA 선택
   7. Sap 시스템 크기입니다.  
      새 시스템에서 제공하는 SAP의 양입니다. 시스템에 필요한 SAP의 수를 모를 경우 SAP 기술 파트너 또는 시스템 통합자에 문의하세요.
   8. 시스템 가용성 -  
      HA를 선택합니다.
   9. 관리자 사용자 이름 및 관리자 암호 -  
      컴퓨터에 로그온하는 데 사용할 수 있게 만들어진 새 사용자입니다.
   10. 서브넷 ID  
   서브넷이 VM을 할당하도록 정의된 기존 VNet에 VM을 배포하려는 경우 해당 서브넷의 ID 이름을 지정합니다. ID는 대개 /subscriptions/**&lt;구독 ID&gt;**/resourceGroups/**&lt;리소스 그룹 이름&gt;**/providers/Microsoft.Network/virtualNetworks/**&lt;가상 네트워크 이름&gt;**/subnets/**&lt;서브넷 이름&gt;** 과 같은 형식입니다.

### <a name="deploy-linux-manually-via-azure-portal"></a>Azure Portal을 통해 Linux를 수동으로 배포

먼저 NFS 클러스터에 대한 가상 머신을 만들어야 합니다. 그런 다음, 부하 분산 장치를 만들고 백 엔드 풀의 가상 머신을 사용합니다.

1. 리소스 그룹 만들기
1. Virtual Network 만들기
1. 가용성 집합 만들기  
   최대 업데이트 도메인 설정
1. Virtual Machine 1 만들기  
   SLES4SAP 12 SP1 이상을 사용합니다. 이 예제에서는 SLES4SAP 12 SP1 이미지(https://portal.azure.com/#create/SUSE.SUSELinuxEnterpriseServerforSAPApplications12SP1PremiumImage-ARM)를 사용합니다.  
   SAP Applications 12 SP1용 SLES 사용  
   이전에 만든 가용성 집합 선택  
1. Virtual Machine 2 만들기  
   SLES4SAP 12 SP1 이상을 사용합니다. 이 예제에서는 SLES4SAP 12 SP1 이미지(https://portal.azure.com/#create/SUSE.SUSELinuxEnterpriseServerforSAPApplications12SP1PremiumImage-ARM)를 사용합니다.  
   SAP Applications 12 SP1용 SLES 사용  
   이전에 만든 가용성 집합 선택  
1. 두 가상 머신 모두에 하나 이상의 데이터 디스크 추가  
   데이터 디스크는 /usr/sap/`<SAPSID`> 디렉터리에 사용됩니다
1. 부하 분산 장치(내부) 만들기  
   1. 프런트 엔드 IP 주소 만들기
      1. IP 주소: 10.0.0.7(ASCS용)
         1. 부하 분산 장치 열기, 프런트 엔드 IP 풀 선택 및 추가 클릭
         1. 새 프런트 엔드 IP 풀의 이름 입력(예: **nw1-ascs-frontend**)
         1. 할당을 정적으로 설정하고 IP 주소 입력(예: **10.0.0.7**)
         1. 확인 클릭
      1. IP 주소: 10.0.0.8(ASCS ERS용)
         * 위의 단계를 반복하여 ERS에 대한 IP 주소를 만듭니다(예: **10.0.0.8** 및 **nw1-aers-backend**).
   1. 백 엔드 풀 만들기
      1. ASCS에 대한 백 엔드 풀 만들기
         1. 부하 분산 장치를 열고 백 엔드 풀을 선택한 다음 추가 클릭
         1. 새 백 엔드 풀의 이름 입력(예: **nw1-ascs-backend**)
         1. 가상 머신 추가 클릭
         1. 이전에 만든 가용성 집합 선택
         1. (A)SCS 클러스터의 가상 머신 선택
         1. 확인 클릭
      1. ASCS ERS에 대한 백 엔드 풀 만들기
         * 위의 단계를 반복하여 ERS에 대한 백 엔드 풀을 만듭니다(예: **nw1-aers-backend**).
   1. 상태 프로브 만들기
      1. 포트: 620**00**(ASCS용)
         1. 부하 분산 장치를 열고 상태 프로브를 선택한 다음 추가 클릭
         1. 새 상태 프로브의 이름 입력(예: **nw1-ascs-hp**)
         1. 프로토콜로 TCP를 선택하고, 620**00** 포트를 선택한 다음, 간격은 5, 비정상 임계값은 2로 유지
         1. 확인 클릭
      1. 포트: 621**02**(ASCS ERS용)
         * 위의 단계를 반복하여 ERS에 대한 상태 프로브를 만듭니다(예: 621**02** 및 **nw1-aers-hp**).
   1. 부하 분산 규칙
      1. TCP: 32**00**(ASCS용)
         1. 부하 분산 장치를 열고, 부하 분산 규칙을 설정한 다음, 추가 클릭
         1. 새 부하 분산 장치 규칙의 이름 입력(예: **nw1-lb-3200**)
         1. 이전에 만든 프런트 엔드 IP 주소, 백 엔드 풀 및 상태 프로브 선택(예: **nw1-ascs-frontend**)
         1. 프로토콜로 **TCP**를 유지하고. 포트로 **3200** 입력
         1. 유휴 상태 시간 제한을 30분으로 증가
         1. **부동 IP를 사용하도록 설정**
         1. 확인 클릭
      1. ASCS에 대한 추가 포트
         * ASCS의 경우 36**00**, 39**00**, 81**00**, 5**00**13, 5**00**14, 5**00**16 포트 및 TCP에 대해 위의 단계를 반복합니다.
      1. ASCS ERS에 대한 추가 포트
         * ASCS ERS의 경우 33**02**, 5**02**13, 5**02**14, 5**02**16 포트 및 TCP에 대해 위의 단계를 반복합니다.

> [!IMPORTANT]
> Azure Load Balancer 뒤에 배치 하는 Azure Vm에서 TCP 타임 스탬프를 사용 하지 마십시오. TCP 타임 스탬프를 사용 하도록 설정 하면 상태 프로브 실패 합니다. 매개 변수를 설정 **net.ipv4.tcp_timestamps** 하 **0**합니다. 자세한 내용은 참조 하십시오 [부하 분산 장치 상태 프로브](https://docs.microsoft.com/en-us/azure/load-balancer/load-balancer-custom-probe-overview)합니다.

### <a name="create-pacemaker-cluster"></a>Pacemaker 클러스터 만들기

[Azure의 SUSE Linux Enterprise Server에서 Pacemaker 설정](high-availability-guide-suse-pacemaker.md) 단계에 따라 이 (A)SCS 서버에 대한 기본 Pacemaker 클러스터를 만듭니다.

### <a name="installation"></a>설치

다음 항목에는 접두사 **[A]**(모든 노드에 적용됨), **[1]**(노드 1에만 적용됨), **[2]**(노드 2에만 적용됨) 접두사가 표시되어 있습니다.

1. **[A]** SUSE 커넥터 설치

   <pre><code>sudo zypper install sap-suse-cluster-connector
   </code></pre>

   > [!NOTE]
   > 클러스터 노드의 호스트 이름에는 대시를 사용하지 마세요. 대시를 사용하면 클러스터가 작동하지 않습니다. 이것은 알려진 제한 사항이며 SUSE는 수정 프로그램을 개발 중입니다. 수정 프로그램은 sap-suse-cloud-connector 패키지의 패치로 릴리스될 예정입니다.

   SAP SUSE 클러스터 커넥터의 새 버전을 설치했는지 확인합니다. 기존 버전은 sap_suse_cluster_connector라고 하며 새 버전은 **sap suse-cluster-connector**라고 합니다.

   ```
   sudo zypper info sap-suse-cluster-connector
   
   Information for package sap-suse-cluster-connector:
   ---------------------------------------------------
   Repository     : SLE-12-SP3-SAP-Updates
   Name           : sap-suse-cluster-connector
   <b>Version        : 3.0.0-2.2</b>
   Arch           : noarch
   Vendor         : SUSE LLC <https://www.suse.com/>
   Support Level  : Level 3
   Installed Size : 41.6 KiB
   <b>Installed      : Yes</b>
   Status         : up-to-date
   Source package : sap-suse-cluster-connector-3.0.0-2.2.src
   Summary        : SUSE High Availability Setup for SAP Products
   ```

1. **[A]** SAP 리소스 에이전트 업데이트  
   
   이 문서에서 설명하는 새 구성을 사용하려면 리소스 에이전트 패키지용 패치가 필요합니다. 다음 명령을 사용하여 패치가 이미 설치되었는지를 확인할 수 있습니다.

   <pre><code>sudo grep 'parameter name="IS_ERS"' /usr/lib/ocf/resource.d/heartbeat/SAPInstance
   </code></pre>

   다음과 같은 출력이 표시되어야 합니다.

   <pre><code>&lt;parameter name="IS_ERS" unique="0" required="0"&gt;
   </code></pre>

   grep 명령을 실행하여 IS_ERS 매개 변수를 찾을 수 없는 경우에는 [SUSE 다운로드 페이지](https://download.suse.com/patch/finder/#bu=suse&familyId=&productId=&dateRange=&startDate=&endDate=&priority=&architecture=&keywords=resource-agents)에 나와 있는 패치를 설치해야 합니다.

   <pre><code># example for patch for SLES 12 SP1
   sudo zypper in -t patch SUSE-SLE-HA-12-SP1-2017-885=1
   # example for patch for SLES 12 SP2
   sudo zypper in -t patch SUSE-SLE-HA-12-SP2-2017-886=1
   </code></pre>

1. **[A]** 호스트 이름 확인 설정

   DNS 서버를 사용하거나 모든 노드의 /etc/hosts를 수정할 수 있습니다. 이 예에서는 /etc/hosts 파일 사용 방법을 보여줍니다.
   다음 명령에서 IP 주소와 호스트 이름 바꾸기

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   다음 줄을 /etc/hosts에 삽입합니다. 환경에 맞게 IP 주소와 호스트 이름 변경   

   <pre><code># IP address of the load balancer frontend configuration for NFS
   <b>10.0.0.4 nw1-nfs</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS
   <b>10.0.0.7 nw1-ascs</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS ERS
   <b>10.0.0.8 nw1-aers</b>
   # IP address of the load balancer frontend configuration for database
   <b>10.0.0.13 nw1-db</b>
   </code></pre>

## <a name="prepare-for-sap-netweaver-installation"></a>SAP NetWeaver 설치 준비

1. **[A]** 공유 디렉터리 만들기

   <pre><code>sudo mkdir -p /sapmnt/<b>NW1</b>
   sudo mkdir -p /usr/sap/trans
   sudo mkdir -p /usr/sap/<b>NW1</b>/SYS
   sudo mkdir -p /usr/sap/<b>NW1</b>/ASCS<b>00</b>
   sudo mkdir -p /usr/sap/<b>NW1</b>/ERS<b>02</b>
   
   sudo chattr +i /sapmnt/<b>NW1</b>
   sudo chattr +i /usr/sap/trans
   sudo chattr +i /usr/sap/<b>NW1</b>/SYS
   sudo chattr +i /usr/sap/<b>NW1</b>/ASCS<b>00</b>
   sudo chattr +i /usr/sap/<b>NW1</b>/ERS<b>02</b>
   </code></pre>

1. **[A]** autofs 구성

   <pre><code>sudo vi /etc/auto.master
   
   # Add the following line to the file, save and exit
   +auto.master
   /- /etc/auto.direct
   </code></pre>

   아래 코드를 사용하여 파일을 만듭니다.

   <pre><code>sudo vi /etc/auto.direct
   
   # Add the following lines to the file, save and exit
   /sapmnt/<b>NW1</b> -nfsvers=4,nosymlink,sync <b>nw1-nfs</b>:/<b>NW1</b>/sapmntsid
   /usr/sap/trans -nfsvers=4,nosymlink,sync <b>nw1-nfs</b>:/<b>NW1</b>/trans
   /usr/sap/<b>NW1</b>/SYS -nfsvers=4,nosymlink,sync <b>nw1-nfs</b>:/<b>NW1</b>/sidsys
   </code></pre>

   autofs를 다시 시작하여 새 공유를 탑재합니다.

   <pre><code>sudo systemctl enable autofs
   sudo service autofs restart
   </code></pre>

1. **[A]** 스왑 파일 구성

   <pre><code>sudo vi /etc/waagent.conf
   
   # Set the property ResourceDisk.EnableSwap to y
   # Create and use swapfile on resource disk.
   ResourceDisk.EnableSwap=<b>y</b>
   
   # Set the size of the SWAP file with property ResourceDisk.SwapSizeMB
   # The free space of resource disk varies by virtual machine size. Make sure that you do not set a value that is too big. You can check the SWAP space with command swapon
   # Size of the swapfile.
   ResourceDisk.SwapSizeMB=<b>2000</b>
   </code></pre>

   에이전트를 다시 시작하여 변경 내용을 활성화합니다.

   <pre><code>sudo service waagent restart
   </code></pre>


### <a name="installing-sap-netweaver-ascsers"></a>SAP NetWeaver ASCS/ERS 설치

1. **[1]** ASCS 인스턴스에 대한 가상 IP 리소스 및 상태 프로브 만들기

   <pre><code>sudo crm node standby <b>nw1-cl-1</b>
   
   sudo crm configure primitive fs_<b>NW1</b>_ASCS Filesystem device='<b>nw1-nfs</b>:/<b>NW1</b>/ASCS' directory='/usr/sap/<b>NW1</b>/ASCS<b>00</b>' fstype='nfs4' \
     op start timeout=60s interval=0 \
     op stop timeout=60s interval=0 \
     op monitor interval=20s timeout=40s
   
   sudo crm configure primitive vip_<b>NW1</b>_ASCS IPaddr2 \
     params ip=<b>10.0.0.7</b> cidr_netmask=<b>24</b> \
     op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>NW1</b>_ASCS anything \
     params binfile="/usr/bin/nc" cmdline_options="-l -k 620<b>00</b>" \
     op monitor timeout=20s interval=10 depth=0
   
   sudo crm configure group g-<b>NW1</b>_ASCS fs_<b>NW1</b>_ASCS nc_<b>NW1</b>_ASCS vip_<b>NW1</b>_ASCS \
      meta resource-stickiness=3000
   </code></pre>

   클러스터 상태가 정상이며 모든 리소스가 시작되었는지 확인합니다. 리소스가 실행되는 노드는 중요하지 않습니다.

   <pre><code>sudo crm_mon -r
   
   # Node nw1-cl-1: standby
   # <b>Online: [ nw1-cl-0 ]</b>
   # 
   # Full list of resources:
   # 
   # stonith-sbd     (stonith:external/sbd): <b>Started nw1-cl-0</b>
   #  Resource Group: g-NW1_ASCS
   #      fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    <b>Started nw1-cl-0</b>
   #      nc_NW1_ASCS        (ocf::heartbeat:anything):      <b>Started nw1-cl-0</b>
   #      vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       <b>Started nw1-cl-0</b>
   </code></pre>

1. **[1]** SAP NetWeaver ASCS 설치  

   ASCS에 대한 부하 분산 장치 프런트 엔드 구성의 IP 주소에 매핑되는 가상 호스트 이름(예: <b>nw1-ascs</b>, <b>10.0.0.7</b>) 및 부하 분산 장치의 프로브에 사용한 인스턴스 번호(예: <b>00</b>)를 사용하여 첫 번째 노드에 SAP NetWeaver ASCS를 루트로 설치합니다.

   sapinst 매개 변수 SAPINST_REMOTE_ACCESS_USER를 사용하면 루트 권한이 없는 사용자의 sapinst 연결을 허용할 수 있습니다.

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

   설치에서 /usr/sap/**NW1**/ASCS**00**에 하위 폴더를 만들지 못하면 ASCS**00** 폴더의 소유자와 그룹을 설정하고 다시 시도합니다.

   <pre><code>chown nw1adm /usr/sap/<b>NW1</b>/ASCS<b>00</b>
   chgrp sapsys /usr/sap/<b>NW1</b>/ASCS<b>00</b>
   </code></pre>

1. **[1]** ERS 인스턴스에 대한 가상 IP 리소스 및 상태 검사 프로브 만들기

   <pre><code>sudo crm node online <b>nw1-cl-1</b>
   sudo crm node standby <b>nw1-cl-0</b>
   
   sudo crm configure primitive fs_<b>NW1</b>_ERS Filesystem device='<b>nw1-nfs</b>:/<b>NW1</b>/ASCSERS' directory='/usr/sap/<b>NW1</b>/ERS<b>02</b>' fstype='nfs4' \
     op start timeout=60s interval=0 \
     op stop timeout=60s interval=0 \
     op monitor interval=20s timeout=40s
   
   sudo crm configure primitive vip_<b>NW1</b>_ERS IPaddr2 \
     params ip=<b>10.0.0.8</b> cidr_netmask=<b>24</b> \
     op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>NW1</b>_ERS anything \
    params binfile="/usr/bin/nc" cmdline_options="-l -k 621<b>02</b>" \
    op monitor timeout=20s interval=10 depth=0
   
   # WARNING: Resources nc_NW1_ASCS,nc_NW1_ERS violate uniqueness for parameter "binfile": "/usr/bin/nc"
   # Do you still want to commit (y/n)? y
   
   sudo crm configure group g-<b>NW1</b>_ERS fs_<b>NW1</b>_ERS nc_<b>NW1</b>_ERS vip_<b>NW1</b>_ERS
   </code></pre>

   클러스터 상태가 정상이며 모든 리소스가 시작되었는지 확인합니다. 리소스가 실행되는 노드는 중요하지 않습니다.

   <pre><code>sudo crm_mon -r
   
   # Node <b>nw1-cl-0: standby</b>
   # <b>Online: [ nw1-cl-1 ]</b>
   # 
   # Full list of resources:
   #
   # stonith-sbd     (stonith:external/sbd): <b>Started nw1-cl-1</b>
   #  Resource Group: g-NW1_ASCS
   #      fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    <b>Started nw1-cl-1</b>
   #      nc_NW1_ASCS        (ocf::heartbeat:anything):      <b>Started nw1-cl-1</b>
   #      vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       <b>Started nw1-cl-1</b>
   #  Resource Group: g-NW1_ERS
   #      fs_NW1_ERS (ocf::heartbeat:Filesystem):    <b>Started nw1-cl-1</b>
   #      nc_NW1_ERS (ocf::heartbeat:anything):      <b>Started nw1-cl-1</b>
   #      vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       <b>Started nw1-cl-1</b>
   </code></pre>

1. **[2]** SAP NetWeaver ERS 설치

   ERS에 대한 부하 분산 장치 프런트 엔드 구성의 IP 주소에 매핑되는 가상 호스트 이름(예: <b>nw1-aers</b>, <b>10.0.0.8</b>) 및 부하 분산 장치의 프로브에 사용한 인스턴스 번호(예: <b>02</b>)를 사용하여 두 번째 노드에 SAP NetWeaver ERS를 루트로 설치합니다.

   sapinst 매개 변수 SAPINST_REMOTE_ACCESS_USER를 사용하면 루트 권한이 없는 사용자의 sapinst 연결을 허용할 수 있습니다.

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

   > [!NOTE]
   > SWPM SP 20 PL 05 이상을 사용합니다. 그 이전 버전은 권한을 올바르게 설정하지 않으므로 설치가 실패합니다.

   설치에서 /usr/sap/**NW1**/ERS**02**에 하위 폴더를 만들지 못하면 ERS**02** 폴더의 소유자와 그룹을 설정하고 다시 시도합니다.

   <pre><code>chown nw1adm /usr/sap/<b>NW1</b>/ERS<b>02</b>
   chgrp sapsys /usr/sap/<b>NW1</b>/ERS<b>02</b>
   </code></pre>


1. **[1]** ASCS/SCS 및 ERS 인스턴스 프로필 조정
 
   * ASCS/SCS 프로필

   <pre><code>sudo vi /sapmnt/<b>NW1</b>/profile/<b>NW1</b>_<b>ASCS00</b>_<b>nw1-ascs</b>
   
   # Change the restart command to a start command
   #Restart_Program_01 = local $(_EN) pf=$(_PF)
   Start_Program_01 = local $(_EN) pf=$(_PF)
   
   # Add the following lines
   service/halib = $(DIR_CT_RUN)/saphascriptco.so
   service/halib_cluster_connector = /usr/bin/sap_suse_cluster_connector
   
   # Add the keep alive parameter
   enque/encni/set_so_keepalive = true
   </code></pre>

   * ERS 프로필

   <pre><code>sudo vi /sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ERS<b>02</b>_<b>nw1-aers</b>
   
   # Change the restart command to a start command
   #Restart_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   Start_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   
   # Add the following lines
   service/halib = $(DIR_CT_RUN)/saphascriptco.so
   service/halib_cluster_connector = /usr/bin/sap_suse_cluster_connector
   
   # remove Autostart from ERS profile
   # Autostart = 1
   </code></pre>

1. **[A]** 연결 유지 구성

   SAP NetWeaver 애플리케이션 서버와 ASCS/SCS 간의 통신은 소프트웨어 부하 분산 장치를 통해 라우팅됩니다. 부하 분산 장치는 구성 가능한 시간 제한이 지나면 비활성 연결을 끊습니다. 이 연결 끊김을 방지하려면 SAP NetWeaver ASCS/SCS 프로필에서 매개 변수를 설정하고 Linux 시스템 설정을 변경해야 합니다. 자세한 내용은 [SAP Note 1410736][1410736]을 참조하세요.

   ASCS/SCS profile 매개 변수 enque/encni/set_so_keepalive는 마지막 단계에서 이미 추가된 상태입니다.

   <pre><code># Change the Linux system configuration
   sudo sysctl net.ipv4.tcp_keepalive_time=120
   </code></pre>

1. **[A]** 설치 후 SAP 사용자 구성

   <pre><code># Add sidadm to the haclient group
   sudo usermod -aG haclient <b>nw1</b>adm
   </code></pre>

1. **[1]** sapservice 파일에 ASCS 및 ERS SAP 서비스 추가

   ASCS 서비스 항목을 두 번째 노드에 추가하고 ERS 서비스 항목을 첫 번째 노드에 복사합니다.

   <pre><code>cat /usr/sap/sapservices | grep ASCS<b>00</b> | sudo ssh <b>nw1-cl-1</b> "cat >>/usr/sap/sapservices"
   sudo ssh <b>nw1-cl-1</b> "cat /usr/sap/sapservices" | grep ERS<b>02</b> | sudo tee -a /usr/sap/sapservices
   </code></pre>

1. **[1]** SAP 클러스터 리소스 만들기

큐에 넣기 서버 1 아키텍처 (ENSA1)를 사용 하는 경우 아래와 같이 리소스를 정의 합니다.

   <pre><code>sudo crm configure property maintenance-mode="true"
   
   sudo crm configure primitive rsc_sap_<b>NW1</b>_ASCS<b>00</b> SAPInstance \
    operations \$id=rsc_sap_<b>NW1</b>_ASCS<b>00</b>-operations \
    op monitor interval=11 timeout=60 on_fail=restart \
    params InstanceName=<b>NW1</b>_ASCS<b>00</b>_<b>nw1-ascs</b> START_PROFILE="/sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ASCS<b>00</b>_<b>nw1-ascs</b>" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 failure-timeout=60 migration-threshold=1 priority=10
   
   sudo crm configure primitive rsc_sap_<b>NW1</b>_ERS<b>02</b> SAPInstance \
    operations \$id=rsc_sap_<b>NW1</b>_ERS<b>02</b>-operations \
    op monitor interval=11 timeout=60 on_fail=restart \
    params InstanceName=<b>NW1</b>_ERS<b>02</b>_<b>nw1-aers</b> START_PROFILE="/sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ERS<b>02</b>_<b>nw1-aers</b>" AUTOMATIC_RECOVER=false IS_ERS=true \
    meta priority=1000
   
   sudo crm configure modgroup g-<b>NW1</b>_ASCS add rsc_sap_<b>NW1</b>_ASCS<b>00</b>
   sudo crm configure modgroup g-<b>NW1</b>_ERS add rsc_sap_<b>NW1</b>_ERS<b>02</b>
   
   sudo crm configure colocation col_sap_<b>NW1</b>_no_both -5000: g-<b>NW1</b>_ERS g-<b>NW1</b>_ASCS
   sudo crm configure location loc_sap_<b>NW1</b>_failover_to_ers rsc_sap_<b>NW1</b>_ASCS<b>00</b> rule 2000: runs_ers_<b>NW1</b> eq 1
   sudo crm configure order ord_sap_<b>NW1</b>_first_start_ascs Optional: rsc_sap_<b>NW1</b>_ASCS<b>00</b>:start rsc_sap_<b>NW1</b>_ERS<b>02</b>:stop symmetrical=false
   
   sudo crm node online <b>nw1-cl-0</b>
   sudo crm configure property maintenance-mode="false"
   </code></pre>

  큐에 넣기 서버 복제의 경우 SAP NW 7.52 기준으로 포함 하 여 2 도입 하는 SAP 지원 합니다. 큐에 넣기 서버 2 ABAP 플랫폼 1809부터 기본적으로 설치 됩니다. SAP 참고 참고 [2630416](https://launchpad.support.sap.com/#/notes/2630416) 큐에 넣기 서버 2 지원에 대 한 합니다.
큐에 넣기 서버 2 아키텍처를 사용 하는 경우 ([ENSA2](https://help.sap.com/viewer/cff8531bc1d9416d91bb6781e628d4e0/1709%20001/en-US/6d655c383abf4c129b0e5c8683e7ecd8.html)), 리소스를 다음과 같이 정의 합니다.

<pre><code>sudo crm configure property maintenance-mode="true"
   
   sudo crm configure primitive rsc_sap_<b>NW1</b>_ASCS<b>00</b> SAPInstance \
    operations \$id=rsc_sap_<b>NW1</b>_ASCS<b>00</b>-operations \
    op monitor interval=11 timeout=60 on_fail=restart \
    params InstanceName=<b>NW1</b>_ASCS<b>00</b>_<b>nw1-ascs</b> START_PROFILE="/sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ASCS<b>00</b>_<b>nw1-ascs</b>" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000
   
   sudo crm configure primitive rsc_sap_<b>NW1</b>_ERS<b>02</b> SAPInstance \
    operations \$id=rsc_sap_<b>NW1</b>_ERS<b>02</b>-operations \
    op monitor interval=11 timeout=60 on_fail=restart \
    params InstanceName=<b>NW1</b>_ERS<b>02</b>_<b>nw1-aers</b> START_PROFILE="/sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ERS<b>02</b>_<b>nw1-aers</b>" AUTOMATIC_RECOVER=false IS_ERS=true 
   
   sudo crm configure modgroup g-<b>NW1</b>_ASCS add rsc_sap_<b>NW1</b>_ASCS<b>00</b>
   sudo crm configure modgroup g-<b>NW1</b>_ERS add rsc_sap_<b>NW1</b>_ERS<b>02</b>
   
   sudo crm configure colocation col_sap_<b>NW1</b>_no_both -5000: g-<b>NW1</b>_ERS g-<b>NW1</b>_ASCS
   sudo crm configure order ord_sap_<b>NW1</b>_first_start_ascs Optional: rsc_sap_<b>NW1</b>_ASCS<b>00</b>:start rsc_sap_<b>NW1</b>_ERS<b>02</b>:stop symmetrical=false
   
   sudo crm node online <b>nw1-cl-0</b>
   sudo crm configure property maintenance-mode="false"
   </code></pre>

  이전 버전에서 업그레이드 하 고 큐에 넣기 서버 2로 전환 하는 경우 sap 참고 참조 [2641019](https://launchpad.support.sap.com/#/notes/2641019)합니다. 

   클러스터 상태가 정상이며 모든 리소스가 시작되었는지 확인합니다. 리소스가 실행되는 노드는 중요하지 않습니다.


   <pre><code>sudo crm_mon -r
   
   # Online: <b>[ nw1-cl-0 nw1-cl-1 ]</b>
   #
   # Full list of resources:
   #
   # stonith-sbd     (stonith:external/sbd): <b>Started nw1-cl-1</b>
   #  Resource Group: g-NW1_ASCS
   #      fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    <b>Started nw1-cl-1</b>
   #      nc_NW1_ASCS        (ocf::heartbeat:anything):      <b>Started nw1-cl-1</b>
   #      vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       <b>Started nw1-cl-1</b>
   #      rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   <b>Started nw1-cl-1</b>
   #  Resource Group: g-NW1_ERS
   #      fs_NW1_ERS (ocf::heartbeat:Filesystem):    <b>Started nw1-cl-0</b>
   #      nc_NW1_ERS (ocf::heartbeat:anything):      <b>Started nw1-cl-0</b>
   #      vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       <b>Started nw1-cl-0</b>
   #      rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   <b>Started nw1-cl-0</b>
   </code></pre>

## <a name="2d6008b0-685d-426c-b59e-6cd281fd45d7"></a> SAP NetWeaver 애플리케이션 서버 준비

일부 데이터베이스에서는 데이터베이스 인스턴스 설치가 애플리케이션 서버에서 실행되어야 합니다. 이러한 경우 애플리케이션 서버 가상 머신을 사용할 수 있도록 준비합니다.

아래 단계에서는 ASCS/SCS 및 HANA 서버와 다른 서버에 애플리케이션 서버를 설치한다고 가정합니다. 그 외의 경우에는 호스트 이름 확인을 구성하는 단계 등 아래의 일부 단계를 수행할 필요가 없습니다.

1. 운영 체제 구성

   더티 캐시의 크기를 줄입니다. 자세한 내용은 [큰 RAM이 있는 SLES 11/12 서버의 쓰기 성능 저하](https://www.suse.com/support/kb/doc/?id=7010287)를 참조하세요.

   <pre><code>sudo vi /etc/sysctl.conf

   # Change/set the following settings
   vm.dirty_bytes = 629145600
   vm.dirty_background_bytes = 314572800
   </code></pre>

1. 호스트 이름 확인 설정

   DNS 서버를 사용하거나 모든 노드의 /etc/hosts를 수정할 수 있습니다. 이 예에서는 /etc/hosts 파일 사용 방법을 보여줍니다.
   다음 명령에서 IP 주소와 호스트 이름 바꾸기

   ```bash
   sudo vi /etc/hosts
   ```

   다음 줄을 /etc/hosts에 삽입합니다. 환경에 맞게 IP 주소와 호스트 이름 변경

   <pre><code># IP address of the load balancer frontend configuration for NFS
   <b>10.0.0.4 nw1-nfs</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS/SCS
   <b>10.0.0.7 nw1-ascs</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ERS
   <b>10.0.0.8 nw1-aers</b>
   # IP address of the load balancer frontend configuration for database
   <b>10.0.0.13 nw1-db</b>
   # IP address of all application servers
   <b>10.0.0.20 nw1-di-0</b>
   <b>10.0.0.21 nw1-di-1</b>
   </code></pre>

1. sapmnt 디렉터리를 만듭니다.

   <pre><code>sudo mkdir -p /sapmnt/<b>NW1</b>
   sudo mkdir -p /usr/sap/trans

   sudo chattr +i /sapmnt/<b>NW1</b>
   sudo chattr +i /usr/sap/trans
   </code></pre>

1. autofs를 구성합니다.

   <pre><code>sudo vi /etc/auto.master
   
   # Add the following line to the file, save and exit
   +auto.master
   /- /etc/auto.direct
   </code></pre>

   다음 코드를 사용하여 새 파일을 만듭니다.

   <pre><code>sudo vi /etc/auto.direct
   
   # Add the following lines to the file, save and exit
   /sapmnt/<b>NW1</b> -nfsvers=4,nosymlink,sync <b>nw1-nfs</b>:/<b>NW1</b>/sapmntsid
   /usr/sap/trans -nfsvers=4,nosymlink,sync <b>nw1-nfs</b>:/<b>NW1</b>/trans
   </code></pre>

   autofs를 다시 시작하여 새 공유를 탑재합니다.

   <pre><code>sudo systemctl enable autofs
   sudo service autofs restart
   </code></pre>

1. 스왑 파일을 구성합니다.

   <pre><code>sudo vi /etc/waagent.conf
   
   # Set the property ResourceDisk.EnableSwap to y
   # Create and use swapfile on resource disk.
   ResourceDisk.EnableSwap=<b>y</b>
   
   # Set the size of the SWAP file with property ResourceDisk.SwapSizeMB
   # The free space of resource disk varies by virtual machine size. Make sure that you do not set a value that is too big. You can check the SWAP space with command swapon
   # Size of the swapfile.
   ResourceDisk.SwapSizeMB=<b>2000</b>
   </code></pre>

   에이전트를 다시 시작하여 변경 내용을 활성화합니다.

   <pre><code>sudo service waagent restart
   </code></pre>

## <a name="install-database"></a>데이터베이스 설치

이 예제에서는 SAP NetWeaver가 SAP HANA에 설치됩니다. 이 설치에 지원되는 모든 데이터베이스를 사용할 수 있습니다. Azure에서 SAP HANA를 설치하는 방법에 대한 자세한 내용은 [Azure VM(Virtual Machines)의 SAP HANA 고가용성][sap-hana-ha]을 참조하세요. 지원되는 데이터베이스 목록은 [SAP Note 1928533][1928533]을 참조하세요.

1. SAP 데이터베이스 인스턴스 설치 실행

   데이터베이스에 대한 부하 분산 장치 프런트 엔드 구성의 IP 주소에 매핑되는 가상 호스트 이름(예: <b>nw1-db</b> 및 <b>10.0.0.13</b>)을 사용하여 SAP NetWeaver 데이터베이스 인스턴스를 루트로 설치합니다.

   sapinst 매개 변수 SAPINST_REMOTE_ACCESS_USER를 사용하면 루트 권한이 없는 사용자의 sapinst 연결을 허용할 수 있습니다.

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

## <a name="sap-netweaver-application-server-installation"></a>SAP NetWeaver 애플리케이션 서버 설치

다음 단계에 따라 SAP 애플리케이션 서버를 설치합니다.

1. 애플리케이션 서버 준비

   위에 있는 [SAP NetWeaver 애플리케이션 서버 준비](high-availability-guide-suse.md#2d6008b0-685d-426c-b59e-6cd281fd45d7) 챕터의 단계에 따라 애플리케이션 서버를 준비합니다.

1. SAP NetWeaver 애플리케이션 서버 설치

   기본 또는 추가 SAP NetWeaver 애플리케이션 서버를 설치합니다.

   sapinst 매개 변수 SAPINST_REMOTE_ACCESS_USER를 사용하면 루트 권한이 없는 사용자의 sapinst 연결을 허용할 수 있습니다.

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

1. SAP HANA 보안 저장소 업데이트

   설치한 SAP HANA System Replication의 가상 이름을 가리키도록 SAP HANA 보안 저장소를 업데이트합니다.

   다음 명령을 실행하여 항목을 나열합니다.
   <pre><code>hdbuserstore List
   </code></pre>

   이 경우 모든 항목을 나열해야 하며 다음과 비슷합니다.
   <pre><code>DATA FILE       : /home/nw1adm/.hdb/nw1-di-0/SSFS_HDB.DAT
   KEY FILE        : /home/nw1adm/.hdb/nw1-di-0/SSFS_HDB.KEY
   
   KEY DEFAULT
     ENV : 10.0.0.14:<b>30313</b>
     USER: <b>SAPABAP1</b>
     DATABASE: <b>HN1</b>
   </code></pre>

   출력은 기본 항목의 IP 주소가 부하 분산 장치의 IP 주소가 아니라 가상 머신을 가리키고 있음을 나타냅니다. 이 항목은 부하 분산 장치의 가상 호스트 이름을 가리키도록 변경해야 합니다. 동일한 포트(위 출력의 **30313**) 및 데이터베이스 이름(위 출력의 **HN1**)을 사용해야 합니다.

   <pre><code>su - <b>nw1</b>adm
   hdbuserstore SET DEFAULT <b>nw1-db:30313@HN1</b> <b>SAPABAP1</b> <b>&lt;password of ABAP schema&gt;</b>
   </code></pre>

## <a name="test-the-cluster-setup"></a>클러스터 설정 테스트

다음 테스트는 SUSE의 모범 사례 가이드에서 테스트 사례를 복사한 것입니다. 이 테스트는 작업자 편의를 위해 복사되었습니다. 또한 항상 모범 사례 가이드를 읽고 추가되었을 수 있는 모든 추가 테스트를 수행해야 합니다.

1. HAGetFailoverConfig, HACheckConfig 및 HACheckFailoverConfig 테스트

   ASCS 인스턴스를 현재 실행 중인 노드에서 \<sapsid>adm으로 다음 명령을 실행합니다. 이러한 명령이 실패: 메모리 부족을 나타내며 실패할 경우 호스트 이름의 대시 때문일 수 있습니다. 이것은 알려진 문제로, SUSE에서는 sap-suse-cluster-connector 패키지에서 이 문제를 수정할 예정입니다.

   <pre><code>nw1-cl-0:nw1adm 54> sapcontrol -nr <b>00</b> -function HAGetFailoverConfig
   
   # 15.08.2018 13:50:36
   # HAGetFailoverConfig
   # OK
   # HAActive: TRUE
   # HAProductVersion: Toolchain Module
   # HASAPInterfaceVersion: Toolchain Module (sap_suse_cluster_connector 3.0.1)
   # HADocumentation: https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
   # HAActiveNode:
   # HANodes: nw1-cl-0, nw1-cl-1
   
   nw1-cl-0:nw1adm 55> sapcontrol -nr 00 -function HACheckConfig
   
   # 15.08.2018 14:00:04
   # HACheckConfig
   # OK
   # state, category, description, comment
   # SUCCESS, SAP CONFIGURATION, Redundant ABAP instance configuration, 2 ABAP instances detected
   # SUCCESS, SAP CONFIGURATION, Redundant Java instance configuration, 0 Java instances detected
   # SUCCESS, SAP CONFIGURATION, Enqueue separation, All Enqueue server separated from application server
   # SUCCESS, SAP CONFIGURATION, MessageServer separation, All MessageServer separated from application server
   # SUCCESS, SAP CONFIGURATION, ABAP instances on multiple hosts, ABAP instances on multiple hosts detected
   # SUCCESS, SAP CONFIGURATION, Redundant ABAP SPOOL service configuration, 2 ABAP instances with SPOOL service detected
   # SUCCESS, SAP STATE, Redundant ABAP SPOOL service state, 2 ABAP instances with active SPOOL service detected
   # SUCCESS, SAP STATE, ABAP instances with ABAP SPOOL service on multiple hosts, ABAP instances with active ABAP SPOOL service on multiple hosts detected
   # SUCCESS, SAP CONFIGURATION, Redundant ABAP BATCH service configuration, 2 ABAP instances with BATCH service detected
   # SUCCESS, SAP STATE, Redundant ABAP BATCH service state, 2 ABAP instances with active BATCH service detected
   # SUCCESS, SAP STATE, ABAP instances with ABAP BATCH service on multiple hosts, ABAP instances with active ABAP BATCH service on multiple hosts detected
   # SUCCESS, SAP CONFIGURATION, Redundant ABAP DIALOG service configuration, 2 ABAP instances with DIALOG service detected
   # SUCCESS, SAP STATE, Redundant ABAP DIALOG service state, 2 ABAP instances with active DIALOG service detected
   # SUCCESS, SAP STATE, ABAP instances with ABAP DIALOG service on multiple hosts, ABAP instances with active ABAP DIALOG service on multiple hosts detected
   # SUCCESS, SAP CONFIGURATION, Redundant ABAP UPDATE service configuration, 2 ABAP instances with UPDATE service detected
   # SUCCESS, SAP STATE, Redundant ABAP UPDATE service state, 2 ABAP instances with active UPDATE service detected
   # SUCCESS, SAP STATE, ABAP instances with ABAP UPDATE service on multiple hosts, ABAP instances with active ABAP UPDATE service on multiple hosts detected
   # SUCCESS, SAP STATE, SCS instance running, SCS instance status ok
   # SUCCESS, SAP CONFIGURATION, SAPInstance RA sufficient version (nw1-ascs_NW1_00), SAPInstance includes is-ers patch
   # SUCCESS, SAP CONFIGURATION, Enqueue replication (nw1-ascs_NW1_00), Enqueue replication enabled
   # SUCCESS, SAP STATE, Enqueue replication state (nw1-ascs_NW1_00), Enqueue replication active
   
   nw1-cl-0:nw1adm 56> sapcontrol -nr 00 -function HACheckFailoverConfig
   
   # 15.08.2018 14:04:08
   # HACheckFailoverConfig
   # OK
   # state, category, description, comment
   # SUCCESS, SAP CONFIGURATION, SAPInstance RA sufficient version, SAPInstance includes is-ers patch
   </code></pre>

1. 수동으로 ASCS 인스턴스 마이그레이션

   테스트 시작 전 리소스 상태:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:anything):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ERS (ocf::heartbeat:anything):      Started nw1-cl-1
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

   루트 권한으로 다음 명령을 실행하여 ASCS 인스턴스를 마이그레이션합니다.

   <pre><code>nw1-cl-0:~ # crm resource migrate rsc_sap_NW1_ASCS00 force
   # INFO: Move constraint created for rsc_sap_NW1_ASCS00
   
   nw1-cl-0:~ # crm resource unmigrate rsc_sap_NW1_ASCS00
   # INFO: Removed migration constraints for rsc_sap_NW1_ASCS00
   
   # Remove failed actions for the ERS that occurred as part of the migration
   nw1-cl-0:~ # crm resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   테스트 후 리소스 상태:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:anything):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:anything):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

1. HAFailoverToNode 테스트

   테스트 시작 전 리소스 상태:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:anything):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:anything):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

   \<sapsid>adm으로 다음 명령을 실행하여 ASCS 인스턴스를 마이그레이션합니다.

   <pre><code>nw1-cl-0:nw1adm 55> sapcontrol -nr 00 -host nw1-ascs -user nw1adm &lt;password&gt; -function HAFailoverToNode ""
   
   # run as root
   # Remove failed actions for the ERS that occurred as part of the migration
   nw1-cl-0:~ # crm resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   테스트 후 리소스 상태:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:anything):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ERS (ocf::heartbeat:anything):      Started nw1-cl-1
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

1. 노드 작동 중단 시뮬레이트

   테스트 시작 전 리소스 상태:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:anything):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ERS (ocf::heartbeat:anything):      Started nw1-cl-1
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

   ASCS 인스턴스를 실행 중인 노드에서 루트 권한으로 다음 명령을 실행합니다.

   <pre><code>nw1-cl-0:~ # echo b > /proc/sysrq-trigger
   </code></pre>

   SBD를 사용하는 경우 종료된 노드에서 Pacemaker가 자동으로 시작되지 않습니다. 노드가 다시 시작된 후의 상태는 다음과 같습니다.

   <pre><code>Online: [ nw1-cl-1 ]
   OFFLINE: [ nw1-cl-0 ]
   
   Full list of resources:
   
   stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:anything):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ERS (ocf::heartbeat:anything):      Started nw1-cl-1
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   
   Failed Actions:
   * rsc_sap_NW1_ERS02_monitor_11000 on nw1-cl-1 'not running' (7): call=219, status=complete, exitreason='none',
       last-rc-change='Wed Aug 15 14:38:38 2018', queued=0ms, exec=0ms
   </code></pre>

   다음 명령을 사용하여 종료된 노드에서 Pacemaker를 시작하고, SBD 메시지를 정리하고, 실패한 리소스를 정리합니다.

   <pre><code># run as root
   # list the SBD device(s)
   nw1-cl-0:~ # cat /etc/sysconfig/sbd | grep SBD_DEVICE=
   # SBD_DEVICE="/dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116;/dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1;/dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3"
   
   nw1-cl-0:~ # sbd -d /dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116 -d /dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1 -d /dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3 message nw1-cl-0 clear
   
   nw1-cl-0:~ # systemctl start pacemaker
   nw1-cl-0:~ # crm resource cleanup rsc_sap_NW1_ASCS00
   nw1-cl-0:~ # crm resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   테스트 후 리소스 상태:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:anything):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:anything):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

1. ASCS 인스턴스의 수동 다시 시작 테스트

   테스트 시작 전 리소스 상태:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:anything):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:anything):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

   예를 들어 트랜잭션 su01에서 사용자를 편집하여 큐에 넣기 잠금을 만듭니다. ASCS 인스턴스를 실행 중인 노드에서 \<sapsid>adm으로 다음 명령을 실행합니다. 이러한 명령은 ASCS 인스턴스를 중지했다가 다시 시작합니다. 큐에 넣기 서버 1 아키텍처를 사용 하는 경우 큐에 넣기 잠금은이 테스트에서 손실 될 예정입니다. 큐에 넣기 서버 2 아키텍처를 사용 하는 큐에 넣기 유지 됩니다. 

   <pre><code>nw1-cl-1:nw1adm 54> sapcontrol -nr 00 -function StopWait 600 2
   </code></pre>

   ASCS 인스턴스는 이제 Pacemaker에서 사용되지 않도록 설정됩니다.

   <pre><code>rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Stopped (disabled)
   </code></pre>

   동일한 노드에서 ASCS 인스턴스를 다시 시작합니다.

   <pre><code>nw1-cl-1:nw1adm 54> sapcontrol -nr 00 -function StartWait 600 2
   </code></pre>

   트랜잭션 su01의 큐에 넣기 잠금이 손실되므로 백 엔드를 다시 설정했어야 합니다. 테스트 후 리소스 상태:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:anything):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:anything):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

1. 메시지 서버 프로세스 종료

   테스트 시작 전 리소스 상태:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:anything):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:anything):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

   루트 권한으로 다음 명령을 실행하여 메시지 서버 프로세스를 확인한 후 종료합니다.

   <pre><code>nw1-cl-1:~ # pgrep ms.sapNW1 | xargs kill -9
   </code></pre>

   메시지 서버를 한 번만 종료하면 sapstart에 의해 다시 시작됩니다. 충분히 자주 종료하면 Pacemaker는 ASCS 인스턴스를 다른 노드로 이동합니다. 테스트 후에 다음 명령을 루트 권한으로 실행하여 ASCS 및 ERS 인스턴스의 리소스 상태를 정리합니다.

   <pre><code>nw1-cl-0:~ # crm resource cleanup rsc_sap_NW1_ASCS00
   nw1-cl-0:~ # crm resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   테스트 후 리소스 상태:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:anything):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ERS (ocf::heartbeat:anything):      Started nw1-cl-1
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

1. 큐에 넣기 서버 프로세스 종료

   테스트 시작 전 리소스 상태:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:anything):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ERS (ocf::heartbeat:anything):      Started nw1-cl-1
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

   ASCS 인스턴스를 실행 중인 노드에서 루트 권한으로 다음 명령을 실행하여 큐에 넣기 서버를 종료합니다.

   <pre><code>nw1-cl-0:~ # pgrep en.sapNW1 | xargs kill -9
   </code></pre>

   ASCS 인스턴스는 즉시 다른 노드로 장애 조치(Failover)되어야 합니다. 또한 ASCS 인스턴스가 시작된 후 ERS 인스턴스도 장애 조치(Failover)되어야 합니다. 테스트 후에 다음 명령을 루트 권한으로 실행하여 ASCS 및 ERS 인스턴스의 리소스 상태를 정리합니다.

   <pre><code>nw1-cl-0:~ # crm resource cleanup rsc_sap_NW1_ASCS00
   nw1-cl-0:~ # crm resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   테스트 후 리소스 상태:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:anything):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:anything):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

1. 큐에 넣기 복제 서버 프로세스 종료

   테스트 시작 전 리소스 상태:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:anything):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:anything):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

   ERS 인스턴스를 실행 중인 노드에서 루트 권한으로 다음 명령을 실행하여 큐에 넣기 복제 서버 프로세스를 종료합니다.

   <pre><code>nw1-cl-0:~ # pgrep er.sapNW1 | xargs kill -9
   </code></pre>

   이 명령을 한 번만 실행하는 경우 sapstart가 프로세스 다시 시작합니다. 충분히 자주 실행하면 sapstart가 해당 프로세스를 다시 시작하지 않으며 리소스는 중지된 상태가 됩니다. 테스트 후에 다음 명령을 루트 권한으로 실행하여 ERS 인스턴스의 리소스 상태를 정리합니다.

   <pre><code>nw1-cl-0:~ # crm resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   테스트 후 리소스 상태:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:anything):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:anything):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

1. 큐에 넣기 sapstartsrv 프로세스 종료

   테스트 시작 전 리소스 상태:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:anything):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:anything):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

   ASCS를 실행 중인 노드에서 루트 권한으로 다음 명령을 실행합니다.

   <pre><code>nw1-cl-1:~ # pgrep -fl ASCS00.*sapstartsrv
   # 59545 sapstartsrv
   
   nw1-cl-1:~ # kill -9 59545
   </code></pre>

   sapstartsrv 프로세스는 항상 Pacemaker 리소스 에이전트에 의해 다시 시작되어야 합니다. 테스트 후 리소스 상태:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:anything):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:anything):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

## <a name="next-steps"></a>다음 단계

* [SAP용 Azure Virtual Machines 계획 및 구현][planning-guide]
* [SAP용 Azure Virtual Machines 배포][deployment-guide]
* [SAP용 Azure Virtual Machines DBMS 배포][dbms-guide]
* [Azure의 SAP HANA(큰 인스턴스) 고가용성 및 재해 복구](hana-overview-high-availability-disaster-recovery.md) - Azure의 SAP HANA(큰 인스턴스)에 대한 고가용성 및 재해 복구 계획을 설정하는 방법을 알아봅니다.
* Azure VM에서 SAP HANA의 재해 복구를 계획하고 고가용성을 설정하는 방법을 알아보려면 [Azure VM(Virtual Machines)의 SAP HANA 고가용성][sap-hana-ha]을 참조하세요.
