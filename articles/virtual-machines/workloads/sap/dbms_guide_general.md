---
title: SAP 워크로드용 Azure Virtual Machines DBMS 배포 시 고려 사항 | Microsoft Docs
description: SAP 워크로드용 Azure Virtual Machines DBMS 배포 시 고려 사항
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/04/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 15c0368b2d0bd85f6fee65ffa2c9d6776d07f162
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88650618"
---
# <a name="considerations-for-azure-virtual-machines-dbms-deployment-for-sap-workload"></a>SAP 워크로드용 Azure Virtual Machines DBMS 배포 시 고려 사항
[1114181]:https://launchpad.support.sap.com/#/notes/1114181
[1409604]:https://launchpad.support.sap.com/#/notes/1409604
[1597355]:https://launchpad.support.sap.com/#/notes/1597355
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2039619]:https://launchpad.support.sap.com/#/notes/2039619
[2069760]:https://launchpad.support.sap.com/#/notes/2069760
[2171857]:https://launchpad.support.sap.com/#/notes/2171857
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[deployment-guide]:deployment-guide.md
[deployment-guide-3]:deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e
[planning-guide]:planning-guide.md

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png


[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

이 가이드는 Microsoft Azure에서의 SAP 소프트웨어 구현 및 배포하는 방법에 대한 설명서의 일부입니다. 이 가이드를 읽기 전에 [계획 및 구현 가이드][planning-guide]를 참조하세요. 이 문서에서는 Azure IaaS(서비스 제공 인프라) 기능을 사용하여 Microsoft Azure VM(Virtual Machines)에서 SAP 관련 DBMS 시스템의 일반적인 배포 측면에 대해 설명합니다.

이 문서는 지정된 플랫폼에서 SAP 소프트웨어 설치 및 배포에 대한 기본 리소스를 나타내는 SAP 설치 설명서 및 SAP 정보를 보완합니다.

이 문서에서는 Azure VM에서 SAP 관련 DBMS 시스템을 실행할 때 고려해야 할 사항을 소개합니다. 이 챕터에는 특정 DBMS 시스템에 대한 참조가 거의 없습니다. 대신 특정 DBMS 시스템은 이 문서의 뒷부분에서 소개하는 문서 내에서 다루고 있습니다.

## <a name="definitions"></a>정의
문서 전체에서 다음과 같은 용어가 사용됩니다.

* **IaaS**: 서비스 제공 인프라(Infrastructure as a Service)입니다.
* **PaaS**: 서비스 제공 플랫폼(Platform as a Service)입니다.
* **SaaS**: 서비스 제공 소프트웨어(Software as a Service)입니다.
* **SAP 구성 요소**: ECC(ERP 중앙 구성 요소), BW(비즈니스 웨어하우스), 솔루션 관리자 또는 EP(엔터프라이즈 포털)와 같은 개별 SAP 애플리케이션입니다. SAP 구성 요소는 기존의 ABAP 또는 Java 기술을 기반으로 하거나 비즈니스 개체와 같은 비NetWeaver 기반 애플리케이션을 기반으로 사용할 수 있습니다.
* **SAP 환경**: 개발, 품질 보증, 교육, 재해 복구 또는 프로덕션과 같은 비즈니스 기능을 수행하기 위해 논리적으로 그룹화된 하나 이상의 SAP 구성 요소입니다.
* **SAP 환경**: 고객의 IT 자산 중 SAP 자산 전체를 의미합니다. SAP 환경에는 모든 프로덕션 및 비프로덕션 환경이 포함됩니다.
* **SAP 시스템**: DBMS 계층과 애플리케이션 계층(예: SAP ERP 개발 시스템, SAP Business Warehouse 테스트 시스템 또는 SAP CRM 프로덕션 시스템)의 조합입니다. Azure 배포에서는 온-프레미스와 Azure 간에 이러한 두 계층의 분할을 지원하지 않습니다. 따라서 SAP 시스템은 온-프레미스에 배포되거나 Azure에 배포됩니다. Azure 또는 온-프레미스에는 SAP 배경의 서로 다른 시스템을 배포할 수 있습니다. 예를 들어 Azure에는 SAP CRM 개발 및 테스트 시스템을 배포할 수 있지만 온-프레미스에는 SAP CRM 프로덕션 시스템을 배포할 수 있습니다.
* **프레미스 간**: VM이 온-프레미스 데이터 센터와 Azure 간에 사이트 간, 다중 사이트 또는 ExpressRoute 방식으로 연결된 Azure 구독에 배포되는 시나리오를 설명합니다. 공통 Azure 설명서에서 이러한 종류의 배포를 크로스-프레미스 시나리오라고도 합니다. 

    연결하는 이유는 온-프레미스 도메인, 온-프레미스 Active Directory 및 온-프레미스 DNS를 Azure로 확장하기 위한 것입니다. 온-프레미스 배경은 구독의 Azure 자산으로 확장됩니다. 이 확장을 사용하면 VM이 온-프레미스 도메인에 속할 수 있습니다. 온-프레미스 도메인의 도메인 사용자는 서버에 액세스하고 이러한 VM에서 서비스(예: DBMS 서비스)를 실행할 수 있습니다. 온-프레미스에 배포된 VM과 Azure에 배포된 VM 간의 통신 및 이름 확인이 가능합니다. 이 시나리오는 Azure에 SAP 자산을 배포하는 데 사용하는 가장 일반적인 시나리오입니다. 자세한 내용은 [VPN 게이트웨이 계획 및 설계](../../../vpn-gateway/vpn-gateway-about-vpngateways.md)를 참조하세요.

> [!NOTE]
> 프로덕션 SAP 시스템의 경우 SAP 시스템을 실행 중인 Azure Virtual Machines가 온-프레미스 도메인의 멤버인 SAP 시스템의 프레미스 간 배포가 지원됩니다. 일부 또는 전체 SAP 지형을 Azure로 배포하기 위한 크로스-프레미스 구성이 지원됩니다. Azure에서 전체 SAP 환경을 실행하는 경우에도 해당 VM이 온-프레미스 도메인 및 Active Directory/LDAP에 속해야 합니다. 
>
> 이전 버전의 설명서에서는 하이브리드 IT 시나리오에 대해 설명했습니다. *하이브리드*라는 용어는 온-프레미스와 Azure 간에 크로스-프레미스 연결이 있다는 사실에서 비롯되었습니다. 이 경우 하이브리드는 Azure의 VM이 온-프레미스 Active Directory의 일부임을 의미하기도 합니다.
>
>

일부 Microsoft 문서에서는 특히 DBMS 고가용성 구성의 경우 크로스-프레미스 시나리오를 약간 다르게 설명합니다. SAP 관련 문서의 경우 프레미스 간 시나리오는 사이트 간 또는 프라이빗 [ExpressRoute](https://azure.microsoft.com/services/expressroute/) 연결 및 온-프레미스와 Azure 간에 분산된 SAP 환경으로 요약됩니다.

## <a name="resources"></a>리소스
Azure의 SAP 워크로드에 사용할 수 있는 다른 문서가 있습니다. [Azure의 SAP 워크로드: 시작](./get-started.md)부터 시작한 다음 관심 영역을 선택합니다.

이 문서에서 다루는 영역에 대해 Azure의 SAP와 관련된 SAP Note는 다음과 같습니다.

| Note 번호 | 제목 |
| --- | --- |
| [1928533] |Azure의 SAP 애플리케이션: 지원 제품 및 Azure VM 유형 |
| [2015553] |Microsoft Azure의 SAP: 필수 구성 요소 지원 |
| [1999351] |SAP용 고급 Azure 모니터링 문제 해결 |
| [2178632] |Microsoft Azure의 SAP용 주요 모니터링 메트릭 |
| [1409604] |Windows에서의 가상화: 고급 모니터링 |
| [2191498] |Azure 포함 Linux의 SAP: 고급 모니터링 |
| [2039619] |Oracle Database를 사용하는 Microsoft Azure의 SAP 애플리케이션: 지원되는 제품 및 버전 |
| [2233094] |DB6: Linux, UNIX 및 Windows용 IBM DB2를 사용하는 Azure의 SAP 애플리케이션: 추가 정보 |
| [2243692] |Microsoft Azure(IaaS) VM의 Linux: SAP 라이선스 문제 |
| [1984787] |SUSE LINUX Enterprise Server 12: 설치 참고 사항 |
| [2002167] |Red Hat Enterprise Linux 7.x: 설치 및 업그레이드 |
| [2069760] |Oracle Linux 7.x SAP 설치 및 업그레이드 |
| [1597355] |Linux에 대한 스왑 공간 권장 사항 |
| [2171857] |Oracle Database 12c: Linux에서 파일 시스템 지원 |
| [1114181] |Oracle Database 11g: Linux에서 파일 시스템 지원 |


모든 Linux의 SAP 참고에 대한 자세한 내용은 [SAP 커뮤니티 WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes)를 참조하세요.

Microsoft Azure 아키텍처 및 Microsoft Azure Virtual Machines 배포와 작동 방법에 대한 실무 지식이 있어야 합니다. 자세한 내용은 [Azure 설명서](../../../index.yml)를 참조하세요.

일반적으로 Windows, Linux 및 DBMS 설치와 구성은 기본적으로 온-프레미스에 설치하는 가상 머신 또는 운영 체제 미설치 머신과 동일합니다. Azure IaaS를 사용하는 경우와 다른 몇 가지 아키텍처와 시스템 관리 구현 결정이 있습니다. 이 문서에서는 Azure IaaS를 사용할 때 준비할 특정 아키텍처 및 시스템 관리 차이점에 대해 설명합니다.


## <a name="storage-structure-of-a-vm-for-rdbms-deployments"></a><a name="65fa79d6-a85f-47ee-890b-22e794f51a64"></a>RDBMS 배포를 위한 VM의 스토리지 구조
이 챕터를 수행하려면 [배포 가이드][deployment-guide]의 [이 챕터][deployment-guide-3]에 제공된 정보를 읽고 이해합니다. 이 챕터를 읽기 전에 다양한 VM 시리즈와 Standard 및 Premium Storage 간의 차이점을 이해하고 파악해야 합니다. 

Azure Vm에 대 한 Azure Storage에 대해 알아보려면 [Azure vm 용 managed Disks 소개](../../managed-disks-overview.md)를 참조 하세요.

기본 구성에서는 일반적으로 운영 체제, DBMS 및 최종 SAP 이진 파일이 데이터베이스 파일과 분리된 배포 구조를 사용하는 것이 좋습니다. Azure Virtual Machines에서 실행되는 SAP 시스템에 운영 체제, 데이터베이스 관리 시스템 실행 파일 및 SAP 실행 파일과 함께 기본 VHD(또는 디스크)를 설치하는 것이 좋습니다. 

DBMS 데이터 및 로그 파일은 Standard Storage 또는 Premium Storage에 저장됩니다. 이러한 이미지는 별도의 디스크에 저장되며 원래 Azure 운영 체제 이미지 VM에 논리 디스크로 연결됩니다. Linux 배포의 경우 특히 SAP HANA에 대한 다양한 권장 사항이 문서화되어 있습니다.

디스크 레이아웃을 계획할 때 다음 항목에 가장 잘 맞는 레이아웃을 찾습니다.

* 데이터 파일 수
* 파일에 포함된 디스크 수
* 단일 디스크당 IOPS 할당량
* 디스크당 데이터 처리량
* VM 크기당 가능한 추가 데이터 디스크 수
* VM에서 제공할 수 있는 전체 스토리지 처리량
* 다양한 Azure Storage 유형에서 제공할 수 있는 대기 시간
* VM SLA

Azure는 데이터 디스크 IOPS 할당량을 적용합니다. 이러한 할당량은 디스크가 Standard Storage에서 호스트되는지 또는 Premium Storage에서 호스트되는지에 따라 달라집니다. 또한 I/O 대기 시간도 두 스토리지 유형 간에 다릅니다. Premium Storage는 더 나은 I/O 대기 시간을 제공합니다. 

각 VM 유형에는 연결할 수 있는 데이터 디스크 수가 제한되어 있습니다. 또한 특정 VM 유형만 Premium Storage를 사용할 수 있습니다. 일반적으로 CPU 및 메모리 요구 사항을 기반으로 특정 VM 유형을 사용하기로 결정합니다. 또한 일반적으로 디스크 수 또는 Premium Storage 디스크 유형에 따라 크기를 조정하는 IOPS, 대기 시간 및 디스크 처리량 요구 사항을 고려할 수 있습니다. 각 디스크에서 수행할 IOPS 및 처리량에 따라 디스크 크기가 결정될 수 있습니다(특히 Premium Storage의 경우).

> [!NOTE]
> DBMS 배포의 경우 모든 데이터, 트랜잭션 로그 또는 다시 실행 파일에 Premium Storage를 사용하는 것이 좋습니다. 프로덕션 또는 비프로덕션 시스템을 배포할지는 중요하지 않습니다.

> [!NOTE]
> Azure의 고유한 [단일 VM SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/)를 활용하려면 연결된 모든 디스크가 기본 VHD를 포함하는 Premium Storage 유형이어야 합니다.

> [!NOTE]
> Azure 데이터 센터 근처에 공동 배치된 타사 데이터 센터에 있는 스토리지 하드웨어에서 SAP 데이터베이스의 기본 데이터베이스 파일(예: 데이터 및 로그 파일)을 호스트할 수는 없습니다. SAP 워크로드의 경우에는 기본 Azure 서비스로 표시되는 스토리지에서만 SAP 데이터베이스의 데이터 및 트랜잭션 로그 파일을 호스트할 수 있습니다.

데이터베이스 파일 및 로그/다시 실행 파일의 배치와 사용하는 Azure Storage 유형은 IOPS, 대기 시간 및 처리량 요구 사항에 따라 정의됩니다. IOPS가 충분하려면 여러 디스크를 사용하거나 더 큰 Premium Storage 디스크를 사용해야 할 수도 있습니다. 여러 디스크를 사용하는 경우 데이터 파일 또는 로그 및 다시 실행 파일을 포함하는 디스크에서 소프트웨어 스트라이프를 작성합니다. 이러한 경우 기본 Premium Storage 디스크의 IOPS 및 디스크 처리량 SLA 또는 Standard Storage 디스크의 달성 가능한 최대 IOPS가 결과 스트라이프 세트에 누적됩니다.

이미 설명한 대로 단일 VHD에서 제공할 수 있는 IOPS 요구 사항을 초과하는 경우 여러 VHD에서 데이터베이스 파일에 필요한 IOPS 수의 균형을 조정합니다. IOPS 부하를 디스크에 분산시키는 가장 쉬운 방법은 서로 여러 디스크에 소프트웨어 스트라이프를 빌드하는 것입니다. 그런 다음, SAP DBMS의 여러 데이터 파일을 소프트웨어 스트라이프에서 얻은 LUN에 배치합니다. 스트라이프의 디스크 수는 IOPS, 디스크 처리량 및 볼륨 요구 사항에 따라 결정됩니다.


---
> ![Windows][Logo_Windows] Windows
>
> Windows 스토리지 공간을 사용하여 여러 Azure VHD에 스트라이프 세트를 만드는 것이 좋습니다. 최소 Windows Server 2012 R2 또는 Windows Server 2016을 사용합니다.
>
> ![Linux][Logo_Linux] Linux
>
> Linux에서 소프트웨어 RAID를 빌드하는 경우 MDADM 및 LVM(논리 볼륨 관리자)만 지원됩니다. 자세한 내용은 다음을 참조하세요.
>
> - [Linux에서 소프트웨어 RAID 구성](../../linux/configure-raid.md)(MDADM 사용)
> - [Azure에서 Linux VM에 LVM 구성](../../linux/configure-lvm.md)(LVM 사용)
>
>

---

> [!NOTE]
> Azure Storage는 VHD의 이미지 3개를 유지하므로 스트라이핑할 때 중복성을 구성하는 것은 적합하지 않습니다. I/O가 서로 다른 VHD를 통해 분산되도록 스트라이핑을 구성하기만 하면 됩니다.
>

### <a name="managed-or-nonmanaged-disks"></a>관리되거나 관리되지 않는 디스크
Azure Storage 계정은 관리 구성 요소일뿐 아니라 제한의 대상이 됩니다. Standard Storage 계정 및 Premium Storage 계정 간에 제한 사항이 다릅니다. 기능 및 제한 사항에 대한 자세한 내용은 [Azure Storage 확장성 및 성능 목표](../../../storage/common/scalability-targets-standard-account.md)를 참조하세요.

Standard Storage의 경우 스토리지 계정당 IOPS에 제한이 있다는 점에 주의하세요. [Azure Storage 확장성 및 성능 목표](../../../storage/common/scalability-targets-standard-account.md) 문서에서 **총 요청 비율**이 포함된 행을 참조하세요. Azure 구독당 스토리지 계정 수의 초기 제한도 있습니다. 이러한 스토리지 계정의 제한에 도달하지 않도록 다른 스토리지 계정에서 더 큰 SAP 환경에 맞게 VHD의 균형을 조정합니다. 이는 1,000개가 넘는 VHD가 있는 수백 개의 가상 머신에 대해 설명하는 것은 지루한 작업입니다.

SAP 워크로드와 함께 DBMS 배포에 Standard Storage를 사용하지 않는 것이 좋으므로 Standard Storage에 대한 참조 및 권장 사항은 이 간략한 [문서](/archive/blogs/mast/configuring-azure-virtual-machines-for-optimal-storage-performance)로 제한됩니다.

Microsoft는 2017년에 서로 다른 Azure Storage 계정에서 VHD를 계획하고 배포하는 관리 작업을 방지하기 위해 [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/)를 도입했습니다. 관리 디스크는 Standard Storage 및 Premium Storage에 사용할 수 있습니다. 관리 디스크의 주요 이점은 다음과 같은 관리되지 않는 디스크 목록과 비교됩니다.

- 관리 디스크의 경우 Azure는 배포 시 여러 스토리지 계정에 다른 VHD를 자동으로 배포합니다. 이러한 방식으로 데이터 볼륨, I/O 처리량 및 IOPS에 대한 스토리지 계정 한도에 도달하지 않습니다.
- Azure Storage는 관리 디스크를 사용하여 Azure 가용성 집합의 개념을 인식합니다. VM이 Azure 가용성 집합의 일부인 경우 VM의 기본 VHD 및 연결된 디스크는 서로 다른 장애 도메인 및 업데이트 도메인에 배포됩니다.


> [!IMPORTANT]
> Azure Managed Disks의 이점을 고려할 때 일반적으로 DBMS 배포 및 SAP 배포에 Azure Managed Disks를 사용하는 것이 좋습니다.
>

관리되지 않는 디스크에서 관리 디스크로 변환하려면:

- [비관리 디스크에서 관리 디스크로 Windows 가상 머신 변환](../../windows/convert-unmanaged-to-managed-disks.md)
- [Linux 가상 머신을 비관리 디스크에서 관리 디스크로 변환](../../linux/convert-unmanaged-to-managed-disks.md)


### <a name="caching-for-vms-and-data-disks"></a><a name="c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f"></a>VM 및 데이터 디스크에 대한 캐싱
VM에 디스크를 탑재할 때 VM과 Azure Storage에 있는 디스크 간의 I/O 트래픽을 캐시할지 여부를 선택할 수 있습니다. Standard 및 Premium Storage에서는 이 유형의 캐시에 대해 두 가지 기술을 사용합니다.

다음 권장 사항은 표준 DBMS에 대한 이러한 I/O 특성을 가정합니다.

- 대부분 데이터베이스의 데이터 파일에 대한 읽기 작업입니다. 이러한 읽기는 DBMS 시스템의 성능에 중요합니다.
- 데이터 파일에 대한 쓰기는 검사점 또는 상수 스트림에 따라 갑자기 발생합니다. 하루 평균, 읽기보다 쓰기 수가 적습니다. 데이터 파일의 읽기와 반대되는 이러한 쓰기는 비동기적이며 어떤 사용자 트랜잭션도 보류하지 않습니다.
- 트랜잭션 로그 또는 다시 실행 파일에 대한 읽기는 거의 없습니다. 트랜잭션 로그 백업 수행 시의 큰 I/O는 예외입니다.
- 트랜잭션 또는 다시 실행 로그 파일에 대한 주요 부하는 쓰기입니다. 워크로드의 특성에 따라 4KB 정도의 작은 I/O가 있거나, 다른 경우에 1MB 이상의 I/O 크기가 있을 수 있습니다.
- 모든 쓰기는 신뢰할 수 있는 방식으로 디스크에서 유지되어야 합니다.

Standard Storage의 경우 가능한 캐시 유형은 다음과 같습니다.

* None
* 읽기
* 읽기/쓰기

일관성 있고 결정적 성능을 얻으려면 DBMS 관련 데이터 파일, 로그 및 다시 실행 파일, 테이블 공간을 포함하는 모든 디스크에 대한 캐시를 표준 스토리지에서 **없음**으로 설정합니다. 기본 VHD의 캐싱은 기본값으로 유지할 수 있습니다.

Premium Storage의 경우 캐싱 옵션은 다음과 같습니다.

* None
* 읽기
* 읽기/쓰기
* 없음 + Write Accelerator(Azure M 시리즈 VM에만 해당)
* 읽기 + Write Accelerator(Azure M 시리즈 VM에만 해당)

Premium Storage의 경우 SAP 데이터베이스의 **데이터 파일 읽기 캐싱**을 사용하고 **로그 파일의 디스크에 대한 캐싱 없음**을 선택하는 것이 좋습니다.

M 시리즈 배포의 경우 DBMS 배포에 Azure Write Accelerator를 사용하는 것이 좋습니다. Azure Write Accelerator에 대한 세부 정보, 제한 및 배포는 [Write Accelerator 사용](../../how-to-enable-write-accelerator.md)을 참조하세요.


### <a name="azure-nonpersistent-disks"></a>Azure 비영구 디스크
VM이 배포되면 Azure VM에서 비영구 디스크를 제공합니다. VM을 다시 부팅하면 해당 드라이브의 모든 콘텐츠가 초기화됩니다. 데이터베이스의 데이터 파일과 로그/다시 실행 파일은 어떤 경우에도 이러한 비영구 드라이브에 배치하면 안 됩니다. 이러한 비영구 드라이브가 tempdb 및 temp 테이블 공간에 적합할 수 있는 일부 데이터베이스에는 예외가 있을 수 있습니다. 이러한 비영구 드라이브는 해당 VM 제품군의 처리량으로 제한되므로 A 시리즈 VM에는 해당 드라이브를 사용하지 마세요. 

자세한 내용은 [Azure의 Windows VM에서 임시 드라이브 이해](/archive/blogs/mast/understanding-the-temporary-drive-on-windows-azure-virtual-machines)를 참조하세요.

---
> ![Windows][Logo_Windows] Windows
>
> Azure VM의 드라이브 D는 Azure 컴퓨팅 노드의 일부 로컬 디스크에서 지원하는 비지속형 드라이브입니다. 비지속형이므로 D 드라이브의 콘텐츠에 대한 변경 내용은 VM을 재부팅하면 손실됩니다. 변경 내용에는 저장된 파일, 생성된 디렉터리, 설치된 애플리케이션이 포함됩니다.
>
> ![Linux][Logo_Linux] Linux
>
> Linux Azure VM은 Azure 컴퓨팅 노드의 로컬 디스크에서 지원하는 비지속형 드라이브의 /mnt/resource에 자동으로 탑재됩니다. 비지속형이므로 /mnt/resource의 콘텐츠에 대한 변경 내용은 VM을 재부팅하면 손실됩니다. 변경 내용에는 저장된 파일, 생성된 디렉터리, 설치된 애플리케이션이 포함됩니다.
>
>

---



### <a name="microsoft-azure-storage-resiliency"></a><a name="10b041ef-c177-498a-93ed-44b3441ab152"></a>Microsoft Azure Storage 복원력
Microsoft Azure Storage는 최소 3개의 별도 스토리지 노드에 기본 VHD(OS 포함) 및 연결된 디스크 또는 Blob을 저장합니다. 이 스토리지 유형을 LRS(로컬 중복 스토리지)라고 합니다. LRS는 Azure의 모든 스토리지 유형에 대한 기본값입니다.

다른 중복 방법이 있습니다. 자세한 내용은 [Azure Storage 복제](../../../storage/common/storage-redundancy.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)를 참조하세요.

> [!NOTE]
>Premium Storage는 데이터베이스, 로그 및 다시 실행 파일을 저장하는 디스크 및 DBMS VM에 권장되는 스토리지 유형입니다. Premium Storage에 사용할 수 있는 중복 방법은 LRS뿐입니다. 따라서 데이터베이스 데이터 복제를 다른 Azure 지역 또는 가용성 영역으로 설정하도록 데이터베이스 메서드를 구성해야 합니다. 데이터베이스 메서드에는 SQL Server Always On, Oracle Data Guard 및 HANA 시스템 복제가 포함됩니다.


> [!NOTE]
> DBMS 배포의 경우 Standard Storage에 GRS(지역 중복 스토리지)를 사용하지 않는 것이 좋습니다. GRS는 성능에 심각한 영향을 주며 VM에 연결된 여러 VHD에 대한 쓰기 순서를 고려하지 않습니다. 여러 VHD에서 쓰기 순서를 고려하지 않으면 복제 대상 측의 데이터베이스가 일치하지 않을 수 있습니다. 이러한 상황은 일반적으로 원본 VM 측에서 데이터베이스, 로그 및 다시 실행 파일이 여러 VHD에 분산된 경우 발생합니다.



## <a name="vm-node-resiliency"></a>VM 노드 복원력
Azure는 VM에 대해 여러 가지 SLA를 제공합니다. 자세한 내용은 [Virtual Machines에 대한 SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/)의 최신 릴리스를 참조하세요. DBMS 계층은 일반적으로 SAP 시스템의 가용성에 매우 중요하므로 가용성 집합, 가용성 영역 및 유지 관리 이벤트를 이해해야 합니다. 이러한 모든 개념에 대한 자세한 내용은 [Azure에서 Windows 가상 머신의 가용성 관리](../../windows/manage-availability.md) 및 [Azure에서 Linux 가상 머신의 가용성 관리](../../linux/manage-availability.md)를 참조하세요.

SAP 워크로드를 사용하는 프로덕션 DBMS 시나리오에 대한 최소 권장 사항은 다음과 같습니다.

- 동일한 Azure 지역에서 별도의 가용성 집합에 두 개의 VM을 배포합니다.
- 동일한 Azure 가상 네트워크에서 이 두 VM을 실행하고 동일한 서브넷에서 NIC를 연결합니다.
- 데이터베이스 메서드를 사용하여 두 번째 VM에 대한 상시 대기를 유지합니다. 메서드는 SQL Server Always On, Oracle Data Guard 또는 HANA System Replication일 수 있습니다.

또한 다른 Azure 지역에 세 번째 VM을 배포하고, 동일한 데이터베이스 메서드를 사용하여 다른 Azure 지역에 비동기 복제본을 제공할 수 있습니다.

Azure 가용성 집합을 설정하는 방법에 대한 자세한 내용은 [이 자습서](../../windows/tutorial-availability-sets.md)를 참조하세요.



## <a name="azure-network-considerations"></a>Azure 네트워크 고려 사항
대규모 SAP 배포에서는 [Azure 가상 데이터 센터](/azure/architecture/vdc/networking-virtual-datacenter)의 청사진을 사용합니다. 가상 네트워크 구성 및 조직의 다른 부분에 대한 사용 권한 및 역할 할당에 사용합니다.

다음 모범 사례는 수백 명의 고객 배포에 대한 결과입니다.

- SAP 애플리케이션이 배포된 가상 네트워크는 인터넷에 액세스할 수 없습니다.
- 데이터베이스 VM은 애플리케이션 계층과 동일한 가상 네트워크에서 실행됩니다.
- 가상 네트워크 내의 VM에는 개인 IP 주소의 정적 할당이 있습니다. 자세한 내용은 [Azure에서 IP 주소 형식 및 할당 메서드](../../../virtual-network/public-ip-addresses.md)를 참조하세요.
- DBMS VM 간의 라우팅 제한은 로컬 DBMS VM에 설치된 방화벽으로 설정되지 *않습니다*. 대신 트래픽 라우팅은 [NSG(네트워크 보안 그룹)](../../../virtual-network/security-overview.md)로 정의됩니다.
- 트래픽을 DBMS VM으로 분리하고 격리하려면 다른 NIC를 VM에 할당합니다. 모든 NIC는 서로 다른 IP 주소를 가지며 모든 NIC는 다른 가상 네트워크 서브넷에 할당됩니다. 모든 서브넷의 NSG 규칙은 서로 다릅니다. 네트워크 트래픽 격리 또는 분리는 라우팅에 대한 측정값입니다. 네트워크 처리량에 대한 할당량을 설정하는 데 사용되지 않습니다.

> [!NOTE]
> Azure를 통해 고정 IP 주소를 할당하는 것은 개별 가상 NIC에 할당하는 것을 의미합니다. 게스트 OS 내에서 고정 IP 주소를 vNIC에 할당하지 않아야 합니다. Azure Backup과 같은 일부 Azure 서비스는 최소한 기본 가상 NIC가 고정 IP 주소가 아닌 DHCP로 설정된다는 사실에 의존합니다. 자세한 내용은 [Azure Virtual Machine 백업 문제 해결](../../../backup/backup-azure-vms-troubleshoot.md#networking)을 참조하세요. VM에 여러 개의 고정 IP 주소를 할당하려면 여러 가상 NIC를 VM에 할당합니다.
>


> [!IMPORTANT]
> SAP 애플리케이션과 SAP NetWeaver, Hybris 또는 S/4HANA 기반 SAP 시스템의 DBMS 계층 사이의 통신 경로에 [네트워크 가상 어플라이언스](https://azure.microsoft.com/solutions/network-appliances/)를 구성하는 것은 지원되지 않습니다. 이러한 제한 사항은 기능 및 성능 때문입니다. SAP 애플리케이션 계층과 DBMS 계층 간의 통신 경로는 직접 통신이어야 합니다. ASG 및 NSG 규칙에서 직접 통신 경로를 허용하는 경우에는 [해당 ASG(애플리케이션 보안 그룹) 및 NSG 규칙](../../../virtual-network/security-overview.md)이 제한에 포함되지 않습니다. 
>
> 네트워크 가상 어플라이언스를 지원하지 않는 다른 시나리오는 다음과 같습니다.
>
> * [SAP 애플리케이션용 SUSE Linux Enterprise Server의 Azure VM에 있는 SAP NetWeaver에 대한 고가용성](./high-availability-guide-suse.md)에 설명된 Linux Pacemaker 클러스터 노드를 나타내는 Azure VM과 SBD 디바이스 간 통신 경로
> * [Azure에서 파일 공유를 사용하여 Windows 장애 조치(Failover) 클러스터에 SAP ASCS/SCS 인스턴스 클러스터링](./sap-high-availability-guide-wsfc-file-share.md)에 설명된 대로 설정된 Windows Server SOFS(Scale-Out File Server)와 Azure VM 간 통신 경로 
>
> 통신 경로의 네트워크 가상 어플라이언스는 두 통신 파트너 간의 네트워크 대기 시간을 쉽게 두 배로 늘릴 수 있습니다. 또한 SAP 애플리케이션 계층과 DBMS 계층 간의 중요한 경로에서 처리량을 제한할 수 있습니다. 일부 고객 시나리오에서 네트워크 가상 어플라이언스로 인해 Linux Pacemaker 클러스터가 실패할 수 있습니다. Linux Pacemaker 클러스터 노드 간 통신이 네트워크 가상 어플라이언스를 통해 해당 SBD 디바이스와 통신하는 경우가 여기에 해당합니다.
>

> [!IMPORTANT]
> 지원되지 *않는* 다른 디자인은 SAP 애플리케이션 계층과 DBMS 계층을 서로 [피어링](../../../virtual-network/virtual-network-peering-overview.md)되지 않은 다른 Azure 가상 네트워크로 분리하는 것입니다. 다른 Azure 가상 네트워크를 사용하는 대신, Azure 가상 네트워크 내의 서브넷을 사용하여 SAP 애플리케이션 계층과 DBMS 계층을 분리하는 것이 좋습니다. 
>
> 권장 사항을 따르지 않고 두 계층을 다른 가상 네트워크로 분리하려는 경우에는 두 가상 네트워크가 [피어링](../../../virtual-network/virtual-network-peering-overview.md)되어야 합니다. 
>
> [피어링](../../../virtual-network/virtual-network-peering-overview.md)된 두 Azure 가상 네트워크 간의 네트워크 트래픽에는 전송 비용이 부과됩니다. 테라바이트급의 대용량 데이터 볼륨이 SAP 애플리케이션 계층과 DBMS 계층 간에 교환됩니다. SAP 애플리케이션 계층 및 DBMS 계층이 두 피어링된 Azure Virtual Network 간에 분리된 경우 상당한 비용을 누적시킬 수 있습니다.

Azure 가용성 집합 내에서 프로덕션 DBMS 배포에 두 개의 VM을 사용합니다. 또한 SAP 애플리케이션 계층 및 두 DBMS VM에 대한 관리 및 운영 트래픽에 대해 별도의 라우팅을 사용합니다. 다음 이미지를 참조하세요.

![두 서브넷에 있는 두 VM에 대한 다이어그램](./media/virtual-machines-shared-sap-deployment-guide/general_two_dbms_two_subnets.PNG)


### <a name="use-azure-load-balancer-to-redirect-traffic"></a>Azure Load Balancer를 사용하여 트래픽 리디렉션
SQL Server Always On 또는 HANA System Replication과 같은 기능에 사용되는 개인 가상 IP 주소를 사용하려면 Azure Load Balancer를 구성해야 합니다. Load Balancer는 프로브 포트를 사용하여 활성 DBMS 노드를 결정하고 해당 활성 데이터베이스 노드로만 트래픽을 라우팅할 수 있습니다. 

데이터베이스 노드를 장애 조치(failover)하는 경우 SAP 애플리케이션을 다시 구성할 필요가 없습니다. 대신 가장 일반적인 SAP 애플리케이션 아키텍처가 개인 가상 IP 주소에 다시 연결됩니다. 한편 부하 분산 장치는 개인 가상 IP 주소에 대한 트래픽을 두 번째 노드로 리디렉션하여 노드 장애 조치(failover)에 대응했습니다.

Azure는 기본 SKU와 표준 SKU의 두 가지 [부하 분산 장치 SKU](../../../load-balancer/load-balancer-overview.md)를 제공합니다. Azure 가용성 영역을 통해 배포하지 않으려는 경우 기본 부하 분산 장치 SKU가 정상적으로 수행됩니다.

DBMS VM과 SAP 애플리케이션 계층 간의 트래픽은 항상 부하 분산 장치를 통해 언제나 라우팅되나요? 대답은 부하 분산 장치를 구성하는 방법에 따라 다릅니다. 

이 시점에서 DBMS VM에 들어오는 트래픽은 항상 부하 분산 장치를 통해 라우팅됩니다. DBMS VM에서 애플리케이션 계층 VM으로 나가는 트래픽 경로는 부하 분산 장치의 구성에 따라 다릅니다. 

부하 분산 장치는 DirectServerReturn 옵션을 제공합니다. 이 옵션이 구성되면 DBMS VM에서 SAP 애플리케이션 계층으로 향하는 트래픽이 부하 분산 장치를 통해 라우팅되지 *않습니다*. 대신 애플리케이션 계층으로 직접 이동합니다. DirectServerReturn이 구성되지 않으면 SAP 애플리케이션 계층으로 반환되는 트래픽이 부하 분산 장치를 통해 라우팅됩니다.

SAP 애플리케이션 계층과 DBMS 계층 사이에 배치되는 부하 분산 장치와 함께 DirectServerReturn을 구성하는 것이 좋습니다. 이 구성은 두 계층 간의 네트워크 대기 시간을 줄입니다.

SQL Server Always On을 사용하여 이 구성을 설정하는 방법에 대한 예는 [Azure에서 Always On 가용성 그룹에 대한 ILB 수신기 구성](/previous-versions/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-ps-sql-int-listener)을 참조하세요.

Azure에서 게시된 GitHub JSON 템플릿을 SAP 인프라 배포에 대한 참조로 사용하는 경우 이 [SAP 3계층 시스템용 템플릿](https://github.com/Azure/azure-quickstart-templates/tree/4099ad9bee183ed39b88c62cd33f517ae4e25669/sap-3-tier-marketplace-image-converged-md)을 연구합니다. 이 템플릿에서 부하 분산 장치에 대한 올바른 설정을 볼 수 있습니다.

### <a name="azure-accelerated-networking"></a>Azure 가속 네트워킹
Azure VM 간의 네트워크 대기 시간을 줄이려면 [Azure 가속화된 네트워킹](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/)을 선택하는 것이 좋습니다. SAP 워크로드, 특히 SAP 애플리케이션 계층 및 SAP DBMS 계층에 대해 Azure VM을 배포할 때 이 도구를 사용합니다.

> [!NOTE]
> 모든 VM 유형이 가속화된 네트워킹을 지원하지는 않습니다. 이전 문서에서는 가속화된 네트워킹을 지원하는 VM 유형이 나와 있습니다.
>

---
> ![Windows][Logo_Windows] Windows
>
> Windows용 가속화된 네트워킹을 사용하여 VM을 배포하는 방법을 알아보려면 [가속화된 네트워킹을 사용하여 Windows 가상 머신 만들기](../../../virtual-network/create-vm-accelerated-networking-powershell.md)를 참조하세요.
>
> ![Linux][Logo_Linux] Linux
>
> Linux 배포에 대한 자세한 내용은 [가속화된 네트워킹을 사용하여 Linux 가상 머신 만들기](../../../virtual-network/create-vm-accelerated-networking-cli.md)를 참조하세요.
>
>

---

> [!NOTE]
> SUSE, Red Hat 및 Oracle Linux의 경우 가속 네트워킹은 최신 릴리스에서 지원됩니다. SLES 12 SP2 또는 RHEL 7.2와 같은 이전 릴리스에서는 Azure 가속화된 네트워킹을 지원하지 않습니다.
>


## <a name="deployment-of-host-monitoring"></a>호스트 모니터링 배포
Azure Virtual Machines에서 SAP 애플리케이션을 프로덕션에 사용하려면 Azure Virtual Machines를 실행하는 실제 호스트에서 호스트 모니터링 데이터를 가져올 수 있는 기능이 필요합니다. SAPOSCOL 및 SAP HostAgent에서 이 기능을 사용하려면 특정 SAP HostAgent 패치 수준이 필요합니다. 정확한 패치 수준은 SAP Note [1409604]에 설명되어 있습니다.

SAPOSCOL 및 SAP 호스트 에이전트에 호스트 데이터를 전달하는 구성 요소의 배포 및 이러한 구성 요소의 수명 주기 관리에 대한 자세한 내용은 [배포 가이드][deployment-guide]를 참조하세요.


## <a name="next-steps"></a>다음 단계
특정 DBMS에 대한 자세한 내용은 다음을 참조하세요.

- [SAP 워크로드에 대한 SQL Server Azure Virtual Machines DBMS 배포](dbms_guide_sqlserver.md)
- [SAP 워크로드에 대한 Oracle Azure Virtual Machines DBMS 배포](dbms_guide_oracle.md)
- [SAP 워크로드에 대한 IBM DB2 Azure Virtual Machines DBMS 배포](dbms_guide_ibm.md)
- [SAP 워크로드에 대한 SAP ASE Azure Virtual Machines DBMS 배포](dbms_guide_sapase.md)
- [Azure에서 SAP maxDB, Live Cache 및 Content Server 배포](dbms_guide_maxdb.md)
- [Azure 운영 가이드의 SAP HANA](hana-vm-operations.md)
- [Azure Virtual Machines의 SAP HANA 고가용성](sap-hana-availability-overview.md)
- [Azure 가상 머신의 SAP HANA Backup 가이드](sap-hana-backup-guide.md)