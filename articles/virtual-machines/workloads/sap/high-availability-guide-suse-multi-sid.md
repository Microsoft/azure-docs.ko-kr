---
title: SLES 다중 SID 가이드의 SAP NetWeaver에 대 한 Azure Vm 고가용성 Microsoft Docs
description: Sap NetWeaver on SUSE Linux Enterprise Server sap 응용 프로그램에 대 한 다중 SID 고가용성 가이드
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 01/16/2020
ms.author: radeltch
ms.openlocfilehash: 7471fc6d7f10c849ba79fedf88961d6c3c99913f
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/22/2020
ms.locfileid: "76314201"
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms-on-suse-linux-enterprise-server-for-sap-applications-multi-sid-guide"></a>SAP 응용 프로그램에 대 한 SUSE Linux Enterprise Server Azure Vm의 SAP NetWeaver에 대 한 고가용성-다중 SID 가이드

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

이 문서에서는 SAP 응용 프로그램에 대 한 SUSE Linux Enterprise Server를 사용 하 여 Azure Vm의 두 노드 클러스터에 여러 SAP NetWeaver 또는 S4HANA 항상 사용 가능한 시스템 (즉, 다중 SID)을 배포 하는 방법을 설명 합니다.  

예제 구성, 설치 명령 등 세 가지 SAP NetWeaver 7.50 시스템은 단일 노드 고가용성 클러스터에 배포 됩니다. SAP 시스템 Sid는 다음과 같습니다.
* **N w 1**: ascs 인스턴스 번호 **00** 및 가상 호스트 이름 **msnw1ascs**; ERS 인스턴스 번호 **02** 및 가상 호스트 이름 **msnw1ers**.  
* **N w 2**: ascs 인스턴스 번호 **10** 및 가상 호스트 이름 **msnw2ascs**; ERS 인스턴스 번호 **12** 및 가상 호스트 이름 **msnw2ers**.  
* **NW3**: ascs 인스턴스 번호 **20** 및 가상 호스트 이름 **msnw3ascs**; ERS 인스턴스 번호 **22** 및 가상 호스트 이름 **msnw3ers**.  

이 문서에서는 데이터베이스 계층과 SAP NFS 공유의 배포에 대해 다루지 않습니다. 이 문서의 예제에서는 NFS 클러스터가 배포 된 것으로 가정 하 여 N W 2 NFS 공유에 대 한 가상 이름 n w 2-nfs를 사용 하 고 NW3 NFS 공유에는 nw3를 사용 합니다.  

시작 하기 전에 먼저 다음 SAP 참고 사항 및 백서를 참조 하세요.

* SAP Note [1928533][1928533], 다음 항목을 포함합니다.
  * SAP 소프트웨어 배포에 지원되는 Azure VM 크기 목록
  * Azure VM 크기에 대한 중요한 용량 정보
  * 지원되는 SAP 소프트웨어 및 운영 체제(OS)와 데이터베이스 조합
  * Microsoft Azure에서 Windows 및 Linux에 필요한 SAP 커널 버전

