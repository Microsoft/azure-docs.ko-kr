---
title: RHEL에서 Azure NetApp Files로 standby를 사용 하 여 SAP HANA 확장 Microsoft Docs
description: Sap 응용 프로그램용 Azure NetApp Files를 사용 하는 Red Hat Enterprise Linux의 SAP NetWeaver에 대 한 고가용성 가이드
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
ms.date: 06/15/2020
ms.author: radeltch
ms.openlocfilehash: 9978137edb7874a8b93e0c9a5f1f9979ce449277
ms.sourcegitcommit: c6b9a46404120ae44c9f3468df14403bcd6686c1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88893173"
---
# <a name="deploy-a-sap-hana-scale-out-system-with-standby-node-on-azure-vms-by-using-azure-netapp-files-on-red-hat-enterprise-linux"></a>Red Hat Enterprise Linux에서 Azure NetApp Files를 사용하여 Azure VM에 대기 노드가 있는 SAP HANA 스케일 아웃 시스템 배포 

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-azure-doc]:https://docs.microsoft.com/azure/azure-netapp-files/
[anf-avail-matrix]:https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all 
[anf-register]:https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register
[anf-sap-applications-azure]:https://www.netapp.com/us/media/tr-4746.pdf

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
[1900823]:https://launchpad.support.sap.com/#/notes/1900823
[2292690]:https://launchpad.support.sap.com/#/notes/2292690
[2455582]:https://launchpad.support.sap.com/#/notes/2455582
[2593824]:https://launchpad.support.sap.com/#/notes/2593824
[2009879]:https://launchpad.support.sap.com/#/notes/2009879

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[sap-hana-ha]:sap-hana-high-availability.md
[nfs-ha]:high-availability-guide-suse-nfs.md


이 문서에서는 공유 저장소 볼륨에 대 한 [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md) 를 사용 하 여 Azure Red Hat Enterprise Linux vm (가상 머신)에서 대기 모드로 확장 구성에서 항상 사용 가능한 SAP HANA 시스템을 배포 하는 방법을 설명 합니다.  

예제 구성, 설치 명령 등에서 HANA 인스턴스는 **03** 이 고 HANA 시스템 ID는 **h n 1**입니다. 예제는 HANA 2.0 SP4 및 SAP 7.6 용 Red Hat Enterprise Linux을 기반으로 합니다. 

시작 하기 전에 다음 SAP note 및 백서를 참조 하세요.

* [Azure NetApp Files 설명서][anf-azure-doc] 
* SAP Note [1928533] 에는 다음이 포함 됩니다.  
  * SAP 소프트웨어 배포에 지원 되는 Azure VM 크기 목록
  * Azure VM 크기에 대한 중요한 용량 정보
  * 지원되는 SAP 소프트웨어 및 운영 체제(OS)와 데이터베이스 조합
  * Microsoft Azure의 Windows 및 Linux에 필요한 SAP 커널 버전
* SAP Note [2015553]: AZURE에서 SAP 지원 sap 소프트웨어 배포에 대 한 필수 구성 요소를 나열 합니다.
* SAP Note [2002167] Red Hat Enterprise Linux에 대해 권장 되는 OS 설정
* SAP Note [2009879]에는 Red Hat Enterprise Linux용 SAP HANA 지침이 있습니다.
* SAP Note [2178632]: AZURE에서 sap에 대해 보고 된 모든 모니터링 메트릭에 대 한 자세한 정보를 포함 합니다.
* SAP Note [2191498]: Azure에서 Linux에 필요한 Sap Host Agent 버전을 포함 합니다.
* SAP Note [2243692]: Azure에서 LINUX의 sap 라이선스에 대 한 정보 포함
* SAP Note [1999351]: Sap 용 Azure 고급 모니터링 확장에 대 한 추가 문제 해결 정보가 포함 되어 있습니다.
* SAP Note [1900823]: SAP HANA 저장소 요구 사항에 대 한 정보를 포함 합니다.
* [Sap Community Wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes): Linux에 필요한 모든 SAP note를 포함 합니다.
* [Linux에서 SAP용 Azure Virtual Machines 계획 및 구현][planning-guide]
* [Linux에서 SAP용 Azure Virtual Machines 배포][deployment-guide]
* [Linux에서 SAP용 Azure Virtual Machines DBMS 배포][dbms-guide]
* 일반 RHEL 설명서
  * [High Availability Add-On Overview](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)(고가용성 추가 기능 개요)
  * [High Availability Add-On Administration](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)(고가용성 추가 기능 관리)
  * [High Availability Add-On Reference](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)(고가용성 추가 기능 참조)
  * [Red Hat Enterprise Linux 네트워킹 가이드](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/networking_guide)
