---
title: RHEL의 ANF를 사용 하 여 SAP HANA 확장의 고가용성 | Microsoft Docs
description: Azure Vm (가상 머신)에서 ANF를 사용 하 여 SAP HANA의 고가용성을 설정 합니다.
services: virtual-machines-linux
documentationcenter: ''
author: rdeltcheva
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/11/2020
ms.author: radeltch
ms.openlocfilehash: 030677276fa077c06a95e7c677fec956b9c2a947
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88556407"
---
# <a name="high-availability-of-sap-hana-scale-up-with-azure-netapp-files-on-red-hat-enterprise-linux"></a>Red Hat Enterprise Linux Azure NetApp Files를 사용 하 여 SAP HANA 확장의 고가용성

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

이 문서에서는 Azure NetApp Files (ANF)를 사용 하 여 HANA 파일 시스템이 NFS를 통해 탑재 될 때 확장 배포에서 SAP HANA 시스템 복제를 구성 하는 방법을 설명 합니다. 예제 구성 및 설치 명령, 인스턴스 번호 **03**및 HANA 시스템 ID **h n 1** 가 사용 됩니다. SAP HANA 복제는 하나의 기본 노드와 하나 이상의 보조 노드로 구성됩니다.

이 문서의 단계가 다음 접두사로 표시 되 면 의미는 다음과 같습니다.

- **[A]**: 단계가 모든 노드에 적용 됩니다.
- **[1]**: 단계는 노드 1에만 적용 됩니다.
- **[2]**: 단계가 노드 2에만 적용 됨
 
다음 SAP Note 및 문서를 먼저 읽어 보세요.

