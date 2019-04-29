---
title: SAP 워크로드 계획 및 배포 검사 목록 | Microsoft Docs
description: Azure의 SAP 워크로드 계획 및 배포 검사 목록
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
ms.date: 04/01/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: fef2d42282291bb0ea6afeea03e60234d3d47a4d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60648791"
---
# <a name="sap-workload-on-azure-planning-and-deployment-checklist"></a>Azure의 SAP 워크로드 계획 및 배포 검사 목록 

이 검사 목록은 해당 SAP NetWeaver, S/4HANA 및 Hybris 애플리케이션을 Azure Infrastructure as a Service로 이동하는 고객을 위한 것입니다.  이 검사 목록은 프로젝트 기간 동안 고객 및/또는 SAP 파트너가 검토해야 합니다. 프로젝트의 시작 및 계획 단계에서 많은 검사를 수행하는 것이 중요합니다. 배포가 완료되면 배포된 Azure 인프라 또는 SAP 소프트웨어 릴리스의 기본 변경 내용이 복잡해질 수 있습니다. 전체 프로젝트의 주요 시점에서 이 검사 목록을 검토합니다.  이렇게 하면 작은 문제가 큰 문제로 발전하기 전에 검색할 수 있고 필요한 변경을 다시 엔지니어링하고 테스트하기 위한 시간도 충분해집니다. 어떤 경우에도 검사 목록이 완전하다고 할 수는 없습니다. 개별 상황에 따라, 수행해야 하는 더 많은 검사가 있을 수도 있습니다. 

어셈블한 검사 목록에 Azure와는 별개인 작업은 포함되지 않습니다.  예제: SAP 애플리케이션 인터페이스는 Azure 공용 클라우드 또는 호스팅 공급자로 전환되는 동안 변경됩니다.    

이 검사 목록은 이미 배포된 시스템에도 사용할 수 있습니다. 배포한 이후에 Write Accelerator, 가용성 영역 등의 새 기능과 새 VM 유형이 추가되었을 수 있습니다.  따라서 검사 목록을 정기적으로 검토하여 Azure 플랫폼의 새로운 기능에 대해 알아보는 것이 유용합니다. 

## <a name="project-preparation-and-planning-phase"></a>프로젝트 준비 및 계획 단계
이 단계에서는 Azure 공용 클라우드로의 SAP 워크로드 마이그레이션을 계획합니다. 다음과 같은 최소한의 엔터티 및 항목 세트가 논의되고 정의되었습니다.

