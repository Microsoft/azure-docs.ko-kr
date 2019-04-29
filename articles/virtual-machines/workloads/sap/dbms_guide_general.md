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
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/04/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8b19c0fd8af2792a4ffb877e5c6a7fc6b3f94511
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60836135"
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

이 가이드는 Microsoft Azure에서 SAP 소프트웨어 배포 및 구현 하는 방법에 대 한 설명서의 일부입니다. 이 가이드를 읽기 전에 검토 합니다 [계획 및 구현 가이드][planning-guide]합니다. 이 문서에서는 Azure 인프라 서비스 (IaaS) 기능을 사용 하 여 Microsoft Azure virtual machines (Vm)에서 SAP 관계형 DBMS 시스템의 일반 배포 기능을 다룹니다.

이 백서는 SAP 설치 설명서 및 지정 된 플랫폼에서 설치 및 SAP 소프트웨어 배포에 대 한 기본 리소스를 나타내는 SAP Note를 보완 합니다.

이 문서에서는 Azure VM에서 SAP 관련 DBMS 시스템을 실행할 때 고려해야 할 사항을 소개합니다. 이 챕터에는 특정 DBMS 시스템에 대한 참조가 거의 없습니다. 대신 특정 DBMS 시스템은이 문서의 후이 문서 내에서 처리 됩니다.

## <a name="definitions"></a>정의
설명서에서는 이러한 용어가 사용 됩니다.

* **IaaS**: 서비스 형태의 인프라를 제공 합니다.
* **PaaS**: 서비스 형태의 플랫폼입니다.
* **SaaS**: 서비스 형태의 소프트웨어를 제공 합니다.
* **SAP 구성 요소**: ERP 중앙 구성 요소 (ECC), BW (Business Warehouse), 솔루션 관리자 또는 엔터프라이즈 포털 (EP)와 같은 개별 SAP 응용 프로그램입니다. SAP 구성 요소는 기존의 ABAP 또는 Java 기술을 또는 비즈니스 개체와 같은 비 NetWeaver 기반 응용 프로그램에 기반 할 수 있습니다.
* **SAP 환경**: 개발, 품질 보증, 교육, 재해 복구 또는 프로덕션 같은 비즈니스 기능을 수행 하려면 하나 이상의 SAP 구성 요소가 논리적으로 그룹화 합니다.
* **SAP 지형**: 고객의 IT 자산 중 SAP 자산 전체를 의미합니다. SAP 지형에는 모든 프로덕션 및 비프로덕션 환경이 포함 됩니다.
* **SAP 시스템**: 예를 들어 SAP ERP 개발 시스템의 응용 프로그램 계층과 DBMS 계층의 조합 SAP Business Warehouse 시스템 또는 SAP CRM 프로덕션 시스템을 테스트 합니다. Azure 배포에서 온-프레미스와 Azure 간에 이러한 두 계층 분할 지원 되지 않습니다. 결과적으로, SAP 시스템은 온-프레미스 배포 또는 Azure에 배포 합니다. Azure 또는 온-프레미스에서 SAP 자산의 서로 다른 시스템을 배포할 수 있습니다. 예를 들어, SAP CRM 개발을 배포 하 고 테스트 시스템을 Azure 하지만 SAP CRM 프로덕션 시스템 온-프레미스 배포 수 있습니다.
* **크로스-프레미스**: 시나리오는 사이트 간, 다중 사이트에 있는 Azure 구독에 배포 된 Vm 또는 온-프레미스 데이터 간의 Azure ExpressRoute 연결 센터를 Azure에 설명 합니다. 공통 Azure 설명서에서 이러한 종류의 배포를 크로스-프레미스 시나리오라고도 합니다. 

    연결하는 이유는 온-프레미스 도메인, 온-프레미스 Active Directory 및 온-프레미스 DNS를 Azure로 확장하기 위한 것입니다. 온-프레미스 배경은 구독의 Azure 자산으로 확장됩니다. 이 확장을 사용하면 VM이 온-프레미스 도메인에 속할 수 있습니다. 온-프레미스 도메인의 도메인 사용자는 서버에 액세스 하 고 예: DBMS 서비스의 해당 Vm에서 서비스를 실행할 수 있습니다. 온-프레미스에 배포된 VM과 Azure에 배포된 VM 간의 통신 및 이름 확인이 가능합니다. 이 시나리오는 Azure에서 SAP 자산 배포를 사용 하 여에서 가장 일반적인 시나리오입니다. 자세한 내용은 [VPN 게이트웨이 계획 및 설계](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design)를 참조하세요.

> [!NOTE]
> 크로스-프레미스 배포 SAP 시스템의 경우 온-프레미스 도메인의 멤버인 중인 SAP 시스템을 실행 하는 Azure virtual machines는 및 프로덕션 SAP 시스템에 대 한 지원 됩니다. 일부 또는 전체 SAP 지형을 Azure로 배포하기 위한 크로스-프레미스 구성이 지원됩니다. Azure에서 전체 SAP 지형을 실행 LDAP Active Directory 및 온-프레미스 도메인의 일부가 되려면 해당 Vm에 필요 합니다. 
>
> 문서의 이전 버전에서는 하이브리드-IT 시나리오 언급 되었습니다. 용어 *하이브리드* 은 온-프레미스와 Azure 간의 프레미스 간 연결을가 사실을 기반으로 합니다. 이 경우 하이브리드 Azure의 Vm이 온-프레미스 Active Directory의 일부에는 또한 의미 합니다.
>
>

