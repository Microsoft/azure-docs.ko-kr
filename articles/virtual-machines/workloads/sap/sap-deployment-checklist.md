---
title: SAP 워크로드 계획 및 배포 검사 목록 | Microsoft Docs
description: Azure에 대 한 SAP 워크 로드 배포를 계획 하 고 워크 로드를 배포 하기 위한 검사 목록
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
ms.date: 08/10/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f195d4096baaa1d6a03b4b6c7c589ccef8fbd036
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88651729"
---
# <a name="sap-workloads-on-azure-planning-and-deployment-checklist"></a>Azure의 SAP 워크 로드: 계획 및 배포 검사 목록

이 검사 목록은 SAP NetWeaver, S/4HANA 및 Hybris 응용 프로그램을 Azure infrastructure as a service로 이동 하는 고객을 위한 것입니다. 프로젝트를 진행 하는 동안 고객 및/또는 SAP 파트너가 검사 목록을 검토 해야 합니다. 프로젝트를 시작 하 고 계획 단계를 수행 하는 동안 많은 검사가 완료 되는 것을 기억해 야 합니다. 배포가 완료 되 면 배포 된 Azure 인프라 또는 SAP 소프트웨어 릴리스를 간단 하 게 변경 하는 것이 복잡할 수 있습니다.

프로젝트 중 주요 마일스 톤의 검사 목록을 검토 합니다. 이렇게 하면 문제가 발생 하기 전에 사소한 문제를 감지할 수 있습니다. 또한 필요한 변경 내용을 다시 엔지니어링 하 고 테스트 하는 데 충분 한 시간을 갖게 됩니다. 이 검사 목록 완료를 고려 하지 마세요. 상황에 따라 더 많은 검사를 수행 해야 할 수도 있습니다.

검사 목록에는 Azure와 무관 한 작업이 포함 되어 있지 않습니다. 예를 들어 SAP 응용 프로그램 인터페이스는 Azure 플랫폼 또는 호스팅 공급자로 이동 하는 동안 변경 됩니다.

이 검사 목록은 이미 배포 된 시스템에도 사용할 수 있습니다. 쓰기 가속기 및 가용성 영역와 같은 새로운 기능과 새 VM 형식이 배포 된 후에 추가 되었을 수 있습니다. 따라서 정기적으로 검사 목록을 검토 하 여 Azure 플랫폼의 새로운 기능을 알고 있는지 확인 하는 것이 유용 합니다.

## <a name="project-preparation-and-planning-phase"></a>프로젝트 준비 및 계획 단계
이 단계에서는 Azure 플랫폼에 대 한 SAP 워크 로드의 마이그레이션을 계획 합니다. 이 단계에서 최소한 다음 문서를 만들고 마이그레이션의 다음 요소를 정의 하 고 설명 해야 합니다.