1. 개괄적인 디자인 문서 - 이 문서는 다음 내용을 포함해야 합니다.
    1. SAP 구성 요소 및 애플리케이션의 현재 인벤토리 및 Azure의 대상 애플리케이션 인벤토리
    2. 관련된 여러 다른 대상의 책임과 할당을 정의하는 RACI(책임 할당 매트릭스)를 만들고 사용합니다. 상위 수준에서 시작한 후, 계획 전반 및 첫 번째 배포를 진행하면서 점점 더 세부적인 수준에서 작업합니다.
    2. 개괄적인 솔루션 아키텍처
    3. 배포할 Azure 지역 결정. Azure 지역 목록에 대해서는 [Azure 지역](https://azure.microsoft.com/global-infrastructure/regions/)을 확인하세요. 각 Azure 지역에서 사용할 수 있는 서비스에 대해서는 문서 [지역별 사용 가능 제품](https://azure.microsoft.com/global-infrastructure/services/)을 확인하세요.
    4. 네트워킹 온-프레미스에서 Azure에 연결 하는 아키텍처입니다. [Azure의 가상 데이터 센터 청사진](https://docs.microsoft.com/azure/architecture/vdc/)을 숙지합니다.
    5. Azure에서 높은 비즈니스 영향 데이터를 실행하기 위한 보안 원칙. 관련 자료를 읽으려면 먼저 [Azure 보안 설명서](https://docs.microsoft.com/azure/security/)를 읽어보세요.
2.  기술 디자인 문서 – 이 문서는 다음 내용을 포함합니다.
    1.  솔루션 블록 다이어그램 
    2.  Azure의 컴퓨팅, 스토리지 및 네트워킹 구성 요소의 크기 조정. Azure VM의 SAP 크기 조정에 대해서는 SAP 지원 참고 [#1928533](https://launchpad.support.sap.com/#/notes/1928533)을 참조하세요. 
    3.  비즈니스 연속성 및 재해 복구 아키텍처
    4.  자세한 OS, DB, 커널 및 SAP 팩 버전. SAP NetWeaver 또는 S/4HANA에서 지원되는 OS 릴리스는 Azure VM에서 지원되지 않습니다. DBMS 릴리스의 경우도 마찬가지입니다. 다음 원본을 확인하고 필요한 경우 SAP 및 Azure 지원 창에 표시되도록 SAP 릴리스, DBMS 릴리스 또는 OS 릴리스를 업그레이드해야 합니다. SAP 및 Microsoft에서 완전한 지원을 받으려면 SAP 및 Azure 지원 릴리스 조합 내에 속해야 합니다. 필요한 경우 소프트웨어 구성 요소 중 일부를 업그레이드하는 것을 계획해야 합니다. 지원되는 SAP, OS 및 DBMS 소프트웨어에 대한 자세한 내용은 다음 위치에 설명되어 있습니다.
        1.  SAP 지원 참고 사항 [#1928533](https://launchpad.support.sap.com/#/notes/1928533). 이 참고 사항은 Azure VM에서 지원되는 최소 OS 릴리스를 정의합니다. 또한 대부분의 비 HANA 데이터베이스에 필요한 최소 데이터베이스 릴리스를 정의합니다. 이 참고 사항은 다양한 SAP 지원 Azure VM 유형의 SAP 크기 조정을 제공합니다.
        2.  SAP 지원 참고 사항 [#2039619](https://launchpad.support.sap.com/#/notes/2039619). 이 참고 사항은 Azure의 Oracle 지원 매트릭스를 정의합니다. Oracle은 SAP 워크로드에 대해 Azure에서 Windows 및 Oracle Linux만 게스트 OS로 지원합니다. 이러한 지원 설명은 SAP 인스턴스를 실행하는 SAP 애플리케이션 계층에도 적용됩니다. 그러나 Oracle은 Pacemaker를 통해 Oracle Linux의 SAP Central Services에 대한 고가용성을 지원하지 않습니다. Oracle Linux에서 ASCS에 대한 고가용성이 필요한 경우 Linux용 SIOS Protection Suite를 활용해야 합니다. SAP 인증 데이터에 대한 자세한 내용은 SAP 지원 노트 [#1662610 - Linux용 SIOS Protection Suite 지원 세부 정보](https://launchpad.support.sap.com/#/notes/1662610)를 확인하세요. Windows에서는 SAP Central Services용 SAP 지원 Windows 장애 조치(Failover) 클러스터 장애 조치(Failover) 솔루션이 Oracle DBMS 계층과 함께 지원됩니다. 
        3.  다른 OS 릴리스의 SAP HANA용 지원 매트릭스를 가져오기 위한 SAP 지원 참고 사항 [#2235581](https://launchpad.support.sap.com/#/notes/2235581)
        4.  SAP HANA에서 지원하는 Azure VM 및 [HANA 대규모 인스턴스](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)는 [여기](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)에 나와 있습니다.
        5.  [SAP 제품 가용성 매트릭스](https://support.sap.com/en/)
        6.  다른 SAP 특정 제품에 대한 기타 SAP 참고 사항  
    5.  SAP 프로덕션 시스템의 경우 엄격한 3계층 디자인이 권장됩니다. 동일한 VM에서 ASCS + APP 서버를 조합하는 것은 권장되지 않습니다.  SAP Central Services에 다중 SID 클러스터 구성을 사용하는 것은 Azure의 Windows 게스트 OS에서 지원됩니다. 반면, Azure의 Linux 운영 체제에서는 SAP Central Services 다중 SID 클러스터 구성이 지원되지 않습니다. Windows 게스트 OS 사례에 대한 설명서는 다음에서 찾을 수 있습니다.
        1.  [Azure에서 Windows Server 장애 조치(Failover) 클러스터링 및 공유 디스크를 사용하는 SAP ASCS/SCS 인스턴스 다중 SID 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ascs-ha-multi-sid-wsfc-shared-disk)
        2.  [Azure에서 Windows Server 장애 조치(Failover) 클러스터링 및 파일 공유를 사용하는 SAP ASCS/SCS 인스턴스 다중 SID 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ascs-ha-multi-sid-wsfc-file-share)
    6.  고가용성 및 재해 복구 아키텍처
        1.  RTO 및 RPO에 따라, 필요한 고가용성 및 재해 복구 아키텍처 구성을 정의합니다.
        2.  동일한 영역 내의 고가용성의 경우, Azure에서 원하는 DBMS가 제공해야 하는 기능을 확인합니다. 대부분의 DBMS는 프로덕션 시스템에서 권장되는 동기화 방법인 동기 상시 대기를 제공합니다. 또한 [SAP 워크로드용 Azure Virtual Machines DBMS 배포 시 고려 사항](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general) 및 관련 문서부터 시작해서 다양한 데이터베이스에 대한 SAP 관련 설명서를 확인하세요.
            1.  SQL Server에 대해 [여기](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server?view=sql-server-2017)에서 설명된 것처럼, DBMS 계층의 공유 디스크 구성에서 Windows 장애 조치(Failover) 클러스터 서비스를 사용하는 것은 지원되지 **않습니다**. 대신, 다음과 같은 솔루션을 사용할 수 있습니다.
                1.  [SQL Server AlwaysOn](https://docs.microsoft.com/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-ps-sql-alwayson-availability-groups) 
                2.  [Oracle Data Guard](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/configure-oracle-dataguard)
                3.  [HANA System Replication](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html)
        3.  여러 Azure 지역 간에 재해 복구의 경우, 다른 DBMS 공급업체가 제공하는 가능한 기능을 확인합니다. 대부분은 비동기 복제 또는 로그 전달을 지원합니다.
        4.  SAP 애플리케이션 계층의 경우, 동일한 Azure 지역 또는 DR 지역에 있는 프로덕션 배포의 복제본인 비즈니스 재발 테스트 시스템을 실행할지 여부를 정의합니다. 후자의 경우, 해당 비즈니스 재발 시스템을 프로덕션의 DR 대상으로 지정할 수 있습니다.
        5.  DR 사이트에 비프로덕션 시스템을 배치하지 않으려면 SAP 애플리케이션 계층을 Azure DR 지역에 복제하는 실행 가능한 방법으로 Azure Site Recovery를 검토합니다. [다중 계층 SAP NetWeaver 앱 배포를 위한 재해 복구 설정](https://docs.microsoft.com/azure/site-recovery/site-recovery-sap)도 참조하세요. 
        6.  [Azure 가용성 영역](https://docs.microsoft.com/azure/availability-zones/az-overview)을 활용하여 조합된 HA/DR 구성을 사용하려는 경우 가용성 영역을 사용할 수 있는 Azure 지역과 두 가용성 영역 간의 네트워크 대기 시간 증가로 인해 유발될 수 있는 제한 사항을 숙지하세요.  
3.  고객/파트너는 모든 SAP 인터페이스(SAP 및 SAP 이외)의 인벤토리를 만들어야 합니다. 
4.  Foundation Services 디자인 - 이 디자인은 다음과 같은 항목을 포함합니다.
    1.  Active Directory 및 DNS 디자인
    2.  Azure 내 네트워크 토폴로지 및 다른 SAP 시스템의 할당
    3.  Azure의 인프라 및 SAP 애플리케이션을 관리하는 다른 팀을 위한 [역할 기반 액세스](https://docs.microsoft.com/azure/role-based-access-control/overview) 구조
    3.  리소스 그룹 토폴로지 
    4.  [태그 지정 전략](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags#tags-and-billing)
    5.  VM 및 기타 인프라 구성 요소 및/또는 논리적 이름에 대한 명명 규칙
5.  Microsoft 프리미어 지원 계약 – MS TAM(기술 담당 관리자)을 식별합니다. SAP의 지원 요구 사항에 대해서는 SAP 지원 참고 [#2015553](https://launchpad.support.sap.com/#/notes/2015553)을 읽어보세요. 
6.  Azure 구독의 수 및 다른 구독의 코어 할당량을 정의합니다. 필요에 따라 [지원 요청을 열어 Azure 구독 할당량을 늘립니다](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request). 
7.  SAP 데이터를 Azure로 마이그레이션하기 위한 데이터 감소 및 데이터 마이그레이션 계획. SAP NetWeaver 시스템의 경우 SAP는 많은 수의 데이터 볼륨을 제한하는 방법에 대한 지침을 제공합니다. SAP는 SAP ERP 시스템의 데이터 관리에 대해 [이 심층 가이드](https://help.sap.com/http.svc/rc/2eb2fba8f8b1421c9a37a8d7233da545/7.0/en-US/Data_Management_Guide_Version_70E.PDF)를 발표했습니다. 그러나 일부 콘텐츠는 일반적으로 NetWeaver 및 S/4HANA 시스템에 적용됩니다.
8.  자동화된 배포 방법을 정의하고 결정합니다. Azure의 인프라 배포 이면에서 사용되는 자동화의 목표는 결정적 방식으로 배포하고 결정적 결과를 얻는 것입니다. 많은 고객이 Powershell 또는 CLI 기반 스크립트를 사용합니다. 하지만 SAP용 Azure 인프라를 배포하고 SAP 소프트웨어를 설치하는 데 사용할 수 있는 다양한 오픈 소스 기술이 있습니다. GitHub에서 예제를 찾을 수 있습니다.
    1.  [Azure Cloud의 자동화된 SAP 배포](https://github.com/Azure/sap-hana)
    2.  [SAP HANA 설치](https://github.com/AzureCAT-GSI/SAP-HANA-ARM)
9.  고객, 시스템 통합업체, Microsoft 및 기타 관련 당사자 간의 정기적인 디자인 및 배포 검토 케이던스를 정의합니다.

 
## <a name="pilot-phase-strongly-recommended"></a>파일럿 단계(강력 권장)
 
파일럿은 프로젝트 계획 및 준비 이전에 또는 동시에 실행할 수 있습니다. 이 단계에서 계획 및 준비 단계 동안 수행한 접근 방법 및 디자인을 테스트할 수도 있습니다. 파일럿 단계를 실제 개념 증명으로 늘릴 수 있습니다. 파일럿 배포 동안 전체 HA/DR 솔루션 뿐만 아니라 보안 디자인을 설정하고 유효한지 검사하는 것이 좋습니다. 일부 고객의 경우 이 단계에서 확장성 테스트도 수행할 수 있습니다. 다른 고객은 SAP 샌드박스 시스템의 배포를 파일럿 단계로 사용합니다. 파일럿 실행을 위해 Azure로 마이그레이션하려는 시스템을 식별했다고 가정하겠습니다.

1. Azure로의 데이터 전송을 최적화합니다. 통해 고객의 경우 전송에 크게 의존 [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) 온-프레미스에서 Express 회로 대역폭 충분 한 경우 가장 빠른 되었습니다. 다른 고객의 경우는 인터넷을 통해 진행하는 것이 더 빠른 것으로 확인되었습니다.
2. SAP의 유형이 다른 플랫폼 마이그레이션에서는 데이터베이스 데이터의 내보내기 및 가져오기, 내보내기 및 가져오기 단계 테스트와 최적화가 여기에 해당합니다. SQL Server를 대상 플랫폼으로 포함하는 대규모 마이그레이션의 경우 [여기](https://techcommunity.microsoft.com/t5/Running-SAP-Applications-on-the/SAP-OS-DB-Migration-to-SQL-Server-8211-FAQ-v6-2-April-2017/ba-p/368070)에서 권장 사항을 찾을 수 있습니다. 마이그레이션을 SAP 릴리스 업그레이드와 결합하고 설명서[예: [SUM 2.0 SP03의 DMO(데이터베이스 마이그레이션 옵션)](https://launchpad.support.sap.com/#/notes/2631152)]에 나와 있는 것처럼 특정 원본 및 대상 DBMS 플랫폼 조합을 이행할 때 조합된 릴리스 업그레이드 또는 [SAP DMO](https://blogs.sap.com/2013/11/29/database-migration-option-dmo-of-sum-introduction/) 프로세스가 필요하지 않은 경우 마이그레이션 모니터/SWPM 접근 방법을 수행할 수 있습니다. 
   1.  원본으로 내보내기, Azure로 파일 업로드 내보내기, 성능 가져오기.  내보내기와 가져오기 간의 중복을 최대화합니다.
   2.  인프라 크기 조정에 반영하기 위해 목표 및 대상 플랫폼 간의 데이터베이스 볼륨 평가    
   3.  유효성 검사 및 타이밍 최적화 
3. 기술 유효성 검사 
   1. VM 유형
      1.  SAP 지원 참고 사항, SAP HANA 하드웨어 디렉터리 및 SAP PAM의 리소스를 확인하여 Azure의 지원되는 VM, 해당 VM 유형의 지원되는 OS 릴리스 및 지원되는 SAP 및 DBMS 릴리스에 변경된 내용이 없는지 검토합니다.
      2.  Azure에 배포하는 애플리케이션 및 인프라의 크기가 유효한지 다시 확인합니다. 기존 애플리케이션을 이동하는 경우 종종, 사용하는 인프라 및 [SAP 벤치마크 웹 페이지](https://www.sap.com/dmc/exp/2018-benchmark-directory/#/sd)에서 필요한 SAPS를 파생하고 SAPS 지원 참고 사항 [#1928533](https://launchpad.support.sap.com/#/notes/1928533)에 나열된 SAPS 번호와 비교할 수 있습니다. 또한 [이 문서](https://techcommunity.microsoft.com/t5/Running-SAP-Applications-on-the/SAPS-ratings-on-Azure-VMs-8211-where-to-look-and-where-you-can/ba-p/368208)의 내용에 유의하세요.
      3.  최대 스토리지 처리량과 계획 단계에서 선택한 여러 다른 VM 유형의 네트워크 처리량에 대한 Azure VM의 크기 조정을 평가하고 테스트합니다. 해당 데이터는 다음 위치에서 찾을 수 있습니다.
          1.  [Azure의 Windows 가상 머신 크기](https://docs.microsoft.com/azure/virtual-machines/windows/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json). 크기 조정을 위해서는 **캐시되지 않은 최대 디스크 처리량**을 고려하는 것이 중요합니다.
          2.  [Azure의 Linux 가상 머신 크기](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json). 크기 조정을 위해서는 **캐시되지 않은 최대 디스크 처리량**을 고려하는 것이 중요합니다.
   2. Storage
      1.  사용 하 여 [Azure 표준 SSD 저장소](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-ssd) 성능 이외의 중요 한 DBMS 배포 및 SAP 응용 프로그램 계층을 나타내는 Vm에 대 한 최소
      2.  사용 하지 않도록 권장 [Azure 표준 HDD 디스크](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-hdd) 일반적
      2.  사용 하 여 [Azure Premium Storage](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#premium-ssd) 성능에 민감한 멀리 DBMS Vm에 대 한
      2.  [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/)를 사용합니다.
      3.  M-Series가 있는 DBMS 로그 드라이브에 대해 Azure Write Accelerator를 사용합니다. [Write Accelerator](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator)에 설명된 Write Accelerator 제한 및 사용법을 숙지합니다.
      4.  다른 DBMS 형식의 경우 [일반 SAP 관련 DBMS 설명서](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general) 및 일반 문서가 나타내는 DBMS 특정 설명서를 확인합니다.
      5.  SAP HANA의 경우 [Azure에서 SAP HANA 인프라 구성 및 작업](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)에 자세한 내용이 나와 있습니다.
      6.  디바이스 ID를 사용하여 Azure 데이터 디스크를 Azure Linux VM에 탑재해서는 안됩니다. 대신 UUID(Universally Unique Identifier)를 사용합니다. 예를 들어 Azure 데이터 디스크를 탑재하기 위해 그래픽 도구를 사용하는 경우 주의해야 합니다. /etc/fstab의 항목을 다시 확인하여 디스크가 UUID를 사용하여 탑재되는지 확인합니다.
          1.  자세한 내용은 [여기](https://docs.microsoft.com/azure/virtual-machines/linux/attach-disk-portal#connect-to-the-linux-vm-to-mount-the-new-disk)
   3. 네트워킹
      1.  여러 다른 Azure Virtual Network 간 또는 내부의 VNet 인프라와 SAP 애플리케이션 배포를 테스트하고 평가합니다.
          1.  다음을 기준으로 단일 Azure Virtual Network 내의 허브 및 스포크 가상 네트워크 아키텍처나 마이크로 구분 접근 방식을 평가합니다.
              1.  [피어링된 Azure Vnet](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) 간의 데이터 교환으로 인한 비용. 비용에 대해서는 [Virtual Network 가격](https://azure.microsoft.com/pricing/details/virtual-network/)을 확인합니다.
              2.  가상 네트워크의 서브넷에 호스트된 애플리케이션 또는 VM이 보안 위협을 초래하는 경우, 비교되는 Azure Virtual Network 간의 빠른 피어링 연결 해제를 활용하여 가상 네트워크 내에서 서브넷을 구분하도록 NSG를 변경합니다.
              3.  온-프레미스, 외부 세계 및 Azure에서 구축한 가상 데이터 센터 간의 네트워크 트래픽에 대한 중앙 로깅 및 감사
          2.  SAP 애플리케이션 계층과 SAP DBMS 계층 간의 데이터 경로를 평가하고 테스트합니다. 
              1.  SAP 애플리케이션과 SAP NetWeaver, Hybris 또는 S/4HANA 기반 SAP 시스템의 DBMS 계층 사이의 통신 경로에 [Azure 네트워크 가상 어플라이언스](https://azure.microsoft.com/solutions/network-appliances/)를 배치하는 것은 전혀 지원되지 않습니다.
              2.  피어링되지 않은 다른 Azure Virtual Network에 SAP 애플리케이션 계층과 SAP DBMS를 배치하는 것은 지원되지 않습니다.
              3.  [Azure NSG 및 ASG 규칙](https://docs.microsoft.com/azure/virtual-network/security-overview)은 SAP 애플리케이션 계층과 SAP DBMS 계층 간의 경로를 정의하기 위해 지원됩니다.
          3.  [Azure Accelerated Networking](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/)을 SAP 애플리케이션 계층과 SAP DBMS 계층에서 사용되는 VM에서 사용하도록 설정했습니다. Azure에서 가속화된 네트워킹을 지원하려면 다음과 같은 여러 OS 수준이 필요합니다.
              1.  Windows Server 2012 R2 또는 이후 버전
              2.  SUSE Linux 12 SP3 또는 이후 버전
              3.  RHEL 7.4 또는 이후 버전
              4.  Oracle Linux 7.5. RHCKL 커널을 사용하는 경우 이 릴리스는 3.10.0-862.13.1.el7이어야 합니다. Oracle UEK 커널을 사용하는 경우 릴리스 5가 필요합니다.
          4.   SAP 지원 참고 [#500235](https://launchpad.support.sap.com/#/notes/500235) 및 SAP 지원 참고 [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E)에 따라 SAP 애플리케이션 계층 VM 및 DBMS VM 간의 네트워크 대기 시간을 테스트하고 평가합니다. SAP 지원 참고 [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E)의 네트워크 대기 시간 지침에 따라 결과를 평가합니다. 네트워크 대기 시간은 보통 및 적절한 범위에 있어야 합니다. [여기](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture#networking-architecture-for-hana-large-instance)에 설명된 대로 VM과 HANA 대규모 인스턴스 단위 간의 트래픽에 예외가 적용됩니다.
          5.   ILB 배포가 Direct Server Return을 사용하도록 설정되어 있는지 확인합니다. 이 설정은 Azure ILB가 DBMS 계층의 고가용성 구성에 사용되는 경우 대기 시간을 줄여줍니다.
          6.   Linux 네트워크 매개 변수는 Linux 게스트 운영 체제 검사와 함께에서 Azure Load Balancer를 사용 하는 경우 **net.ipv4.tcp_timestamps** 로 설정 된 **0**합니다. 이전 버전의 SAP 권장 사항에 대해 유의 [#2382421](https://launchpad.support.sap.com/#/notes/2382421)합니다. 한편 SAP note는 Azure Load Balancer와 함께에서 작동 하도록 0으로 설정 해야 하는 매개 변수가 있다는 사실을 반영 하도록 업데이트 됩니다.
   4. 고가용성 및 재해 복구 배포. 
      1. 특정 Azure 가용성 영역을 정의하지 않고 SAP 애플리케이션 계층을 배포하는 경우 단일 SAP 시스템의 SAP 대화 인스턴스 또는 미들웨어 인스턴스를 실행하는 모든 VM이 [가용성 집합](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability)에 배포되어 있는지 확인합니다. 
         1.   SAP Central Services 및 DBMS에 대해 고가용성이 필요하지 않은 경우 이러한 VM을 SAP 애플리케이션 계층이 있는 동일한 가용성 집합으로 배포할 수 있습니다.
      2. 수동 복제본을 사용하여 고가용성을 위해 SAP Central Services 및 DBMS 계층을 보호하는 경우 SAP Central Services용 노드 2개와 DBMS 노드 2개를 각각 별도의 가용성 집합에 둡니다.
      3. Azure 가용성 영역에 배포하는 경우 가용성 집합을 활용할 수 없습니다. 그러나 활성 및 수동 Central Services 노드를 두 개의 다른 가용성 영역에 배포하여 영역 간의 대기 시간을 최소화해야 합니다.
         1.   가용성 영역 간의 DBMS 및 SAP Central Services 계층에 대해 Windows 또는 Pacemaker 장애 조치(Failover) 클러스터를 설정하는 경우 [Azure 표준 Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones)를 사용해야 합니다. [기본 Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview#skus)는 영역 배포에 사용할 수 없습니다. 
   5. 시간 제한 설정
      1. 여러 다른 SAP 인스턴스의 SAP NetWeaver 개발자 흔적을 확인하고 큐에 넣기 서버와 SAP 작업 프로세스 간에 연결 끊김이 없었는지 확인합니다. 이러한 연결 끊김은 다음 두 레지스트리 매개 변수를 설정하여 방지할 수 있습니다.
         1.   HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters\KeepAliveTime = 120000 - [이 문서](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-2000-server/cc957549(v=technet.10)) 참조
         2.   HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters\KeepAliveInterval = 120000 - [이 문서](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-2000-server/cc957548(v=technet.10)) 참조 
      2. 온-프레미스에 배포된 SAP GUI 인터페이스와 Azure에 배포된 SAP 애플리케이션 계층 간의 GUI 시간 초과를 방지하기 위해 다음 매개 변수가 default.pfl에 설정되어 있는지 또는 인스턴스 프로필에 설정되어 있는지를 확인합니다.
         1.   rdisp/keepalive_timeout = 3600
         2.   rdisp/keepalive = 20
      3. Windows 장애 조치(failover) 클러스터 구성을 사용하는 경우 무대응 노드에 반응하는 시간이 Azure에 대해 올바르게 설정되어 있는지 확인합니다. Microsoft의 문서 [장애 조치(Failover) 클러스터 네트워크 임계값 조정](https://blogs.msdn.microsoft.com/clustering/2012/11/21/tuning-failover-cluster-network-thresholds/)에는 매개 변수와 해당 매개 변수가 장애 조치(Failover) 민감도에 미치는 영향이 나와 있습니다. 나열된 매개 변수 중에서 다음 2개의 매개 변수를 다음 값으로 설정해야 합니다.
         1.   SameSubNetDelay = 2
         2.   SameSubNetThreshold = 15
4. 고가용성 및 재해 복구 절차 테스트
   1. 장애 조치(failover) 구성이 디자인한 대로 작동하는지 여부를 파악하기 위해 VM을 종료하거나(Windows 게스트 OS) 운영 체제를 비상 모드로 전환하여(Linux 게스트 OS) 장애 조치(Failover) 상황을 시뮬레이트합니다. 
   2. 장애 조치(failover)를 실행하는 데 걸리는 시간을 측정합니다. 시간이 너무 오래 걸리면 다음을 고려합니다.
      1.   SUSE Linux의 경우 Azure Fencing Agent 대신 SBD 디바이스를 사용하여 장애 조치(failover) 속도를 높입니다.
      2.   SAP HANA의 경우 데이터 다시 로드 시간이 너무 오래 걸리는 경우 더 많은 스토리지 대역폭을 프로비전하는 것이 좋습니다.
   3. 백업/복원 시퀀스 및 타이밍을 테스트하고 필요한 경우 조정합니다. 백업 시간만으로 충분하지 않은지 확인합니다. 또한 복원을 테스트하고 복원 작업의 타이밍을 확인합니다. RTO가 데이터베이스 또는 VM 복원 프로세스에 의존하는 경우 복원 시간이 RTO SLA 내에 포함되는지 확인합니다.
   4. 지역 DR 기능 및 아키텍처에 대해 테스트합니다.
5. 보안 검사
   1.  구현한 Azure RBAC(역할 기반 액세스) 아키텍처의 유효성을 테스트합니다. 목표는 다른 팀의 액세스 권한 및 사용 권한을 분리하고 제한하는 것입니다. 예를 들어, SAP 기본 팀 멤버는 VM을 배포하고 Azure 스토리지의 디스크를 지정된 Azure Virtual Network에 할당할 수 있어야 합니다. 그러나 SAP 기본 팀은 고유의 가상 네트워크를 만들거나 기존 가상 네트워크의 설정을 변경할 수 없어야 합니다. 한편, 네트워크 팀의 멤버는 VM을 SAP 애플리케이션 및 DBMS VM이 실행되는 가상 네트워크에 배포할 수 없어야 합니다. 또한, 네트워크 팀의 멤버는 VM의 특성을 변경하거나 VM 또는 디스크를 삭제할 수 없어야 합니다.  
   2.  [NSG 및 ASC](https://docs.microsoft.com/azure/virtual-network/security-overview) 규칙이 예상대로 작동하고 보호된 리소스를 보호하는지 확인합니다.
   3.  암호화해야 하는 모든 리소스가 암호화되는지 확인합니다. 인증서를 백업하고, 해당 인증서를 저장 및 액세스하고, 암호화된 엔터티를 복원하는 프로세스를 정의하고 실행합니다. 
   4.  [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-faq) 을 사용하거나 OS 지원 관점에서 가능한 경우 OS 디스크를 사용합니다.
   5.  너무 많은 계층의 암호화가 사용되지 않았는지 확인합니다. Azure Disk Encryption을 사용한 후 추가로 DBMS Transparent Database Encryption 방법 중 하나를 사용하는 것이 항상 바람직한 것은 아닙니다.
6. 성능 테스트
   1.  SAP 추적 및 측정값을 기준으로 하는 SAP에서 상위 10개의 온라인 보고서를 현재 구현(해당하는 경우)과 비교합니다. 
   2.  SAP 추적 및 측정값을 기준으로 하는 SAP에서 상위 10개의 일괄 처리 작업을 현재 구현(해당하는 경우)과 비교합니다. 
   3.  SAP 추적 및 측정값을 기준으로 하는 SAP에서 인터페이스를 통한 SAP 시스템으로의 데이터 전송을 비교합니다. 전송은 온-프레미스에서 Azure로 이동 하는 등의 다른 위치 간에 이제 알고 있는 인터페이스에 집중 


## <a name="non-production-phase"></a>비프로덕션 단계 
이 단계에서는 성공적인 파일럿 또는 PoC 후에 비프로덕션 SAP 시스템을 Azure에 배포하기 시작한다고 가정합니다. 개념 증명 이외의 모든 학습 및 환경은 이러한 배포에 맞게 조정해야 합니다. PoC에 나열된 모든 조건 및 단계도 이 유형의 배포에 적용됩니다. 이 단계에서는 일반적으로 개발 시스템, 단위 테스트 시스템 및 비즈니스 재발 테스트 시스템을 Azure에 배포합니다. 향후 프로덕션 시스템에 필요하므로 단일 SAP 애플리케이션 라인에 포함된 하나 이상의 비프로덕션 시스템에 완전한 고가용성 구성을 적용하는 것이 좋습니다. 해당 단계에서 고려해야 할 추가 단계는 다음과 같습니다.  

1.  이전 플랫폼의 시스템을 Azure로 이동하기 전에 CPU 사용량, 스토리지 처리량 및 IOPS 데이터와 같은 리소스 소비량 데이터를 수집합니다. 특히 이러한 작업을 DBMS 계층 단위에서 수행해야 하지만 애플리케이션 계층 단위에서도 수행합니다. 또한 네트워크 및 스토리지 대기 시간을 측정합니다.
2.  시스템의 가용성 사용 시간 패턴을 기록합니다. 목표는 비프로덕션 시스템을 연중무휴로 사용할 수 있어야 하는지 여부 또는 주 또는 월의 특정 단계 동안 종료해도 되는 비프로덕션 시스템이 있는지 파악하는 것입니다.
3.  테스트를 거친 후 Azure에서 고유한 OS 이미지를 만들지 여부 또는 Azure 이미지 갤러리에 있는 이미지를 사용할 것인지 여부를 정의합니다. Azure 갤러리 이외의 이미지를 사용하는 경우 OS 공급업체와의 지원 계약을 반영하는 올바른 이미지를 만들어야 합니다. 일부 OS 공급업체를 위해 Azure 갤러리는 BYOL(사용자 라이선스 필요) 이미지를 제공합니다. 다른 OS 이미지의 경우, Azure의 견적 가격에 지원이 포함됩니다. 사용자 고유의 OS 이미지를 만들려는 경우 다음 문서에서 설명서를 찾을 수 있습니다.
    1.  [이 설명서](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource)에 따라 Azure에 배포된 Windows VM의 일반화된 이미지를 작성할 수 있습니다.
    2.  [이 설명서](https://docs.microsoft.com/azure/virtual-machines/linux/capture-image)에 따라 Azure에 배포된 Linux VM의 일반화된 이미지를 작성할 수 있습니다.
3.  Azure VM 갤러리의 SUSE 및 Red Hat Linux 이미지를 사용하는 경우 Azure VM 갤러리에서 Linux 공급업체가 제공하는 SAP용 이미지를 사용해야 합니다.
4.  SAP는 Microsoft 지원 계약에 대한 지원 요구 사항을 충족하는지 확인합니다. SAP 지원 참고 사항 [#2015553](https://launchpad.support.sap.com/#/notes/2015553)에서 정보를 찾을 수 있습니다. HANA 대규모 인스턴스의 경우 문서 [온보딩 요구 사항](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-onboarding-requirements) 문서를 참조하세요.
4.  적절한 사람이 [계획된 유지 관리 알림](https://azure.microsoft.com/blog/a-new-planned-maintenance-experience-for-your-virtual-machines/)을 받는지 확인합니다. 그래야 가동 중지 시간 및 VM 다시 부팅 시간을 적절히 선택할 수 있습니다.
5.  [Channel9](https://channel9.msdn.com/)와 같은 채널의 Microsoft의 Azure 설명서 프레젠테이션에서 배포에 적용할 수 있는 새로운 기능이 있는지 지속적으로 확인합니다.
6.  지원 참고 사항 [#1928533](https://launchpad.support.sap.com/#/notes/1928533)과 같은 Azure 관련 SAP 참고 사항에서 새 VM SKU 또는 새로 지원되는 OS 및 DBMS 릴리스가 있는지 확인합니다. 새 VM 유형의 가격을 이전 VM 유형의 가격과 비교하여 최상의 가격 대비 성능 비율을 사용하여 VM을 배포할 수 있도록 합니다.
7.  SAP 지원 참고 사항, SAP HANA 하드웨어 디렉터리 및 SAP PAM의 리소스를 확인하여 Azure의 지원되는 VM, 해당 VM의 지원되는 OS 릴리스 및 지원되는 SAP 및 DBMS 릴리스에 변경된 내용이 없는지 검토합니다.
8.  [여기](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)에서 Azure의 새로운 HANA 인증 SKU를 확인하고, 가격과 예상 가격을 비교한 후 더 나은 가격 대비 성능 비율의 단위로 변경합니다. 
9.  새 VM 유형을 활용하고 원하는 새 Azure 기능을 통합하도록 배포 스크립트를 조정합니다.
10. 인프라를 배포한 후, SAP 지원 참고 [#500235](https://launchpad.support.sap.com/#/notes/500235) 및 SAP 지원 참고 [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E)에 따라 SAP 애플리케이션 계층 VM 및 DBMS VM 간의 네트워크 대기 시간을 테스트하고 평가합니다. SAP 지원 참고 [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E)의 네트워크 대기 시간 지침에 따라 결과를 평가합니다. 네트워크 대기 시간은 보통 및 적절한 범위에 있어야 합니다. [여기](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture#networking-architecture-for-hana-large-instance)에 설명된 대로 VM과 HANA 대규모 인스턴스 단위 간의 트래픽에 예외가 적용됩니다. [SAP 워크로드용 Azure Virtual Machines DBMS 배포 시 고려 사항](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general#azure-network-considerations) 및 [Azure의 SAP HANA 인프라 구성 및 운영](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)에 언급된 어떤 제한도 배포에 적용되지 않는지 확인합니다.
11. 워크로드를 적용하기 전에 개념 증명 단계에 나열된 다른 모든 검사를 수행합니다.
12. 워크로드가 적용되면, 해당 Azure 시스템의 리소스 소비량을 기록하고 이전 플랫폼에서 가져온 기록과 비교합니다. 더 큰 차이가 확인되면 향후 배포의 VM 크기를 조정합니다. 크기를 줄이는 경우 VM의 스토리지 및 네트워크 대역폭도 줄어듭니다.
    1.  [Azure의 Windows 가상 머신 크기](https://docs.microsoft.com/azure/virtual-machines/windows/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json). 
    2.  [Azure에서 Linux 가상 머신에 대한 크기](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json) 
13. 시스템 복사 기능 및 프로세스를 진행합니다. 목표는 개발 시스템 또는 테스트 시스템을 쉽게 복사하도록 하여 프로젝트 팀이 새 시스템을 빠르게 얻도록 하는 것입니다. 이러한 작업을 수행하기 위한 도구로 [SAP LaMa](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+Landscape+Management+%28SAP+LaMa%29+at+a+Glance)를 사용하는 것이 좋습니다.
14. 한쪽에서는 업무를 분리하도록 팀의 Azure 역할 기반 액세스, 사용 권한 및 프로세스를 최적화하고 개선합니다. 다른 쪽에서는 모든 팀이 Azure 인프라에서 해당 작업을 수행할 수 있도록 합니다.
15. 고가용성 및 재해 복구 절차를 연습하고, 테스트하고, 문서화하여 직원이 이러한 작업을 실행할 수 있도록 합니다. 단점을 파악하고 배포에 통합할 새로운 Azure 기능을 조정합니다.

 
## <a name="production-preparation-phase"></a>프로덕션 준비 단계 
이 단계에서는 비프로덕션 배포의 모든 환경 및 학습을 수집한 후 향후 프로덕션 배포에 적용할 수 있습니다. 이전 단계 외에, 현재 호스팅 위치와 Azure 간의 데이터 전송 작업도 준비해야 합니다. 

1.  Azure로 이동하기 전에 프로덕션 시스템의 필요한 SAP 릴리스 업그레이드를 진행합니다.
2.  프로덕션 시스템의 마이그레이션 이후에 수행해야 하는 기능 및 비즈니스 테스트를 비즈니스 소유자와 협의합니다.
    1.  이러한 모든 테스트를 현재 호스팅 위치의 원본 시스템으로 실행해야 합니다. 시스템이 Azure로 이동된 후 첫 테스트는 피하고 싶을 것입니다.
2.  Azure로의 프로덕션 마이그레이션 프로세스를 테스트합니다. 같은 기간 내에 모든 프로덕션 시스템을 Azure로 이동하지 않는 경우, 동일한 호스팅 위치에 있어야 하는 프로덕션 시스템 그룹을 작성합니다. 데이터 마이그레이션을 연습하고 테스트합니다. 일반적인 방법 목록은 다음과 같습니다.
    1.  Azure에서 데이터베이스 콘텐츠를 시드하고 동기화하려면 백업/복원 등의 DBMS 방법과 SQL Server AlwaysOn, HANA 시스템 복제 또는 로그 전달을 함께 사용합니다.
    2.  좀 더 작은 데이터베이스에 백업/복원을 사용합니다.
    3.  SAP SWPM 도구로 구현된 SAP 마이그레이션 모니터를 사용하여 유형이 다른 마이그레이션을 수행합니다.
    4.  SAP 릴리스 업그레이드와 조합해야 할 경우 [SAP DMO](https://blogs.sap.com/2013/11/29/database-migration-option-dmo-of-sum-introduction/) 프로세스를 사용합니다. 원본 및 대상 DBMS 간의 모든 조합이 지원되는 것은 아닙니다. 자세한 내용은 다양한 DMO 릴리스의 특정 SAP 지원 참고 사항을 참조하세요. [예: [SUM 2.0 SP04의 DMO(데이터베이스 마이그레이션 옵션)](https://launchpad.support.sap.com/#/notes/2644872)]
    5.  백업 또는 SAP 내보내기 파일을 이동해야 하는 경우 인터넷을 통한 데이터 전송과 ExpressRoute를 통한 데이터 전송 중 어떤 방식의 처리량이 더 높은지 테스트합니다. 인터넷을 통해 데이터를 이동하는 경우 향후 프로덕션 시스템을 위해 필요한 NSG/ASG 보안 규칙 중 일부를 변경해야 할 수 있습니다.
3.  이전 플랫폼의 시스템을 Azure로 이동하기 전에 CPU 사용량, 스토리지 처리량 및 IOPS 데이터와 같은 리소스 소비량 데이터를 수집합니다. 특히 이러한 작업을 DBMS 계층 단위에서 수행해야 하지만 애플리케이션 계층 단위에서도 수행합니다. 또한 네트워크 및 스토리지 대기 시간을 측정합니다.
4.  SAP 지원 참고 사항, SAP HANA 하드웨어 디렉터리 및 SAP PAM의 리소스를 확인하여 Azure의 지원되는 VM, 해당 VM의 지원되는 OS 릴리스 및 지원되는 SAP 및 DBMS 릴리스에 변경된 내용이 없는지 검토합니다. 
4.  VM 유형 및 Azure 기능에 대해 결정한 최신 변경 내용에 따라 배포 스크립트를 조정합니다.
5.  인프라 및 애플리케이션의 배포 후에 일련의 검사에 진행하여 다음에 대해 유효성을 확인합니다.
    1.  올바른 VM 유형을 올바른 특성 및 스토리지 크기로 배포했습니다.
    2.  VM이 원하는 올바른 OS 릴리스 및 패치에 있으며 균일한지 확인합니다.
    3.  VM의 보안 강화가 필요한지와 균일한지 확인합니다.
    4.  올바른 애플리케이션 릴리스 및 패치를 설치 및 배포했는지 확인합니다.
    5.  VM을 계획대로 Azure 가용성 집합에 배포했습니다.
    6.  Azure Premium Storage를 대기 시간이 중요한 디스크에 사용했거나 [SLA가 99.9%인 단일 VM](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/)이 필요합니다.
    7.  Azure Write Accelerator의 올바른 배포를 확인합니다.
        1.  VM 내의 스토리지 공간 또는 스트라이프 세트가 Azure Write Accelerator 지원이 필요한 디스크 간에 올바르게 작성되었는지 확인합니다.
            1.  [Linux에서 소프트웨어 RAID 구성](https://docs.microsoft.com/azure/virtual-machines/linux/configure-raid)을 확인합니다.
            2.  [Azure에서 Linux VM의 LVM 구성](https://docs.microsoft.com/azure/virtual-machines/linux/configure-lvm)을 확인합니다.
    8.  [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/)가 단독으로 사용되었습니다
    9.  VM을 올바른 가용성 집합 및 가용성 영역에 배포했습니다.
    10. [Azure Accelerated Networking](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/)을 SAP 애플리케이션 계층과 SAP DBMS 계층에서 사용되는 VM에서 사용하도록 설정했습니다.
    11. SAP 애플리케이션과 SAP NetWeaver, Hybris 또는 S/4HANA 기반 SAP 시스템의 DBMS 계층 사이의 통신 경로에 [Azure 네트워크 가상 어플라이언스](https://azure.microsoft.com/solutions/network-appliances/)가 배치되어 있지 않습니다.
    12. ASG 및 NSG 규칙은 원하는 계획된 방식으로 통신을 허용하고 필요한 경우 통신을 차단할 수 있도록 합니다.
    13. 앞서 설명된 시간 제한 설정이 올바르게 설정되었습니다.
    14. SAP 지원 참고 [#500235](https://launchpad.support.sap.com/#/notes/500235) 및 SAP 지원 참고 [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E)에 따라 SAP 애플리케이션 계층 VM 및 DBMS VM 간의 네트워크 대기 시간을 테스트하고 평가합니다. SAP 지원 참고 [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E)의 네트워크 대기 시간 지침에 따라 결과를 평가합니다. 네트워크 대기 시간은 보통 및 적절한 범위에 있어야 합니다. [여기](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture#networking-architecture-for-hana-large-instance)에 설명된 대로 VM과 HANA 대규모 인스턴스 단위 간의 트래픽에 예외가 적용됩니다.
    15. 필요한 경우 필요한 암호화 방법을 사용해서 암호화가 배포되었는지 여부를 확인합니다.
    16. 인터페이스 및 기타 애플리케이션이 새로 배포된 인프라에 연결될 수 있는지 여부를 확인합니다.
6.  Azure의 계획된 유지 관리에 대응하기 위한 플레이북을 만듭니다. 계획된 유지 관리의 경우 다시 부팅할 시스템 및 VM의 순서를 정의합니다.
    

## <a name="go-live-phase"></a>빠른 지원 단계
빠른 지원 단계에서는 이전 단계에서 개발한 플레이북을 따라 진행해야 합니다. 테스트 및 학습한 단계를 실행합니다. 구성 및 프로세스에서 갑작스럽게 변경하는 경우를 피합니다. 그 외에도 다음과 같은 조치가 적용됩니다.

1. Azure Portal 모니터링 및 기타 모니터링 도구가 작동하는지 확인합니다.  권장되는 도구는 Perfmon(Windows) 또는 SAR(Linux)입니다. 
    1.  CPU 카운터 
        1.  평균 CPU 시간 - 합계(모든 CPU)
        2.  평균 CPU 시간 – 각 개별 프로세서(즉, m128VM에서 128개 프로세서)
        3.  CPU 시간 커널 – 각 개별 프로세서
        4.  CPU 시간 사용자 – 각 개별 프로세서
    5.  메모리 
        1.  사용 가능한 메모리
        2.  메모리 페이지 입력/초
        3.  메모리 페이지 출력/sec
    4.  디스크 
        1.  디스크 읽기 kb/초 - 개별 디스크당 
        2.  디스크 읽기/초 - 개별 디스크당
        3.  디스크 읽기 ms/읽기 - 개별 디스크당
        4.  디스크 쓰기 kb/초 - 개별 디스크당 
        5.  디스크 쓰기/초 - 개별 디스크당
        6.  디스크 쓰기 ms/읽기 - 개별 디스크당
    5.  네트워크 
        1.  네트워크 패킷 입력/초
        2.  네트워크 패킷 출력/초
        3.  네트워크 kb 입력/초
        4.  네트워크 kb 출력/초 
2.  데이터 마이그레이션 후 비즈니스 소유자와 합의한 모든 유효성 검사 테스트를 수행합니다. 원래 원본 시스템에서 제공된 유효성 검사 테스트 결과만 수락합니다.
3.  인터페이스가 작동하는지 여부 및 다른 애플리케이션이 새로 배포한 프로덕션 시스템과 통신할 수 있는지 여부를 확인합니다.
4.  SAP 트랜잭션 STMS를 통해 전송 및 수정 시스템을 확인합니다.
5.  시스템이 프로덕션용으로 출시되면 데이터베이스 백업을 수행합니다.
6.  시스템이 프로덕션용으로 출시되면 SAP 애플리케이션 계층 VM의 VM 백업을 수행합니다.
7.  현재 빠른 지원 단계에 속하지 않지만 이 빠른 지원 단계에서 Azure로 이동한 SAP 시스템과 통신하는 SAP 시스템의 경우 SM51에서 호스트 이름 버퍼를 다시 설정해야 합니다. 이 단계를 수행하면 Azure로 이동한 애플리케이션 인스턴스의 이름과 연결된 이전에 캐시한 IP 주소가 제거됩니다.  


## <a name="post-production"></a>프로덕션 후
이 단계에서는 모니터링, 운영 및 시스템 관리에 관한 모든 작업이 진행됩니다. SAP의 관점에서, 이전 호스팅 위치를 사용하여 수행해야 하는 일반적인 작업이 적용됩니다. 수행하려는 Azure 특정 작업은 다음과 같습니다.

1. 높은 요금이 부과된 시스템의 Azure 청구서 분석
2. VM 및 스토리지 쪽에서 가격 대비 성능 효율 최적화
3. 시스템이 종료될 수 있는 시간 최적화  


## <a name="next-steps"></a>다음 단계
다음 설명서를 참조하세요.

- [SAP NetWeaver에 대한 Azure Virtual Machines 계획 및 구현](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)
- [SAP NetWeaver에 대한 Azure Virtual Machines 배포](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide)
- [SAP 워크로드용 Azure Virtual Machines DBMS 배포 시 고려 사항](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)