일부 Microsoft 문서 특히 DBMS 고가용성 구성에 대 한 프레미스 간 시나리오를 약간 다르게 설명합니다. SAP 관련 문서의 경우 프레미스 간 시나리오 좁혀진에 사이트 간 또는 개인 [ExpressRoute](https://azure.microsoft.com/services/expressroute/) 연결 및 온-프레미스와 Azure 간에 분산 되는 SAP 지형입니다.

## <a name="resources"></a>리소스
문서가 다른 사용 가능한 SAP 워크 로드에 Azure 에서입니다. 시작 [Azure에서 SAP 워크 로드: 시작](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started) 를 관심 분야를 선택 합니다.

다음 SAP Note는이 문서에서 다루는 영역 관련 하 여 Azure의 SAP 관련 되어 있습니다.

| Note 번호 | 제목 |
| --- | --- |
| [1928533] |Azure의 SAP 응용 프로그램: 지원 제품 및 Azure VM 유형 |
| [2015553] |Microsoft Azure의 SAP: 필수 구성 요소 지원 |
| [1999351] |SAP용 고급 Azure 모니터링 문제 해결 |
| [2178632] |Microsoft Azure의 SAP용 주요 모니터링 메트릭 |
| [1409604] |Windows에서의 가상화: 고급 모니터링 |
| [2191498] |Azure 포함 Linux의 SAP: 고급 모니터링 |
| [2039619] |Oracle Database를 사용하는 Microsoft Azure의 SAP 애플리케이션: 지원되는 제품 및 버전 |
| [2233094] |DB6: Linux, UNIX 및 Windows 용 IBM DB2를 사용 하 여 Azure의 SAP 응용 프로그램: 추가 정보 |
| [2243692] |Microsoft Azure(IaaS) VM의 Linux: SAP 라이선스 문제 |
| [1984787] |SUSE LINUX Enterprise Server 12: 설치 참고 사항 |
| [2002167] |Red Hat Enterprise Linux 7.x: 설치 및 업그레이드 |
| [2069760] |Oracle Linux 7.x SAP 설치 및 업그레이드 |
| [1597355] |Linux에 대한 스왑 공간 권장 사항 |
| [2171857] |Oracle Database 12c. Linux에서 파일 시스템 지원 |
| [1114181] |Oracle 데이터베이스 11g: Linux에서 파일 시스템 지원 |


에 대 한 내용은 SAP Note를 Linux에 대 한 참조를 [SAP community wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes)합니다.

Microsoft Azure 아키텍처 및 Microsoft Azure virtual machines 배포 및 운영 하는 방법에 대 한 실무 지식이 필요 합니다. 자세한 내용은 [Azure 문서](https://docs.microsoft.com/azure/)합니다.

일반적으로 Windows, Linux 및 DBMS 설치 및 구성은 기본적으로 온-프레미스에 설치할 완전 컴퓨터 또는 가상 머신과 동일 합니다. 일부 아키텍처 및 시스템 관리 구현이 것과 다른 Azure IaaS를 사용 하는 경우. 이 문서에서는 특정 아키텍처 및 시스템 관리를 준비 하기 위해 Azure IaaS를 사용 하는 경우의 차이점을 설명 합니다.


## <a name="65fa79d6-a85f-47ee-890b-22e794f51a64"></a>RDBMS 배포에 대 한 VM의 저장소 구조
이 챕터를 수행 하려면 읽기 및에 표시 된 정보를 이해 [이 장] [ deployment-guide-3] 의 합니다 [Deployment Guide][deployment-guide]합니다. 배우고 이해는 다른 VM 시리즈 및이 챕터를 읽기 전에 표준 및 프리미엄 저장소 간의 차이점을 해야 합니다. 

Azure Vm에 대 한 Azure Storage에 대 한 자세한 참조 하세요.

- [Azure Windows vm을 managed disks 소개](../../windows/managed-disks-overview.md)합니다.
- [Azure Linux vm을 managed disks 소개](../../linux/managed-disks-overview.md)합니다.

기본 구성에서는 일반적으로 운영 체제, DBMS 및 최종 SAP 이진 파일이 데이터베이스 파일과에서 구분 되는 배포 구조를 권장 합니다. Azure virtual machines에서 실행 되는 SAP 시스템은 기본 VHD 또는 디스크에 운영 체제, 데이터베이스 관리 시스템 실행 파일 및 SAP 실행 파일을 사용 하 여 설치 하는 것이 좋습니다. 

DBMS 데이터 및 로그 파일은 standard storage 또는 premium storage에 저장 됩니다. 별도 디스크에 저장 하 고 원래 Azure 운영 체제 이미지 VM에 논리 디스크로 연결. Linux 배포에 대 한 다양 한 권장 사항은 특히 SAP HANA에 대 한 설명 되어 있습니다.

디스크 레이아웃을 계획할 때 이러한 항목 간에 최상의 균형을 찾습니다.

* 데이터 파일 수
* 파일에 포함된 디스크 수
* 단일 디스크당 IOPS 할당량
* 디스크당 데이터 처리량
* VM 크기당 가능한 추가 데이터 디스크 수
* VM에서 제공할 수 있는 전체 저장소 처리량
* 다양한 Azure Storage 유형에서 제공할 수 있는 대기 시간
* VM Sla입니다.

Azure는 데이터 디스크 IOPS 할당량을 적용합니다. 이러한 할당량은 standard storage 및 premium storage에서 호스트 되는 디스크에 대 한 다른입니다. 또한 I/O 대기 시간도 두 저장소 유형 간에 다릅니다. Premium storage는 더 나은 I/O 대기 시간을 제공 합니다. 

각 VM 유형에 연결할 수 있는 데이터 디스크 수가 제한에 있습니다. 또 다른 제한은 특정 VM 유형만 premium storage를 사용할 수 있습니다. 일반적으로 CPU 및 메모리 요구 사항에 따라 특정 VM 유형을 사용 하도록 결정할 수도 있습니다. 또한 좋습니다 IOPS, 대기 시간 및 디스크 처리량 요구 일반적으로 디스크 수가 또는 프리미엄 저장소 디스크 유형을 사용 하 여 크기가 조정 됩니다. 각 디스크는 처리량 및 IOPS 수를 premium storage 사용 하 여 특히 디스크 크기를 요구할 수 있습니다.

> [!NOTE]
> DBMS 배포에 대 한 모든 데이터를 트랜잭션 로그에 대 한 premium storage 사용을 권장 했습니다 또는 파일을 다시 실행 합니다. 프로덕션 또는 비프로덕션 시스템을 배포 하는 것인지 중요 하지 않습니다.

> [!NOTE]
> Azure의 이점을 활용 하도록 고유의 [단일 VM SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/), 연결 된 모든 디스크는 기본 VHD를 포함 하는 프리미엄 저장소 유형 이어야 합니다.

> [!NOTE]
> 기본 데이터베이스 파일을 호스팅, 데이터 및 로그 파일과 같은 SAP 데이터베이스 옆에 있는 Azure 데이터 센터에 공동 배치 된 타사 데이터 센터에 위치한 저장소 하드웨어에서 지원 되지 않습니다. SAP 워크 로드에 대 한 기본 Azure 서비스로 표시 되는 유일한 storage는 SAP 데이터베이스의 데이터 및 트랜잭션 로그 파일에 대 한 지원 됩니다.

사용할 Azure Storage의 형식과 데이터베이스 파일 및 로그와 다시 실행 파일의 배치는 IOPS, 대기 시간 및 처리량 요구 사항에 따라 정의 됩니다. 충분 한 IOPS를 하도록 강제할 수 있습니다 여러 디스크를 사용 하거나 더 큰 premium storage 디스크를 사용 합니다. 여러 디스크를 사용 하는 경우 데이터 파일 또는 로그 포함 파일을 다시 실행 하는 디스크에 걸쳐 소프트웨어 stripe를 빌드하십시오. 이러한 경우, IOPS 및 디스크 처리량 Sla 기본 premium storage 디스크 또는 표준 저장소 디스크의 최대 달성 가능한 IOPS는 결과 스트라이프 세트에 대 한 누적 됩니다.

이미 설명한 것 처럼 IOPS 요구 사항에 따라 도움이 단일 VHD를 초과 하면 다양 한 Vhd에서 데이터베이스 파일에 필요한 IOPS 수를 조정 합니다. 디스크 IOPS 부하를 분산 하는 가장 쉬운 방법은 여러 디스크에 스트라이프를 소프트웨어 빌드 방법은입니다. 다음 소프트웨어 stripe에서 얻은 Lun에 다양 한 SAP dbms 데이터 파일을 배치 합니다. 스트라이프 디스크 수 요구 IOPs 요구 사항, 디스크 처리량 요구 및 볼륨에 따라 좌우 됩니다.


- - -
> ![Windows][Logo_Windows] Windows
>
> 여러 Azure Vhd에 걸쳐 있는 스트라이프 세트를 만들려면 Windows 저장소 공간을 사용 하는 것이 좋습니다. 적어도 사용 하 여 Windows Server 2012 R2 또는 Windows Server 2016 합니다.
>
> ![Linux][Logo_Linux] Linux
>
> 소프트웨어 Linux에서 RAID를 빌드하는 MDADM 및 LVM 논리 볼륨 관리자 ()만 지원 됩니다. 자세한 내용은 다음을 참조하세요.
>
> - [Linux에서 소프트웨어 RAID 구성](https://docs.microsoft.com/azure/virtual-machines/linux/configure-raid) MDADM을 사용 하 여
> - [Azure에서 Linux VM에 LVM 구성](https://docs.microsoft.com/azure/virtual-machines/linux/configure-lvm)(LVM 사용)
>
>

- - -

> [!NOTE]
> Azure Storage는 세 개의 Vhd 이미지, 때문에 해당 스트라이프 할 때 중복성을 구성 하려면 적합 하지 않습니다. I/o는 여러 Vhd에 분산 되도록 스트라이프를 구성 해야 합니다.
>

### <a name="managed-or-nonmanaged-disks"></a>관리 되거나 관리 되지 않는 디스크
Azure 저장소 계정이 관리 구문 및 제한의 대상이 됩니다. 표준 저장소 계정 및 premium storage 계정 간에 다를 제한 합니다. 기능 및 제한 사항에 대 한 정보를 참조 하세요 [Azure Storage 확장성 및 성능 목표](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets)합니다.

Standard storage의 경우 저장소 계정당 IOPS에 제한이 있는지를 기억 합니다. 포함 된 행을 참조 하세요 **총 요청 속도** 문서의 [Azure Storage 확장성 및 성능 목표](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets)합니다. Azure 구독 당 저장소 계정 수의 초기 한도 이기도합니다. 이러한 저장소 계정 제한에 도달 하지 않으려면 다른 저장소 계정에서 더 큰 SAP 지형에 대 한 Vhd를 분산 합니다. 천 개 이상의 Vhd 사용 하 여 몇 백 가상 머신에 대 한 이야기 하는 경우 이것이 지루한 작업입니다.

표준 저장소를 사용 하 여 SAP 워크 로드를 함께 DBMS 배포에 대 한 권장 되지 않습니다, 참조 및 표준 저장소에 대 한 권장 사항 되므로 짧은 제한 [문서](https://blogs.msdn.com/b/mast/archive/2014/10/14/configuring-azure-virtual-machines-for-optimal-storage-performance.aspx)

Microsoft 관리 작업을 계획 하 고 서로 다른 Azure 저장소 계정에서 Vhd를 배포를 방지 하기 위해 도입 [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/) 2017에서. 관리 디스크로 standard storage 및 premium storage를 사용할 수 있습니다. 관리 되지 않는 디스크에 비해 관리 디스크의 주요 이점은 다음과 같습니다.

- 관리 되는 디스크에 대 한 Azure 분산 여러 Vhd 서로 다른 저장소 계정을 자동으로 배포 시. 이러한 방식으로 저장소 계정의 데이터 볼륨의 경우 I/O 처리량을 제한 하 고 IOPS 적중 되지 않습니다.
- 관리 디스크를 사용 하 여 Azure Storage는 Azure 가용성 집합의 개념을 인식 합니다. VM을 Azure 가용성 집합의 일부 이면 기본 VHD 및 VM의 연결 된 디스크를 서로 다른 장애 및 업데이트 도메인에 배포 됩니다.


> [!IMPORTANT]
> Azure Managed Disks의 장점을 들어 사용 하는 Azure Managed Disks에 DBMS 배포 및 SAP 배포에 대 한 일반적 권장 합니다.
>

관리 되지 않는에서 managed disks로 변환을 참조 하세요.

- [비관리 디스크에서 Windows 가상 컴퓨터를 managed disks로 변환](https://docs.microsoft.com/azure/virtual-machines/windows/convert-unmanaged-to-managed-disks)합니다.
- [비관리 디스크에서 Linux 가상 컴퓨터를 managed disks로 변환](https://docs.microsoft.com/azure/virtual-machines/linux/convert-unmanaged-to-managed-disks)합니다.


### <a name="c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f"></a>VM 및 데이터 디스크에 대한 캐싱
Vm에 디스크를 탑재할 때 VM과 Azure storage에 있는 해당 디스크 간의 I/O 트래픽을 캐시 되는지 여부를 선택할 수 있습니다. 이 유형의 캐시에 대 한 두 가지 기술을 사용 하는 표준 및 프리미엄 저장소.

다음 권장 사항은 표준 DBMS에 대 한 이러한 I/O 특징을 가정합니다.

- 데이터베이스의 데이터 파일에 대 한 읽기 작업 대부분을 것입니다. 이러한 읽기는 DBMS 시스템에 대 한 중요 한 성능입니다.
- 데이터 파일에 대 한 쓰기 검사점 또는 지속적인 기반 버스트에서 발생 합니다. 읽기 보다 더 적은 쓰기 가지 하루 동안 평균을 계산 합니다. 데이터 파일에서 읽기와는 달리 이러한 쓰기는 비동기적 이며 모든 사용자 트랜잭션을 지연 하지 않습니다.
- 트랜잭션 로그 또는 다시 실행 파일에 대한 읽기는 거의 없습니다. 트랜잭션 로그 백업을 수행 하면 대형 I/o은 예외입니다.
- 트랜잭션 또는 다시 실행 로그 파일에 대 한 기본 부하가 씁니다. 워크 로드의 특성에 따라, 있습니다 I/o 4KB로 또는 다른 경우, I/O 크기로 1MB 이상으로 작은 합니다.
- 모든 쓰기는 신뢰할 수 있는 방식으로 디스크에 유지 해야 합니다.

표준 저장소에 대 한 가능한 캐시 유형은 다음과 같습니다.

* 없음
* 읽기
* 읽기/쓰기

일관성 있고 결정적 성능을 얻으려면, DBMS 관련 데이터 파일을 포함 하는 모든 디스크 로그와 파일 및 테이블 공간을 다시 실행에 대 한 표준 저장소에 캐싱이 설정 **NONE**합니다. 기본 VHD의 캐싱은 기본값으로 유지할 수 있습니다.

Premium storage의 경우 다음과 같은 캐싱 옵션이 존재 합니다.

* 없음
* 읽기
* 읽기/쓰기
* 없음 + Write Accelerator는 Azure M 시리즈 Vm에만 적용 됩니다
* 읽기 + Azure M 시리즈 Vm에 대해서만 Write Accelerator

Premium storage를 사용 하는 권장 **데이터 파일 읽기 캐싱** 선택한 데이터베이스는 sap **로그 파일의 디스크에 대 한 캐싱 없음**합니다.

M 시리즈 배포의 경우 DBMS 배포용 Azure Write Accelerator를 사용 하는 것이 좋습니다. 세부 정보, 제한 사항 및 배포에 Azure Write Accelerator [Write Accelerator 사용](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator)합니다.


### <a name="azure-nonpersistent-disks"></a>Azure 비영구 디스크
VM을 배포한 후 azure Vm에서 비영구 디스크를 제공 합니다. VM 다시 부팅 한 경우 해당 드라이브에 모든 콘텐츠는 초기화 합니다. 것을 데이터 파일 및 로그 다시 실행 파일과 데이터베이스의 어떠한 상황에 위치 해야 해당 비지속형된 드라이브입니다. 있을 일부 데이터베이스에 대 한 예외 이러한 비지속형된 드라이브 tempdb 및 임시 테이블 스페이스에 대 한 적절 한 수 있습니다. 비지속형된 드라이브는 VM 제품군을 사용 하 여 처리량에서 제한이 있으므로 A 시리즈 Vm에 대 한 해당 드라이브를 사용 하지 마십시오. 

자세한 내용은 [Azure에서 Windows Vm의 임시 드라이브 이해](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/)합니다.

- - -
> ![Windows][Logo_Windows] Windows
>
> Azure VM에서 D 드라이브는 Azure 계산 노드에서 일부 로컬 디스크에서 지원 되는 비지속형된 드라이브입니다. 이기 때문에 비지속형 VM 다시 부팅 되 면 D 드라이브 내용에 대 한 변경 내용을 손실 됩니다. 변경 내용 저장 된 파일, 생성 된 디렉터리 및 설치 된 응용 프로그램을 포함 합니다.
>
> ![Linux][Logo_Linux] Linux
>
> Linux Azure Vm에는 자동으로 드라이브의 /mnt/resource에는 Azure 계산 노드에서 로컬 디스크에서 지 원하는 비지속형된 드라이브를 탑재 합니다. 이기 때문에 비지속형 VM 다시 부팅 되 면 모든 변경 내용이/mnt/리소스에서 손실 됩니다. 변경 내용 저장 된 파일, 생성 된 디렉터리 및 설치 된 응용 프로그램을 포함 합니다.
>
>

- - -



### <a name="10b041ef-c177-498a-93ed-44b3441ab152"></a>Microsoft Azure Storage 복원력
Microsoft Azure Storage는 세 개 이상의 별도 저장소 노드에 OS 및 연결 된 디스크 또는 blob을 사용 하 여 기본 VHD를 저장합니다. 이 유형의 저장소 (LRS) 로컬 중복 저장소를 라고 합니다. LRS는 모든 유형의 Azure의 저장소에 대 한 기본값입니다.

다른 중복 메서드가 있습니다. 자세한 내용은 [Azure Storage 복제](https://docs.microsoft.com/azure/storage/common/storage-redundancy?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)를 참조하세요.

> [!NOTE]
>Premium storage에 DBMS Vm 및 데이터베이스와 로그를 저장 하 고 파일을 다시 실행 하는 디스크에 대 한 저장소 권장 되는 형식입니다. Premium storage에만 사용할 수 있는 중복성 방법은 LRS입니다. 결과적으로, 다른 Azure 지역 또는 가용성 영역으로 데이터베이스 데이터 복제를 사용 하도록 설정 하려면 데이터베이스 방법을 구성 해야 합니다. 데이터베이스 메서드에 SQL Server Always On, Oracle Data Guard 및 HANA 시스템 복제를 포함 합니다.


> [!NOTE]
> DBMS 배포의 경우 지역 중복 저장소 (GRS)를 사용 하 여 표준 저장소에 대 한 권장 되지 않습니다. GRS는 심각한 성능에 영향을 및 여러 다른 Vhd를 VM에 연결 된 쓰기 순서를 따르지 않습니다. 복제 대상 쪽에서 일관 되지 않은 데이터베이스에 연결을 허용 하지 않는 쓰기 순서가 다른 Vhd에서 잠재적으로 됩니다. 이 이런 경우 원본 VM 쪽에는 일반적으로 데이터베이스 및 로그 다시 실행 파일 여러 Vhd에 분산 된 경우 발생 합니다.



## <a name="vm-node-resiliency"></a>VM 노드 복원력
Azure Vm에 대 한 몇 가지 다른 Sla를 제공합니다. 자세한 내용은 참조의 최신 릴리스에 [Virtual Machines 용 SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/)합니다. SAP 시스템의 DBMS 계층 가용성에 일반적으로 중요 한 이기 때문에 가용성 집합과 가용성 영역을 유지 관리 이벤트를 이해 해야 합니다. 이러한 개념에 대 한 자세한 내용은 참조 하세요. [Azure에서 Windows 가상 머신의 가용성 관리](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability) 하 고 [Azure에서 Linux 가상 머신의 가용성 관리](https://docs.microsoft.com/azure/virtual-machines/linux/manage-availability)합니다.

SAP 워크 로드를 사용 하 여 프로덕션 DBMS 시나리오에 최소 권장 되는 다음과 같습니다.

- 별도 가용성 집합에서 동일한 Azure 지역에에서 2 개의 Vm을 배포 합니다.
- 동일한 Azure 가상 네트워크에서 이러한 두 Vm을 실행 하 고 Nic를 동일한 서브넷에서 연결 합니다.
- 데이터베이스 메서드를 사용하여 두 번째 VM에 대한 상시 대기를 유지합니다. 메서드는 SQL Server Always On, Oracle Data Guard 또는 HANA System Replication일 수 있습니다.

또한 세 번째 다른 Azure 지역의 VM을 배포를 동일한 데이터베이스 방법을 사용 하 여 다른 Azure 지역에 비동기 복제본을 제공 합니다.

Azure 가용성 집합을 설정 하는 방법에 대 한 자세한 내용은 [이 자습서](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets)합니다.



## <a name="azure-network-considerations"></a>Azure 네트워크 고려 사항
대규모 SAP 배포에서의 청사진을 사용 하 여 [Azure Virtual Datacenter](https://docs.microsoft.com/azure/architecture/vdc/networking-virtual-datacenter)합니다. 조직의 다른 부분에 가상 네트워크 구성 및 사용 권한 및 역할 할당에 사용 합니다.

이러한 모범 사례는 고객 배포에 수백 개의 결과:

- SAP 응용 프로그램에 배포 된 가상 네트워크를 인터넷에 액세스할 수 없습니다.
- 응용 프로그램 계층을 동일한 가상 네트워크에서 데이터베이스 Vm을 실행 합니다.
- 가상 네트워크 내에 Vm에 개인 IP 주소는 정적 할당. 자세한 내용은 [IP 주소 유형 및 Azure에서 할당 방법](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm)합니다.
- DBMS Vm 간에 라우팅 제한이 *되지* 로컬 DBMS Vm에 설치 하는 방화벽을 사용 하 여 설정 합니다. 대신 사용 하 여 정의 된 트래픽 라우팅 [네트워크 보안 그룹 (Nsg)](https://docs.microsoft.com/azure/virtual-network/security-overview)합니다.
- 분리 하 고 DBMS VM에 트래픽을 격리, 다른 Nic를 VM에 할당 합니다. 모든 NIC를 다른 IP 주소를 가져오고 모든 NIC는 다른 가상 네트워크 서브넷에 할당 합니다. 모든 서브넷에 다른 NSG 규칙이 있습니다. 네트워크 트래픽 분리를 격리 라우팅에 대 한 측정값입니다. 네트워크 처리량에 대 한 할당량을 설정 하는 사용 되지 않습니다.

> [!NOTE]
> Azure 통해 고정 IP 주소를 할당은 개별 가상 Nic에 할당 하는 것을 의미 합니다. 가상 NIC에 게스트 OS 내에서 고정 IP 주소를 할당 안 함 Azure Backup과 같은 일부 Azure 서비스는 사실에 의존에서 고정 IP 주소가 아닌 DHCP에 최소 기본 가상 NIC 설정 됩니다. 자세한 내용은 [문제를 해결 하는 Azure 가상 머신 백업](https://docs.microsoft.com/azure/backup/backup-azure-vms-troubleshoot#networking)합니다. VM에 여러 고정 IP 주소에 할당 하려면 VM에 여러 가상 Nic를 할당 합니다.
>


> [!IMPORTANT]
> 구성 [네트워크 가상 어플라이언스](https://azure.microsoft.com/solutions/network-appliances/) SAP 응용 프로그램의는 SAP NetWeaver-DBMS 계층 사이의 통신 경로에 Hybris 또는 S/4HANA 기반 SAP 시스템 지원 되지 않습니다. 이 제한은 기능 및 성능 이유로입니다. SAP 응용 프로그램 계층과 DBMS 계층 간의 통신 경로 직접 이어야 합니다. 제한 없는 [응용 프로그램 보안 그룹 (ASG) 및 NSG 규칙](https://docs.microsoft.com/azure/virtual-network/security-overview) ASG 및 NSG 규칙을 직접 통신 경로 허용 하는 경우. 
>
> 네트워크 가상 어플라이언스는 지원 되지 않습니다 여기서 다른 시나리오에:
>
> * Linux Pacemaker를 나타내는 Azure Vm 간의 통신 경로 클러스터 노드 및 SBD 장치에 설명 된 대로 [SAP 응용 프로그램용 SUSE Linux Enterprise Server의 Azure Vm에서 SAP NetWeaver에 대 한 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse)합니다.
> * Azure Vm 및 Windows Server 스케일 아웃 파일 서버 (SOFS) 간의 통신 경로 설정에 설명 된 대로 최대 [Azure에서 파일 공유를 사용 하 여 SAP ASCS/SCS 인스턴스에 Windows 장애 조치 클러스터에서 클러스터](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-file-share)합니다. 
>
> 네트워크 가상 어플라이언스 통신 경로에 두 개의 통신 파트너 간의 네트워크 대기 시간이 쉽게 double 수 있습니다. SAP 응용 프로그램 계층과 DBMS 계층 사이 중요 경로에서 처리량을 제한할 수도 있습니다. 일부 고객 시나리오, 네트워크 가상 어플라이언스 Pacemaker Linux 클러스터 실패를 일으킬 수 있습니다. 이들은 경우 Linux Pacemaker 클러스터 노드 간 통신 SBD 장치를 네트워크 가상 어플라이언스를 통해 통신 하는 위치입니다.
>

> [!IMPORTANT]
> 다른 디자인의 *되지* 지원 되는 SAP 응용 프로그램 계층과 DBMS 계층의 분리 되지 않은 다른 Azure 가상 네트워크에도 [피어 링](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) 서로 합니다. SAP 응용 프로그램 계층과 DBMS 계층이 서로 다른 Azure 가상 네트워크를 사용 하 여 대신 Azure virtual network 내의 서브넷을 사용 하 여 분리 하는 것이 좋습니다. 
>
> 두 가상 네트워크가 있어야 권장 사항을 따르고 대신 서로 다른 가상 네트워크에 두 계층을 분리할 필요가 않으려면 [피어 링](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)합니다. 
>
> 알고 있어야 하는 네트워크 간 트래픽 [피어 링](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) Azure virtual network는 전송 비용이 될 수 있습니다. 큰 데이터 볼륨 수 테라바이트에 달하는 구성 된 SAP 응용 프로그램 계층과 DBMS 계층 간에 교환 됩니다. SAP 응용 프로그램 계층과 DBMS 계층 피어 링 된 두 Azure virtual network 간에 분리 되는 경우에 상당한 비용을 누적 할 수 있습니다.

프로덕션 Azure 가용성 내 DBMS 배포에 대 한 두 개의 Vm을 사용 하 여 설정합니다. 또한 SAP 응용 프로그램 계층 및 두 개의 DBMS Vm에 관리 및 운영 트래픽에 대해 별도 라우팅을 사용 합니다. 다음 이미지를 참조하세요.

![두 서브넷에 있는 두 VM에 대한 다이어그램](./media/virtual-machines-shared-sap-deployment-guide/general_two_dbms_two_subnets.PNG)


### <a name="use-azure-load-balancer-to-redirect-traffic"></a>Azure Load Balancer를 사용 하 여 트래픽을 리디렉션할 수
SQL Server Always On 또는 HANA 시스템 복제와 같은 기능에 사용 되는 개인 가상 IP 주소를 사용 하 여 Azure load balancer의 구성이 필요 합니다. 부하 분산 장치 프로브 포트를 사용 하 여 활성 DBMS 노드를 확인 하 고 해당 활성 데이터베이스 노드를 단독으로 트래픽을 라우팅합니다. 

데이터베이스 노드를 장애 조치의 경우 다시 구성 하려면 SAP 응용 프로그램에 대 한 않아도가 됩니다. 대신, 가장 일반적인 SAP 응용 프로그램 아키텍처는 개인 가상 IP 주소에 대해 다시 연결합니다. 한편, 부하 분산 장치가 트래픽을 개인 가상 IP 주소에 대해 두 번째 노드를 리디렉션하여 노드 장애 조치를 반응 합니다.

두 개의 다른 azure 제품 [부하 분산 장치 Sku](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview): 기본 SKU 및 표준 SKU입니다. Azure 가용성 영역에서 배포 하려는 경우가 아니면 기본 load balancer SKU 정상적으로 수행 합니다.

DBMS Vm에 항상을 load balancer를 통해 항상 라우팅됩니다 SAP 응용 프로그램 계층 사이의 트래픽을 인가요? 대답은 부하 분산 장치를 구성하는 방법에 따라 다릅니다. 

이번에 들어오는 트래픽은 DBMS VM은 항상 load balancer를 통해 라우팅됩니다. 나가는 트래픽 경로 DBMS VM에서 응용 프로그램 계층 VM의 부하 분산 장치 구성에 따라 달라 집니다. 

부하 분산 장치는 DirectServerReturn 옵션을 제공합니다. 해당 옵션을 구성 하는 경우는 DBMS VM에서 SAP 응용 프로그램 계층으로 트래픽은 *되지* load balancer를 통해 라우팅됩니다. 대신, 응용 프로그램 계층에 직접 이동합니다. DirectServerReturn 구성 되지 않은 경우에 load balancer를 통해 SAP 응용 프로그램 계층에 반환 되는 트래픽이 라우팅됩니다.

SAP 응용 프로그램 계층과 DBMS 계층 사이 배치 되는 부하 분산 장치를 사용 하 여 함께에서 DirectServerReturn를 구성 하는 것이 좋습니다. 이 구성은 두 계층 사이의 네트워크 대기 시간을 줄입니다.

SQL Server Always On을 사용 하 여이 구성을 설정 하는 방법의 예제를 참조 하세요 [Azure에서 Always On 가용성 그룹에 대 한 ILB 수신기 구성](https://docs.microsoft.com/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-ps-sql-int-listener)합니다.

Azure에서 SAP 인프라 배포에 대 한 참조로 게시 된 GitHub JSON 템플릿을 사용 하는 경우이 연구 [SAP 3 계층 시스템에 대 한 템플릿을](https://github.com/Azure/azure-quickstart-templates/tree/4099ad9bee183ed39b88c62cd33f517ae4e25669/sap-3-tier-marketplace-image-converged-md)합니다. 이 템플릿에서 또한 부하 분산 장치에 대 한 올바른 설정을 볼 수 있습니다.

### <a name="azure-accelerated-networking"></a>Azure 가속 네트워킹
Azure Vm 간에 네트워크 대기 시간을 더 줄이기 위해 선택 하는 권장 [Azure Accelerated Networking](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/)합니다. SAP 응용 프로그램 계층 및 SAP DBMS 계층에 대해 특히 SAP 워크 로드를 Azure Vm을 배포할 때 사용 합니다.

> [!NOTE]
> 일부 VM 유형은 가속 네트워킹을 지원합니다. 이전 문서에서는 가속 네트워킹을 지 원하는 VM 형식을 나열 합니다.
>

- - -
> ![Windows][Logo_Windows] Windows
>
> 네트워킹에 대 한 Windows 가속을 사용 하 여 Vm을 배포 하는 방법에 알아보려면 참조 [가속 네트워킹을 사용 하 여 Windows 가상 머신을 만드는](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell)합니다.
>
> ![Linux][Logo_Linux] Linux
>
> Linux 배포판에 대 한 자세한 내용은 참조 하세요. [가속 네트워킹을 사용 하 여 Linux 가상 머신 만들기](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli)합니다.
>
>

- - -

> [!NOTE]
> SUSE, Red Hat 및 Oracle Linux의 경우 가속 네트워킹은 최신 릴리스에서 지원됩니다. 예: SLES 12 SP2 또는 RHEL 7.2 이전 릴리스에서 Azure Accelerated Networking을 지원 하지 않습니다.
>


## <a name="deployment-of-host-monitoring"></a>호스트 모니터링 배포
Azure virtual machines의 SAP 응용 프로그램의 프로덕션 사용에 대 한 SAP를 Azure virtual machines를 실행 하는 물리적 호스트에서 호스트 모니터링 데이터를 가져오는 기능이 필요 합니다. SAPOSCOL 및 SAP HostAgent에서 이 기능을 사용하려면 특정 SAP HostAgent 패치 수준이 필요합니다. 정확한 패치 수준은 SAP Note [1409604]에 설명되어 있습니다.

SAPOSCOL 및 SAP 호스트 에이전트 호스트 데이터를 제공 하는 구성 요소의 배포 및 해당 구성 요소의 수명 주기 관리에 대 한 자세한 내용은 참조는 [Deployment guide][deployment-guide]합니다.


## <a name="next-steps"></a>다음 단계
특정 DBMS에 대 한 자세한 내용은 다음을 참조 하세요.

- [SAP 워크로드에 대한 SQL Server Azure Virtual Machines DBMS 배포](dbms_guide_sqlserver.md)
- [SAP 워크로드에 대한 Oracle Azure Virtual Machines DBMS 배포](dbms_guide_oracle.md)
- [SAP 워크로드에 대한 IBM DB2 Azure Virtual Machines DBMS 배포](dbms_guide_ibm.md)
- [SAP 워크로드에 대한 SAP ASE Azure Virtual Machines DBMS 배포](dbms_guide_sapase.md)
- [Azure에서 SAP maxDB, Live Cache 및 Content Server 배포](dbms_guide_maxdb.md)
- [Azure 운영 가이드의 SAP HANA](hana-vm-operations.md)
- [Azure Virtual Machines의 SAP HANA 고가용성](sap-hana-availability-overview.md)
- [Azure virtual machines에서 SAP HANA backup 가이드](sap-hana-backup-guide.md)