* SAP Note [2015553][2015553]는 Azure에서 SAP을 지원하는 SAP 소프트웨어 배포에 대한 필수 구성 요소를 나열합니다.
* SAP Note [2205917][2205917]에는 SAP 애플리케이션용 SUSE Linux Enterprise Server에 권장되는 OS 설정이 나와 있습니다.
* SAP Note [1944799][1944799]에는 SAP 애플리케이션용 SUSE Linux Enterprise Server에 대한 SAP HANA 지침이 나와 있습니다.
* SAP Note [2178632][2178632]는 Azure에서 SAP에 대해 보고된 모든 모니터링 메트릭에 대한 자세한 정보를 포함하고 있습니다.
* SAP Note [2191498][2191498]는 Azure에서 Linux에 필요한 SAP Host Agent 버전을 포함하고 있습니다.
* SAP Note [2243692][2243692]는 Azure에서 Linux의 SAP 라이선스에 대한 정보를 포함하고 있습니다.
* SAP Note [1984787][1984787]은 SUSE LINUX Enterprise Server 12에 대한 일반 정보를 포함하고 있습니다.
* SAP Note [1999351][1999351]은 SAP용 Azure 고급 모니터링 확장을 위한 추가 문제 해결 정보를 포함하고 있습니다.
* [SAP Community WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes)는 Linux에 필요한 모든 SAP Note를 포함하고 있습니다.
* [Linux에서 SAP 용 Azure Virtual Machines 계획 및 구현][planning-guide]
* [Linux에서 SAP 용 Azure Virtual Machines 배포][deployment-guide]
* [Linux에서 SAP 용 Azure Virtual Machines DBMS 배포][dbms-guide]
* [SUSE SAP HA 모범 사례 가이드][suse-ha-guide] 이 가이드에는 온-프레미스에서 Netweaver HA 및 SAP HANA 시스템 복제를 설정 하는 데 필요한 모든 정보가 포함 되어 있습니다. 이 가이드를 일반 기준으로 사용하세요. 여기서 훨씬 더 자세한 정보를 제공합니다.
* [SUSE 고가용성 확장 12 SP3 릴리스 정보][suse-ha-12sp3-relnotes]
* [다중 SID 클러스터에 대 한 SUSE 지원](https://www.suse.com/c/sap-workloads-going-green/)

## <a name="overview"></a>개요

장애 조치 (failover)가 발생 하는 경우 클러스터에 참여 하는 가상 머신은 모든 리소스를 실행할 수 있도록 크기를 조정 해야 합니다. 각 SAP SID는 다중 SID 고가용성 클러스터에서 서로 독립적인 장애 조치 (failover) 될 수 있습니다.  SBD fence를 사용 하는 경우 여러 클러스터 간에 SBD 장치를 공유할 수 있습니다.  

고가용성을 위해 SAP NetWeaver에는 항상 사용 가능한 NFS 공유가 필요 합니다. 이 예에서는 SAP NFS 공유가 항상 사용 가능한 [nfs 파일 서버](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs)에 호스트 되는 것으로 가정 합니다 .이 서버는 여러 sap 시스템에서 사용할 수 있습니다. 또는 공유는 [Azure NetApp FILES NFS 볼륨](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes)에 배포 됩니다.  

![SAP NetWeaver 고가용성 개요](./media/high-availability-guide-suse/ha-suse-multi-sid.png)

> [!IMPORTANT]
> Azure Vm에서 SUSE Linux를 게스트 운영 체제로 사용 하는 SAP ASCS/ERS의 다중 SID 클러스터링에 대 한 지원은 동일한 클러스터에서 **5 개의** sap sid로 제한 됩니다. 각 새 SID는 복잡성이 증가 합니다. 동일한 클러스터에서 SAP 큐에서 복제 서버 1과 큐에 넣기 복제 서버 2를 혼합 하 여 **사용할 수 없습니다**. 다중 SID 클러스터링은 하나의 Pacemaker 클러스터에서 서로 다른 Sid를 사용 하 여 여러 SAP ASCS/ERS 인스턴스를 설치 하는 방법을 설명 합니다. 현재 다중 SID 클러스터링은 ASCS/ERS에 대해서만 지원 됩니다.  

> [!TIP]
> SAP ASCS/ERS의 다중 SID 클러스터링은 더 복잡 한 솔루션입니다. 구현 하는 것이 더 복잡 합니다. 또한 유지 관리 작업 (예: OS 패치)을 실행할 때 더 높은 관리 노력이 수반 됩니다. 실제 구현을 시작 하기 전에 배포 및 Vm, NFS 탑재, Vip, 부하 분산 장치 구성 등의 관련 된 모든 구성 요소를 신중 하 게 계획 해야 합니다.  

NFS 서버, SAP NetWeaver ASCS, SAP NetWeaver SCS, SAP NetWeaver ERS 및 SAP HANA 데이터베이스는 가상 호스트 이름 및 가상 IP 주소를 사용합니다. Azure에서는 가상 IP 주소를 사용하려면 부하 분산 장치가 필요합니다. [표준 부하 분산 장치](https://docs.microsoft.com/azure/load-balancer/quickstart-load-balancer-standard-public-portal)를 사용 하는 것이 좋습니다.  

다음 목록에서는 세 가지 SAP 시스템을 사용 하는이 다중 SID 클러스터 예제에 대 한 (A) SCS 및 ERS 부하 분산 장치 구성을 보여 줍니다. 각 Sid에 대 한 각 ASCS 및 ERS 인스턴스에 대해 별도의 프런트 엔드 IP, 상태 프로브 및 부하 분산 규칙이 필요 합니다. ASCS/ASCS 클러스터의 일부인 모든 Vm을 하나의 백 엔드 풀에 할당 합니다.  

### <a name="ascs"></a>(A)SCS

* 프런트 엔드 구성
  * N W 1에 대 한 IP 주소: 10.3.1.14
  * N W 2에 대 한 IP 주소: 10.3.1.16
  * NW3에 대 한 IP 주소: 10.3.1.13
* 백 엔드 구성
  * (A)SCS/ERS 클러스터의 일부분이어야 하는 모든 가상 머신의 주 네트워크 인터페이스에 연결됨
* 프로브 포트
  * 포트 620<strong>&lt;nr&gt;</strong>, 따라서 N W 1, N W 2 및 NW3 프로브 포트 620**00**, 620**10** 및 620**20**
* 부하 분산 규칙- 
* 각 인스턴스, 즉 N W 1/ASCS, N W 2/ASCS 및 NW3/ASCS에 대해 하나를 만듭니다.
  * 표준 Load Balancer 사용 하는 경우 **HA 포트** 를 선택 합니다.
  * 기본 Load Balancer 사용 하는 경우 다음 포트에 대 한 부하 분산 규칙을 만듭니다.
    * 32<strong>&lt;nr&gt;</strong> TCP
    * 36<strong>&lt;nr&gt;</strong> TCP
    * 39<strong>&lt;nr&gt;</strong> TCP
    * 81<strong>&lt;nr&gt;</strong> TCP
    * 5<strong>&lt;nr&gt;</strong>13 TCP
    * 5<strong>&lt;nr&gt;</strong>14 TCP
    * 5<strong>&lt;nr&gt;</strong>16 TCP

### <a name="ers"></a>ERS

* 프런트 엔드 구성
  * N W 1 10.3.1.15에 대 한 IP 주소
  * N W 2 10.3.1.17에 대 한 IP 주소
  * NW3 10.3.1.19에 대 한 IP 주소
* 백 엔드 구성
  * (A)SCS/ERS 클러스터의 일부분이어야 하는 모든 가상 머신의 주 네트워크 인터페이스에 연결됨
* 프로브 포트
  * 포트 621<strong>&lt;nr&gt;</strong>, 따라서 N W 1, N w 2 및 N # 프로브 포트 621**02**, 621**12** 및 621**22**
* 부하 분산 규칙-각 인스턴스, 즉 N W 1/ERS, N W 2/ERS 및 NW3/ERS에 대해 하나를 만듭니다.
  * 표준 Load Balancer 사용 하는 경우 **HA 포트** 를 선택 합니다.
  * 기본 Load Balancer 사용 하는 경우 다음 포트에 대 한 부하 분산 규칙을 만듭니다.
    * 32<strong>&lt;nr&gt;</strong> TCP
    * 33<strong>&lt;nr&gt;</strong> TCP
    * 5<strong>&lt;nr&gt;</strong>13 TCP
    * 5<strong>&lt;nr&gt;</strong>14 TCP
    * 5<strong>&lt;nr&gt;</strong>16 TCP


> [!Note]
> 공용 IP 주소가 없는 Vm이 내부 (공용 IP 주소 없음) 표준 Azure 부하 분산 장치의 백 엔드 풀에 배치 되는 경우 공용 엔드포인트으로의 라우팅을 허용 하기 위해 추가 구성을 수행 하지 않는 한 아웃 바운드 인터넷 연결이 없습니다. 아웃 바운드 연결을 설정 하는 방법에 대 한 자세한 내용은 [SAP 고가용성 시나리오에서 Azure 표준 Load Balancer를 사용 하 여 Virtual Machines에 대 한 공용 엔드포인트 연결](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-standard-load-balancer-outbound-connections)을 참조 하세요.  

> [!IMPORTANT]
> Azure Load Balancer 뒤에 배치 되는 Azure Vm에서 TCP 타임 스탬프를 사용 하도록 설정 하지 마세요. TCP 타임 스탬프를 사용 하도록 설정 하면 상태 프로브가 실패 합니다. **Tcp_timestamps** 매개 변수를 **0**으로 설정 합니다. 자세한 내용은 [Load Balancer 상태 프로브](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview)를 참조 하세요.

## <a name="sap-nfs-shares"></a>SAP NFS 공유

SAP NetWeaver에는 전송, 프로필 디렉터리 등에 대 한 공유 저장소가 필요 합니다. 항상 사용 가능한 SAP 시스템의 경우 항상 사용 가능한 NFS 공유를 사용 하는 것이 중요 합니다. SAP NFS 공유에 대 한 아키텍처를 결정 해야 합니다. 한 가지 옵션은 여러 SAP 시스템 간에 공유 될 수 있는 [SUSE Linux Enterprise Server의 Azure vm에서 항상 사용 가능한 NFS 클러스터][nfs-ha]를 빌드하는 것입니다. 

또 다른 옵션은 [Azure NetApp FILES NFS 볼륨](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes)에 공유를 배포 하는 것입니다.  Azure NetApp Files를 사용 하 여 SAP NFS 공유에 대 한 고가용성을 제공 합니다.

## <a name="deploy-the-first-sap-system-in-the-cluster"></a>클러스터에 첫 번째 SAP 시스템 배포

이제 SAP NFS 공유에 대 한 아키텍처를 결정 했으므로 해당 설명서에 따라 클러스터에 첫 번째 SAP 시스템을 배포 합니다.

* 항상 사용 가능한 NFS 서버를 사용 하는 경우 [sap 응용 프로그램 SUSE Linux Enterprise Server에서 Azure vm의 Sap NetWeaver에 대 한 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse)을 따릅니다.  
* NFS 볼륨 Azure NetApp Files 사용 하는 경우 [sap 응용 프로그램에 대 한 Azure NetApp Files를 사용 하 SUSE Linux Enterprise Server에서 Azure vm의 Sap NetWeaver에 대 한 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files) 을 따릅니다.

위에 나열 된 문서에서는 필요한 인프라를 준비 하 고, 클러스터를 빌드하고, SAP 응용 프로그램을 실행 하기 위해 OS를 준비 하는 단계를 안내 합니다.  

> [!TIP]
> 클러스터에 추가 SAP Sid를 추가 하기 전에 첫 번째 시스템이 배포 된 후 클러스터의 장애 조치 (failover) 기능을 항상 테스트 합니다. 이렇게 하면 클러스터에 추가 SAP 시스템의 복잡성을 추가 하기 전에 클러스터 기능이 작동 한다는 것을 알 수 있습니다.   

## <a name="deploy-additional-sap-systems-in-the-cluster"></a>클러스터에 추가 SAP 시스템 배포

이 예제에서는 system **n w 1** 가 클러스터에 이미 배포 되어 있다고 가정 합니다. **N w 2** 및 **NW3**클러스터에서 배포 하는 방법을 보여 줍니다. 

다음 항목에는 접두사 **[A]** (모든 노드에 적용됨), **[1]** (노드 1에만 적용됨), **[2]** (노드 2에만 적용됨) 접두사가 표시되어 있습니다.

### <a name="prerequisites"></a>필수 조건 

> [!IMPORTANT]
> 클러스터에 추가 SAP 시스템을 배포 하기 위한 지침을 수행 하기 전에 첫 번째 시스템 배포 중에만 필요한 단계를 설명 하는 지침에 따라 클러스터의 첫 번째 SAP 시스템을 배포 합니다.  

이 문서에서는 다음을 가정 합니다.
* Pacemaker 클러스터가 이미 구성 되어 실행 되 고 있습니다.  
* 하나 이상의 SAP 시스템 (ASCS/ERS 인스턴스)이 이미 배포 되어 클러스터에 실행 되 고 있습니다.  
* 클러스터 장애 조치 (failover) 기능이 테스트 되었습니다.  
* 모든 SAP 시스템에 대 한 NFS 공유를 배포 합니다.  

### <a name="prepare-for-sap-netweaver-installation"></a>SAP NetWeaver 설치 준비

1. 새로 배포한 시스템 (즉, **n w 2**, **NW3**)에 대 한 구성을 기존 Azure Load Balancer에 추가 합니다. 지침에 따라 [Azure Portal를 통해 수동으로 배포 Azure Load Balancer](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files#deploy-azure-load-balancer-manually-via-azure-portal)합니다. 구성에 대 한 IP 주소, 상태 프로브 포트, 부하 분산 규칙을 조정 합니다.  

2. **[A]** 추가 SAP 시스템에 대 한 이름 확인을 설정 합니다. DNS 서버를 사용 하거나 모든 노드에서 `/etc/hosts`를 수정할 수 있습니다. 이 예제에서는 `/etc/hosts` 파일을 사용 하는 방법을 보여 줍니다.  사용자 환경에 대 한 IP 주소 및 호스트 이름을 조정 합니다. 

    ```
    sudo vi /etc/hosts
    # IP address of the load balancer frontend configuration for NW2 ASCS
    10.3.1.16 msnw2ascs
    # IP address of the load balancer frontend configuration for NW3 ASCS
    10.3.1.13 msnw3ascs
    # IP address of the load balancer frontend configuration for NW2 ERS
    10.3.1.17 msnw2ers
    # IP address of the load balancer frontend configuration for NW3 ERS
    10.3.1.19 msnw3ers
    # IP address for virtual host name for the NFS server for NW2
    10.3.1.31 nw2-nfs
    # IP address for virtual host name for the NFS server for NW3
    10.3.1.32 nw3-nfs
   ```

3. **[A]** 클러스터에 배포 하는 추가 **n w 2** 및 **NW3** SAP 시스템에 대 한 공유 디렉터리를 만듭니다. 

    ```
    sudo mkdir -p /sapmnt/NW2
    sudo mkdir -p /usr/sap/NW2/SYS
    sudo mkdir -p /usr/sap/NW2/ASCS10
    sudo mkdir -p /usr/sap/NW2/ERS12
    sudo mkdir -p /sapmnt/NW3
    sudo mkdir -p /usr/sap/NW3/SYS
    sudo mkdir -p /usr/sap/NW3/ASCS20
    sudo mkdir -p /usr/sap/NW3/ERS22

    
    sudo chattr +i /sapmnt/NW2
    sudo chattr +i /usr/sap/NW2/SYS
    sudo chattr +i /usr/sap/NW2/ASCS10
    sudo chattr +i /usr/sap/NW2/ERS12
    sudo chattr +i /sapmnt/NW3
    sudo chattr +i /usr/sap/NW3/SYS
    sudo chattr +i /usr/sap/NW3/ASCS20
    sudo chattr +i /usr/sap/NW3/ERS22
   ```

4. **[A]** 클러스터에 배포 하는 추가 sap 시스템에 대 한/Sapmnt/SID 및/usr/sap/SID/SYS 파일 시스템을 탑재 하도록 `autofs`를 구성 합니다. 이 예제에서는 **n w 2** 및 **NW3**입니다.  

   클러스터에 배포 하는 추가 SAP 시스템의 파일 시스템을 사용 하 여 파일 `/etc/auto.direct`를 업데이트 합니다.  

   * NFS 파일 서버를 사용 하는 경우 [여기](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse#prepare-for-sap-netweaver-installation) 에 있는 지침을 따르세요.
   * Azure NetApp Files 사용 하는 경우 [여기](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files#prepare-for-sap-netweaver-installation) 에 있는 지침을 따르세요. 

   새로 추가 된 공유를 탑재 하려면 `autofs` 서비스를 다시 시작 해야 합니다.  

### <a name="install-ascs--ers"></a>ASCS/ERS 설치

1. 클러스터에 배포 하는 추가 SAP 시스템의 ASCS 인스턴스에 대 한 가상 IP 및 상태 프로브 클러스터 리소스를 만듭니다. 여기에 표시 된 예제는 항상 사용 가능한 NFS 서버를 사용 하 여 **n w 2** 및 **NW3** ascs에 대 한 것입니다.  

   > [!IMPORTANT]
   > 최신 테스트로 인해 netcat이 백로그로 인 한 요청 응답을 중지 하 고 하나의 연결만 처리할 수 있는 경우를 확인할 수 있습니다. Netcat 리소스는 Azure 부하 분산 장치 요청에 대 한 수신 대기를 중지 하 고 부동 IP는 사용할 수 없게 됩니다.  
   > 
   > 기존 Pacemaker 클러스터의 경우 [Azure 부하 분산 장치 검색 강화](https://www.suse.com/support/kb/doc/?id=7024128)의 지침에 따라 netcat을 socat로 바꾸는 것이 좋습니다. 변경 작업을 수행 하려면 짧은 가동 중지 시간이 필요 합니다.  

    ```
      sudo crm configure primitive fs_NW2_ASCS Filesystem device='nw2-nfs:/NW2/ASCS' directory='/usr/sap/NW2/ASCS10' fstype='nfs4' \
       op start timeout=60s interval=0 \
       op stop timeout=60s interval=0 \
       op monitor interval=20s timeout=40s
   
      sudo crm configure primitive vip_NW2_ASCS IPaddr2 \
        params ip=10.3.1.16 cidr_netmask=24 \
        op monitor interval=10 timeout=20
   
      sudo crm configure primitive nc_NW2_ASCS anything \
        params binfile="/usr/bin/socat" cmdline_options="-U TCP-LISTEN:62010,backlog=10,fork,reuseaddr /dev/null" \
        op monitor timeout=20s interval=10 depth=0
   
      sudo crm configure group g-NW2_ASCS fs_NW2_ASCS nc_NW2_ASCS vip_NW2_ASCS \
         meta resource-stickiness=3000

      sudo crm configure primitive fs_NW3_ASCS Filesystem device='nw3-nfs:/NW3/ASCS' directory='/usr/sap/NW3/ASCS20' fstype='nfs4' \
        op start timeout=60s interval=0 \
        op stop timeout=60s interval=0 \
        op monitor interval=20s timeout=40s
   
      sudo crm configure primitive vip_NW3_ASCS IPaddr2 \
       params ip=10.3.1.13 cidr_netmask=24 \
       op monitor interval=10 timeout=20
   
      sudo crm configure primitive nc_NW3_ASCS anything \
        params binfile="/usr/bin/socat" cmdline_options="-U TCP-LISTEN:62020,backlog=10,fork,reuseaddr /dev/null" \
        op monitor timeout=20s interval=10 depth=0
   
      sudo crm configure group g-NW3_ASCS fs_NW3_ASCS nc_NW3_ASCS vip_NW3_ASCS \
        meta resource-stickiness=3000
    ```

   리소스를 만들 때 다른 클러스터 리소스에 할당 될 수 있습니다. 그룹화 하는 경우 클러스터 노드 중 하나로 마이그레이션됩니다. 클러스터 상태가 양호 이며 모든 리소스가 시작 되었는지 확인 합니다. 리소스가 실행되는 노드는 중요하지 않습니다.

2. **[1]** SAP NetWeaver ASCS 설치  

   ASCS에 대 한 부하 분산 장치 프런트 엔드 구성의 IP 주소에 매핑되는 가상 호스트 이름을 사용 하 여 SAP NetWeaver ASCS를 루트로 설치 합니다. 예를 들어 시스템 **n w 2**의 경우 가상 호스트 이름은 <b>msnw2ascs</b>, <b>10.3.1.16</b> 및 부하 분산 장치 프로브에 사용한 인스턴스 번호 (예: <b>10</b>)입니다. 시스템 **NW3**경우 가상 호스트 이름은 <b>msnw3ascs</b>, <b>10.3.1.13</b> 및 부하 분산 장치 프로브에 사용한 인스턴스 번호 (예: <b>20</b>)입니다.

   sapinst 매개 변수 SAPINST_REMOTE_ACCESS_USER를 사용하면 루트 권한이 없는 사용자의 sapinst 연결을 허용할 수 있습니다. 매개 변수 SAPINST_USE_HOSTNAME를 사용 하 여 가상 호스트 이름을 사용 하 여 SAP를 설치할 수 있습니다.  

     ```
      sudo swpm/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=virtual_hostname
     ```

   /Usr/sap/**sid**/ASCS**instance #** 에서 하위 폴더를 만들지 못한 경우에는 소유자를 **SID**adm으로 설정 하 고 sapsys를 ascs**인스턴스 #** 로 설정 하 고 다시 시도 하세요.

3. **[1]** 클러스터에 배포 하는 추가 SAP 시스템의 ERS 인스턴스에 대 한 가상 IP 및 상태 프로브 클러스터 리소스를 만듭니다. 여기에 표시 된 예제는 항상 사용 가능한 NFS 서버를 사용 하 여 **n w 2** 및 **NW3** ERS에 대 한 것입니다. 

   ```
    sudo crm configure primitive fs_NW2_ERS Filesystem device='nw2-nfs:/NW2/ASCSERS' directory='/usr/sap/NW2/ERS12' fstype='nfs4' \
      op start timeout=60s interval=0 \
      op stop timeout=60s interval=0 \
      op monitor interval=20s timeout=40s
   
    sudo crm configure primitive vip_NW2_ERS IPaddr2 \
      params ip=10.3.1.17 cidr_netmask=24 \
      op monitor interval=10 timeout=20
   
    sudo crm configure primitive nc_NW2_ERS anything \
     params binfile="/usr/bin/socat" cmdline_options="-U TCP-LISTEN:62112,backlog=10,fork,reuseaddr /dev/null" \
     op monitor timeout=20s interval=10 depth=0
   
    # WARNING: Resources nc_NW2_ASCS,nc_NW2_ERS violate uniqueness for parameter "binfile": "/usr/bin/socat"
    # Do you still want to commit (y/n)? y
   
    sudo crm configure group g-NW2_ERS fs_NW2_ERS nc_NW2_ERS vip_NW2_ERS

    sudo crm configure primitive fs_NW3_ERS Filesystem device='nw3-nfs:/NW3/ASCSERS' directory='/usr/sap/NW3/ERS22' fstype='nfs4' \
      op start timeout=60s interval=0 \
      op stop timeout=60s interval=0 \
      op monitor interval=20s timeout=40s
   
    sudo crm configure primitive vip_NW3_ERS IPaddr2 \
      params ip=10.3.1.19 cidr_netmask=24 \
      op monitor interval=10 timeout=20
   
    sudo crm configure primitive nc_NW3_ERS anything \
     params binfile="/usr/bin/socat" cmdline_options="-U TCP-LISTEN:62122,backlog=10,fork,reuseaddr /dev/null" \
     op monitor timeout=20s interval=10 depth=0
   
    # WARNING: Resources nc_NW3_ASCS,nc_NW3_ERS violate uniqueness for parameter "binfile": "/usr/bin/socat"
    # Do you still want to commit (y/n)? y
   
    sudo crm configure group g-NW3_ERS fs_NW3_ERS nc_NW3_ERS vip_NW3_ERS
   ```

   리소스를 만들 때 다른 클러스터 노드에 할당 될 수 있습니다. 그룹화 하는 경우 클러스터 노드 중 하나로 마이그레이션됩니다. 클러스터 상태가 양호 이며 모든 리소스가 시작 되었는지 확인 합니다.  

   그런 다음, 새로 만든 ERS 그룹의 리소스가 동일한 SAP 시스템의 ASCS 인스턴스가 설치 된 클러스터 노드와 반대 되는 클러스터 노드에서 실행 되 고 있는지 확인 합니다.  예를 들어 N W 2 ASCS가 `slesmsscl1`에 설치 된 경우에는 `slesmsscl2`에서 N W 2 ERS 그룹이 실행 중인지 확인 합니다.  다음 명령을 실행 하 여 N W 2 ERS 그룹을 `slesmsscl2`로 마이그레이션할 수 있습니다. 

    ```
      crm resource migrate g-NW2_ERS slesmsscl2 force
    ```

4. **[2]** SAP NetWeaver ERS 설치

   해당 ERS에 대 한 부하 분산 장치 프런트 엔드 구성의 IP 주소에 매핑되는 가상 호스트 이름을 사용 하 여 다른 노드에 SAP NetWeaver ERS를 루트로 설치 합니다. 예를 들어 system **n w 2**의 경우 가상 호스트 이름은 <b>msnw2ers</b>, <b>10.3.1.17</b> 및 부하 분산 장치 프로브에 사용한 인스턴스 번호 (예: <b>12</b>)가 됩니다. 시스템 **NW3**경우 가상 호스트 이름 <b>msnw3ers</b>, <b>10.3.1.19</b> 및 부하 분산 장치 프로브에 사용한 인스턴스 번호 (예: <b>22</b>). 

   sapinst 매개 변수 SAPINST_REMOTE_ACCESS_USER를 사용하면 루트 권한이 없는 사용자의 sapinst 연결을 허용할 수 있습니다. 매개 변수 SAPINST_USE_HOSTNAME를 사용 하 여 가상 호스트 이름을 사용 하 여 SAP를 설치할 수 있습니다.  

    ```
     sudo swpm/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=virtual_hostname
    ```

   > [!NOTE]
   > SWPM SP 20 PL 05 이상을 사용합니다. 그 이전 버전은 권한을 올바르게 설정하지 않으므로 설치가 실패합니다.

   /Usr/sap/**n w 2**/ers**인스턴스 #** 에서 하위 폴더를 만들지 못한 경우에는 소유자를 **sid**adm으로 설정 하 고 그룹을 ERS**인스턴스 #** 폴더의 sapsys로 설정 하 고 다시 시도 하세요.

   새로 배포 된 SAP 시스템의 ERS 그룹을 다른 클러스터 노드로 마이그레이션하려는 경우에는 해당 그룹에 대 한 위치 제약 조건을 제거 해야 합니다. 다음 명령을 실행 하 여 제약 조건을 제거할 수 있습니다. 예는 SAP systems **n w 2** and **NW3**에 대해 제공 됩니다.  

    ```
      crm resource unmigrate g-NW2_ERS
      crm resource unmigrate g-NW3_ERS
    ```

5. **[1]** 새로 설치 된 SAP 시스템에 대 한 ASCS/SCS 및 ERS 인스턴스 프로필을 조정 합니다. 아래에 표시 된 예제는 N W 2에 대 한 것입니다. 클러스터에 추가 된 모든 SAP 인스턴스에 대 한 ASCS/SCS 및 ERS 프로필을 조정 해야 합니다.  
 
 * ASCS/SCS 프로필

   ```
   sudo vi /sapmnt/NW2/profile/NW2_ASCS10_msnw2ascs
   
   # Change the restart command to a start command
   #Restart_Program_01 = local $(_EN) pf=$(_PF)
   Start_Program_01 = local $(_EN) pf=$(_PF)
   
   # Add the following lines
   service/halib = $(DIR_CT_RUN)/saphascriptco.so
   service/halib_cluster_connector = /usr/bin/sap_suse_cluster_connector
   
   # Add the keep alive parameter
   enque/encni/set_so_keepalive = true
   ```

 * ERS 프로필

   ```
   sudo vi /sapmnt/NW2/profile/NW2_ERS12_msnw2ers
   
   # Change the restart command to a start command
   #Restart_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   Start_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   
   # Add the following lines
   service/halib = $(DIR_CT_RUN)/saphascriptco.so
   service/halib_cluster_connector = /usr/bin/sap_suse_cluster_connector
   
   # remove Autostart from ERS profile
   # Autostart = 1
   ```

6. **[A]** 새로 배포 된 sap 시스템 (이 예제에서는 **n w 2** 및 **NW3**)에 대 한 sap 사용자를 구성 합니다. 

   ```
   # Add sidadm to the haclient group
   sudo usermod -aG haclient nw2adm
   sudo usermod -aG haclient nw3adm
   ```

7. 새로 설치 된 SAP 시스템용 ASCS 및 ERS SAP 서비스를 `sapservice` 파일에 추가 합니다. 아래에 표시 된 예제는 SAP systems **n w 2** 및 **NW3**에 대 한 것입니다.  

   ASCS 서비스 항목을 두 번째 노드에 추가하고 ERS 서비스 항목을 첫 번째 노드에 복사합니다. SAP 시스템의 ASCS 인스턴스가 설치 된 노드에서 각 SAP 시스템에 대 한 명령을 실행 합니다.  

    ```
     # Execute the following commands on slesmsscl1,assuming the NW2 ASCS instance was installed on slesmsscl1
     cat /usr/sap/sapservices | grep ASCS10 | sudo ssh slesmsscl2 "cat >>/usr/sap/sapservices"
     sudo ssh slesmsscl2 "cat /usr/sap/sapservices" | grep ERS12 | sudo tee -a /usr/sap/sapservices
     # Execute the following commands on slesmsscl2, assuming the NW3 ASCS instance was installed on slesmsscl2
     cat /usr/sap/sapservices | grep ASCS20 | sudo ssh slesmsscl1 "cat >>/usr/sap/sapservices"
     sudo ssh slesmsscl1 "cat /usr/sap/sapservices" | grep ERS22 | sudo tee -a /usr/sap/sapservices
    ```

8. **[1]** 새로 설치 된 sap 시스템에 대 한 sap 클러스터 리소스를 만듭니다. 

   ENSA1 (큐에 넣기 서버 1 아키텍처)를 사용 하는 경우 다음과 같이 SAP systems **n w 2** 및 **NW3** 에 대 한 리소스를 정의 합니다.

    ```
     sudo crm configure property maintenance-mode="true"
    
     sudo crm configure primitive rsc_sap_NW2_ASCS10 SAPInstance \
      operations \$id=rsc_sap_NW2_ASCS10-operations \
      op monitor interval=11 timeout=60 on_fail=restart \
      params InstanceName=NW2_ASCS10_msnw2ascs START_PROFILE="/sapmnt/NW2/profile/NW2_ASCS10_msnw2ascs" \
      AUTOMATIC_RECOVER=false \
      meta resource-stickiness=5000 failure-timeout=60 migration-threshold=1 priority=10
    
     sudo crm configure primitive rsc_sap_NW2_ERS12 SAPInstance \
      operations \$id=rsc_sap_NW2_ERS12-operations \
      op monitor interval=11 timeout=60 on_fail=restart \
      params InstanceName=NW2_ERS12_msnw2ers START_PROFILE="/sapmnt/NW2/profile/NW2_ERS12_msnw2ers" AUTOMATIC_RECOVER=false IS_ERS=true \
      meta priority=1000
    
     sudo crm configure modgroup g-NW2_ASCS add rsc_sap_NW2_ASCS10
     sudo crm configure modgroup g-NW2_ERS add rsc_sap_NW2_ERS12
    
     sudo crm configure colocation col_sap_NW2_no_both -5000: g-NW2_ERS g-NW2_ASCS
     sudo crm configure location loc_sap_NW2_failover_to_ers rsc_sap_NW2_ASCS10 rule 2000: runs_ers_NW2 eq 1
     sudo crm configure order ord_sap_NW2_first_start_ascs Optional: rsc_sap_NW2_ASCS10:start rsc_sap_NW2_ERS12:stop symmetrical=false
   
     sudo crm configure primitive rsc_sap_NW3_ASCS20 SAPInstance \
      operations \$id=rsc_sap_NW3_ASCS20-operations \
      op monitor interval=11 timeout=60 on_fail=restart \
      params InstanceName=NW3_ASCS10_msnw3ascs START_PROFILE="/sapmnt/NW3/profile/NW3_ASCS20_msnw3ascs" \
      AUTOMATIC_RECOVER=false \
      meta resource-stickiness=5000 failure-timeout=60 migration-threshold=1 priority=10
    
     sudo crm configure primitive rsc_sap_NW3_ERS22 SAPInstance \
      operations \$id=rsc_sap_NW3_ERS22-operations \
      op monitor interval=11 timeout=60 on_fail=restart \
      params InstanceName=NW3_ERS22_msnw3ers START_PROFILE="/sapmnt/NW3/profile/NW3_ERS22_msnw2ers" AUTOMATIC_RECOVER=false IS_ERS=true \
      meta priority=1000
    
     sudo crm configure modgroup g-NW3_ASCS add rsc_sap_NW3_ASCS20
     sudo crm configure modgroup g-NW3_ERS add rsc_sap_NW3_ERS22
    
     sudo crm configure colocation col_sap_NW3_no_both -5000: g-NW3_ERS g-NW3_ASCS
     sudo crm configure location loc_sap_NW3_failover_to_ers rsc_sap_NW3_ASCS10 rule 2000: runs_ers_NW3 eq 1
     sudo crm configure order ord_sap_NW3_first_start_ascs Optional: rsc_sap_NW3_ASCS20:start rsc_sap_NW3_ERS22:stop symmetrical=false
     sudo crm configure property maintenance-mode="false"
    ```

   Sap는 복제를 비롯 하 여 SAP NW 7.52에 대 한 지원 서비스를 도입 했습니다. ABAP Platform 1809부터 시작 하 여 큐에 넣기 서버 2가 기본적으로 설치 됩니다. 큐에 넣기 서버 2 지원에 대해서는 SAP note [2630416](https://launchpad.support.sap.com/#/notes/2630416) 을 참조 하세요.
   [ENSA2](https://help.sap.com/viewer/cff8531bc1d9416d91bb6781e628d4e0/1709%20001/en-US/6d655c383abf4c129b0e5c8683e7ecd8.html)(큐에 넣기 서버 2 아키텍처)를 사용 하는 경우 다음과 같이 SAP systems **n w 2** 및 **NW3** 에 대 한 리소스를 정의 합니다.

    ```
     sudo crm configure property maintenance-mode="true"
    
     sudo crm configure primitive rsc_sap_NW2_ASCS10 SAPInstance \
      operations \$id=rsc_sap_NW2_ASCS10-operations \
      op monitor interval=11 timeout=60 on_fail=restart \
      params InstanceName=NW2_ASCS10_msnw2ascs START_PROFILE="/sapmnt/NW2/profile/NW2_ASCS10_msnw2ascs" \
      AUTOMATIC_RECOVER=false \
      meta resource-stickiness=5000 
    
     sudo crm configure primitive rsc_sap_NW2_ERS12 SAPInstance \
      operations \$id=rsc_sap_NW2_ERS12-operations \
      op monitor interval=11 timeout=60 on_fail=restart \
      params InstanceName=NW2_ERS12_msnw2ers START_PROFILE="/sapmnt/NW2/profile/NW2_ERS12_msnw2ers" AUTOMATIC_RECOVER=false IS_ERS=true 
    
     sudo crm configure modgroup g-NW2_ASCS add rsc_sap_NW2_ASCS10
     sudo crm configure modgroup g-NW2_ERS add rsc_sap_NW2_ERS12
    
     sudo crm configure colocation col_sap_NW2_no_both -5000: g-NW2_ERS g-NW2_ASCS
     sudo crm configure order ord_sap_NW2_first_start_ascs Optional: rsc_sap_NW2_ASCS10:start rsc_sap_NW2_ERS12:stop symmetrical=false
   
     sudo crm configure primitive rsc_sap_NW3_ASCS20 SAPInstance \
      operations \$id=rsc_sap_NW3_ASCS20-operations \
      op monitor interval=11 timeout=60 on_fail=restart \
      params InstanceName=NW3_ASCS10_msnw3ascs START_PROFILE="/sapmnt/NW3/profile/NW3_ASCS20_msnw3ascs" \
      AUTOMATIC_RECOVER=false \
      meta resource-stickiness=5000
    
     sudo crm configure primitive rsc_sap_NW3_ERS22 SAPInstance \
      operations \$id=rsc_sap_NW3_ERS22-operations \
      op monitor interval=11 timeout=60 on_fail=restart \
      params InstanceName=NW3_ERS22_msnw3ers START_PROFILE="/sapmnt/NW3/profile/NW3_ERS22_msnw2ers" AUTOMATIC_RECOVER=false IS_ERS=true
    
     sudo crm configure modgroup g-NW3_ASCS add rsc_sap_NW3_ASCS20
     sudo crm configure modgroup g-NW3_ERS add rsc_sap_NW3_ERS22
    
     sudo crm configure colocation col_sap_NW3_no_both -5000: g-NW3_ERS g-NW3_ASCS
     sudo crm configure order ord_sap_NW3_first_start_ascs Optional: rsc_sap_NW3_ASCS20:start rsc_sap_NW3_ERS22:stop symmetrical=false
     sudo crm configure property maintenance-mode="false"
    ```

   이전 버전에서 업그레이드 하 고 큐에 넣기 서버 2로 전환 하는 경우 SAP note [2641019](https://launchpad.support.sap.com/#/notes/2641019)을 참조 하세요. 

   클러스터 상태가 정상이며 모든 리소스가 시작되었는지 확인합니다. 리소스가 실행되는 노드는 중요하지 않습니다.
   다음 예에서는 SAP systems **n w 2** 및 **NW3** 가 클러스터에 추가 된 후의 클러스터 리소스 상태를 보여 줍니다. 

    ```
     sudo crm_mon -r
    
    # Online: [ slesmsscl1 slesmsscl2 ]
    
    #Full list of resources:
    
    #stonith-sbd     (stonith:external/sbd): Started slesmsscl1
    # Resource Group: g-NW1_ASCS
    #     fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl2
    #     nc_NW1_ASCS        (ocf::heartbeat:anything):      Started slesmsscl2
    #     vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl2
    #     rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started slesmsscl2
    # Resource Group: g-NW1_ERS
    #     fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
    #     nc_NW1_ERS (ocf::heartbeat:anything):      Started slesmsscl1
    #     vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
    #     rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
    # Resource Group: g-NW2_ASCS
    #     fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
    #     nc_NW2_ASCS        (ocf::heartbeat:anything):      Started slesmsscl1
    #     vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
    #     rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
    # Resource Group: g-NW2_ERS
    #     fs_NW2_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
    #     nc_NW2_ERS (ocf::heartbeat:anything):      Started slesmsscl2
    #     vip_NW2_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
    #     rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
    # Resource Group: g-NW3_ASCS
    #     fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
    #     nc_NW3_ASCS        (ocf::heartbeat:anything):      Started slesmsscl1
    #     vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
    #     rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
    # Resource Group: g-NW3_ERS
    #     fs_NW3_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
    #     nc_NW3_ERS (ocf::heartbeat:anything):      Started slesmsscl2
    #     vip_NW3_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
    #     rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
    ```

   다음 그림에서는 Hawk (HA 웹 Konsole)에서 리소스를 표시 하는 방법을 보여 줍니다. 여기에는 SAP system **n w 2** 의 리소스가 확장 되어 있습니다.  

   [![SAP NetWeaver 고가용성 개요](./media/high-availability-guide-suse/ha-suse-multi-sid-hawk.png)](./media/high-availability-guide-suse/ha-suse-multi-sid-hawk-detail.png#lightbox)

### <a name="proceed-with-the-sap-installation"></a>SAP 설치를 진행 합니다. 

다음을 수행 하 여 SAP 설치를 완료 합니다.

* [SAP NetWeaver 응용 프로그램 서버 준비](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse#2d6008b0-685d-426c-b59e-6cd281fd45d7)
* [DBMS 인스턴스 설치](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse#install-database)
* [기본 SAP 응용 프로그램 서버 설치](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse#sap-netweaver-application-server-installation)
* 하나 이상의 추가 SAP 응용 프로그램 인스턴스 설치

## <a name="test-the-multi-sid-cluster-setup"></a>다중 SID 클러스터 설정 테스트

다음 테스트는 SUSE의 모범 사례 가이드에서 테스트 사례의 하위 집합입니다. 사용자 편의를 위해 포함 되었습니다. 클러스터 테스트의 전체 목록은 다음 설명서를 참조 하세요.

* 항상 사용 가능한 NFS 서버를 사용 하는 경우 [sap 응용 프로그램 SUSE Linux Enterprise Server에서 Azure vm의 Sap NetWeaver에 대 한 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse)을 따릅니다.  
* NFS 볼륨 Azure NetApp Files 사용 하는 경우 [sap 응용 프로그램에 대 한 Azure NetApp Files를 사용 하 SUSE Linux Enterprise Server에서 Azure vm의 Sap NetWeaver에 대 한 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files) 을 따릅니다.

항상 SUSE 모범 사례 가이드를 읽고 추가 될 수 있는 모든 추가 테스트를 수행 합니다.  
표시 되는 테스트는 3 개의 SAP 시스템이 설치 된 다중 SID 클러스터의 두 노드에 있습니다.  

1. HAGetFailoverConfig 및 HACheckFailoverConfig 테스트

   ASCS 인스턴스가 현재 실행 되 고 있는 노드에서 <sapsid>adm으로 다음 명령을 실행 합니다. 이러한 명령이 “실패: 메모리 부족”을 나타내며 실패할 경우 호스트 이름의 대시 때문일 수 있습니다. 이것은 알려진 문제로, SUSE에서는 sap-suse-cluster-connector 패키지에서 이 문제를 수정할 예정입니다.

   ```
    slesmsscl1:nw1adm 57> sapcontrol -nr 00 -function HAGetFailoverConfig

   # 10.12.2019 21:33:08
   # HAGetFailoverConfig
   # OK
   # HAActive: TRUE
   # HAProductVersion: SUSE Linux Enterprise Server for SAP Applications 12 SP4
   # HASAPInterfaceVersion: SUSE Linux Enterprise Server for SAP Applications 12 SP4 (sap_suse_cluster_connector 3.1.0)
   # HADocumentation: https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
   # HAActiveNode: slesmsscl1
   # HANodes: slesmsscl1, slesmsscl2

    slesmsscl1:nw1adm 53> sapcontrol -nr 00 -function HACheckFailoverConfig

    # 19.12.2019 21:19:58
    # HACheckFailoverConfig
    # OK
    # state, category, description, comment
    # SUCCESS, SAP CONFIGURATION, SAPInstance RA sufficient version, SAPInstance includes is-ers patch

    slesmsscl2:nw2adm 35> sapcontrol -nr 10 -function HAGetFailoverConfig

   # 10.12.2019 21:37:09
   # HAGetFailoverConfig
   # OK
   # HAActive: TRUE
   # HAProductVersion: SUSE Linux Enterprise Server for SAP Applications 12 SP4
   # HASAPInterfaceVersion: SUSE Linux Enterprise Server for SAP Applications 12 SP4 (sap_suse_cluster_connector 3.1.0)
   # HADocumentation: https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
   # HAActiveNode: slesmsscl2
   # HANodes: slesmsscl2, slesmsscl1

    slesmsscl2:nw2adm 52> sapcontrol -nr 10 -function HACheckFailoverConfig

    # 19.12.2019 21:17:39
    # HACheckFailoverConfig
    # OK
    # state, category, description, comment
    # SUCCESS, SAP CONFIGURATION, SAPInstance RA sufficient version, SAPInstance includes is-ers patch

    slesmsscl1:nw3adm 49> sapcontrol -nr 20 -function HAGetFailoverConfig

   # 10.12.2019 23:35:36
   # HAGetFailoverConfig
   # OK
   # HAActive: TRUE
   # HAProductVersion: SUSE Linux Enterprise Server for SAP Applications 12 SP4
   # HASAPInterfaceVersion: SUSE Linux Enterprise Server for SAP Applications 12 SP4 (sap_suse_cluster_connector 3.1.0)
   # HADocumentation: https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
   # HAActiveNode: slesmsscl1
   # HANodes: slesmsscl1, slesmsscl2

    slesmsscl1:nw3adm 52> sapcontrol -nr 20 -function HACheckFailoverConfig

    # 19.12.2019 21:10:42
    # HACheckFailoverConfig
    # OK
    # state, category, description, comment
    # SUCCESS, SAP CONFIGURATION, SAPInstance RA sufficient version, SAPInstance includes is-ers patch
   ```

2. ASCS 인스턴스를 수동으로 마이그레이션합니다. 이 예에서는 SAP 시스템 N W 2 ASCS 인스턴스를 마이그레이션하는 방법을 보여 줍니다.  
   리소스 상태, 테스트를 시작 하기 전:
   ```
    Full list of resources:
    stonith-sbd     (stonith:external/sbd): Started slesmsscl1
     Resource Group: g-NW1_ASCS
         fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW1_ASCS        (ocf::heartbeat:anything):      Started slesmsscl1
         vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW1_ERS
         fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW1_ERS (ocf::heartbeat:anything):      Started slesmsscl2
         vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW2_ASCS
         fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW2_ASCS        (ocf::heartbeat:anything):      Started slesmsscl1
         vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW2_ERS
         fs_NW2_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW2_ERS (ocf::heartbeat:anything):      Started slesmsscl2
         vip_NW2_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW3_ASCS
         fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW3_ASCS        (ocf::heartbeat:anything):      Started slesmsscl2
         vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW3_ERS
         fs_NW3_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW3_ERS (ocf::heartbeat:anything):      Started slesmsscl1
         vip_NW3_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
   ```

   N W 2 ASCS 인스턴스를 마이그레이션하려면 루트로 다음 명령을 실행 합니다.

   ```
    crm resource migrate rsc_sap_NW2_ASCS10 force
    # INFO: Move constraint created for rsc_sap_NW2_ASCS10
    
    crm resource unmigrate rsc_sap_NW2_ASCS10
   # INFO: Removed migration constraints for rsc_sap_NW2_ASCS10
   
   # Remove failed actions for the ERS that occurred as part of the migration
    crm resource cleanup rsc_sap_NW2_ERS12
   ```

   테스트 후 리소스 상태:

   ```
    Full list of resources:
    stonith-sbd     (stonith:external/sbd): Started slesmsscl1
     Resource Group: g-NW1_ASCS
         fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW1_ASCS        (ocf::heartbeat:anything):      Started slesmsscl1
         vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW1_ERS
         fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW1_ERS (ocf::heartbeat:anything):      Started slesmsscl2
         vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW2_ASCS
         fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW2_ASCS        (ocf::heartbeat:anything):      Started slesmsscl2
         vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW2_ERS
         fs_NW2_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW2_ERS (ocf::heartbeat:anything):      Started slesmsscl1
         vip_NW2_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW3_ASCS
         fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW3_ASCS        (ocf::heartbeat:anything):      Started slesmsscl2
         vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW3_ERS
         fs_NW3_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW3_ERS (ocf::heartbeat:anything):      Started slesmsscl1
         vip_NW3_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
   ```

1. 테스트 HAFailoverToNode. 여기에 제공 된 테스트는 SAP 시스템 N W 2 ASCS 인스턴스 마이그레이션을 보여 줍니다.  

   테스트 시작 전 리소스 상태:

   ```
    Full list of resources:
    stonith-sbd     (stonith:external/sbd): Started slesmsscl1
     Resource Group: g-NW1_ASCS
         fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW1_ASCS        (ocf::heartbeat:anything):      Started slesmsscl1
         vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW1_ERS
         fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW1_ERS (ocf::heartbeat:anything):      Started slesmsscl2
         vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW2_ASCS
         fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW2_ASCS        (ocf::heartbeat:anything):      Started slesmsscl2
         vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW2_ERS
         fs_NW2_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW2_ERS (ocf::heartbeat:anything):      Started slesmsscl1
         vip_NW2_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW3_ASCS
         fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW3_ASCS        (ocf::heartbeat:anything):      Started slesmsscl2
         vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW3_ERS
         fs_NW3_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW3_ERS (ocf::heartbeat:anything):      Started slesmsscl1
         vip_NW3_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
   ```

   **N w 2**adm으로 다음 명령을 실행 하 여 N W 2 ascs 인스턴스를 마이그레이션합니다.

   ```
    slesmsscl2:nw2adm 53> sapcontrol -nr 10 -host msnw2ascs -user nw2adm password -function HAFailoverToNode ""
   
   # run as root
   # Remove failed actions for the ERS that occurred as part of the migration
   crm resource cleanup rsc_sap_NW2_ERS12
   # Remove migration constraints
   crm resource clear rsc_sap_NW2_ASCS10
   #INFO: Removed migration constraints for rsc_sap_NW2_ASCS10
   ```

   테스트 후 리소스 상태:

   ```
    Full list of resources:
    stonith-sbd     (stonith:external/sbd): Started slesmsscl1
     Resource Group: g-NW1_ASCS
         fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW1_ASCS        (ocf::heartbeat:anything):      Started slesmsscl1
         vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW1_ERS
         fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW1_ERS (ocf::heartbeat:anything):      Started slesmsscl2
         vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW2_ASCS
         fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW2_ASCS        (ocf::heartbeat:anything):      Started slesmsscl1
         vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW2_ERS
         fs_NW2_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW2_ERS (ocf::heartbeat:anything):      Started slesmsscl2
         vip_NW2_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW3_ASCS
         fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW3_ASCS        (ocf::heartbeat:anything):      Started slesmsscl2
         vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW3_ERS
         fs_NW3_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW3_ERS (ocf::heartbeat:anything):      Started slesmsscl1
         vip_NW3_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
   ```

1. 노드 작동 중단 시뮬레이트

   테스트 시작 전 리소스 상태:

   ```
    Full list of resources:
    stonith-sbd     (stonith:external/sbd): Started slesmsscl1
     Resource Group: g-NW1_ASCS
         fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW1_ASCS        (ocf::heartbeat:anything):      Started slesmsscl2
         vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW1_ERS
         fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW1_ERS (ocf::heartbeat:anything):      Started slesmsscl1
         vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW2_ASCS
         fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW2_ASCS        (ocf::heartbeat:anything):      Started slesmsscl2
         vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW2_ERS
         fs_NW2_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW2_ERS (ocf::heartbeat:anything):      Started slesmsscl1
         vip_NW2_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW3_ASCS
         fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW3_ASCS        (ocf::heartbeat:anything):      Started slesmsscl2
         vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW3_ERS
         fs_NW3_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW3_ERS (ocf::heartbeat:anything):      Started slesmsscl1
         vip_NW3_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
   ```

   하나 이상의 ASCS 인스턴스가 실행 되 고 있는 노드의 루트로 다음 명령을 실행 합니다. 이 예제에서는 N W 1 및 NW3에 대 한 ASCS 인스턴스가 실행 되는 `slesmsscl2`에 대 한 명령을 실행 했습니다.  

   ```
    slesmsscl2:~ # echo b > /proc/sysrq-trigger
   ```

   SBD를 사용하는 경우 종료된 노드에서 Pacemaker가 자동으로 시작되지 않습니다. 노드가 다시 시작된 후의 상태는 다음과 같습니다.

   ```
    Online: [ slesmsscl1 ]
    OFFLINE: [ slesmsscl2 ]
    Full list of resources:

    stonith-sbd     (stonith:external/sbd): Started slesmsscl1
     Resource Group: g-NW1_ASCS
         fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW1_ASCS        (ocf::heartbeat:anything):      Started slesmsscl1
         vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW1_ERS
         fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW1_ERS (ocf::heartbeat:anything):      Started slesmsscl1
         vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW2_ASCS
         fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW2_ASCS        (ocf::heartbeat:anything):      Started slesmsscl1
         vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW2_ERS
         fs_NW2_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW2_ERS (ocf::heartbeat:anything):      Started slesmsscl1
         vip_NW2_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW3_ASCS
         fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW3_ASCS        (ocf::heartbeat:anything):      Started slesmsscl1
         vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW3_ERS
         fs_NW3_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW3_ERS (ocf::heartbeat:anything):      Started slesmsscl1
         vip_NW3_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
    
    Failed Resource Actions:
    * rsc_sap_NW1_ERS02_monitor_11000 on slesmsscl1 'not running' (7): call=125, status=complete, exitreason='',
        last-rc-change='Fri Dec 13 19:32:10 2019', queued=0ms, exec=0ms
    * rsc_sap_NW2_ERS12_monitor_11000 on slesmsscl1 'not running' (7): call=126, status=complete, exitreason='',
        last-rc-change='Fri Dec 13 19:32:10 2019', queued=0ms, exec=0ms
    * rsc_sap_NW3_ERS22_monitor_11000 on slesmsscl1 'not running' (7): call=127, status=complete, exitreason='',
        last-rc-change='Fri Dec 13 19:32:10 2019', queued=0ms, exec=0ms
   ```

   다음 명령을 사용하여 종료된 노드에서 Pacemaker를 시작하고, SBD 메시지를 정리하고, 실패한 리소스를 정리합니다.

   ```# run as root
   # list the SBD device(s)
   slesmsscl2:~ # cat /etc/sysconfig/sbd | grep SBD_DEVICE=
   # SBD_DEVICE="/dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116;/dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1;/dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3"
   
   slesmsscl2:~ # sbd -d /dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116 -d /dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1 -d /dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3 message slesmsscl2 clear
   
   slesmsscl2:~ # systemctl start pacemaker
   slesmsscl2:~ # crm resource cleanup rsc_sap_NW1_ERS02
   slesmsscl2:~ # crm resource cleanup rsc_sap_NW2_ERS12
   slesmsscl2:~ # crm resource cleanup rsc_sap_NW3_ERS22
   ```

   테스트 후 리소스 상태:

   ```
    Full list of resources:
    stonith-sbd     (stonith:external/sbd): Started slesmsscl1
     Resource Group: g-NW1_ASCS
         fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW1_ASCS        (ocf::heartbeat:anything):      Started slesmsscl1
         vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW1_ERS
         fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW1_ERS (ocf::heartbeat:anything):      Started slesmsscl2
         vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW2_ASCS
         fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW2_ASCS        (ocf::heartbeat:anything):      Started slesmsscl1
         vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW2_ERS
         fs_NW2_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW2_ERS (ocf::heartbeat:anything):      Started slesmsscl2
         vip_NW2_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW3_ASCS
         fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW3_ASCS        (ocf::heartbeat:anything):      Started slesmsscl1
         vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW3_ERS
         fs_NW3_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW3_ERS (ocf::heartbeat:anything):      Started slesmsscl2
         vip_NW3_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
   ```

## <a name="next-steps"></a>다음 단계

* [SAP 용 Azure Virtual Machines 계획 및 구현][planning-guide]
* [SAP 용 Azure Virtual Machines 배포][deployment-guide]
* [SAP 용 Azure Virtual Machines DBMS 배포][dbms-guide]
* [Azure의 SAP HANA(큰 인스턴스) 고가용성 및 재해 복구](hana-overview-high-availability-disaster-recovery.md) - Azure의 SAP HANA(큰 인스턴스)에 대한 고가용성 및 재해 복구 계획을 설정하는 방법을 알아봅니다.
* Azure Vm에서 SAP HANA의 고가용성을 설정 하 고 재해 복구를 계획 하는 방법에 대 한 자세한 내용은 [azure Virtual Machines (vm)의 SAP HANA 고가용성][sap-hana-ha] 을 참조 하세요.