1. 개략적인 디자인 문서입니다. 이 문서에는 다음이 포함 되어야 합니다.
    - SAP 구성 요소 및 응용 프로그램의 현재 인벤토리 및 Azure에 대 한 대상 응용 프로그램 인벤토리가 있습니다.
    - 관련 된 파티의 책임과 할당을 정의 하는 RACI (책임 할당 매트릭스)입니다. 높은 수준에서 시작 하 고 계획 및 첫 번째 배포에서 보다 세부적인 수준으로 작업 합니다.
    - 개략적인 솔루션 아키텍처입니다.
    - 배포할 Azure 지역에 대 한 결정입니다. [Azure 지역 목록을](https://azure.microsoft.com/global-infrastructure/regions/)참조 하세요. 각 지역에서 사용할 수 있는 서비스에 대 한 자세한 내용은 [지역별 사용 가능한 제품](https://azure.microsoft.com/global-infrastructure/services/)을 참조 하세요.
    - 온-프레미스에서 Azure로 연결 하기 위한 네트워킹 아키텍처입니다. [Azure에 대 한 가상 데이터 센터 청사진](/azure/architecture/vdc/)의 이해를 시작 합니다.
    - Azure에서 높은 영향의 비즈니스 데이터를 실행 하기 위한 보안 원칙 데이터 보안에 대 한 자세한 내용은 [Azure 보안 설명서](../../../security/index.yml)를 참조 하세요.
2.  기술 디자인 문서입니다. 이 문서에는 다음이 포함 되어야 합니다.
    - 솔루션에 대 한 블록 다이어그램입니다.
    - Azure에서 계산, 저장소 및 네트워킹 구성 요소의 크기를 조정 합니다. Azure Vm의 SAP 크기 조정에 대 한 자세한 내용은 [SAP 
    -  참고 #1928533] ( https://launchpad.support.sap.com/#/notes/1928533) .
    - 비즈니스 연속성 및 재해 복구 아키텍처.
    - OS, DB, 커널 및 SAP 지원 팩 버전에 대 한 자세한 정보를 제공 합니다. SAP NetWeaver 또는 S/4HANA에서 지원 되는 모든 OS 릴리스가 Azure Vm에서 지원 되는 것은 아닙니다. DBMS 릴리스의 경우에도 마찬가지입니다. Sap와 Azure를 지원 하기 위해 SAP 릴리스, DBMS 릴리스 및 OS 릴리스를 정렬 하 고 필요한 경우 다음 원본을 확인 하세요. Sap 및 Microsoft에서 완벽 한 지원을 받으려면 SAP 및 Azure에서 지원 되는 릴리스 조합이 있어야 합니다. 필요한 경우 일부 소프트웨어 구성 요소를 업그레이드 하도록 계획 해야 합니다. 지원 되는 SAP, OS 및 DBMS 소프트웨어에 대 한 자세한 내용은 다음에 설명 되어 있습니다.
        - [SAP support note #1928533](https://launchpad.support.sap.com/#/notes/1928533). 이 정보는 Azure Vm에서 지원 되는 최소 OS 릴리스를 정의 합니다. 또한 대부분의 HANA가 아닌 데이터베이스에 필요한 최소 데이터베이스 릴리스를 정의 합니다. 마지막으로 SAP에서 지 원하는 Azure VM 유형에 대 한 SAP 크기 조정 기능을 제공 합니다.
        - [SAP support note #2015553](https://launchpad.support.sap.com/#/notes/2015553). 이 메모는 Azure storage에 대 한 지원 정책 및 Microsoft에 필요한 지원 관계를 정의 합니다.
        - [SAP support note #2039619](https://launchpad.support.sap.com/#/notes/2039619). 이 정보는 Azure에 대 한 Oracle 지원 매트릭스를 정의 합니다. Oracle은 Azure에서 SAP 워크 로드에 대 한 게스트 운영 체제로 Windows 및 Oracle Linux만 지원 합니다. 이 지원 문은 SAP 인스턴스를 실행 하는 SAP 응용 프로그램 계층에도 적용 됩니다. 그러나 Oracle은 Pacemaker를 통해 Oracle Linux에서 SAP Central Services에 대 한 고가용성을 지원 하지 않습니다. Oracle Linux에서 ASCS에 대 한 고가용성이 필요한 경우 Linux 용 SIOS Protection Suite를 사용 해야 합니다. 자세한 SAP 인증 데이터는 SAP support note [#1662610-Linux 용 SIOS 보호 도구 모음에 대 한 지원 세부 정보](https://launchpad.support.sap.com/#/notes/1662610)를 참조 하세요. Windows의 경우 sap Central Services에 대해 SAP에서 지 원하는 Windows Server 장애 조치 (Failover) 클러스터링 솔루션이 DBMS 계층으로 Oracle과 함께 지원 됩니다.
        - [SAP support note #2235581](https://launchpad.support.sap.com/#/notes/2235581). 이 정보는 다양 한 OS 릴리스에서 SAP HANA에 대 한 지원 매트릭스를 제공 합니다.
        - SAP HANA 지원 되는 Azure Vm 및 [HANA Large 인스턴스](./hana-overview-architecture.md) 는 [SAP 웹 사이트](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)에 나열 됩니다.
        - [SAP 제품 가용성 매트릭스](https://support.sap.com/en/).
        - [SAP support note #2555629-SAP HANA 2.0 동적 계층화 – 하이퍼바이저 및 클라우드 지원](https://launchpad.support.sap.com/#/notes/2555629)
        - [SAP support note #1662610-Linux 용 SIOS 보호 도구 모음에 대 한 지원 세부 정보](https://launchpad.support.sap.com/#/notes/1662610)
        - 다른 SAP 관련 제품에 대 한 SAP 참고 사항     
    - SAP Central Services에 다중 SID 클러스터 구성을 사용 하는 것은 Azure의 Windows, SLES 및 RHEL 게스트 운영 체제에서 지원 됩니다. 이러한 다중 SID 클러스터에 대해 더 많은 ASCS/SCS를 향상 시킬 수 있다는 점에 유의 하세요. 각 게스트 OS 시나리오에 대 한 설명서는 다음 문서에서 찾을 수 있습니다.
        - [Azure에서 Windows Server 장애 조치(Failover) 클러스터링 및 공유 디스크를 사용하는 SAP ASCS/SCS 인스턴스 다중 SID 고가용성](./sap-ascs-ha-multi-sid-wsfc-shared-disk.md)
        - [Azure에서 Windows Server 장애 조치(Failover) 클러스터링 및 파일 공유를 사용하는 SAP ASCS/SCS 인스턴스 다중 SID 고가용성](./sap-ascs-ha-multi-sid-wsfc-file-share.md)
        - [SAP 응용 프로그램에 대 한 SUSE Linux Enterprise Server Azure Vm의 SAP NetWeaver에 대 한 고가용성-다중 SID 가이드](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid)
        - [SAP 응용 프로그램에 대 한 Red Hat Enterprise Linux Azure Vm의 SAP NetWeaver에 대 한 고가용성-다중 SID 가이드](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-multi-sid)
    - 고가용성 및 재해 복구 아키텍처.
        - RTO 및 RPO에 따라 고가용성 및 재해 복구 아키텍처가 어떻게 표시 되어야 하는지 정의 합니다.
        - 영역 내에서 고가용성을 위해 원하는 DBMS가 Azure에서 제공 해야 하는 항목을 확인 합니다. 대부분의 DBMS 패키지는 프로덕션 시스템에 권장 되는 동기 상시 대기의 동기 메서드를 제공 합니다. [Sap 워크 로드 및 관련 문서에 대 한 Azure VIRTUAL MACHINES DBMS 배포에 대 한 고려 사항](./dbms_guide_general.md) 부터 시작 하 여 다른 데이터베이스에 대 한 sap 관련 설명서도 확인 하세요.
           [SQL Server에 대해 설명](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server?view=sql-server-2017)된 것과 같이 DBMS 계층에 대 한 공유 디스크 구성과 함께 Windows Server 장애 조치 (Failover) 클러스터링을 사용 하는 것은 지원 되지 않습니다. 대신 다음과 같은 솔루션을 사용 합니다.
           - [SQL Server Always On](/previous-versions/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-ps-sql-alwayson-availability-groups)
           - [Oracle 데이터 가드](../oracle/configure-oracle-dataguard.md)
           - [HANA 시스템 복제](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html)
        - Azure 지역 간의 재해 복구를 위해 다른 DBMS 공급 업체에서 제공 하는 솔루션을 검토 합니다. 대부분은 비동기 복제 또는 로그 전달을 지원 합니다.
        - SAP 응용 프로그램 계층의 경우, 동일한 Azure 지역 또는 DR 지역에서 프로덕션 배포의 복제본 인 비즈니스 회귀 테스트 시스템을 실행할지 여부를 결정 합니다. 두 번째 경우에는 해당 비즈니스 회귀 시스템을 프로덕션 배포에 대 한 DR 대상으로 지정할 수 있습니다.
        - DR 사이트에 비프로덕션 시스템을 추가 하지 않기로 결정 한 경우 SAP 응용 프로그램 계층을 Azure DR 지역으로 복제 하는 방법으로 Azure Site Recovery를 확인 합니다. 자세한 내용은 [다중 계층 SAP NetWeaver 앱 배포에 대 한 재해 복구 설정](../../../site-recovery/site-recovery-sap.md)을 참조 하세요.
        - [Azure 가용성 영역](../../../availability-zones/az-overview.md)를 사용 하 여 결합 된 HADR 구성을 사용 하기로 결정 한 경우 가용성 영역를 사용할 수 있는 Azure 지역에 익숙해져야 합니다. 또한 두 가용성 영역 간의 네트워크 대기 시간이 증가 하 여 발생할 수 있는 제한 사항을 고려 합니다.  
3.  모든 SAP 인터페이스 (SAP 및 비-sap)의 인벤토리
4.  기초 서비스 디자인. 이 디자인에는 다음 항목이 포함 되어야 합니다.
    - Active Directory 및 DNS 디자인
    - Azure 내의 네트워크 토폴로지와 다른 SAP 시스템에 대 한 할당입니다.
    - Azure에서 인프라 및 SAP 응용 프로그램을 관리 하는 팀을 위한 azure [RBAC (역할 기반 액세스 제어)](../../../role-based-access-control/overview.md) 구조.
    - 리소스 그룹 토폴로지.
    - [태그 지정 전략](../../../azure-resource-manager/management/tag-resources.md#tags-and-billing).
    - Vm 및 기타 인프라 구성 요소 및/또는 논리적 이름의 명명 규칙
5.  Microsoft Professional 또는 프리미어 지원 계약. Microsoft와 함께 프리미어 지원 계약이 있는 경우 Microsoft TAM (기술 계정 관리자)을 확인 하세요. SAP 지원 요구 사항에 대해서는 [sap support note #2015553](https://launchpad.support.sap.com/#/notes/2015553)를 참조 하세요.
6.  구독에 대 한 Azure 구독 및 코어 할당량의 수입니다. 필요 [에 따라 Azure 구독의 할당량을 늘리려면 지원 요청을 여세요](../../../azure-portal/supportability/resource-manager-core-quotas-request.md) .
7.  SAP 데이터를 Azure로 마이그레이션하기 위한 데이터 절감 및 데이터 마이그레이션 계획. Sap NetWeaver 시스템의 경우 SAP는 대량의 데이터 볼륨을 제한 하는 방법에 대 한 지침을 포함 합니다. SAP ERP 시스템의 데이터 관리에 대 한 [sap 가이드](https://wiki.scn.sap.com/wiki/download/attachments/247399467/DVM_%20Guide_7.2.pdf?version=1&modificationDate=1549365516000&api=v2) 를 참조 하세요. 일부 콘텐츠는 일반적으로 NetWeaver 및 S/4HANA 시스템에도 적용 됩니다.
8.  자동화 된 배포 방법. Azure에서 인프라 배포를 자동화 하는 목표는 결정적 방식으로 배포 하 고 결정적 결과를 얻는 것입니다. 많은 고객이 PowerShell 또는 CLI 기반 스크립트를 사용 합니다. 하지만 SAP 용 Azure 인프라를 배포 하 고 SAP 소프트웨어를 설치 하는 데 사용할 수 있는 다양 한 오픈 소스 기술이 있습니다. GitHub에 대 한 예제를 찾을 수 있습니다.
    - [Azure Cloud의 자동화된 SAP 배포](https://github.com/Azure/sap-hana)
    - [SAP HANA 설치](https://github.com/AzureCAT-GSI/SAP-HANA-ARM)
9.  고객, 시스템 통합 업체, Microsoft 및 기타 관련 당사자 간의 정기적인 설계 및 배포 검토 주기를 정의 합니다.


## <a name="pilot-phase-strongly-recommended"></a>파일럿 단계 (강력히 권장)
 
프로젝트를 계획 하 고 준비 하는 동안 파일럿을 실행할 수 있습니다. 파일럿 단계를 사용 하 여 계획 및 준비 단계 중에 수행 된 방법과 디자인을 테스트할 수도 있습니다. 그리고 파일럿 단계를 확장 하 여 개념 증명으로 만들 수 있습니다.

파일럿 배포 중에 전체 HADR 솔루션 및 보안 디자인을 설정 하 고 유효성을 검사 하는 것이 좋습니다. 일부 고객은이 단계에서 확장성 테스트를 수행 합니다. 다른 고객은 SAP 샌드박스 시스템의 배포를 파일럿 단계로 사용 합니다. 파일럿을 위해 Azure로 마이그레이션하려는 시스템을 이미 식별 했다고 가정 합니다.

1. Azure에 대 한 데이터 전송 최적화. 최적 선택은 특정 시나리오에 따라 달라 집니다. Express 경로 회로에 충분 한 대역폭이 있는 경우 [Azure express](https://azure.microsoft.com/services/expressroute/) 경로를 통해 온-프레미스에서 전송 하는 것이 가장 빠릅니다. 다른 시나리오에서는 인터넷을 통해 전송 하는 것이 더 빠릅니다.
2. 데이터 내보내기 및 가져오기를 포함 하는 다른 유형의 SAP platform 마이그레이션을 위해 내보내기 및 가져오기 단계를 테스트 하 고 최적화 합니다. SQL Server 대상 플랫폼인 대량 마이그레이션의 경우 [권장 사항을](https://techcommunity.microsoft.com/t5/Running-SAP-Applications-on-the/SAP-OS-DB-Migration-to-SQL-Server-8211-FAQ-v6-2-April-2017/ba-p/368070)찾을 수 있습니다. 결합 된 릴리스 업그레이드가 필요 하지 않은 경우 마이그레이션 모니터/SWPM을 사용할 수 있습니다. Sap 릴리스 업그레이드와 마이그레이션을 결합할 때 [SAP DMO](https://blogs.sap.com/2013/11/29/database-migration-option-dmo-of-sum-introduction/) 프로세스를 사용할 수 있습니다. 이렇게 하려면 원본 및 대상 DBMS 플랫폼 조합에 대 한 특정 요구 사항을 충족 해야 합니다. 이 프로세스는 [SUM 2.0 SP03의 DMO (데이터베이스 마이그레이션 옵션)](https://launchpad.support.sap.com/#/notes/2631152)에 설명 되어 있습니다.
   1.  원본으로 내보내기, 파일 업로드를 Azure로 내보내기 및 가져오기 성능. 내보내기와 가져오기 간의 중복을 최대화 합니다.
   2.  인프라 크기를 조정 하기 위해 대상 및 대상 플랫폼에서 데이터베이스의 볼륨을 평가 합니다.
   3.  타이밍의 유효성을 검사 하 고 최적화 합니다.
1. 기술 유효성 검사.
   1. VM 유형.
        - SAP 지원 정보, SAP HANA 하드웨어 디렉터리 및 SAP PAM의 리소스를 다시 검토 합니다. Azure에 대해 지원 되는 Vm, 해당 VM 유형에 지원 되는 OS 릴리스, 지원 되는 SAP 및 DBMS 릴리스를 변경 하지 않았는지 확인 합니다.
        - Azure에 배포하는 애플리케이션 및 인프라의 크기가 유효한지 다시 확인합니다. 기존 응용 프로그램을 이동 하는 경우에는 사용 하는 인프라 및 [sap 벤치 마크 웹 페이지](https://www.sap.com/dmc/exp/2018-benchmark-directory/#/sd) 에서 필요한 SAPS를 파생 하 고 [sap support note #1928533](https://launchpad.support.sap.com/#/notes/1928533)에 나열 된 SAPS 번호와 비교할 수 있습니다. 또한 [이 문서는 SAPS 등급을](https://techcommunity.microsoft.com/t5/Running-SAP-Applications-on-the/SAPS-ratings-on-Azure-VMs-8211-where-to-look-and-where-you-can/ba-p/368208) 염두에 두십시오.
        - 계획 단계에서 선택한 VM 유형의 최대 저장소 처리량 및 네트워크 처리량과 관련 하 여 Azure Vm의 크기를 평가 하 고 테스트 합니다. 데이터는 다음 위치에서 찾을 수 있습니다.
           -  [Azure의 Windows 가상 머신에 대 한 크기](../../sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 크기를 조정 하는 데 *최대 캐시 되지 않은 디스크 처리량* 을 고려 하는 것이 중요 합니다.
           -  [Azure의 Linux 가상 머신에 대 한 크기](../../sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 크기를 조정 하는 데 *최대 캐시 되지 않은 디스크 처리량* 을 고려 하는 것이 중요 합니다.
   2. 저장할.
        - [SAP 워크 로드의 문서 Azure Storage 유형을](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide-storage) 확인 하세요.
        - 최소한 SAP 응용 프로그램 계층을 나타내는 Vm에 대해 [Azure 표준 SSD 저장소](../../disks-types.md#standard-ssd) 를 사용 하 고 성능이 중요 하지 않은 dbms를 배포 합니다.
        - 일반적으로 [Azure 표준 HDD 디스크](../../disks-types.md#standard-hdd)를 사용 하지 않는 것이 좋습니다.
        - 원격으로 성능이 중요 한 모든 DBMS Vm에 대해 [Azure Premium Storage](../../disks-types.md#premium-ssd) 를 사용 합니다.
        - [Azure managed disks](https://azure.microsoft.com/services/managed-disks/)를 사용 합니다.
        - M-Series가 있는 DBMS 로그 드라이브에 대해 Azure Write Accelerator를 사용합니다. [쓰기 가속기](../../how-to-enable-write-accelerator.md)에 설명 된 대로 쓰기 가속기 제한 및 사용에 대해 알고 있어야 합니다.
        - 다양 한 DBMS 형식에 대해 일반적인 [SAP 관련 dbms 설명서](./dbms_guide_general.md) 및 일반 문서가 가리키는 dbms 관련 설명서를 확인 합니다.
        - SAP HANA에 대 한 자세한 내용은 [Azure에서 인프라 구성 및 작업 SAP HANA](./hana-vm-operations.md)을 참조 하세요.
        - 디바이스 ID를 사용하여 Azure 데이터 디스크를 Azure Linux VM에 탑재해서는 안됩니다. 대신 UUID(Universally Unique Identifier)를 사용합니다. 예를 들어 Azure 데이터 디스크를 탑재하기 위해 그래픽 도구를 사용하는 경우 주의해야 합니다. /Etc/fstab의 항목을 두 번 확인 하 여 UUID가 디스크를 탑재 하는 데 사용 되는지 확인 합니다. 자세한 내용은 [이 문서](../../linux/attach-disk-portal.md#connect-to-the-linux-vm-to-mount-the-new-disk)에서 찾을 수 있습니다.
   3. 네트워킹
        - 가상 네트워크 인프라와 여러 Azure 가상 네트워크 내에서 또는 여러 Azure 가상 네트워크 내에서 SAP 응용 프로그램의 배포를 테스트 하 고 평가 합니다.
        -  단일 Azure 가상 네트워크 내에서 허브 및 스포크 가상 네트워크 아키텍처 접근 방식이 나 마이크로 분할 방법을 평가 합니다. 평가 기준:
               1. [피어 링 Azure virtual network](../../../virtual-network/virtual-network-peering-overview.md)간의 데이터 교환 비용 비용에 대 한 자세한 내용은 [Virtual Network 가격 책정](https://azure.microsoft.com/pricing/details/virtual-network/)을 참조 하세요.
               2. 네트워크 보안 그룹을 변경 하 여 가상 네트워크 내에서 서브넷을 분리 하는 것과는 반대로 Azure virtual network 간 피어 링의 빠른 연결 끊기의 이점입니다. 이 평가는 가상 네트워크의 서브넷에서 호스트 되는 응용 프로그램 또는 Vm이 보안상 위험할 수 있는 경우에 해당 합니다.
                3. 온-프레미스, 외부 세계 및 Azure에서 빌드한 가상 데이터 센터 간의 네트워크 트래픽에 대 한 중앙 로깅 및 감사
        - SAP 응용 프로그램 계층과 SAP DBMS 계층 간의 데이터 경로를 평가 하 고 테스트 합니다.
            -  Sap 응용 프로그램과 sap NetWeaver, Hybris 또는 S/4HANA를 기반으로 하는 SAP 시스템의 DBMS 계층 간 통신 경로에 [Azure 네트워크 가상 어플라이언스](https://azure.microsoft.com/solutions/network-appliances/) 를 배치 하는 것은 지원 되지 않습니다.
            -  SAP 응용 프로그램 계층 및 SAP DBMS를 피어 링 아닌 다른 Azure virtual network에 배치 하는 것은 지원 되지 않습니다.
            -  [응용 프로그램 보안 그룹 및 네트워크 보안 그룹 규칙](../../../virtual-network/security-overview.md) 을 사용 하 여 sap 응용 프로그램 계층과 sap DBMS 계층 간의 경로를 정의할 수 있습니다.
        - SAP 응용 프로그램 계층 및 SAP DBMS 계층에 사용 되는 Vm에서 [Azure 가속화 된 네트워킹](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) 을 사용 하도록 설정 했는지 확인 합니다. Azure에서 가속화된 네트워킹을 지원하려면 다음과 같은 여러 OS 수준이 필요합니다.
            - Windows Server 2012 R2 이상
            - SUSE Linux 12 SP3 이상
            - RHEL 7.4 이상.
            - Oracle Linux 7.5. RHCKL 커널을 사용 하는 경우 3.10.0-862.13.1 el7가 필요 합니다. Oracle UEK 커널을 사용 하는 경우 릴리스 5가 필요 합니다.
        - Sap 지원 정보 [#500235](https://launchpad.support.sap.com/#/notes/500235) 및 [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E)에 따라 Sap 응용 프로그램 계층 vm과 DBMS vm 간의 네트워크 대기 시간을 테스트 하 고 평가 합니다. [SAP support note #1100926](https://launchpad.support.sap.com/#/notes/1100926/E)의 네트워크 대기 시간 지침에 따라 결과를 평가 합니다. 네트워크 대기 시간은 보통 또는 적절 한 범위에 있어야 합니다. [이 문서](./hana-network-architecture.md#networking-architecture-for-hana-large-instance)에서 설명 하는 것 처럼 VM과 HANA Large Instance 단위 간의 트래픽에는 예외가 적용 됩니다.
        - ILB 배포가 Direct Server Return을 사용 하도록 설정 되었는지 확인 합니다. 이 설정은 DBMS 계층에서 고가용성 구성에 Azure ILBs를 사용 하는 경우 대기 시간을 줄입니다.
        - Azure Load Balancer를 Linux 게스트 운영 체제와 함께 사용 하는 경우 Linux 네트워크 매개 변수 net.tcp. **tcp_timestamps** 가 **0**으로 설정 되어 있는지 확인 합니다. 이 권장 사항은 이전 버전의 [SAP note #2382421](https://launchpad.support.sap.com/#/notes/2382421)권장 사항과 충돌 합니다. 이제 SAP note가 Azure 부하 분산 장치를 사용 하려면이 매개 변수를 **0** 으로 설정 해야 한다는 상태를 업데이트 합니다.
        - 최적의 네트워크 대기 시간을 얻으려면 [Azure 근접 배치 그룹](../../linux/co-location.md) 을 사용 하는 것이 좋습니다. 자세한 내용은 [SAP 응용 프로그램의 최적의 네트워크 대기 시간에 대 한 Azure 근접 배치 그룹](sap-proximity-placement-scenarios.md)을 참조 하세요.
   4. 고가용성 및 재해 복구 배포.
        - 특정 Azure 가용성 영역을 정의 하지 않고 SAP 응용 프로그램 계층을 배포 하는 경우 sap 대화 상자 인스턴스 또는 단일 SAP 시스템의 미들웨어 인스턴스를 실행 하는 모든 Vm이 [가용성 집합](../../windows/manage-availability.md)에 배포 되었는지 확인 합니다.
        - SAP Central Services 및 DBMS에 대 한 고가용성이 필요 하지 않은 경우 SAP 응용 프로그램 계층과 동일한 가용성 집합에 이러한 Vm을 배포할 수 있습니다.
        - 수동 복제를 사용 하 여 고가용성을 위해 SAP Central Services와 DBMS 계층을 보호 하는 경우 SAP Central Services에 대 한 두 노드를 별도의 하나의 가용성 집합과 다른 가용성 집합의 두 DBMS 노드에 저장 합니다.
        - Azure 가용성 영역에 배포 하는 경우 가용성 집합을 사용할 수 없습니다. 하지만 활성 및 수동 중앙 서비스 노드를 서로 다른 두 가용성 영역에 배포 해야 합니다. 대기 시간이 가장 짧은 가용성 영역를 사용 합니다.
          가용성 영역에서 DBMS 및 SAP Central Services 계층에 대 한 Windows 또는 Pacemaker 장애 조치 (failover) 클러스터를 설정 하는 사용 사례에는 [Azure 표준 Load Balancer](../../../load-balancer/load-balancer-standard-availability-zones.md) 를 사용 해야 합니다. 영역 배포에는 [기본 Load Balancer](../../../load-balancer/load-balancer-overview.md) 를 사용할 수 없습니다.
   5. 시간 제한 설정입니다.
        - Sap 인스턴스의 SAP NetWeaver 개발자 추적을 확인 하 여 큐에 넣기 서버와 SAP 작업 프로세스 간에 연결이 중단 되지 않는지 확인 합니다. 이러한 두 레지스트리 매개 변수를 설정 하 여 이러한 연결 중단을 방지할 수 있습니다.
            - HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters\KeepAliveTime = 12만. 자세한 내용은 [KeepAliveTime](/previous-versions/windows/it-pro/windows-2000-server/cc957549(v=technet.10))을 참조 하세요.
            - HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters\KeepAliveInterval = 12만. 자세한 내용은 [KeepAliveInterval](/previous-versions/windows/it-pro/windows-2000-server/cc957548(v=technet.10))를 참조 하세요.
        - 온-프레미스 SAP GUI 인터페이스와 Azure에 배포 된 SAP 응용 프로그램 계층 간의 GUI 시간 제한을 방지 하려면 이러한 매개 변수가 default.pfl 또는 인스턴스 프로필에 설정 되어 있는지 확인 합니다.
            - rdisp/keepalive_timeout = 3600
            - rdisp/keepalive = 20
        - SAP 큐에 넣기 프로세스와 SAP 작업 프로세스 간에 설정 된 연결이 중단 되지 않도록 하려면 **넣지/encni/set_so_keepalive** 매개 변수를 **true**로 설정 해야 합니다. [SAP note #2743751](https://launchpad.support.sap.com/#/notes/2743751)를 참조 하세요.  
        - Windows 장애 조치 (failover) 클러스터 구성을 사용 하는 경우 응답 하지 않는 노드에 대 한 응답 시간이 Azure에 대해 올바르게 설정 되어 있는지 확인 합니다. [장애 조치 (Failover) 클러스터 네트워크 임계값 조정](https://techcommunity.microsoft.com/t5/Failover-Clustering/Tuning-Failover-Cluster-Network-Thresholds/ba-p/371834) 문서는 매개 변수와 장애 조치 (failover) 구분 영향을 줍니다. 클러스터 노드가 동일한 서브넷에 있다고 가정할 경우 다음 매개 변수를 변경 해야 합니다.
            - SameSubNetDelay = 2000
            - SameSubNetThreshold = 15
            - RoutingHistorylength = 30
    6. OS 설정 또는 패치
        - SAP에서 HANA를 실행 하는 방법에 대해서는 다음 notes 및 설명서을 참조 하세요.
            -   [SAP support note #2814271-Azure에서 체크섬 오류로 인해 SAP HANA 백업이 실패 함](https://launchpad.support.sap.com/#/notes/2814271)
            -   [SAP support note #2753418-타이머 대체로 인 한 잠재적 성능 저하](https://launchpad.support.sap.com/#/notes/2753418)
            -   [SAP support note #2791572-Azure에서 Hyper-v에 대 한 VDSO 지원이 누락 되어 성능 저하가 발생 합니다.](https://launchpad.support.sap.com/#/notes/2791572)
            -   [SAP support note #2382421-HANA 및 OS 수준에서 네트워크 구성 최적화](https://launchpad.support.sap.com/#/notes/2382421)
            -   [SAP support note #2694118-Azure의 Red Hat Enterprise Linux HA 추가 기능](https://launchpad.support.sap.com/#/notes/2694118)
            -   [SAP support note #1984787-SUSE LINUX Enterprise Server 12: 설치 참고 사항](https://launchpad.support.sap.com/#/notes/1984787)
            -   [SAP support note #2002167-Red Hat Enterprise Linux 7.x: 설치 및 업그레이드](https://launchpad.support.sap.com/#/notes/0002002167)
            -   [SAP Support Note #2292690 - SAP HANA DB: RHEL 7에 대한 권장 OS 설정](https://launchpad.support.sap.com/#/notes/0002292690)(영문)
            -   [SAP support note #2772999-Red Hat Enterprise Linux 8.x: 설치 및 구성](https://launchpad.support.sap.com/#/notes/2772999)
            -   [SAP support note #2777782-SAP HANA DB: RHEL 8에 권장 되는 OS 설정](https://launchpad.support.sap.com/#/notes/2777782)
            -   [SAP support note #2578899-SUSE Linux Enterprise Server 15: 설치 참고](https://launchpad.support.sap.com/#/notes/2578899)
            -   [SAP support note #2455582-Linux: GCC 6.x로 컴파일된 SAP 응용 프로그램 실행](https://launchpad.support.sap.com/#/notes/0002455582)
            -    [SAP HANA에 대해 인증 된 Azure Vm에서 "하이퍼바이저가 지원 되지 않습니다." 오류가 발생 하 여 SAP support note #2729475-HWCCT이 실패 함](https://launchpad.support.sap.com/#/notes/2729475)
1. 고가용성 및 재해 복구 절차를 테스트 합니다.
   1. Vm (Windows 게스트 운영 체제)을 종료 하거나 운영 체제를 비상 모드 (Linux 게스트 운영 체제)에 배치 하 여 장애 조치 (failover) 상황을 시뮬레이트합니다. 이 단계는 장애 조치 (failover) 구성이 설계 된 대로 작동 하는지 여부를 파악 하는 데 도움이 됩니다.
   1. 장애 조치 (failover)를 실행 하는 데 걸리는 시간을 측정 합니다. 시간이 너무 길면 다음을 고려 하십시오.
        - SUSE Linux의 경우 장애 조치 (failover) 속도를 높이기 위해 Azure Fence 에이전트 대신 SBD 장치를 사용 합니다.
        - SAP HANA의 경우 데이터를 다시 로드 하는 데 너무 오래 걸리는 경우 저장소 대역폭을 추가로 프로 비전 하는 것이 좋습니다.
   3. 백업/복원 순서 및 타이밍을 테스트 하 고 필요한 경우 수정 합니다. 백업 시간이 충분 한지 확인 합니다. 복원 및 시간 복원 작업도 테스트 해야 합니다. RTO가 데이터베이스 또는 VM 복원 프로세스에 의존할 때마다 복원 시간이 RTO Sla 내에 있는지 확인 합니다.
   4. 지역 간 DR 기능 및 아키텍처를 테스트 합니다.
1. 보안 검사.
   1. Azure RBAC (역할 기반 액세스 제어) 아키텍처의 유효성을 테스트 합니다. 목표는 다른 팀의 액세스 및 사용 권한을 분리 하 고 제한 하는 것입니다. 예를 들어 SAP 기본 팀 구성원은 Vm을 배포 하 고 Azure Storage에서 지정 된 Azure virtual network로 디스크를 할당할 수 있어야 합니다. 하지만 SAP 기반 팀은 자신의 가상 네트워크를 만들거나 기존 가상 네트워크의 설정을 변경할 수 없어야 합니다. 네트워크 팀의 구성원은 SAP 응용 프로그램 및 DBMS Vm이 실행 되는 가상 네트워크에 Vm을 배포할 수 없습니다. 이 팀의 구성원이 Vm의 특성을 변경 하거나 Vm 또는 디스크를 삭제할 수도 없어야 합니다.  
   1.  [네트워크 보안 그룹 및 ASC](../../../virtual-network/security-overview.md) 규칙이 예상 대로 작동 하는지 확인 하 고 보호 된 리소스를 보호 합니다.
   1.  암호화해야 하는 모든 리소스가 암호화되는지 확인합니다. 인증서를 백업 하 고, 이러한 인증서를 저장 및 액세스 하 고, 암호화 된 엔터티를 복원 하는 프로세스를 정의 하 고 구현 합니다.
   1.  OS 지원 관점에서 가능한 경우 OS 디스크에 대해 [Azure Disk Encryption](../../../security/fundamentals/azure-disk-encryption-vms-vmss.md) 를 사용 합니다.
   1.  너무 많은 암호화 계층을 사용 하 고 있지 않아야 합니다. 경우에 따라 DBMS 투명한 데이터 암호화 방법 중 하 나와 함께 Azure Disk Encryption를 사용 하 여 동일한 서버에 있는 다른 디스크나 구성 요소를 보호 하는 것이 좋습니다.  예를 들어 SAP DBMS 서버에서 운영 체제 부팅 디스크 (운영 체제에서 ADE를 지 원하는 경우) 및 DBMS 데이터 지 속성 파일에서 사용 하지 않는 데이터 디스크에 대 한 Azure Disk Encryption (ADE)를 사용할 수 있습니다.  예를 들어 DBMS TDE 암호화 키를 보유 하는 디스크에서 ADE를 사용 합니다.
1. 성능 테스트. Sap에서 SAP 추적 및 측정을 기반으로 다음과 같은 비교를 수행 합니다.
   - 해당 하는 경우 상위 10 개의 온라인 보고서를 현재 구현과 비교 합니다.
   - 해당 하는 경우 상위 10 개의 배치 작업을 현재 구현과 비교 합니다.
   - 인터페이스를 통한 데이터 전송을 SAP 시스템으로 비교 합니다. 온-프레미스에서 Azure로 전송 하는 것과 같은 다양 한 위치 간에 전송 되는 것을 알고 있는 인터페이스에 집중 합니다.


## <a name="non-production-phase"></a>비프로덕션 단계 
이 단계에서는 파일럿 또는 개념 증명 (POC) 후 프로덕션이 아닌 SAP 시스템을 Azure에 배포 하기 시작 하는 것으로 가정 합니다. 이 배포에 POC 중에 학습 하 고 경험 한 모든 내용을 통합 합니다. POCs에 대해 나열 된 모든 조건 및 단계는이 배포에도 적용 됩니다.

이 단계에서는 일반적으로 개발 시스템, 유닛 테스트 시스템 및 비즈니스 회귀 테스트 시스템을 Azure에 배포 합니다. 한 SAP 응용 프로그램 줄에 하나 이상의 비프로덕션 시스템을 추가 하는 것이 좋습니다. 이 단계에서 완료 해야 하는 몇 가지 추가 단계는 다음과 같습니다.  

1.  이전 플랫폼에서 Azure로 시스템을 이동 하기 전에 CPU 사용량, 저장소 처리량 및 IOPS 데이터와 같은 리소스 사용량 데이터를 수집 합니다. 특히 DBMS 계층 단위에서이 데이터를 수집 하지만 응용 프로그램 계층 단위 에서도 수집 합니다. 또한 네트워크 및 스토리지 대기 시간을 측정합니다.
2.  시스템의 가용성 사용 시간 패턴을 기록 합니다. 목표는 매일 또는 매월 특정 단계에서 종료할 수 있는 비프로덕션 시스템을 매일 또는 매일 사용 해야 하는지 여부를 파악 하는 것입니다.
3.  Azure에서 Vm에 대 한 고유한 OS 이미지를 만들지 또는 Azure 공유 이미지 갤러리에서 이미지를 사용할지 여부를 테스트 하 고 결정 합니다. 공유 이미지 갤러리의 이미지를 사용 하는 경우에는 OS 공급 업체와의 지원 계약을 반영 하는 이미지를 사용 해야 합니다. 일부 OS 공급 업체의 경우 공유 이미지 갤러리를 사용 하 여 고유한 라이선스 이미지를 가져올 수 있습니다. 다른 OS 이미지의 경우 Azure에서 인용 된 가격에 지원이 포함 됩니다. 사용자 고유의 OS 이미지를 만들려는 경우 다음 문서에서 설명서를 찾을 수 있습니다.
    -   [Azure에 배포 된 Windows VM의 일반화 된 이미지 빌드](../../windows/capture-image-resource.md)
    -   [Azure에 배포 된 Linux VM의 일반화 된 이미지 빌드](../../linux/capture-image.md)
3.  공유 이미지 갤러리에서 SUSE 및 Red Hat Linux 이미지를 사용 하는 경우 공유 이미지 갤러리에서 Linux 공급 업체가 제공 하는 SAP 용 이미지를 사용 해야 합니다.
4.  Microsoft 지원 계약에 대 한 SAP 지원 요구 사항을 충족 하는지 확인 합니다. [SAP support note #2015553](https://launchpad.support.sap.com/#/notes/2015553)를 참조 하세요. HANA Large Instances의 경우 [온 보 딩 요구 사항](./hana-onboarding-requirements.md)을 참조 하세요.
4.  최적의 가동 중지 시간를 선택할 수 있도록 적절 한 사용자가 [계획 된 유지 관리 알림을](https://azure.microsoft.com/blog/a-new-planned-maintenance-experience-for-your-virtual-machines/) 받도록 해야 합니다.
5.  배포에 적용할 수 있는 새로운 기능을 위해 [Channel 9](https://channel9.msdn.com/) 와 같은 채널에서 자주 Azure 프레젠테이션을 확인 합니다.
6.  새 VM Sku 및 새로 지원 되는 OS 및 DBMS 릴리스에 대 한 [지원 참고 #1928533](https://launchpad.support.sap.com/#/notes/1928533)와 같은 AZURE 관련 SAP 참고 사항을 확인 하세요. 새 VM 유형의 가격을 이전 VM 유형의 가격 책정과 비교 하 여 최상의 가격/성능 비율로 Vm을 배포할 수 있습니다.
7.  SAP 지원 정보, SAP HANA 하드웨어 디렉터리 및 SAP PAM을 다시 확인 합니다. Azure에 대해 지원 되는 Vm, 해당 Vm에서 지원 되는 OS 릴리스, 지원 되는 SAP 및 DBMS 릴리스 등이 변경 되지 않았는지 확인 합니다.
8.  [SAP 웹 사이트](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) 에서 Azure의 새로운 HANA 인증 sku를 확인 합니다. 새 Sku의 가격을 사용 하려는 것과 비교할 수 있습니다. 결과적으로 가장 적합 한 가격/성능 비율을 가진 항목을 사용 하도록 변경 해야 합니다.
9.  새 VM 유형을 사용 하도록 배포 스크립트를 조정 하 고 사용 하려는 새로운 Azure 기능을 통합 합니다.
10. 인프라를 배포한 후 sap 지원 정보 [#500235](https://launchpad.support.sap.com/#/notes/500235) 및 [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E)에 따라 Sap 응용 프로그램 계층 vm과 DBMS vm 간의 네트워크 대기 시간을 테스트 하 고 평가 합니다. [SAP support note #1100926](https://launchpad.support.sap.com/#/notes/1100926/E)의 네트워크 대기 시간 지침에 따라 결과를 평가 합니다. 네트워크 대기 시간은 보통 또는 적절 한 범위에 있어야 합니다. [이 문서](./hana-network-architecture.md#networking-architecture-for-hana-large-instance)에서 설명 하는 것 처럼 VM과 HANA Large Instance 단위 간의 트래픽에는 예외가 적용 됩니다. [SAP 워크 로드에 대 한 azure VIRTUAL MACHINES DBMS 배포에 대 한 고려 사항](./dbms_guide_general.md#azure-network-considerations) 및 [azure에 대 한 SAP HANA 인프라 구성 및 작업](./hana-vm-operations.md) 에 대 한 고려 사항에서 언급 한 제한 사항이 배포에 적용 되는지 확인 합니다.
11. [SAP 응용 프로그램을 사용 하 여 최적의 네트워크 대기 시간을 위해 azure 근접 배치 그룹](sap-proximity-placement-scenarios.md)에 설명 된 대로 vm이 올바른 [azure 근접 배치 그룹](../../linux/co-location.md)에 배포 되었는지 확인 합니다.
11. 작업을 적용 하기 전에 개념 증명 단계에 대해 나열 된 다른 모든 검사를 수행 합니다.
12. 워크 로드가 적용 되 면 Azure에서 시스템의 리소스 소비를 기록 합니다. 이 소비량을 이전 플랫폼의 레코드와 비교 합니다. 큰 차이가 있는 경우 향후 배포의 VM 크기를 조정 합니다. 크기, 저장소 및 Vm의 네트워크 대역폭도 줄일 수 있습니다.
    - [Azure에서 Windows 가상 머신에 대한 크기](../../sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
    - [Azure에서 Linux 가상 머신에 대한 크기](../../sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 
13. 시스템 복사 기능 및 프로세스를 시험해 보세요. 목표는 개발 시스템 또는 테스트 시스템을 쉽게 복사할 수 있도록 하는 것입니다. 따라서 프로젝트 팀은 새로운 시스템을 신속 하 게 가져올 수 있습니다. 이러한 작업에는 [SAP LaMa](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+Landscape+Management+%28SAP+LaMa%29+at+a+Glance) 를 사용 하는 것이 좋습니다.
14. 팀의 Azure 역할 기반 액세스, 권한 및 프로세스를 최적화 하 고 h) 하 여 의무를 분리 해야 합니다. 동시에 모든 팀이 Azure 인프라에서 해당 작업을 수행할 수 있는지 확인 합니다.
15. 직원 들이 이러한 작업을 실행할 수 있도록 고가용성 및 재해 복구 절차를 수행 하 고, 테스트 하 고, 문서화 합니다. 단점을 파악 하 고 배포에 통합 하는 새로운 Azure 기능을 조정 합니다.


## <a name="production-preparation-phase"></a>프로덕션 준비 단계 
이 단계에서는 비프로덕션 배포 중에 경험을 얻은 내용을 수집 하 고 향후 프로덕션 배포에 적용 합니다. 또한 현재 호스팅 위치와 Azure 간의 데이터 전송 작업을 준비 해야 합니다.

1.  Azure로 이동 하기 전에 프로덕션 시스템의 필수 SAP 릴리스 업그레이드를 완료 하세요.
1.  프로덕션 시스템을 마이그레이션한 후에 수행 해야 하는 기능 및 비즈니스 테스트의 비즈니스 소유자에 게 동의 합니다.
1.  이러한 테스트가 현재 호스팅 위치의 원본 시스템으로 완료 되었는지 확인 합니다. 시스템이 Azure로 이동 된 후 처음으로 테스트를 수행 하지 않도록 합니다.
1.  프로덕션 시스템을 Azure로 마이그레이션하는 프로세스를 테스트 합니다. 동일한 시간 프레임 동안 모든 프로덕션 시스템을 Azure로 이동 하지 않는 경우 동일한 호스팅 위치에 있어야 하는 프로덕션 시스템 그룹을 빌드 하세요. 데이터 마이그레이션을 테스트 합니다. 몇 가지 일반적인 방법은 다음과 같습니다.
    - SQL Server Always On, HANA 시스템 복제 또는 로그 전달과 함께 백업/복원과 같은 DBMS 메서드를 사용 하 여 Azure에서 데이터베이스 콘텐츠를 시드 및 동기화 합니다.
    - 더 작은 데이터베이스에 대해 백업/복원을 사용 합니다.
    - Sap SWPM에 통합 된 SAP 마이그레이션 모니터를 사용 하 여 다른 유형의 마이그레이션을 수행 합니다.
    - Sap 릴리스 업그레이드와의 마이그레이션을 결합 해야 하는 경우 [SAP DMO](https://blogs.sap.com/2013/11/29/database-migration-option-dmo-of-sum-introduction/) 프로세스를 사용 합니다. 원본 DBMS와 대상 DBMS의 일부 조합이 지원 되지 않는다는 점에 유의 하세요. 특정 SAP 지원 정보에서 다양 한 DMO 릴리스에 대 한 자세한 정보를 찾을 수 있습니다. 예를 들어 [SUM 2.0 SP04의 DMO (데이터베이스 마이그레이션 옵션)](https://launchpad.support.sap.com/#/notes/2644872)를 사용할 수 있습니다.
    - 백업 또는 SAP 내보내기 파일을 이동 해야 하는 경우 데이터 전송 처리량이 인터넷 또는 Express 경로를 통해 더 나은 지 테스트 합니다. 인터넷을 통해 데이터를 이동 하는 경우 향후 프로덕션 시스템을 위해 준비 해야 하는 네트워크 보안 그룹/응용 프로그램 보안 그룹 규칙의 일부를 변경 해야 할 수 있습니다.
1.  이전 플랫폼에서 Azure로 시스템을 이동 하기 전에 리소스 소비 데이터를 수집 합니다. 유용한 데이터에는 CPU 사용량, 저장소 처리량 및 IOPS 데이터가 포함 됩니다. 특히 DBMS 계층 단위에서이 데이터를 수집 하지만 응용 프로그램 계층 단위 에서도 수집 합니다. 또한 네트워크 및 스토리지 대기 시간을 측정합니다.
1.  SAP 지원 정보 및 필요한 OS 설정, SAP HANA 하드웨어 디렉터리 및 SAP PAM을 다시 확인 합니다. Azure에 대해 지원 되는 Vm, 해당 Vm에서 지원 되는 OS 릴리스, 지원 되는 SAP 및 DBMS 릴리스 등이 변경 되지 않았는지 확인 합니다.
1.  VM 유형 및 Azure 기능에 대 한 최신 결정을 고려 하 여 배포 스크립트를 업데이트 합니다.
1.  인프라 및 응용 프로그램을 배포한 후 다음을 확인 합니다.
    - 올바른 특성 및 저장소 크기를 사용 하 여 올바른 VM 형식이 배포 되었습니다.
    - Vm은 적절 하 고 원하는 OS 릴리스와 패치에 있으며 균일 합니다.
    - Vm은 필요에 따라 일관 된 방식으로 확정 됩니다.
    - 올바른 응용 프로그램 릴리스와 패치가 설치 및 배포 되었습니다.
    - Vm은 계획에 따라 Azure 가용성 집합에 배포 되었습니다.
    - Azure Premium Storage는 대기 시간에 민감한 디스크 또는 [99.9%의 단일 VM SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/) 가 필요한 경우에 사용 됩니다.
    - Azure 쓰기 가속기 올바르게 배포 되었습니다.
        - Vm, 저장소 공간 또는 스트라이프 집합이 쓰기 가속기 필요한 디스크 전체에 올바르게 빌드 되었는지 확인 합니다.
        - [Linux에서 소프트웨어 RAID의 구성을](../../linux/configure-raid.md)확인 합니다.
        - [Azure의 Linux vm에서 LVM의 구성을](../../linux/configure-lvm.md)확인 합니다.
    - [Azure managed disks](https://azure.microsoft.com/services/managed-disks/) 는 독점적으로 사용 됩니다.
    - Vm이 올바른 가용성 집합에 배포 되 고 가용성 영역.
    - SAP 응용 프로그램 계층 및 SAP DBMS 계층에 사용 되는 Vm에서 [Azure 가속화 된 네트워킹](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) 을 사용할 수 있습니다.
    - Sap 응용 프로그램과 sap NetWeaver, Hybris 또는 S/4HANA를 기반으로 하는 SAP 시스템의 DBMS 계층 간 통신 경로에는 [Azure 네트워크 가상 어플라이언스](https://azure.microsoft.com/solutions/network-appliances/) 가 없습니다.
    - 응용 프로그램 보안 그룹 및 네트워크 보안 그룹 규칙은 필요에 따라 통신을 적절 하 고 계획 하 고 차단할 수 있습니다.
    - 앞에서 설명한 대로 시간 제한 설정이 올바르게 설정 됩니다.
    - [SAP 응용 프로그램의 최적의 네트워크 대기 시간을 위해 azure 근접 배치 그룹](sap-proximity-placement-scenarios.md)에 설명 된 대로 vm은 올바른 [azure 근접 배치 그룹](../../linux/co-location.md)에 배포 됩니다.
    - Sap 지원 정보 [#500235](https://launchpad.support.sap.com/#/notes/500235) 및 [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E)에 설명 된 대로 Sap 응용 프로그램 계층 vm과 DBMS vm 간의 네트워크 대기 시간이 테스트 되 고 유효성이 검사 됩니다. [SAP support note #1100926](https://launchpad.support.sap.com/#/notes/1100926/E)의 네트워크 대기 시간 지침에 따라 결과를 평가 합니다. 네트워크 대기 시간은 보통 또는 적절 한 범위에 있어야 합니다. [이 문서](./hana-network-architecture.md#networking-architecture-for-hana-large-instance)에서 설명 하는 것 처럼 VM과 HANA Large Instance 단위 간의 트래픽에는 예외가 적용 됩니다.
    - 암호화는 필요한 경우 적절 한 암호화 방법으로 구현 되었습니다.
    - 인터페이스 및 기타 응용 프로그램은 새로 배포 된 인프라를 연결할 수 있습니다.
1.  계획 된 Azure 유지 관리에 대응 하기 위한 플레이 북를 만듭니다. 계획 된 유지 관리를 위해 시스템 및 Vm을 다시 부팅 해야 하는 순서를 결정 합니다.
    

## <a name="go-live-phase"></a>라이브 단계
라이브 단계 중에는 이전 단계에서 개발한 플레이 북을 따라야 합니다. 테스트 하 고 실행 하는 단계를 실행 합니다. 구성 및 프로세스에서 마지막 분의 변경을 허용 하지 않습니다. 또한 다음 단계를 완료 합니다.

1. Azure Portal 모니터링 및 기타 모니터링 도구가 작동하는지 확인합니다. Windows 용 Windows 성능 모니터 (perfmon) 및 Linux 용 특별 행정구를 권장 합니다.
    - CPU 카운터.
        - 평균 CPU 시간, 전체 (모든 Cpu)
        - 평균 CPU 시간, 각 개별 프로세서 (M128 Vm의 128 프로세서)
        - CPU 커널 시간, 각 개별 프로세서
        - CPU 사용자 시간, 각 개별 프로세서
    - 메모리.
        - 사용 가능한 메모리
        - 초당 메모리 페이지
        - 초당 메모리 페이지
    - 디스크를 선택해야 합니다.
        - 개별 디스크당 디스크 읽기 (KBps)
        - 개별 디스크당 디스크 읽기/초
        - 디스크 읽기 (마이크로초/읽기, 개별 디스크 기준)
        - 개별 디스크당 디스크 쓰기 (KBps)
        - 디스크 쓰기/초, 개별 디스크당
        - 디스크 쓰기 (마이크로초/읽기, 개별 디스크 기준)
    - Network.
        - 초당 네트워크 패킷
        - 초당 네트워크 패킷
        - 초당 네트워크 KB
        - 초당 네트워크 KB
1.  데이터 마이그레이션 후 비즈니스 소유자에 게 동의한 모든 유효성 검사 테스트를 수행 합니다. 원본 시스템에 대 한 결과가 있는 경우에만 유효성 검사 테스트 결과를 허용 합니다.
1.  인터페이스가 작동 하는지 여부와 다른 응용 프로그램이 새로 배포 된 프로덕션 시스템과 통신할 수 있는지 여부를 확인 합니다.
1.  SAP transaction STMS를 통해 전송 및 수정 시스템을 확인 합니다.
1.  프로덕션을 위해 시스템이 출시 된 후 데이터베이스 백업을 수행 합니다.
1.  프로덕션을 위해 시스템이 출시 된 후 SAP 응용 프로그램 계층 Vm에 대 한 VM 백업을 수행 합니다.
1.  현재 진행 중인 단계의 일부가 아니지만이 라이브 단계에서 Azure로 이동한 SAP 시스템과 통신 하는 SAP 시스템의 경우 SM51에서 호스트 이름 버퍼를 다시 설정 해야 합니다. 이렇게 하면 Azure로 이동한 응용 프로그램 인스턴스의 이름과 연결 된 이전에 캐시 된 IP 주소가 제거 됩니다.  


## <a name="post-production"></a>프로덕션 후
이 단계에서는 시스템을 모니터링, 운영 및 관리 하는 방법을 알아봅니다. SAP 관점에서 이전 호스팅 위치에서 완료 해야 하는 일반적인 작업이 적용 됩니다. 이러한 Azure 관련 작업을 완료 합니다.

1. 청구 우선 시스템에 대 한 Azure 청구서를 검토 합니다.
2. VM 쪽 및 저장소 쪽에서 가격/성능 효율성을 최적화 합니다.
3. 시스템을 종료할 수 있는 시간을 최적화 합니다.  


## <a name="next-steps"></a>다음 단계
아래 문서를 참조하세요.

- [SAP NetWeaver에 대한 Azure Virtual Machines 계획 및 구현](./planning-guide.md)
- [SAP NetWeaver에 대한 Azure Virtual Machines 배포](./deployment-guide.md)
- [SAP 워크 로드에 대 한 Azure Virtual Machines DBMS 배포에 대 한 고려 사항](./dbms_guide_general.md)