* Azure 특정 RHEL 설명서:
  * [Install SAP HANA on Red Hat Enterprise Linux for Use in Microsoft Azure](https://access.redhat.com/public-cloud/microsoft-azure)(Microsoft Azure에서 사용할 용도로 Red Hat Enterprise Linux에 SAP HANA 설치)
* [Azure NetApp Files를 사용하는 Microsoft Azure의 NetApp SAP 애플리케이션][anf-sap-applications-azure]


## <a name="overview"></a>개요

HANA 고가용성을 달성 하는 한 가지 방법은 호스트 자동 장애 조치 (failover)를 구성 하는 것입니다. 호스트 자동 장애 조치 (failover)를 구성 하려면 하나 이상의 가상 컴퓨터를 HANA 시스템에 추가 하 고이를 대기 노드로 구성 합니다. 활성 노드가 실패 하면 대기 노드가 자동으로 수행 됩니다. Azure virtual machines와 함께 제공 되는 구성에서는 [Azure NetApp Files에서 NFS](../../../azure-netapp-files/azure-netapp-files-introduction.md)를 사용 하 여 자동 장애 조치 (failover)를 수행 합니다.  

> [!NOTE]
> 대기 노드는 모든 데이터베이스 볼륨에 액세스할 수 있어야 합니다. HANA 볼륨은 NFSv4 볼륨으로 탑재 되어야 합니다. NFSv4 프로토콜의 향상 된 파일 임대 기반 잠금 메커니즘은 펜스에 사용 됩니다 `I/O` . 

> [!IMPORTANT]
> 지원 되는 구성을 작성 하려면 HANA 데이터 및 로그 볼륨을 NFSv 4.1 볼륨으로 배포 하 고 NFSv 4.1 프로토콜을 사용 하 여 탑재 해야 합니다. 대기 노드를 사용한 HANA 호스트 자동 장애 조치 (failover) 구성은 NFSv3에서 지원 되지 않습니다.

![SAP NetWeaver 고가용성 개요](./media/sap-hana-high-availability-rhel/sap-hana-scale-out-standby-netapp-files-rhel.png)

위의 다이어그램에서 네트워크 권장 사항을 SAP HANA 다음 세 개의 서브넷이 하나의 Azure 가상 네트워크 내에 표시 됩니다. 
* 클라이언트 통신
* 저장소 시스템과 통신 하는 경우
* 내부 HANA 노드 간 통신의 경우

Azure NetApp 볼륨은 별도의 서브넷에 있으며 [Azure NetApp Files에 위임](../../../azure-netapp-files/azure-netapp-files-delegate-subnet.md)됩니다.  

이 구성 예제에서 서브넷은 다음과 같습니다.  

  - `client` 10.9.1.0/26  
  - `storage` 10.9.3.0/26  
  - `hana` 10.9.2.0/26  
  - `anf` 10.9.0.0/26 (Azure NetApp Files에 위임 된 서브넷)

## <a name="set-up-the-azure-netapp-files-infrastructure"></a>Azure NetApp Files 인프라 설정 

Azure NetApp Files 인프라 설치를 진행 하기 전에 [Azure NetApp Files 설명서][anf-azure-doc]를 숙지 하세요. 

Azure NetApp Files는 여러 [Azure 지역](https://azure.microsoft.com/global-infrastructure/services/?products=netapp)에서 사용할 수 있습니다. 선택한 Azure 지역에서 Azure NetApp Files를 제공 하는지 확인 합니다.  

Azure 지역에서 Azure NetApp Files 가용성에 대 한 자세한 내용은 [Azure 지역별 가용성 Azure NetApp Files][anf-avail-matrix]을 참조 하세요.  

Azure NetApp Files를 배포 하기 전에 [Azure NetApp Files 지침에 등록][anf-register]하 여 Azure NetApp Files에 대 한 온 보 딩을 요청 합니다. 

### <a name="deploy-azure-netapp-files-resources"></a>Azure NetApp Files 리소스 배포  

다음 지침에서는 이미 [Azure virtual network](../../../virtual-network/virtual-networks-overview.md)를 배포 했다고 가정 합니다. Azure NetApp Files 리소스가 탑재 되는 Azure NetApp Files 리소스 및 Vm은 동일한 Azure virtual network 또는 피어 링 Azure virtual networks에 배포 되어야 합니다.  

1. 리소스를 아직 배포 하지 않은 경우 [Azure NetApp Files에 대 한 온 보 딩](../../../azure-netapp-files/azure-netapp-files-register.md)을 요청 합니다.  

2. [Netapp 계정 만들기](../../../azure-netapp-files/azure-netapp-files-create-netapp-account.md)의 지침에 따라 선택한 Azure 지역에서 netapp 계정을 만듭니다.  

3. [Azure NetApp Files 용량 풀 설정](../../../azure-netapp-files/azure-netapp-files-set-up-capacity-pool.md)의 지침에 따라 Azure NetApp Files 용량 풀을 설정 합니다.  

   이 문서에 제공 된 HANA 아키텍처는 *Ultra Service* 수준에서 단일 Azure NetApp Files 용량 풀을 사용 합니다. Azure의 HANA 워크 로드의 경우 Azure NetApp Files *Ultra* 또는 *Premium* [서비스 수준을](../../../azure-netapp-files/azure-netapp-files-service-levels.md)사용 하는 것이 좋습니다.  

4. [Azure NetApp Files에 서브넷 위임](../../../azure-netapp-files/azure-netapp-files-delegate-subnet.md)의 지침에 설명 된 대로 Azure NetApp Files에 서브넷을 위임 합니다.  

5. [Azure NetApp Files에 대 한 NFS 볼륨 만들기](../../../azure-netapp-files/azure-netapp-files-create-volumes.md)의 지침에 따라 Azure NetApp Files 볼륨을 배포 합니다.  

   볼륨을 배포할 때 **nfsv 4.1** 버전을 선택 해야 합니다. 지정된 Azure NetApp Files [서브넷](/rest/api/virtualnetwork/subnets)에 볼륨을 배포합니다. Azure NetApp 볼륨의 IP 주소는 자동으로 할당됩니다. 
   
   Azure NetApp Files 리소스와 Azure Vm은 동일한 Azure virtual network 또는 피어 링 Azure virtual network에 있어야 합니다. 예를 들어 **h n 1**-Mnt00001, **h n 1**-mnt00001 등은 볼륨 이름**및 nfs://10.9.0.4/h n 1**, mnt00001**nfs://10.9.0.4/**-h n 1, mnt00001 등은 Azure NetApp Files 볼륨에 대 한 파일 경로입니다.  

   * volume **h n 1**-mnt00001 (nfs://10.9.0.4/**h n 1**)
   * volume **h n 1**-mnt00002 (nfs://10.9.0.4/**h n 1**)
   * volume **h n 1**-mnt00001 (nfs://10.9.0.4/**h n 1**-mnt00001)
   * volume **h n 1**-mnt00002 (nfs://10.9.0.4/**h n 1**-mnt00002)
   * volume **h n 1**-shared (nfs://10.9.0.4/**h n 1**-shared)
   
   이 예제에서는 각 HANA 데이터 및 로그 볼륨에 대해 별도의 Azure NetApp Files 볼륨을 사용 했습니다. 더 저렴 하거나 생산적이 아닌 시스템에서 더 비용 최적화 된 구성의 경우 모든 데이터 마운트를 단일 볼륨에 저장 하 고 모든 로그를 다른 단일 볼륨에 탑재할 수 있습니다.  

### <a name="important-considerations"></a>중요한 고려 사항

노드 간 시나리오를 사용 하 여 스케일 아웃 SAP HANA에 대 한 Azure NetApp Files를 만들 때 다음과 같은 중요 한 사항을 고려해 야 합니다.

- 최소 용량 풀은 4 tebibytes (TiB)입니다.  
- 최소 볼륨 크기는 100 GiB (바이트)입니다.
- Azure NetApp Files 및 Azure NetApp Files 볼륨이 탑재 될 모든 가상 머신은 동일한 지역의 동일한 Azure 가상 네트워크 또는 [피어 링 가상 네트워크](../../../virtual-network/virtual-network-peering-overview.md) 에 있어야 합니다.  
- 선택한 가상 네트워크에 Azure NetApp Files에 위임 된 서브넷이 있어야 합니다.
- Azure NetApp Files 볼륨의 처리량은 [Azure NetApp Files 서비스 수준](../../../azure-netapp-files/azure-netapp-files-service-levels.md)에 설명 된 볼륨 할당량 및 서비스 수준의 기능입니다. HANA Azure NetApp 볼륨의 크기를 조정 하는 경우 결과 처리량이 HANA 시스템 요구 사항을 충족 하는지 확인 합니다.  
- Azure NetApp Files [내보내기 정책을](../../../azure-netapp-files/azure-netapp-files-configure-export-policy.md)사용 하면 허용 되는 클라이언트, 액세스 유형 (읽기-쓰기, 읽기 전용 등)을 제어할 수 있습니다. 
- Azure NetApp Files 기능은 아직 영역을 인식 하지 않습니다. 현재이 기능은 Azure 지역의 모든 가용성 영역에 배포 되지 않습니다. 일부 Azure 지역에서 잠재적 대기 시간 영향을 염두에 두어야 합니다.  

> [!IMPORTANT]
> SAP HANA 워크로드의 경우 짧은 대기 시간이 매우 중요합니다. Microsoft 담당자와 협력하여 가상 머신과 Azure NetApp Files 볼륨이 근접하게 배포되도록 해야 합니다.  

### <a name="sizing-for-hana-database-on-azure-netapp-files"></a>Azure NetApp Files에서 HANA 데이터베이스 크기 조정

Azure NetApp Files 볼륨의 처리량은 [Azure NetApp Files 서비스 수준](../../../azure-netapp-files/azure-netapp-files-service-levels.md)에 설명 된 대로 볼륨 크기 및 서비스 수준의 기능입니다. 

Azure에서 SAP 용 인프라를 설계할 때 최소 처리량 특성으로 변환 되는 SAP의 최소 저장소 요구 사항에 주의 해야 합니다.

- 1 메가바이트 i/o 크기를 사용 하는 초당 250 메가바이트 (MB/s)의/hana/log 읽기/쓰기입니다.  
- 16mb 및 64 MB i/o 크기에 대 한/hana/data에 대 한 최소 400 m b/초 읽기 작업입니다.  
- 16mb 및 64 MB i/o 크기를 사용 하는/hana/data에 대 한 최소 250 m b/s의 쓰기 작업입니다. 

볼륨 할당량 1TiB당 [Azure NetApp Files 처리량 한도](../../../azure-netapp-files/azure-netapp-files-service-levels.md)는 다음과 같습니다.
- Premium Storage 계층-64 MiB/s  
- Ultra Storage 계층-128 MiB/s  

데이터 및 로그에 대 한 SAP 최소 처리량 요구 사항과/hana/shared에 대 한 지침을 충족 하기 위해 권장 되는 크기는 다음과 같습니다.

| 볼륨 | 크기<br>Premium Storage 계층 | 크기<br>Ultra Storage 계층 | 지원되는 NFS 프로토콜 |
| --- | --- | --- | --- |
| /hana/log/ | 4TiB | 2TiB | v4.1 |
| /hana/data | 6.3TiB | 3.2TiB | v4.1 |
| /hana/shared | 4 작업자 노드 당 1xRAM | 4 작업자 노드 당 1xRAM | v3 또는 v4.1 |

이 문서에서 제공 하는 레이아웃에 대 한 SAP HANA 구성은 Azure NetApp Files Ultra Storage 계층을 사용 합니다.

| 볼륨 | 크기<br>Ultra Storage 계층 | 지원되는 NFS 프로토콜 |
| --- | --- | --- |
| /hana/log/mnt00001 | 2TiB | v4.1 |
| /hana/log/mnt00002 | 2TiB | v4.1 |
| /hana/data/mnt00001 | 3.2TiB | v4.1 |
| /hana/data/mnt00002 | 3.2TiB | v4.1 |
| /hana/shared | 2TiB | v3 또는 v4.1 |

> [!NOTE]
> 여기에 명시 된 Azure NetApp Files 크기 조정 권장 사항은 SAP에서 인프라 공급자에 대해 권장 하는 최소 요구 사항을 충족 하는 것입니다. 실제 고객 배포 및 워크 로드 시나리오에서는 이러한 크기가 충분 하지 않을 수 있습니다. 이러한 권장 사항을 시작점으로 삼아 워크로드의 요구 사항에 따라 조정합니다.  

> [!TIP]
> 볼륨을 *분리* 하거나 가상 컴퓨터를 중지 하거나 SAP HANA를 중지 하지 않고도 Azure NetApp Files 볼륨의 크기를 동적으로 조정할 수 있습니다. 이 방법을 사용 하면 응용 프로그램의 예상 처리량과 예기치 않은 처리량 요구를 유연 하 게 충족할 수 있습니다.

## <a name="deploy-linux-virtual-machines-via-the-azure-portal"></a>Azure Portal을 통해 Linux 가상 컴퓨터 배포

먼저 Azure NetApp Files 볼륨을 만들어야 합니다. 그런 후에 다음 단계를 수행 합니다.
1. [Azure 가상 네트워크](../../../virtual-network/virtual-networks-overview.md)에 [azure 가상 네트워크 서브넷](../../../virtual-network/virtual-network-manage-subnet.md) 을 만듭니다. 
1. VM을 배포합니다. 
1. 추가 네트워크 인터페이스를 만들고 네트워크 인터페이스를 해당 Vm에 연결 합니다.  

   각 가상 머신에는 세 개의 Azure virtual network 서브넷 (및)에 해당 하는 세 개의 네트워크 인터페이스가 있습니다 `client` `storage` `hana` . 

   자세한 내용은 [여러 네트워크 인터페이스 카드를 사용 하 여 Azure에서 Linux 가상 머신 만들기](../../linux/multiple-nics.md)를 참조 하세요.  

> [!IMPORTANT]
> SAP HANA 워크로드의 경우 짧은 대기 시간이 매우 중요합니다. 짧은 대기 시간을 얻으려면 Microsoft 담당자와 협력 하 여 가상 머신 및 Azure NetApp Files 볼륨이 근접 하 게 배포 되었는지 확인 합니다. SAP HANA Azure NetApp Files를 사용 하는 [새 SAP HANA 시스템을 온 보 딩](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxjSlHBUxkJBjmARn57skvdUQlJaV0ZBOE1PUkhOVk40WjZZQVJXRzI2RC4u) 하는 경우 필요한 정보를 제출 합니다. 
 
다음 지침에서는 리소스 그룹, Azure 가상 네트워크 및 세 개의 Azure virtual network 서브넷 (및)을 이미 만들었다고 가정 합니다. `client` `storage` `hana` Vm을 배포할 때 클라이언트 네트워크 인터페이스가 Vm의 기본 인터페이스를 갖도록 클라이언트 서브넷을 선택 합니다. 또한 저장소 서브넷 게이트웨이를 통해 위임 Azure NetApp Files 된 서브넷에 대 한 명시적 경로를 구성 해야 합니다. 

> [!IMPORTANT]
> 선택한 OS가 사용 중인 특정 VM 형식에 대 한 SAP HANA에 대해 SAP 인증 되는지 확인 합니다. 이러한 유형에 대 한 SAP HANA 인증 VM 유형 및 OS 릴리스 목록은 [SAP HANA 인증 된 IaaS 플랫폼](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) 사이트로 이동 합니다. 나열 된 VM 형식에 대 한 세부 정보를 클릭 하 여 해당 형식에 대해 지원 되는 SAP HANA OS 릴리스의 전체 목록을 가져옵니다.  

1. SAP HANA에 대 한 가용성 집합을 만듭니다. 최대 업데이트 도메인을 설정 했는지 확인 합니다.  

2. 다음 단계를 수행 하 여 세 개의 가상 머신 (**hanadb1**, **hanadb2**, **hanadb3**)를 만듭니다.  

   a. SAP HANA에 대해 지원 되는 Azure 갤러리의 Red Hat Enterprise Linux 이미지를 사용 합니다. 이 예제에서는 RHEL----HA 7.6 이미지를 사용 했습니다.  

   b. SAP HANA에 대해 이전에 만든 가용성 집합을 선택 합니다.  

   c. 클라이언트 Azure 가상 네트워크 서브넷을 선택 합니다. [가속화 네트워크](../../../virtual-network/create-vm-accelerated-networking-cli.md)를 선택 합니다.  

   가상 컴퓨터를 배포 하는 경우 네트워크 인터페이스 이름이 자동으로 생성 됩니다. 이 지침에서는 **hanadb1**, **hanadb2**및 **Hanadb3**클라이언트와 같이 클라이언트 Azure 가상 네트워크 서브넷에 연결 된 자동으로 생성 된 네트워크 인터페이스를 참조 합니다. 

3. `storage`가상 네트워크 서브넷 (이 예제에서는 **hanadb1**, **hanadb2**및 **hanadb3**)에 대 한 세 개의 네트워크 인터페이스를 각 가상 머신에 대해 하나씩 만듭니다.  

4. `hana`가상 네트워크 서브넷 (이 예제에서는 **hanadb1**, **hanadb2**, **hanadb3**)에 대 한 세 개의 네트워크 인터페이스를 각 가상 머신에 대해 하나씩 만듭니다.  

5. 다음 단계를 수행 하 여 새로 만든 가상 네트워크 인터페이스를 해당 가상 컴퓨터에 연결 합니다.  

    a. [Azure Portal](https://portal.azure.com/#home)의 가상 머신으로 이동 합니다.  

    b. 왼쪽 창에서 **Virtual Machines**을 선택 합니다. 가상 컴퓨터 이름 (예: **hanadb1**)을 필터링 한 다음 가상 컴퓨터를 선택 합니다.  

    c. **개요** 창에서 **중지** 를 선택 하 여 가상 컴퓨터의 할당을 취소 합니다.  

    d. **네트워킹**을 선택 하 고 네트워크 인터페이스를 연결 합니다. **네트워크 인터페이스 연결** 드롭다운 목록에서 및 서브넷에 대해 이미 생성 된 네트워크 인터페이스를 선택 합니다 `storage` `hana` .  
    
    e. **저장**을 선택합니다. 
 
    f. 나머지 가상 컴퓨터 (이 예제에서는  **hanadb2** 및 **hanadb3**)에 대해 b ~ e 단계를 반복 합니다.
 
    g. 지금은 가상 컴퓨터를 중지 됨 상태로 둡니다. 다음에는 새로 연결 된 모든 네트워크 인터페이스에 대해 [가속화 된 네트워킹](../../../virtual-network/create-vm-accelerated-networking-cli.md) 을 사용 하도록 설정 합니다.  

6. `storage`다음 단계를 수행 하 여 및 서브넷에 대 한 추가 네트워크 인터페이스에 대해 가속화 된 네트워킹을 사용 하도록 설정 합니다 `hana` .  

    a. [Azure Portal](https://portal.azure.com/#home)에서 [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) 를 엽니다.  

    b. 다음 명령을 실행 하 여 및 서브넷에 연결 된 추가 네트워크 인터페이스에 대 한 가속화 된 네트워킹을 사용 하도록 설정 `storage` `hana` 합니다.  

    <pre><code>
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb1-storage</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb2-storage</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb3-storage</b> --accelerated-networking true
    
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb1-hana</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb2-hana</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb3-hana</b> --accelerated-networking true

    </code></pre>

7. 다음 단계를 수행 하 여 가상 컴퓨터를 시작 합니다.  

    a. 왼쪽 창에서 **Virtual Machines**을 선택 합니다. 가상 컴퓨터 이름 (예: **hanadb1**)을 필터링 한 다음 선택 합니다.  

    b. **개요** 창에서 **시작**을 선택 합니다.  

## <a name="operating-system-configuration-and-preparation"></a>운영 체제 구성 및 준비

다음 섹션의 지침에는 다음 중 하나가 접두사로 추가 됩니다.
* **[A]**: 모든 노드에 적용 가능
* **[1]**: 노드 1에만 적용 가능
* **[2]**: 노드 2에만 적용 가능
* **[3]**: 노드 3에만 적용 가능

다음 단계를 수행 하 여 OS를 구성 하 고 준비 합니다.

1. **[A]** 가상 컴퓨터에서 호스트 파일을 유지 관리 합니다. 모든 서브넷에 대 한 항목을 포함 합니다. 이 예제의에 추가 된 항목은 다음과 같습니다 `/etc/hosts` .  

    <pre><code>
    # Storage
    10.9.3.4   hanadb1-storage
    10.9.3.5   hanadb2-storage
    10.9.3.6   hanadb3-storage
    # Client
    10.9.1.5   hanadb1
    10.9.1.6   hanadb2
    10.9.1.7   hanadb3
    # Hana
    10.9.2.4   hanadb1-hana
    10.9.2.5   hanadb2-hana
    10.9.2.6   hanadb3-hana
    </code></pre>

3. **[A]** Azure NetApp Files에 대 한 통신이 저장소 네트워크 인터페이스를 통해 이동 하도록 네트워크 경로를 추가 합니다.  

   이 예제에서는를 사용 `Networkmanager` 하 여 추가 네트워크 경로를 구성 합니다. 다음 지침에서는 저장소 네트워크 인터페이스가 인 것으로 가정 합니다 `eth1` .  
   먼저 장치에 대 한 연결 이름을 결정 `eth1` 합니다. 이 예제에서 장치에 대 한 연결 `eth1` 이름은 `Wired connection 1` 입니다.  

    <pre><code>
    # Execute as root
    nmcli connection
    # Result
    #NAME                UUID                                  TYPE      DEVICE
    #System eth0         5fb06bd0-0bb0-7ffb-45f1-d6edd65f3e03  ethernet  eth0
    #Wired connection 1  4b0789d1-6146-32eb-83a1-94d61f8d60a7  ethernet  eth1
    </code></pre>

   그런 다음을 통해 위임 된 네트워크 Azure NetApp Files에 대 한 추가 경로를 구성 `eth1` 합니다.  

    <pre><code>
    # Add the following route 
    # ANFDelegatedSubnet/cidr via StorageSubnetGW dev StorageNetworkInterfaceDevice
    nmcli connection modify <b>"Wired connection 1"</b> +ipv4.routes <b>"10.9.0.0/26 10.9.3.1"</b>
    </code></pre>

    VM을 다시 부팅 하 여 변경 내용을 활성화 합니다.  

3. **[A]** NFS 클라이언트 패키지를 설치 합니다.  

    <pre><code>
    yum install nfs-utils
    </code></pre>

3. **[A]** [Azure NetApp Files를 사용 하 여 Microsoft Azure에서 Netapp SAP 응용 프로그램][anf-sap-applications-azure]에 설명 된 대로 NFS를 사용 하 여 Azure netapp에서 SAP HANA를 실행 하기 위해 OS를 준비 합니다. NetApp 구성 설정에 대 한 */etc/sysctl.d/netapp-hana.conf* 구성 파일을 만듭니다.  

    <pre><code>
    vi /etc/sysctl.d/netapp-hana.conf
    # Add the following entries in the configuration file
    net.core.rmem_max = 16777216
    net.core.wmem_max = 16777216
    net.core.rmem_default = 16777216
    net.core.wmem_default = 16777216
    net.core.optmem_max = 16777216
    net.ipv4.tcp_rmem = 65536 16777216 16777216
    net.ipv4.tcp_wmem = 65536 16777216 16777216
    net.core.netdev_max_backlog = 300000 
    net.ipv4.tcp_slow_start_after_idle=0 
    net.ipv4.tcp_no_metrics_save = 1
    net.ipv4.tcp_moderate_rcvbuf = 1
    net.ipv4.tcp_window_scaling = 1
    net.ipv4.tcp_timestamps = 1
    net.ipv4.tcp_sack = 1
    </code></pre>

4. **[A]** 추가 최적화 설정을 사용 하 여 */etc/sysctl.d/ms-az.conf* 구성 파일을 만듭니다.  

    <pre><code>
    vi /etc/sysctl.d/ms-az.conf
    # Add the following entries in the configuration file
    net.ipv6.conf.all.disable_ipv6 = 1
    net.ipv4.tcp_max_syn_backlog = 16348
    net.ipv4.ip_local_port_range = 40000 65300
    net.ipv4.conf.all.rp_filter = 0
    sunrpc.tcp_slot_table_entries = 128
    vm.swappiness=10
    </code></pre>

5. **[A]** [Azure NetApp Files를 사용 하 여 Microsoft Azure에서 Netapp SAP 응용 프로그램][anf-sap-applications-azure]에 권장 되는 대로 sunrpc 설정을 조정 합니다.  

    <pre><code>
    vi /etc/modprobe.d/sunrpc.conf
    # Insert the following line
    options sunrpc tcp_max_slot_table_entries=128
    </code></pre>

6. **[A]** HANA 구성에 대 한 red Hat.

    SAP Note [2292690], [2455582], [2593824] 및에 설명 된 대로 RHEL를 구성 <https://access.redhat.com/solutions/2447641> 합니다.

    > [!NOTE]
    > HANA 2.0 SP04을 설치 하는 경우 `compat-sap-c++-7` SAP note [2593824]에 설명 된 대로 패키지를 설치 해야 SAP HANA를 설치할 수 있습니다. 

## <a name="mount-the-azure-netapp-files-volumes"></a>Azure NetApp Files 볼륨 탑재

1. **[A]** HANA 데이터베이스 볼륨의 탑재 위치를 만듭니다.  

    <pre><code>
    mkdir -p /hana/data/<b>HN1</b>/mnt00001
    mkdir -p /hana/data/<b>HN1</b>/mnt00002
    mkdir -p /hana/log/<b>HN1</b>/mnt00001
    mkdir -p /hana/log/<b>HN1</b>/mnt00002
    mkdir -p /hana/shared
    mkdir -p /usr/sap/<b>HN1</b>
    </code></pre>

2. **[1]** **h n 1**에/usr/sap에 대 한 노드 관련 디렉터리를 만듭니다.  

    <pre><code>
    # Create a temporary directory to mount <b>HN1</b>-shared
    mkdir /mnt/tmp
    # if using NFSv3 for this volume, mount with the following command
    mount <b>10.9.0.4</b>:/<b>HN1</b>-shared /mnt/tmp
    # if using NFSv4.1 for this volume, mount with the following command
    mount -t nfs -o sec=sys,vers=4.1 <b>10.9.0.4</b>:/<b>HN1</b>-shared /mnt/tmp
    cd /mnt/tmp
    mkdir shared usr-sap-<b>hanadb1</b> usr-sap-<b>hanadb2</b> usr-sap-<b>hanadb3</b>
    # unmount /hana/shared
    cd
    umount /mnt/tmp
    </code></pre>

3. **[A]** NFS 도메인 설정을 확인 합니다. 도메인이 기본 Azure NetApp Files 도메인(예: **`defaultv4iddomain.com`** )으로 구성되어 있고 매핑이 **nobody**로 설정되어 있는지 확인합니다.  

    > [!IMPORTANT]
    > VM의 `/etc/idmapd.conf`에서 NFS 도메인을 Azure NetApp Files의 기본 도메인 구성( **`defaultv4iddomain.com`** )과 일치하도록 설정해야 합니다. NFS 클라이언트(예: VM)의 도메인 구성과 NFS 서버(예: Azure NetApp 구성)가 일치하지 않는 경우 VM에 탑재된 Azure NetApp 볼륨의 파일에 대한 사용 권한이 `nobody`로 표시됩니다.  

    <pre><code>
    sudo cat /etc/idmapd.conf
    # Example
    [General]
    Domain = <b>defaultv4iddomain.com</b>
    [Mapping]
    Nobody-User = <b>nobody</b>
    Nobody-Group = <b>nobody</b>
    </code></pre>

4. **[A]** `nfs4_disable_idmapping`을 확인합니다. **Y**로 설정되어야 합니다. `nfs4_disable_idmapping`이 있는 디렉터리 구조를 만들려면 mount 명령을 실행합니다. 커널/드라이버용으로 액세스가 예약되어 있기 때문에 /sys/modules 아래에 디렉터리를 수동으로 만들 수 없습니다.  

    <pre><code>
    # Check nfs4_disable_idmapping 
    cat /sys/module/nfs/parameters/nfs4_disable_idmapping
    # If you need to set nfs4_disable_idmapping to Y
    mkdir /mnt/tmp
    mount 10.9.0.4:/HN1-shared /mnt/tmp
    umount  /mnt/tmp
    echo "Y" > /sys/module/nfs/parameters/nfs4_disable_idmapping
    # Make the configuration permanent
    echo "options nfs nfs4_disable_idmapping=Y" >> /etc/modprobe.d/nfs.conf
    </code></pre>

   매개 변수를 변경 하는 방법에 대 한 자세한 `nfs4_disable_idmapping` 내용은을 참조 하십시오 https://access.redhat.com/solutions/1749883 .

6. **[A]** 공유 Azure NetApp Files 볼륨을 탑재 합니다.  

    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.9.0.4:/<b>HN1</b>-data-mnt00001 /hana/data/<b>HN1</b>/mnt00001  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.9.0.4:/<b>HN1</b>-data-mnt00002 /hana/data/<b>HN1</b>/mnt00002  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.9.0.4:/<b>HN1</b>-log-mnt00001 /hana/log/<b>HN1</b>/mnt00001  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.9.0.4:/<b>HN1</b>-log-mnt00002 /hana/log/HN1/mnt00002  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.9.0.4:/<b>HN1</b>-shared/shared /hana/shared  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount all volumes
    sudo mount -a 
    </code></pre>

7. **[1]** **hanadb1**에 노드 특정 볼륨을 탑재 합니다.  

    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.9.0.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb1</b> /usr/sap/<b>HN1</b>  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    </code></pre>

8. **[2]** **hanadb2**에 노드 특정 볼륨을 탑재 합니다.  

    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.9.0.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb2</b> /usr/sap/<b>HN1</b>  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    </code></pre>

9. **[3]** **hanadb3**에 노드 특정 볼륨을 탑재 합니다.  

    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.9.0.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb3</b> /usr/sap/<b>HN1</b>  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    </code></pre>

10. **[A]** 모든 HANA 볼륨이 NFS 프로토콜 버전 **NFSv4**로 탑재 되어 있는지 확인 합니다.  

    <pre><code>
    sudo nfsstat -m
    # Verify that flag vers is set to <b>4.1</b> 
    # Example from <b>hanadb1</b>
    /hana/data/<b>HN1</b>/mnt00001 from 10.9.0.4:/<b>HN1</b>-data-mnt00001
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.9.3.4,local_lock=none,addr=10.9.0.4
    /hana/log/<b>HN1</b>/mnt00002 from 10.9.0.4:/<b>HN1</b>-log-mnt00002
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.9.3.4,local_lock=none,addr=10.9.0.4
    /hana/data/<b>HN1</b>/mnt00002 from 10.9.0.4:/<b>HN1</b>-data-mnt00002
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.9.3.4,local_lock=none,addr=10.9.0.4
    /hana/log/<b>HN1</b>/mnt00001 from 10.9.0.4:/<b>HN1</b>-log-mnt00001
    Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.9.3.4,local_lock=none,addr=10.9.0.4
    /usr/sap/<b>HN1</b> from 10.9.0.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb1</b>
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.9.3.4,local_lock=none,addr=10.9.0.4
    /hana/shared from 10.9.0.4:/<b>HN1</b>-shared/shared
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.9.3.4,local_lock=none,addr=10.9.0.4
    </code></pre>

## <a name="installation"></a>설치  

이 예제에서는 Azure를 사용 하는 대기 노드를 사용 하 여 스케일 아웃 구성에 SAP HANA을 배포 하는 경우 HANA 2.0 SP4를 사용 했습니다.  

### <a name="prepare-for-hana-installation"></a>HANA 설치 준비

1. **[A]** HANA 설치 전에 루트 암호를 설정 합니다. 설치가 완료 된 후 루트 암호를 사용 하지 않도록 설정할 수 있습니다. Execute as `root` 명령 `passwd` 입니다.  

2. **[1]** 암호를 입력 하 라는 메시지가 표시 되지 않고 SSH를 통해 **hanadb2** 및 **hanadb3**에 로그인 할 수 있는지 확인 합니다.  

    <pre><code>
    ssh root@<b>hanadb2</b>
    ssh root@<b>hanadb3</b>
    </code></pre>

3. **[A]** HANA 2.0 s p 4에 필요한 추가 패키지를 설치 합니다. 자세한 내용은 SAP Note [2593824](https://launchpad.support.sap.com/#/notes/2593824)을 참조 하세요. 

    <pre><code>
    yum install libgcc_s1 libstdc++6 compat-sap-c++-7 libatomic1 
    </code></pre>

4. **[2], [3]** SAP HANA `data` 및 디렉터리의 소유권 `log` 을 **h n 1**adm으로 변경 합니다.   

    <pre><code>
    # Execute as root
    sudo chown hn1adm:sapsys /hana/data/<b>HN1</b>
    sudo chown hn1adm:sapsys /hana/log/<b>HN1</b>
    </code></pre>

5. **[A]** HANA 설치를 방해 하지 않도록 일시적으로 방화벽을 사용 하지 않도록 설정 합니다. HANA 설치가 완료 된 후에 다시 사용 하도록 설정할 수 있습니다. 
   <pre><code>
    # Execute as root
    systemctl stop firewalld
    systemctl disable firewalld
   </code></pre>

### <a name="hana-installation"></a>HANA 설치

1. **[1]** [SAP HANA 2.0 설치 및 업데이트 가이드](https://help.sap.com/viewer/2c1988d620e04368aa4103bf26f17727/2.0.04/en-US/7eb0167eb35e4e2885415205b8383584.html)의 지침에 따라 SAP HANA를 설치 합니다. 이 예제에서는 master, one worker 및 1 개의 대기 노드를 사용 하 여 확장 SAP HANA를 설치 합니다.  

   a. HANA 설치 소프트웨어 디렉터리에서 **hdblcm** 프로그램을 시작 합니다. `internal_network`매개 변수를 사용 하 고 내부 HANA 노드 간 통신에 사용 되는 서브넷에 대 한 주소 공간을 전달 합니다.  

    <pre><code>
    ./hdblcm --internal_network=10.9.2.0/26
    </code></pre>

   b. 프롬프트에서 다음 값을 입력 합니다.

     * **작업 선택**: **1** (설치의 경우)을 입력 합니다.
     * **설치용 추가 구성 요소**: **2, 3** 입력
     * 설치 경로: Enter 키를 누릅니다 (기본값은/hana/shared).
     * **로컬 호스트 이름**: 기본값을 적용 하려면 enter 키를 누릅니다.
     * **시스템에 호스트를 추가**하 시겠습니까?: **y** 를 입력 합니다.
     * **추가할 쉼표로 구분 된 호스트 이름**: **hanadb2, hanadb3을** 입력 합니다.
     * **루트 사용자 이름** [root]: enter 키를 눌러 기본값을 적용 합니다.
     * 호스트 hanadb2 역할의 경우: **1**  (작업자의 경우)을 입력 합니다.
     * Host hanadb2의 **호스트 장애 조치 (Failover) 그룹** [기본값]: enter 키를 눌러 기본값을 적용 합니다.
     * 호스트 hanadb2의 **저장소 파티션 번호** [<<assign automatically>>]: Enter 키를 눌러 기본값을 적용 합니다.
     * Host hanadb2의 **작업자 그룹** [기본값]: enter 키를 눌러 기본값을 적용 합니다.
     * 호스트 hanadb3에 대 한 **역할 선택** : **2** (대기)를 입력 합니다.
     * Host hanadb3의 **호스트 장애 조치 (Failover) 그룹** [기본값]: enter 키를 눌러 기본값을 적용 합니다.
     * Host hanadb3의 **작업자 그룹** [기본값]: enter 키를 눌러 기본값을 적용 합니다.
     * **SAP HANA 시스템 ID**: **h n 1** 을 입력 합니다.
     * **인스턴스 번호** [00]: **03** 을 입력 합니다.
     * **로컬 호스트 작업자 그룹** [기본값]: enter 키를 눌러 기본값을 적용 합니다.
     * **시스템 사용량 선택/인덱스 [4] 입력**: **4** (사용자 지정)를 입력 합니다.
     * **데이터 볼륨의 위치** [/hana/data/HN1]: enter 키를 눌러 기본값을 적용 합니다.
     * **로그 볼륨의 위치** [/hana/log/HN1]: enter 키를 눌러 기본값을 적용 합니다.
     * **최대 메모리 할당 제한** [n]: **n** 을 입력 합니다.
     * 호스트 hanadb1 [hanadb1] **에 대 한 인증서 호스트 이름** : 기본값을 적용 하려면 enter 키를 누릅니다.
     * 호스트 hanadb2 [hanadb2] **에 대 한 인증서 호스트 이름** : 기본값을 적용 하려면 enter 키를 누릅니다.
     * 호스트 hanadb3 [hanadb3] **에 대 한 인증서 호스트 이름** : 기본값을 적용 하려면 enter 키를 누릅니다.
     * **Hn1adm (시스템 관리자) 암호**: 암호를 입력 합니다.
     * 시스템 **데이터베이스 사용자 (시스템) 암호**: 시스템 암호를 입력 합니다.
     * **시스템 데이터베이스 사용자 (시스템) 암호 확인**: 시스템 암호 입력
     * **컴퓨터를 다시 부팅 한 후 시스템을 다시 시작** 하려면 [n]: **n** 을 입력 합니다. 
     * **계속 하 시겠습니까 (y/n)**: 요약의 유효성을 검사 하 고 모든 항목이 양호 하면 **y** 를 입력 합니다.


2. **[1]** 확인 global.ini  

   global.ini를 표시 하 고 내부 SAP HANA 노드 간 통신에 대 한 구성이 준비 되었는지 확인 합니다. **통신** 섹션을 확인 합니다. 서브넷에 대 한 주소 공간이 있어야 하 `hana` 고 `listeninterface` 로 설정 되어야 합니다 `.internal` . **Internal_hostname_resolution** 섹션을 확인 합니다. 서브넷에 속하는 HANA 가상 컴퓨터의 IP 주소가 있어야 합니다 `hana` .  

   <pre><code>
    sudo cat /usr/sap/<b>HN1</b>/SYS/global/hdb/custom/config/global.ini
    # Example 
    #global.ini last modified 2019-09-10 00:12:45.192808 by hdbnameserve
    [communication]
    internal_network = <b>10.9.2.0/26</b>
    listeninterface = .internal
    [internal_hostname_resolution]
    <b>10.9.2.4</b> = <b>hanadb1</b>
    <b>10.9.2.5</b> = <b>hanadb2</b>
    <b>10.9.2.6</b> = <b>hanadb3</b>
   </code></pre>

3. **[1]** 클라이언트 통신에 클라이언트 IP 주소가 사용 되도록 호스트 매핑을 추가 합니다. 섹션 `public_host_resolution` 을 추가 하 고 클라이언트 서브넷에서 해당 IP 주소를 추가 합니다.  

   <pre><code>
    sudo vi /usr/sap/HN1/SYS/global/hdb/custom/config/global.ini
    #Add the section
    [public_hostname_resolution]
    map_<b>hanadb1</b> = <b>10.9.1.5</b>
    map_<b>hanadb2</b> = <b>10.9.1.6</b>
    map_<b>hanadb3</b> = <b>10.9.1.7</b>
   </code></pre>

4. **[1]** SAP HANA 다시 시작 하 여 변경 내용을 활성화 합니다.  

   <pre><code>
    sudo -u <b>hn1</b>adm /usr/sap/hostctrl/exe/sapcontrol -nr <b>03</b> -function StopSystem HDB
    sudo -u <b>hn1</b>adm /usr/sap/hostctrl/exe/sapcontrol -nr <b>03</b> -function StartSystem HDB
   </code></pre>

5. **[1]** 클라이언트 인터페이스가 통신을 위해 서브넷의 IP 주소를 사용 하 게 되는지 확인 `client` 합니다.  

   <pre><code>
    # Execute as hn1adm
    /usr/sap/HN1/HDB03/exe/hdbsql -u SYSTEM -p "<b>password</b>" -i 03 -d SYSTEMDB 'select * from SYS.M_HOST_INFORMATION'|grep net_publicname
    # Expected result
    "<b>hanadb3</b>","net_publicname","<b>10.9.1.7</b>"
    "<b>hanadb2</b>","net_publicname","<b>10.9.1.6</b>"
    "<b>hanadb1</b>","net_publicname","<b>10.9.1.5</b>"
   </code></pre>

   구성을 확인 하는 방법에 대 한 자세한 내용은 SAP Note [2183363-SAP HANA 내부 네트워크 구성](https://launchpad.support.sap.com/#/notes/2183363)을 참조 하세요.  

5. **[A]** 방화벽을 다시 사용 하도록 설정 합니다.  
   - HANA 중지
       <pre><code>
        sudo -u <b>hn1</b>adm /usr/sap/hostctrl/exe/sapcontrol -nr <b>03</b> -function StopSystem HDB
       </code></pre>
   - 방화벽 다시 사용
       <pre><code>
        # Execute as root
        systemctl start firewalld
        systemctl enable firewalld
       </code></pre>

   - 필요한 방화벽 포트 열기

       > [!IMPORTANT]
       > HANA 노드 통신과 클라이언트 트래픽을 허용 하는 방화벽 규칙을 만듭니다. 필수 포트 목록은 [모든 SAP 제품의 TCP/IP 포트](https://help.sap.com/viewer/ports)에 나열되어 있습니다. 다음 명령은 예제 일 뿐입니다. 이 시나리오에서는 시스템 번호 03이 사용 됩니다.

       <pre><code>
        # Execute as root
        sudo firewall-cmd --zone=public --add-port=30301/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30301/tcp
        sudo firewall-cmd --zone=public --add-port=30303/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30303/tcp
        sudo firewall-cmd --zone=public --add-port=30306/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30306/tcp
        sudo firewall-cmd --zone=public --add-port=30307/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30307/tcp
        sudo firewall-cmd --zone=public --add-port=30313/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30313/tcp
        sudo firewall-cmd --zone=public --add-port=30315/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30315/tcp
        sudo firewall-cmd --zone=public --add-port=30317/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30317/tcp
        sudo firewall-cmd --zone=public --add-port=30340/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30340/tcp
        sudo firewall-cmd --zone=public --add-port=30341/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30341/tcp
        sudo firewall-cmd --zone=public --add-port=30342/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30342/tcp
        sudo firewall-cmd --zone=public --add-port=1128/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=1128/tcp
        sudo firewall-cmd --zone=public --add-port=1129/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=1129/tcp
        sudo firewall-cmd --zone=public --add-port=40302/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=40302/tcp
        sudo firewall-cmd --zone=public --add-port=40301/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=40301/tcp
        sudo firewall-cmd --zone=public --add-port=40307/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=40307/tcp
        sudo firewall-cmd --zone=public --add-port=40303/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=40303/tcp
        sudo firewall-cmd --zone=public --add-port=40340/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=40340/tcp
        sudo firewall-cmd --zone=public --add-port=50313/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=50313/tcp
        sudo firewall-cmd --zone=public --add-port=50314/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=50314/tcp
        sudo firewall-cmd --zone=public --add-port=30310/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30310/tcp
        sudo firewall-cmd --zone=public --add-port=30302/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30302/tcp
       </code></pre>

   - HANA 시작
       <pre><code>
        sudo -u <b>hn1</b>adm /usr/sap/hostctrl/exe/sapcontrol -nr <b>03</b> -function StartSystem HDB
       </code></pre>

6. 기본 Azure NetApp Files 저장소에 대 한 SAP HANA 최적화 하려면 다음 SAP HANA 매개 변수를 설정 합니다.

   - `max_parallel_io_requests`**128**
   - `async_read_submit`**설정**
   - `async_write_submit_active`**설정**
   - `async_write_submit_blocks`**모두**

   자세한 내용은 [Azure NetApp Files를 사용 하 여 Microsoft Azure에서 Netapp SAP 응용 프로그램][anf-sap-applications-azure]을 참조 하세요. 

   SAP HANA 2.0 시스템부터에서 매개 변수를 설정할 수 있습니다 `global.ini` . 자세한 내용은 SAP Note [1999930](https://launchpad.support.sap.com/#/notes/1999930)을 참조 하세요.  
   
   1.0 시스템 버전 SPS12 및 이전 버전의 SAP HANA 경우 SAP Note [2267798](https://launchpad.support.sap.com/#/notes/2267798)에 설명 된 대로 설치 중에 이러한 매개 변수를 설정할 수 있습니다.  

7. Azure NetApp Files에서 사용 하는 저장소의 파일 크기 제한은 16tb입니다. SAP HANA는 저장소 제한을 암시적으로 인식 하지 못하므로 파일 크기 제한인 16TB에 도달 하면 자동으로 새 데이터 파일을 만들지 않습니다. SAP HANA는 16TB를 초과 하 여 파일을 확장 하려고 시도 하면 오류가 발생 하 고 결과적으로 인덱스 서버 작동이 중단 됩니다. 

   > [!IMPORTANT]
   > 저장소 하위 시스템의 [16gb 제한을](../../../azure-netapp-files/azure-netapp-files-resource-limits.md) 초과 하 여 데이터 파일의 증가를 SAP HANA 방지 하려면에서 다음 매개 변수를 설정 `global.ini` 합니다.  
   > - datavolume_striping = true
   > - datavolume_striping_size_gb = 15000 자세한 내용은 SAP Note [2400005](https://launchpad.support.sap.com/#/notes/2400005)를 참조 하세요.
   > SAP Note [2631285](https://launchpad.support.sap.com/#/notes/2631285)에 주의 하세요. 

## <a name="test-sap-hana-failover"></a>테스트 SAP HANA 장애 조치 

1. SAP HANA 작업자 노드에서 노드 충돌을 시뮬레이트합니다. 다음을 수행합니다. 

   a. 노드 충돌을 시뮬레이트하려면 먼저 **h n 1**adm으로 다음 명령을 실행 하 여 환경의 상태를 캡처합니다.  

   <pre><code>
    # Check the landscape status
    python /usr/sap/HN1/HDB<b>03</b>/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | yes    | ignore |          |        |         0 |         0 | default  | default  | master 3   | slave      | standby     | standby     | standby | standby | default | -       |

    # Check the instance status
    sapcontrol -nr <b>03</b>  -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
   </code></pre>

   b. 노드 충돌을 시뮬레이트하려면 작업자 노드의 루트로 다음 명령을 실행 합니다 .이 경우에는 **hanadb2** 입니다.  
   
   <pre><code>
    echo b > /proc/sysrq-trigger
   </code></pre>

   c. 시스템에서 장애 조치 (failover)를 모니터링 합니다. 장애 조치 (failover)가 완료 되 면 상태를 캡처합니다. 예를 들면 다음과 같습니다.  

    <pre><code>
    # Check the instance status
    sapcontrol -nr <b>03</b>  -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GRAY
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
    # Check the landscape status
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | no     | info   |          |        |         2 |         0 | default  | default  | master 2   | slave      | worker      | standby     | worker  | standby | default | -       |
    | hanadb3 | yes    | info   |          |        |         0 |         2 | default  | default  | master 3   | slave      | standby     | slave       | standby | worker  | default | default |
   </code></pre>

   > [!IMPORTANT]
   > 노드가 커널에 영향을 주는 경우 `kernel.panic` *모든* HANA 가상 머신에서를 20 초로 설정 하 여 SAP HANA 장애 조치 (failover)의 지연을 방지 합니다. 구성은에서 수행 됩니다 `/etc/sysctl` . 가상 컴퓨터를 다시 부팅 하 여 변경 내용을 활성화 합니다. 이러한 변경이 수행 되지 않는 경우 노드에 커널에 문제가 발생 하는 경우 장애 조치 (failover)가 10 분 이상 걸릴 수 있습니다.  

2. 다음을 수행 하 여 이름 서버를 중지 합니다.

   a. 테스트 하기 전에 **h n 1**adm으로 다음 명령을 실행 하 여 환경의 상태를 확인 합니다.  

   <pre><code>
    #Landscape status 
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | yes    | ignore |          |        |         0 |         0 | default  | default  | master 3   | slave      | standby     | standby     | standby | standby | default | -       |
    # Check the instance status
    sapcontrol -nr 03  -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
   </code></pre>

   b. 활성 마스터 노드에서 **h n 1**adm으로 다음 명령을 실행 합니다 .이 경우 **hanadb1** 입니다.  

    <pre><code>
        hn1adm@hanadb1:/usr/sap/HN1/HDB03> HDB kill
    </code></pre>
    
    대기 노드 **hanadb3** 는 마스터 노드로 사용 됩니다. 장애 조치 (failover) 테스트가 완료 된 후의 리소스 상태는 다음과 같습니다.  

    <pre><code>
     # Check the instance status
     sapcontrol -nr 03  -function GetSystemInstanceList
     GetSystemInstanceList
     OK
     hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
     hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
     hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
     hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GRAY
     # Check the landscape status
     python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
     | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
     |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
     |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
     | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
     | hanadb1 | no     | info   |          |        |         1 |         0 | default  | default  | master 1   | slave      | worker      | standby     | worker  | standby | default | -       |
     | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
     | hanadb3 | yes    | info   |          |        |         0 |         1 | default  | default  | master 3   | master     | standby     | master      | standby | worker  | default | default |
    </code></pre>

   c. **Hanadb1** 에서 HANA 인스턴스를 다시 시작 합니다 (즉, 이름 서버가 종료 된 동일한 가상 머신에서). **Hanadb1** 노드는 환경에 다시 참가 하 고 대기 역할을 유지 합니다.  

   <pre><code>
    hn1adm@hanadb1:/usr/sap/HN1/HDB03> HDB start
   </code></pre>

   **Hanadb1**에 대 한 SAP HANA 시작 된 후에는 다음 상태가 될 것입니다.  

   <pre><code>
    # Check the instance status
    sapcontrol -nr 03  -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    # Check the landscape status
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | no     | info   |          |        |         1 |         0 | default  | default  | master 1   | slave      | worker      | standby     | worker  | standby | default | -       |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | yes    | info   |          |        |         0 |         1 | default  | default  | master 3   | master     | standby     | master      | standby | worker  | default | default |
   </code></pre>

   d. 다시 한 번, 현재 활성 마스터 노드 (즉, 노드 **hanadb3**)에서 이름 서버를 중지 합니다.  
   
   <pre><code>
    hn1adm@hanadb3:/usr/sap/HN1/HDB03> HDB kill
   </code></pre>

   노드 **hanadb1** 는 마스터 노드의 역할을 다시 시작 합니다. 장애 조치 (failover) 테스트가 완료 되 면 상태는 다음과 같습니다.

   <pre><code>
    # Check the instance status
    sapcontrol -nr 03  -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GRAY
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    # Check the landscape status
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | no     | ignore |          |        |         0 |         0 | default  | default  | master 3   | slave      | standby     | standby     | standby | standby | default | -       |
   </code></pre>

   e. **Hanadb3**에서 SAP HANA를 시작 합니다 .이는 대기 노드로 사용할 준비가 됩니다.  

   <pre><code>
    hn1adm@hanadb3:/usr/sap/HN1/HDB03> HDB start
   </code></pre>

   **Hanadb3**에 대 한 SAP HANA 시작 된 후 상태는 다음과 같습니다.  

   <pre><code>
    # Check the instance status
    sapcontrol -nr 03  -function GetSystemInstanceList & python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    # Check the landscape status
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | no     | ignore |          |        |         0 |         0 | default  | default  | master 3   | slave      | standby     | standby     | standby | standby | default | -       |
   </code></pre>

## <a name="next-steps"></a>다음 단계

* [SAP용 Azure Virtual Machines 계획 및 구현][planning-guide]
* [SAP용 Azure Virtual Machines 배포][deployment-guide]
* [SAP용 Azure Virtual Machines DBMS 배포][dbms-guide]
* Azure Vm에서 SAP HANA의 고가용성을 설정 하 고 재해 복구를 계획 하는 방법에 대 한 자세한 내용은 [azure Virtual Machines (vm)의 SAP HANA 고가용성][sap-hana-ha]을 참조 하세요.