- SAP Note [1928533](https://launchpad.support.sap.com/#/notes/1928533), 다음 항목을 포함합니다.
    - SAP 소프트웨어 배포에 지원되는 Azure VM 크기 목록.
    - Azure VM 크기에 대한 중요한 용량 정보.
    - 지원되는 SAP 소프트웨어 및 OS(운영 체제)와 데이터베이스 조합.
    - Microsoft Azure에서 Windows 및 Linux에 필요한 SAP 커널 버전.
- SAP Note [2015553](https://launchpad.support.sap.com/#/notes/2015553)는 Azure에서 SAP을 지원하는 SAP 소프트웨어 배포에 대한 필수 구성 요소를 나열합니다.
- SAP Note [405827](https://launchpad.support.sap.com/#/notes/405827) 는 HANA 환경용 권장 파일 시스템을 나열 합니다.
- SAP Note [2002167](https://launchpad.support.sap.com/#/notes/2002167) 에는 Red Hat Enterprise Linux에 대 한 권장 OS 설정이 있습니다.
- SAP Note [2009879](https://launchpad.support.sap.com/#/notes/2009879) 에는 Red Hat Enterprise Linux에 대 한 SAP HANA 지침이 있습니다.
- SAP Note [2178632](https://launchpad.support.sap.com/#/notes/2178632)는 Azure에서 SAP에 대해 보고된 모든 모니터링 메트릭에 대한 자세한 정보를 포함하고 있습니다.
- SAP Note [2191498](https://launchpad.support.sap.com/#/notes/2191498)는 Azure에서 Linux에 필요한 SAP Host Agent 버전을 포함하고 있습니다.
- SAP Note [2243692](https://launchpad.support.sap.com/#/notes/2243692)는 Azure에서 Linux의 SAP 라이선스에 대한 정보를 포함하고 있습니다.
- SAP Note [1999351](https://launchpad.support.sap.com/#/notes/1999351)은 SAP용 Azure 고급 모니터링 확장을 위한 추가 문제 해결 정보를 포함하고 있습니다.
- [Sap Community Wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) 에는 Linux에 필요한 모든 sap 노트가 있습니다.
- [Linux에서 SAP용 Azure Virtual Machines 계획 및 구현][planning-guide]
- [Linux에서 SAP용 Azure Virtual Machines 배포][deployment-guide]
- [Linux에서 SAP용 Azure Virtual Machines DBMS 배포][dbms-guide]
- [Pacemaker 클러스터에서 시스템 복제를 SAP HANA 합니다.](https://access.redhat.com/articles/3004101)
- 일반 RHEL 설명서
    - [High Availability Add-On Overview](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)(고가용성 추가 기능 개요)
    - [고가용성 추가 기능 관리.](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
    - [고가용성 추가 기능 참조입니다.](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
    - [HANA 파일 시스템가 NFS 공유에 있는 경우 Pacemaker 클러스터에서 확장 중인 시스템 복제 SAP HANA 구성](https://access.redhat.com/solutions/5156571)
- Azure 특정 RHEL 설명서:
    - [RHEL 고가용성 클러스터에 대 한 지원 정책은 클러스터 구성원으로 Microsoft Azure Virtual Machines 합니다.](https://access.redhat.com/articles/3131341)
    - [Microsoft Azure에서 Red Hat Enterprise Linux 7.4 이상 고가용성 클러스터 설치 및 구성](https://access.redhat.com/articles/3252491)
    - [Microsoft Azure에서 사용할 Red Hat Enterprise Linux SAP HANA를 설치 합니다.](https://access.redhat.com/solutions/3193782)
    - [HANA 파일 시스템이 NFS 공유에 있는 경우 SAP HANA 확장 시스템 복제 Pacemaker 클러스터 구성](https://access.redhat.com/solutions/5156571)
- [Azure NetApp Files를 사용하는 Microsoft Azure의 NetApp SAP 애플리케이션](https://www.netapp.com/us/media/tr-4746.pdf)

## <a name="overview"></a>개요

일반적으로 수직 확장 환경에서는 SAP HANA의 모든 파일 시스템이 로컬 저장소에서 탑재 됩니다. Red Hat Enterprise Linux에서 SAP HANA 시스템 복제의 고가용성을 설정 하는 기능은 [RHEL에서 SAP HANA 시스템 복제 설정](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability-rhel) 가이드에 게시 됩니다.

[Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/) nfs 공유에 대 한 확장 시스템의 고가용성을 SAP HANA 하려면 클러스터에 몇 가지 추가 리소스 구성이 필요 합니다 .이 경우 HANA 리소스를 복구 하려면 한 노드가 ANF의 NFS 공유에 대 한 액세스 권한을 잃게 됩니다.  클러스터는 NFS 탑재를 관리 하 여 리소스의 상태를 모니터링할 수 있도록 합니다. 파일 시스템 탑재와 SAP HANA 리소스 간의 종속성이 적용 됩니다.  

![ANF의 SAP HANA HA 확장](./media/sap-hana-high-availability-rhel/sap-hana-scale-up-netapp-files-red-hat.png)

SAP HANA 파일 시스템는 각 노드에서 Azure NetApp Files를 사용 하 여 NFS 공유에 탑재 됩니다. /Hana/data,/hana/log 및/hana/shared 파일 시스템은 각 노드에 대해 고유 합니다. 

Node1에 탑재 됨 (**hanadb1**)

- 10.32.2.4/**hanadb1**-mnt00001 on/hana/data
- 10.32.2.4:/hana/log의 mnt00001**hanadb1**
- 10.32.2.4:/hana/shared에서 mnt00001**hanadb1**

노드 2에 탑재 됨 (**hanadb2**)

- 10.32.2.4/**hanadb2**-mnt00001 on/hana/data
- 10.32.2.4:/hana/log의 mnt00001**hanadb2**
- 10.32.2.4:/hana/shared에서 mnt00001**hanadb2**

> [!NOTE]
> /Hana/shared,/hana/data 및/hana/log 파일 시스템은 두 노드 간에 공유 되지 않습니다. 각 클러스터 노드에는 별도의 파일 시스템이 있습니다.   

SAP HANA 시스템 복제 구성은 전용 가상 호스트 이름 및 가상 IP 주소를 사용 합니다. Azure에서는 가상 IP 주소를 사용하려면 부하 분산 장치가 필요합니다. 다음 목록에는 부하 분산 장치의 구성이 나와 있습니다.

- 프런트 엔드 구성: h n 1에 대 한 IP 주소 10.32.0.10
- 백 엔드 구성: HANA 시스템 복제의 일부분이어야 하는 모든 가상 머신의 주 네트워크 인터페이스에 연결됨
- 프로브 포트: 포트 62503
- 부하 분산 규칙: 30313 TCP, 30315 TCP, 30317 TCP, 30340 TCP, 30341 TCP, 30342 TCP (기본 Azure 부하 분산 장치를 사용 하는 경우)  

## <a name="set-up-the-azure-netapp-file-infrastructure"></a>Azure NetApp 파일 인프라 설정

Azure NetApp Files 인프라를 설정 하는 과정을 진행 하기 전에 Azure [Netapp 파일 설명서](https://docs.microsoft.com/azure/azure-netapp-files/)를 숙지 하세요.

Azure NetApp Files는 여러 [Azure 지역](https://azure.microsoft.com/global-infrastructure/services/?products=netapp)에서 사용할 수 있습니다. 선택한 Azure 지역에서 Azure NetApp Files를 제공 하는지 확인 합니다.

Azure 지역에서 Azure NetApp Files 가용성에 대 한 자세한 내용은 [Azure 지역별 가용성 Azure NetApp Files](https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all)을 참조 하세요.

Azure NetApp Files를 배포 하기 전에 [Azure NetApp Files 지침에 등록](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register)하 여 Azure NetApp Files에 대 한 온 보 딩을 요청 합니다.

### <a name="deploy-azure-netapp-files-resources"></a>Azure NetApp Files 리소스 배포

다음 지침에서는 이미 [Azure virtual network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)를 배포 했다고 가정 합니다. Azure NetApp Files 리소스가 탑재 되는 Azure NetApp Files 리소스 및 Vm은 동일한 Azure virtual network 또는 피어 링 Azure virtual networks에 배포 되어야 합니다.

1. 리소스를 아직 배포 하지 않은 경우 [Azure NetApp Files에 대 한 온 보 딩](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register)을 요청 합니다.

2. [Netapp 계정 만들기](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-netapp-account)의 지침에 따라 선택한 Azure 지역에서 netapp 계정을 만듭니다.

3.  [Azure NetApp Files 용량 풀 설정](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-set-up-capacity-pool)의 지침에 따라 Azure NetApp Files 용량 풀을 설정 합니다.

    이 문서에 제공 된 HANA 아키텍처는 *Ultra* Service 수준에서 단일 Azure NetApp Files 용량 풀을 사용 합니다. Azure의 HANA 워크 로드의 경우 Azure NetApp Files *Ultra* 또는 *Premium* [서비스 수준을](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels)사용 하는 것이 좋습니다.

4.  [Azure NetApp Files에 서브넷 위임](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet)의 지침에 설명 된 대로 Azure NetApp Files에 서브넷을 위임 합니다.

5.  [Azure NetApp Files에 대 한 NFS 볼륨 만들기](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes)의 지침에 따라 Azure NetApp Files 볼륨을 배포 합니다.

    볼륨을 배포할 때 NFSv 4.1 버전을 선택 해야 합니다. 지정된 Azure NetApp Files 서브넷에 볼륨을 배포합니다. Azure NetApp 볼륨의 IP 주소는 자동으로 할당됩니다.

    Azure NetApp Files 리소스와 Azure Vm은 동일한 Azure virtual network 또는 피어 링 Azure virtual network에 있어야 합니다. 예를 들어 hanadb1-mnt00001, hanadb1-mnt00001 등은 볼륨 이름 및 nfs://10.32.2.4/hanadb1-data-mnt00001, nfs://10.32.2.4/hanadb1-log-mnt00001 등은 Azure NetApp Files 볼륨의 파일 경로입니다.
    
    **Hanadb1** 에서

    - Volume hanadb1-mnt00001 (nfs://10.32.2.4:/hanadb1-data-mnt00001) 
    - Volume hanadb1-mnt00001 (nfs://10.32.2.4:/hanadb1-log-mnt00001)
    - Volume hanadb1-mnt00001 (nfs://10.32.2.4:/hanadb1-shared-mnt00001)
    
    **Hanadb2** 에서

    - Volume hanadb2-mnt00001 (nfs://10.32.2.4:/hanadb2-data-mnt00001)
    - Volume hanadb2-mnt00001 (nfs://10.32.2.4:/hanadb2-log-mnt00001)
    - Volume hanadb2-mnt00001 (nfs://10.32.2.4:/hanadb2-shared-mnt00001)

### <a name="important-considerations"></a>중요 고려 사항

SAP HANA 확장 시스템에 대 한 Azure NetApp Files를 만들 때 다음 사항을 고려해 야 합니다.

- 최소 용량 풀은 4 tebibytes (TiB)입니다.
- 최소 볼륨 크기는 100 GiB (바이트)입니다.
- Azure NetApp Files 및 Azure NetApp Files 볼륨이 탑재 될 모든 가상 머신은 동일한 지역의 동일한 Azure 가상 네트워크 또는 [피어 링 가상 네트워크](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) 에 있어야 합니다.
- 선택한 가상 네트워크에 Azure NetApp Files로 위임 된 서브넷이 있어야 합니다.
- Azure NetApp Files 볼륨의 처리량은 [Azure NetApp Files 서비스 수준](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels)에 설명 된 볼륨 할당량 및 서비스 수준의 기능입니다. HANA Azure NetApp 볼륨의 크기를 조정 하는 경우 결과 처리량이 HANA 시스템 요구 사항을 충족 하는지 확인 합니다.
- Azure NetApp Files [내보내기 정책을](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-configure-export-policy)사용 하면 허용 되는 클라이언트, 액세스 유형 (읽기-쓰기, 읽기 전용 등)을 제어할 수 있습니다.
- Azure NetApp Files 기능이 아직 영역을 인식 하지 않습니다. 현재이 기능은 Azure 지역의 모든 가용성 영역에 배포 되지 않습니다. 일부 Azure 지역에서 잠재적 대기 시간 영향을 염두에 두어야 합니다.

> [!IMPORTANT]
> SAP HANA 워크로드의 경우 짧은 대기 시간이 매우 중요합니다. Microsoft 담당자와 협력 하 여 가상 머신 및 Azure NetApp Files 볼륨이 근접 하 게 배포 되었는지 확인 합니다.

### <a name="sizing-of-hana-database-on-azure-netapp-files"></a>Azure NetApp Files HANA 데이터베이스 크기 조정

Azure NetApp Files 볼륨의 처리량은 [Azure NetApp Files 서비스 수준](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels)에 설명 된 대로 볼륨 크기 및 서비스 수준의 기능입니다.

Azure에서 SAP 용 인프라를 설계할 때 최소 처리량 특성으로 변환 되는 SAP의 최소 저장소 요구 사항에 주의 해야 합니다.

- 1 메가바이트 i/o 크기를 사용 하는 초당 250 메가바이트 (MB/s)의/hana/log 읽기/쓰기입니다.
- 16mb 및 64 MB i/o 크기에 대 한/hana/data에 대 한 최소 400 m b/초 읽기 작업입니다.
- 16mb 및 64 MB i/o 크기를 사용 하는/hana/data에 대 한 최소 250 m b/s의 쓰기 작업입니다.

볼륨 할당량 1TiB당 [Azure NetApp Files 처리량 한도](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels)는 다음과 같습니다.

- Premium Storage 계층-64 MiB/s.
- Ultra Storage 계층-128 MiB/s.

/Hana/data 및/hana/log에 대 한 SAP 최소 처리량 요구 사항과/hana/shared에 대 한 지침을 충족 하기 위해 권장 되는 크기는 다음과 같습니다.

|    볼륨    | Premium Storage 계층의 크기 | Ultra Storage 계층의 크기 | 지원 되는 NFS 프로토콜 |
| :----------: | :--------------------------: | :------------------------: | :--------------------: |
|  /hana/log   |            4TiB             |           2TiB            |          v4.1          |
|  /hana/data  |           6.3TiB            |          3.2TiB           |          v4.1          |
| /hana/shared |           1 x RAM            |          1 x RAM           |          v3 또는 v4.1    |


> [!NOTE]
> 여기에 명시 된 Azure NetApp Files 크기 조정 권장 사항은 SAP에서 인프라 공급자에 대해 권장 하는 최소 요구 사항을 충족 하는 것입니다. 실제 고객 배포 및 워크 로드 시나리오에서는 이러한 크기가 충분 하지 않을 수 있습니다. 이러한 권장 사항을 시작점으로 삼아 워크로드의 요구 사항에 따라 조정합니다.

> [!TIP]
> 볼륨을 *분리* 하거나 가상 컴퓨터를 중지 하거나 SAP HANA를 중지 하지 않고도 Azure NetApp Files 볼륨의 크기를 동적으로 조정할 수 있습니다. 이 방법을 사용 하면 응용 프로그램의 예상 처리량과 예기치 않은 처리량 요구를 유연 하 게 충족할 수 있습니다.

> [!NOTE]
> 이 문서에서/hana/shared를 탑재 하는 모든 명령은 NFSv 4.1/hana/shared 볼륨에 대해 제공 됩니다.
> /Hana/shared 볼륨을 NFSv3 볼륨으로 배포한 경우 NFSv3에 대 한/hana/shared의 탑재 명령을 조정 해야 합니다.


## <a name="deploy-linux-virtual-machine-via-azure-portal"></a>Azure Portal를 통해 Linux 가상 컴퓨터 배포 

먼저 Azure NetApp Files 볼륨을 만들어야 합니다. 그런 후에 다음 단계를 수행 합니다.

1.  리소스 그룹을 만듭니다.
2.  가상 네트워크를 만듭니다.
3.  가용성 집합을 만듭니다. 업데이트 도메인의 최대 수를 설정합니다.
4.  부하 분산 장치(내부)를 만듭니다. 표준 부하 분산 장치를 만드는 것이 좋습니다.
    2단계에서 만든 가상 네트워크를 선택합니다.
5.  가상 컴퓨터 1 (**hanadb1**)을 만듭니다. 
6.  가상 컴퓨터 2 (**hanadb2**)를 만듭니다.  
7.  가상 컴퓨터를 만드는 동안 모든 탑재 지점이 Azure NetApp Files의 NFS 공유에 있는 모든 디스크를 추가 하지 않습니다. 
8.  표준 부하 분산 장치를 사용하는 경우 다음 구성 단계를 수행합니다.
    1.  먼저 프런트 엔드 IP 풀을 만듭니다.
        1.  부하 분산 장치를 열고, **프런트 엔드 IP 풀**을 선택하고, **추가**를 선택합니다.
        1.  새 프런트 엔드 IP 풀의 이름을 입력합니다(예: **hana-frontend**).
        1.  **할당** 을 **정적** 으로 설정 하 고 IP 주소를 입력 합니다 (예: **10.32.0.10**).
        1.  **확인**을 선택합니다.
        1.  새 프런트 엔드 IP 풀을 만든 후, 풀 IP 주소를 적어 둡니다.
    1.  다음으로, 백 엔드 풀을 만듭니다.
        1.  부하 분산 장치를 열고, **백 엔드 풀**을 선택한 다음, **추가**를 클릭합니다.
        1.  새 백 엔드 풀의 이름 입력합니다(예: **hana-backend**).
        1.  **가상 머신 추가**를 선택합니다.
        1.  **가상 머신**을 선택합니다.
        1.  SAP HANA 클러스터의 가상 머신 및 해당 IP 주소를 선택합니다.
        1.  **추가**를 선택합니다.
    1.  다음으로, 상태 프로브를 만듭니다.
        1.  부하 분산 장치를 열고, **상태 프로브**를 선택한 다음, **추가**를 선택합니다.
        1.  새 상태 프로브의 이름을 입력합니다(예: **hana-hp**).
        1.  프로토콜 및 포트 62503으로 **TCP**를 선택합니다. 5로 설정된 **간격** 값, 2로 설정된 **비정상 임계값** 값을 유지합니다.
        1.  **확인**을 선택합니다.
    1.  다음으로 부하 분산 규칙을 만듭니다.
        1.  부하 분산 장치를 열고, **부하 분산 규칙**을 선택한 다음, **추가**를 선택합니다.
        1.  새 부하 분산 장치 규칙의 이름을 입력합니다(예: **hana-lb**).
        1.  이전에 만든 프런트 엔드 IP 주소, 백 엔드 풀 및 상태 프로브를 선택합니다(예: **hana-frontend**, **hana-backend** 및 **hana-hp**).
        1.  **HA 포트**를 선택합니다.
        1.  **유휴 상태 시간 제한**을 30분으로 증가시킵니다.
        1.  **부동 IP를 사용하도록 설정**했는지 확인합니다.
        1.  **확인**을 선택합니다.

> [!NOTE] 
> 공용 IP 주소가 없는 VM이 내부(공용 IP 주소 없음) 표준 Azure 부하 분산 장치의 백 엔드 풀에 배치되는 경우 공용 엔드포인트로 라우팅을 허용하기 위해 추가 구성을 수행하지 않는 한 아웃바운드 인터넷 연결이 없습니다. 아웃바운드 연결을 설정하는 방법에 대한 자세한 내용은 [SAP 고가용성 시나리오에서 Azure 표준 Load Balancer를 사용하여 Virtual Machines에 대한 공용 엔드포인트 연결](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-standard-load-balancer-outbound-connections)을 참조하세요.

9. 또는 시나리오에서 기본 부하 분산 장치를 사용하는 경우 다음 구성 단계를 수행합니다.
    1.  부하 분산 장치를 구성합니다. 먼저 프런트 엔드 IP 풀을 만듭니다.
        1.  부하 분산 장치를 열고, **프런트 엔드 IP 풀**을 선택하고, **추가**를 선택합니다.
        1.  새 프런트 엔드 IP 풀의 이름을 입력합니다(예: **hana-frontend**).
        1.  **할당** 을 **정적** 으로 설정 하 고 IP 주소를 입력 합니다 (예: **10.32.0.10**).
        1.  **확인**을 선택합니다.
        1.  새 프런트 엔드 IP 풀을 만든 후, 풀 IP 주소를 적어 둡니다.
    1.  다음으로, 백 엔드 풀을 만듭니다.
        1.  부하 분산 장치를 열고, **백 엔드 풀**을 선택한 다음, **추가**를 클릭합니다.
        1.  새 백 엔드 풀의 이름 입력합니다(예: **hana-backend**).
        1.  **가상 머신 추가**를 선택합니다.
        1.  3단계에서 만든 가용성 집합을 선택합니다.
        1.  SAP HANA 클러스터의 가상 머신을 선택합니다.
        1.  **확인**을 선택합니다.
    1.  다음으로, 상태 프로브를 만듭니다.
        1.  부하 분산 장치를 열고, **상태 프로브**를 선택한 다음, **추가**를 선택합니다.
        1.  새 상태 프로브의 이름을 입력합니다(예: **hana-hp**).
        1.  프로토콜 및 포트 625**03**으로 **TCP**를 선택합니다. 5로 설정된 **간격** 값, 2로 설정된 **비정상 임계값** 값을 유지합니다.
        1.  **확인**을 선택합니다.
    1.  SAP HANA 1.0의 경우 부하 분산 규칙을 만듭니다.
        1.  부하 분산 장치를 열고, **부하 분산 규칙**을 선택한 다음, **추가**를 선택합니다.
        1.  새 부하 분산 장치 규칙의 이름을 입력합니다(예: hana-lb-3**03**15).
        1.  이전에 만든 프런트 엔드 IP 주소, 백 엔드 풀 및 상태 프로브를 선택합니다(예: **hana-frontend**).
        1.  **TCP**로 설정된 **프로토콜**을 유지하고, 포트 3**03**15를 입력합니다.
        1.  **유휴 상태 시간 제한**을 30분으로 증가시킵니다.
        1.  **부동 IP를 사용하도록 설정**했는지 확인합니다.
        1.  **확인**을 선택합니다.
        1.  포트 3**03**17에 대해 이러한 단계를 반복합니다.
    1.  SAP HANA 2.0의 경우 시스템 데이터베이스에 대한 부하 분산 규칙을 만듭니다.
        1.  부하 분산 장치를 열고, **부하 분산 규칙**을 선택한 다음, **추가**를 선택합니다.
        1.  새 부하 분산 장치 규칙의 이름을 입력합니다(예: hana-lb-3**03**13).
        1.  이전에 만든 프런트 엔드 IP 주소, 백 엔드 풀 및 상태 프로브를 선택합니다(예: **hana-frontend**).
        1.  **TCP**로 설정된 **프로토콜**을 유지하고, 포트 3**03**13을 입력합니다.
        1.  **유휴 상태 시간 제한**을 30분으로 증가시킵니다.
        1.  **부동 IP를 사용하도록 설정**했는지 확인합니다.
        1.  **확인**을 선택합니다.
        1.  포트 3**03**14에 대해 이러한 단계를 반복합니다.
    1.  SAP HANA 2.0의 경우 테넌트 데이터베이스에 대한 부하 분산 규칙을 만듭니다.
        1.  부하 분산 장치를 열고, **부하 분산 규칙**을 선택한 다음, **추가**를 선택합니다.
        1.  새 부하 분산 장치 규칙의 이름을 입력합니다(예: hana-lb-3**03**40).
        1.  이전에 만든 프런트 엔드 IP 주소, 백 엔드 풀 및 상태 프로브를 선택합니다(예: **hana-frontend**).
        1.  **TCP**로 설정된 **프로토콜**을 유지하고, 포트 3**03**40을 입력합니다.
        1.  **유휴 상태 시간 제한**을 30분으로 증가시킵니다.
        1.  **부동 IP를 사용하도록 설정**했는지 확인합니다.
        1.  **확인**을 선택합니다.
        1.  포트 3**03**41 및 3**03**42에 대해 이러한 단계를 반복합니다.

SAP HANA에 필요한 포트에 대 한 자세한 내용은 [SAP HANA 테 넌 트 데이터베이스](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6) 가이드 또는 SAP Note [2388694](https://launchpad.support.sap.com/#/notes/2388694)에 있는 [테 넌 트 데이터베이스에](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6/latest/en-US/7a9343c9f2a2436faa3cfdb5ca00c052.html) 대 한 연결 챕터를 참조 하세요.

> [!IMPORTANT]
> Azure Load Balancer 뒤에 배치되는 Azure VM에서 TCP 타임스탬프를 사용하도록 설정하면 안 됩니다. TCP 타임스탬프를 사용하도록 설정하면 상태 프로브에 오류가 발생합니다. 매개 변수 **net.ipv4.tcp_timestamps**를 **0**으로 설정합니다. 자세한 내용은 [Load Balancer 상태 프로브](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview)를 참조하세요. 또한 SAP 참고 [2382421](https://launchpad.support.sap.com/#/notes/2382421)을 참조하세요.

## <a name="mount-the-azure-netapp-files-volume"></a>Azure NetApp Files 볼륨 탑재

1. **[A]** HANA 데이터베이스 볼륨의 탑재 위치를 만듭니다. 

    ```
    mkdir -p /hana/data
    mkdir -p /hana/log
    mkdir -p /hana/shared
    ```

2. **[A]** NFS 도메인 설정을 확인 합니다. 도메인이 기본 Azure NetApp Files 도메인 (예: **defaultv4iddomain.com** )으로 구성 되어 있고 매핑이 없음으로 설정 되어 있는지 **확인 합니다.**

    ```
    sudo cat /etc/idmapd.conf
    # Example
    [General]
    Domain = defaultv4iddomain.com
    [Mapping]
    Nobody-User = nobody
    Nobody-Group = nobody
    ```

    > [!IMPORTANT]    
    > Azure NetApp Files: **defaultv4iddomain.com**의 기본 도메인 구성과 일치 하도록 VM에서/ETC/IDMAPD.CONF의 NFS 도메인을 설정 해야 합니다. NFS 클라이언트의 도메인 구성 (예: VM)과 NFS 서버 (예: Azure NetApp 구성)가 일치 하지 않는 경우 Vm에 탑재 된 Azure NetApp 볼륨의 파일에 대 한 권한이 없음으로 표시 됩니다.
    

3. **[1]** node1에 노드 특정 볼륨 탑재 (**hanadb1**) 

    ```
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.32.2.4:/hanadb1-shared-mnt00001 /hana/shared
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.32.2.4:/hanadb1-log-mnt00001 /hana/log
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.32.2.4:/hanadb1-data-mnt00001 /hana/data
    ```
    
4.  **[2]** 노드-노드-특정 볼륨을 노드 2 (**hanadb2**)에 탑재
    
    ```
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.32.2.4:/hanadb2-shared-mnt00001 /hana/shared
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.32.2.4:/hanadb2-log-mnt00001 /hana/log
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.32.2.4:/hanadb2-data-mnt00001 /hana/data
    ```

5. **[A]** 모든 HANA 볼륨이 NFS 프로토콜 버전 NFSv4로 탑재 되어 있는지 확인 합니다.

    ```
    sudo nfsstat -m
    
    # Verify that flag vers is set to 4.1 
    # Example from hanadb1
    
    /hana/log from 10.32.2.4:/hanadb1-log-mnt00001
    Flags: rw,noatime,vers=4.1,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.32.0.4,local_lock=none,addr=10.32.2.4
    /hana/data from 10.32.2.4:/hanadb1-data-mnt00001
    Flags: rw,noatime,vers=4.1,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.32.0.4,local_lock=none,addr=10.32.2.4
    /hana/shared from 10.32.2.4:/hanadb1-shared-mnt00001
    Flags: rw,noatime,vers=4.1,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.32.0.4,local_lock=none,addr=10.32.2.4
    ```

6. **[A]** **nfs4_disable_idmapping**확인 합니다. **Y**로 설정 되어야 합니다. **Nfs4_disable_idmapping** 있는 디렉터리 구조를 만들려면 mount 명령을 실행 합니다. 커널/드라이버용으로 액세스가 예약되어 있기 때문에 /sys/modules 아래에 디렉터리를 수동으로 만들 수 없습니다.

    ```
    # Check nfs4_disable_idmapping 
    cat /sys/module/nfs/parameters/nfs4_disable_idmapping
    
    # If you need to set nfs4_disable_idmapping to Y
    echo "Y" > /sys/module/nfs/parameters/nfs4_disable_idmapping
    
    # Make the configuration permanent
    echo "options nfs nfs4_disable_idmapping=Y" >> /etc/modprobe.d/nfs.conf
    ```

   **Nfs_disable_idmapping** 매개 변수를 변경 하는 방법에 대 한 자세한 내용은을 참조 하십시오 [https://access.redhat.com/solutions/1749883](https://access.redhat.com/solutions/1749883) . 


## <a name="sap-hana-installation"></a>SAP HANA 설치

1. **[A]** 모든 호스트의 호스트 이름 확인을 설정합니다.

   DNS 서버를 사용하거나 모든 노드의 /etc/hosts 파일을 수정할 수 있습니다. 이 예에서는 /etc/hosts 파일을 사용하는 방법을 보여 줍니다. 다음 명령에서 IP 주소와 호스트 이름을 바꿉니다.

   ```
   sudo vi /etc/hosts
   # Insert the following lines in the /etc/hosts file. Change the IP address and hostname to match your environment  
   10.32.0.4   hanadb1
   10.32.0.5   hanadb2
   ```

2. **[A]** HANA 구성에 대 한 RHEL

   RHEL 버전에 따라 아래 SAP Note에 설명 된 대로 RHEL를 구성 합니다.

   - [2292690 - SAP HANA DB: RHEL 7에 대한 권장 OS 설정](https://launchpad.support.sap.com/#/notes/2292690)
   - [2777782-SAP HANA DB: RHEL 8에 권장 되는 OS 설정](https://launchpad.support.sap.com/#/notes/2777782)
   - [2455582-Linux: GCC 6.x로 컴파일된 SAP 응용 프로그램 실행](https://launchpad.support.sap.com/#/notes/2455582)
   - [2593824-Linux: GCC 4.x로 컴파일된 SAP 응용 프로그램 실행](https://launchpad.support.sap.com/#/notes/2593824) 
   - [2886607-Linux: GCC 4.x로 컴파일된 SAP 응용 프로그램 실행](https://launchpad.support.sap.com/#/notes/2886607)

3. **[A]** SAP HANA 설치

   HANA 2.0 SPS 01로 시작 하는 MDC는 기본 옵션입니다. HANA 시스템을 설치 하는 경우 SYSTEMDB와 동일한 SID를 가진 테 넌 트가 함께 생성 됩니다. 경우에 따라 기본 테 넌 트가 필요 하지 않습니다. 경우에 따라 설치와 함께 초기 테 넌 트를 만들지 않으려는 경우 SAP Note [2629711](https://launchpad.support.sap.com/#/notes/2629711) 을 수행할 수 있습니다.

    HANA DVD에서 **hdblcm** 프로그램을 실행합니다. 프롬프트에서 다음 값을 입력합니다.  
    설치 선택: **1** (설치의 경우)을 입력 합니다.  
    설치할 추가 구성 요소 선택: **1**을 입력합니다.  
    설치 경로 입력 [/hana/shared]: 기본값을 적용 하려면 Enter 키를 누릅니다.  
    로컬 호스트 이름 입력 [..]: Enter 키를 눌러 기본값을 적용 합니다.  
    시스템에 호스트를 추가할까요? (y/n) [n]: **n**  
    SAP HANA 시스템 ID를 입력 합니다. **h n 1**을 입력 합니다.  
    인스턴스 번호 [00] 입력: **03**  
    데이터베이스 모드 선택/인덱스 [1] 입력: 기본값을 적용 하려면 Enter 키를 누릅니다.  
    시스템 사용량 선택/인덱스 [4] 입력: **4** (사용자 지정) 입력  
    데이터 볼륨의 위치 입력 [/hana/data]: 기본값을 적용 하려면 Enter 키를 누릅니다.  
    로그 볼륨의 위치 입력 [/hana/log]: 기본값을 적용 하려면 Enter 키를 누릅니다.  
    최대 메모리 할당 제한? [n]: 기본값을 적용 하려면 Enter 키를 누릅니다.  
    ' ... ' 호스트에 대 한 인증서 호스트 이름을 입력 하십시오. [...]: Enter 키를 눌러 기본값을 적용 합니다.  
    SAP 호스트 에이전트 사용자 (sapadm) 암호 입력: 호스트 에이전트 사용자 암호를 입력 합니다.  
    SAP 호스트 에이전트 사용자 (sapadm) 암호 확인: 호스트 에이전트 사용자 암호를 다시 입력 하 여 확인 합니다.  
    시스템 관리자 (hn1adm) 암호 입력: 시스템 관리자 암호를 입력 합니다.  
    시스템 관리자 (hn1adm) 암호 확인: 시스템 관리자 암호를 다시 입력 하 여 확인 합니다.  
    시스템 관리자 홈 디렉터리 [/usr/sap/HN1/home] 입력: 기본값을 적용 하려면 Enter 키를 누릅니다.  
    시스템 관리자 로그인 셸 입력 [/bin/sh]: 기본값을 적용 하려면 Enter 키를 누릅니다.  
    시스템 관리자 사용자 ID [1001] 입력: 기본값을 적용 하려면 Enter 키를 누릅니다.  
    사용자 그룹의 ID 입력 (sapsys) [79]: 기본값을 적용 하려면 Enter 키를 누릅니다.  
    데이터베이스 사용자 (시스템) 암호 입력: 데이터베이스 사용자 암호를 입력 합니다.  
    데이터베이스 사용자 (시스템) 암호 확인: 데이터베이스 사용자 암호를 다시 입력 하 여 확인 합니다.  
    컴퓨터를 다시 부팅한 다음 시스템 다시 시작? [n]: 기본값을 적용 하려면 Enter 키를 누릅니다.  
    계속할까요? (y/n): 요약의 유효성을 검사합니다. 계속 하려면 **y** 를 입력 하십시오.  

4. **[A]** SAP 호스트 에이전트 업그레이드

   [SAP 소프트웨어 센터](https://launchpad.support.sap.com/#/softwarecenter)에서 최신 SAP 호스트 에이전트 아카이브를 다운로드하고 다음 명령을 실행하여 에이전트를 업그레이드합니다. 다운로드한 파일을 가리키도록 아카이브의 경로를 바꿉니다.

   ```
   sudo /usr/sap/hostctrl/exe/saphostexec -upgrade -archive <path to SAP Host Agent SAR>
   ```

5. **[A]** 방화벽 구성

   Azure 부하 분산 장치 프로브 포트의 방화벽 규칙을 만듭니다.

   ```
   sudo firewall-cmd --zone=public --add-port=62503/tcp
   sudo firewall-cmd --zone=public --add-port=62503/tcp –permanent
   ```

## <a name="configure-sap-hana-system-replication"></a>SAP HANA 시스템 복제 구성

[SAP HANA 시스템 복제](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability-rhel#configure-sap-hana-20-system-replication) 설정의 단계에 따라 SAP HANA 시스템 복제를 구성 합니다. 

## <a name="cluster-configuration"></a>클러스터 구성

이 섹션에서는 Azure NetApp Files를 사용 하 여 NFS 공유에 SAP HANA를 설치할 때 클러스터가 원활 하 게 작동 하는 데 필요한 단계에 대해 설명 합니다. 

### <a name="create-a-pacemaker-cluster"></a>Pacemaker 클러스터 만들기

Azure에서 [Red Hat Enterprise Linux 설정 Pacemaker](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker) 의 단계에 따라이 HANA 서버에 대 한 기본 Pacemaker 클러스터를 만듭니다.

### <a name="configure-filesystem-resources"></a>파일 시스템 리소스 구성

이 예제에서 각 클러스터 노드에는 고유한 HANA NFS 파일 시스템/hana/shared,/hana/data 및/hana/log가 있습니다.   

1. **[1]** 클러스터를 유지 관리 모드로 전환 합니다.

   ```
   pcs property set maintenance-mode=true
   ```

2. **[1]** **hanadb1** 탑재에 대 한 파일 시스템 리소스를 만듭니다.

    ```
    pcs resource create hana_data1 ocf:heartbeat:Filesystem device=10.32.2.4:/hanadb1-data-mnt00001 directory=/hana/data fstype=nfs options=rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 --group hanadb1_nfs
    pcs resource create hana_log1 ocf:heartbeat:Filesystem device=10.32.2.4:/hanadb1-log-mnt00001 directory=/hana/log fstype=nfs options=rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 --group hanadb1_nfs
    pcs resource create hana_shared1 ocf:heartbeat:Filesystem device=10.32.2.4:/hanadb1-shared-mnt00001 directory=/hana/shared fstype=nfs options=rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 --group hanadb1_nfs
    ```

3. **[2]** **hanadb2** 탑재에 대 한 파일 시스템 리소스를 만듭니다.

    ```
    pcs resource create hana_data2 ocf:heartbeat:Filesystem device=10.32.2.4:/hanadb2-data-mnt00001 directory=/hana/data fstype=nfs options=rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 --group hanadb2_nfs
    pcs resource create hana_log2 ocf:heartbeat:Filesystem device=10.32.2.4:/hanadb2-log-mnt00001 directory=/hana/log fstype=nfs options=rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 --group hanadb2_nfs
    pcs resource create hana_shared2 ocf:heartbeat:Filesystem device=10.32.2.4:/hanadb2-shared-mnt00001 directory=/hana/shared fstype=nfs options=rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 --group hanadb2_nfs
    ```

    `OCF_CHECK_LEVEL=20` 특성은 모니터 작업에 추가 되므로 각 모니터는 파일 시스템에서 읽기/쓰기 테스트를 수행 합니다. 이 특성이 없으면 모니터 작업은 파일 시스템이 탑재 되어 있는지만 확인 합니다. 이는 연결이 끊어질 때 파일 시스템에 액세스할 수 없어 탑재 된 상태를 유지할 수 있기 때문에 문제가 될 수 있습니다.

    `on-fail=fence` 특성은 모니터 작업에도 추가 됩니다. 이 옵션을 사용 하면 노드에서 모니터 작업이 실패 하는 경우 해당 노드가 즉시 친 됩니다. 이 옵션을 사용 하지 않으면 실패 한 리소스에 종속 된 모든 리소스를 중지 하 고 실패 한 리소스를 다시 시작한 다음 실패 한 리소스에 종속 된 모든 리소스를 시작 하는 것이 기본 동작입니다. SAPHana 리소스가 실패 한 리소스에 종속 되는 경우에는이 동작에 오랜 시간이 걸릴 수 있지만 완전히 실패할 수도 있습니다. HANA 실행 파일을 보유 하는 NFS 서버에 액세스할 수 없는 경우 SAPHana 리소스를 성공적으로 중지할 수 없습니다.

4. **[1]** 위치 제약 조건 구성

   Hanadb1 고유 탑재를 관리 하는 리소스를 hanadb2에서 실행할 수 없도록 위치 제약 조건을 구성 하 고 그 반대의 경우도 마찬가지입니다.

    ```
    pcs constraint location hanadb1_nfs rule score=-INFINITY resource-discovery=never \#uname eq hanadb2
    pcs constraint location hanadb2_nfs rule score=-INFINITY resource-discovery=never \#uname eq hanadb1
    ```

    `resource-discovery=never`각 노드의 고유 탑재는 동일한 탑재 지점을 공유 하기 때문에 옵션이 설정 됩니다. 예를 들어는 `hana_data1` 탑재 지점을 사용 하 `/hana/data` 고는 `hana_data2` 탑재 지점을 사용 `/hana/data` 합니다. 이로 인해 프로브 작업에 거짓 긍정이 발생할 수 있으며, 클러스터 시작 시 리소스 상태가 확인 되 면 불필요 한 복구 동작이 발생할 수 있습니다. 이는 다음을 설정 하 여 방지할 수 있습니다. `resource-discovery=never`

5. **[1]** 특성 리소스 구성

   특성 리소스를 구성 합니다. 이러한 특성은 노드의 모든 NFS 탑재 (/hana/data,/hana/log 및/hana/data)가 탑재 된 경우 true로 설정 되 고 그렇지 않은 경우 false로 설정 됩니다.

   ```
   pcs resource create hana_nfs1_active ocf:pacemaker:attribute active_value=true inactive_value=false name=hana_nfs1_active
   pcs resource create hana_nfs2_active ocf:pacemaker:attribute active_value=true inactive_value=false name=hana_nfs2_active
   ```

6. **[1]** 위치 제약 조건 구성

   Hanadb1's 특성 리소스가 hanadb2에서 실행 되지 않도록 하는 위치 제약 조건을 구성 하 고 그 반대의 경우도 마찬가지입니다.

   ```
   pcs constraint location hana_nfs1_active avoids hanadb2
   pcs constraint location hana_nfs2_active avoids hanadb1
   ```

7. **[1]** 정렬 제약 조건 만들기

   노드의 모든 NFS 탑재를 탑재 한 후에 노드의 특성 리소스가 시작 되도록 정렬 제약 조건을 구성 합니다.
   
    ```
    pcs constraint order hanadb1_nfs then hana_nfs1_active
    pcs constraint order hanadb2_nfs then hana_nfs2_active
    ```

   > [!TIP]
   > 구성에 그룹 또는 외부에 있는 파일 시스템이 포함 된 경우 `hanadb1_nfs` `hanadb2_nfs` `sequential=false` 파일 시스템 간에 정렬 종속성이 없도록 옵션을 포함 합니다. 모든 파일 시스템은 이전에 시작 해야 `hana_nfs1_active` 하지만 서로 관련 하 여 어떤 순서로도 시작할 필요가 없습니다. 자세한 내용은 [Pacemaker 클러스터에서 HANA 파일 시스템가 NFS 공유에 있는 경우 SAP HANA 시스템 복제를 확장 하는 어떻게 할까요? 구성을](https://access.redhat.com/solutions/5156571) 참조 하세요.

### <a name="configure-sap-hana-cluster-resources"></a>클러스터 리소스 SAP HANA 구성

1. 클러스터 [리소스 만들기 SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability-rhel#create-sap-hana-cluster-resources) 의 단계에 따라 클러스터에 SAP HANA 리소스를 만듭니다. SAP HANA 리소스가 만들어지면 SAP HANA 리소스와 파일 시스템 (NFS 탑재) 간에 위치 규칙 제약 조건을 만들어야 합니다.

2. **[1]** SAP HANA 리소스와 NFS 탑재 간의 제약 조건 구성

   모든 노드의 NFS 탑재를 탑재 한 경우에만 노드에서 SAP HANA 리소스를 실행할 수 있도록 위치 규칙 제약 조건이 설정 됩니다.

    ```
    pcs constraint location SAPHanaTopology_HN1_03-clone rule score=-INFINITY hana_nfs1_active ne true and hana_nfs2_active ne true
    pcs constraint location SAPHana_HN1_03-master rule score=-INFINITY hana_nfs1_active ne true and hana_nfs2_active ne true
    # Take the cluster out of maintenance mode
    sudo pcs property set maintenance-mode=false
    ```

   클러스터 및 모든 리소스의 상태를 확인 합니다.

    ```
    sudo pcs status
    
    Online: [ hanadb1 hanadb2 ]
    
    Full list of resources:
    
    rsc_hdb_azr_agt(stonith:fence_azure_arm):  Started hanadb1
    
    Resource Group: hanadb1_nfs
    hana_data1 (ocf::heartbeat:Filesystem):Started hanadb1
    hana_log1  (ocf::heartbeat:Filesystem):Started hanadb1
    hana_shared1   (ocf::heartbeat:Filesystem):Started hanadb1
    
    Resource Group: hanadb2_nfs
    hana_data2 (ocf::heartbeat:Filesystem):Started hanadb2
    hana_log2  (ocf::heartbeat:Filesystem):Started hanadb2
    hana_shared2   (ocf::heartbeat:Filesystem):Started hanadb2
    
    hana_nfs1_active   (ocf::pacemaker:attribute): Started hanadb1
    hana_nfs2_active   (ocf::pacemaker:attribute): Started hanadb2
    
    Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hanadb1 hanadb2 ]
    
    Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hanadb1 ]
    Slaves: [ hanadb2 ]
    
    Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):  Started hanadb1
    vip_HN1_03 (ocf::heartbeat:IPaddr2):   Started hanadb1
    ```

## <a name="test-the-cluster-setup"></a>클러스터 설정 테스트

이 섹션에서는 설정을 테스트하는 방법을 설명합니다. 

1. 테스트를 시작 하기 전에 Pacemaker에 실패 한 작업 (pc 상태를 통해)이 없는지 확인 하 고, 예기치 않은 위치 제약 조건 (예: 마이그레이션 테스트의 leftovers)이 없으며, HANA 시스템 복제가 동기화 상태 (예: systemReplicationStatus)입니다.

    ```
    sudo su - hn1adm -c "python /usr/sap/HN1/HDB03/exe/python_support/systemReplicationStatus.py"
    ```

2. 노드가 NFS 공유에 대 한 액세스를 잃을 때 (/hana/shared) 장애 시나리오에 대 한 클러스터 구성을 확인 합니다.

   SAP HANA 리소스 에이전트는 `/hana/shared` 장애 조치 (failover) 중에 작업을 수행 하기 위해에 저장 된 이진 파일에 종속 됩니다. 제공 된  `/hana/shared` 시나리오에서 파일 시스템이 NFS를 통해 탑재 됩니다.  
   서버 중 하나가 NFS 공유에 대 한 액세스 권한을 상실 하는 오류를 시뮬레이션 하기 어렵습니다. 실행할 수 있는 테스트는 파일 시스템을 읽기 전용으로 다시 탑재 하는 것입니다.
   이 접근 방식은 활성 노드에 대 한 액세스가 손실 된 경우 클러스터를 장애 조치 (failover) 할 수 있는지 유효성을 검사 `/hana/shared` 합니다.     


   **예상 결과:** 읽기 전용 파일 시스템으로 만들 때 파일 시스템에 대 `/hana/shared` `OCF_CHECK_LEVEL` `hana_shared1` 한 읽기/쓰기 작업을 수행 하는 리소스의 특성은 파일 시스템에 아무것도 쓸 수 없고 HANA 리소스 장애 조치 (failover)를 수행 하기 때문에 실패 합니다.  HANA 노드가 NFS 공유에 액세스할 수 없는 경우에도 동일한 결과가 예상 됩니다. 

   테스트 시작 전 리소스 상태:

    ```
    sudo pcs status
    # Example output
    Full list of resources:
     rsc_hdb_azr_agt        (stonith:fence_azure_arm):      Started hanadb1

     Resource Group: hanadb1_nfs
         hana_data1 (ocf::heartbeat:Filesystem):    Started hanadb1
         hana_log1  (ocf::heartbeat:Filesystem):    Started hanadb1
         hana_shared1       (ocf::heartbeat:Filesystem):    Started hanadb1

    Resource Group: hanadb2_nfs
         hana_data2 (ocf::heartbeat:Filesystem):    Started hanadb2
         hana_log2  (ocf::heartbeat:Filesystem):    Started hanadb2
         hana_shared2       (ocf::heartbeat:Filesystem):    Started hanadb2

     hana_nfs1_active       (ocf::pacemaker:attribute):     Started hanadb1
     hana_nfs2_active       (ocf::pacemaker:attribute):     Started hanadb2

     Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
         Started: [ hanadb1 hanadb2 ]

     Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
         Masters: [ hanadb1 ]
         Slaves: [ hanadb2 ]

     Resource Group: g_ip_HN1_03
         nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hanadb1
         vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hanadb1
    ```

   다음 명령을 사용 하 여 활성 클러스터 노드에/hana/shared를 읽기 전용 모드로 설정할 수 있습니다.

    ```
    sudo mount -o ro 10.32.2.4:/hanadb1-shared-mnt00001 /hana/shared
    ```

   hanadb1는 stonith ()에 설정 된 작업을 기반으로 하 여 다시 부팅 하거나 전원 꺼짐 `pcs property show stonith-action` 합니다.  서버 (hanadb1)가 다운 되 면 HANA 리소스가 hanadb2로 이동 합니다. Hanadb2에서 클러스터의 상태를 확인할 수 있습니다.

    ```
    pcs status

    Full list of resources:

     rsc_hdb_azr_agt        (stonith:fence_azure_arm):      Started hanadb2

     Resource Group: hanadb1_nfs
         hana_data1 (ocf::heartbeat:Filesystem):    Stopped
         hana_log1  (ocf::heartbeat:Filesystem):    Stopped
         hana_shared1       (ocf::heartbeat:Filesystem):    Stopped

     Resource Group: hanadb2_nfs
         hana_data2 (ocf::heartbeat:Filesystem):    Started hanadb2
         hana_log2  (ocf::heartbeat:Filesystem):    Started hanadb2
         hana_shared2       (ocf::heartbeat:Filesystem):    Started hanadb2

     hana_nfs1_active       (ocf::pacemaker:attribute):     Stopped
     hana_nfs2_active       (ocf::pacemaker:attribute):     Started hanadb2

     Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
         Started: [ hanadb2 ]
         Stopped: [ hanadb1 ]

     Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
         Masters: [ hanadb2 ]
         Stopped: [ hanadb1 ]

     Resource Group: g_ip_HN1_03
         nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hanadb2
         vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hanadb2
    ```

   [RHEL의 SAP HANA 시스템 복제 설정](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability-rhel#test-the-cluster-setup)에 설명 된 테스트를 수행 하 여 SAP HANA 클러스터 구성을 철저히 테스트 하는 것이 좋습니다.   