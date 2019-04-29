---
title: Azure Site Recovery로 어떤 워크로드를 보호할 수 있습니까? | Microsoft Docs
description: Azure Site Recovery 서비스에서 재해 복구를 사용하여 보호할 수 있는 워크로드를 설명합니다.
author: rayne-wiselman
ms.service: site-recovery
services: site-recovery
ms.topic: conceptual
ms.date: 12/31/2018
ms.author: raynew
ms.openlocfilehash: 3ad3438f02ead9063a683a39d4ac5823274d55f7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61276140"
---
# <a name="what-workloads-can-you-protect-with-azure-site-recovery"></a>Azure Site Recovery로 어떤 워크로드를 보호할 수 있습니까?

이 문서에서는 [Azure Site Recovery](site-recovery-overview.md) 서비스를 사용한 재해 복구를 위해 보호할 수 있는 워크로드 및 애플리케이션에 대해 설명합니다.



## <a name="overview"></a>개요

조직은 계획되거나 계획되지 않은 중단 중에 워크로드 및 데이터를 안전하고 사용 가능하게 유지하며 가능한 신속히 정상적인 작업 조건으로 복구하는 BCDR(비즈니스 연속성 및 재해 복구) 전략이 필요합니다.

Site Recovery는 BCDR 전략에 기여하는 Azure 서비스로 Site Recovery를 사용하여 애플리케이션 인식 복제를 클라우드 또는 보조 사이트에 배포할 수 있습니다. 앱이 Windows 또는 Linux 기반이거나, VMware 또는 Hyper-V의 어떤 물리적 서버에서 실행되든지 Site Recovery를 사용하여 복제를 오케스트레이션하고 재해 복구 테스트를 수행하며 장애 조치 및 정애 복구를 실행할 수 있습니다.

Site Recovery는 SharePoint, Exchange, Dynamics, SQL Server 및 Active Directory를 비롯한 Microsoft 애플리케이션과 통합됩니다. 또한 Oracle, SAP 및 Red Hat을 비롯한 주요 공급 업체와 긴밀히 협력하고 있습니다. 앱 단위로 복제 솔루션을 사용자 지정할 수 있습니다.

## <a name="why-use-site-recovery-for-application-replication"></a>애플리케이션 복제에 Site Recovery를 사용해야 하는 이유

사이트 복구는 다음과 같이 애플리케이션 수준 보호 및 복구에 기여합니다.

* 지원되는 컴퓨터에서 실행 중인 모든 워크로드에 복제를 제공하는 앱 중립성.
* 가장 중요한 비즈니스 앱의 요구를 충족하는 30초의 낮은 RPO를 통한 근거리 동기 복제.
* 단일 또는 다중 계층 애플리케이션에 대한 앱 일관성 스냅숏.
* AD 복제, SQL AlwaysOn, Exchange 데이터베이스 가용성 그룹(DAG) 및 Oracle Data Guard를 포함하는 다른 애플리케이션 수준 복제 기술을 사용하여 SQL Server AlwaysOn, 파트너쉽과 통합합니다.
* 클릭 한 번으로 전체 애플리케이션 스택을 복구하고 외부 스크립트 또는 수동 작업을 계획에 포함할 수 있는 유연한 복구.
* Site Recovery 및 Azure의 고급 네트워크 관리는 낮은 RTO 네트워크 전환을 위해 IP 주소 예약, 부하 분산 장치 구성 및 Azure Traffic Manager와 통합을 포함하는 앱 네트워크 요구 사항을 단순화합니다.
* 다운로드하고 복구 계획과 통합할 수 있는 프로덕션 준비된 애플리케이션 특정 스크립트를 제공하는 다양한 자동화 라이브러리입니다.

## <a name="workload-summary"></a>워크로드 요약
Site Recovery는 지원되는 컴퓨터에서 실행 중인 모든 앱을 복제할 수 있습니다. 또한 추가 앱별 테스트를 수행하는 제품 팀과 협력하고 있습니다.

| **워크로드** |**Azure VM을 Azure에 복제** |**Hyper-V VM을 보조 사이트로 복제** | **Hyper-V VM을 Azure에 복제** | **VMware VM을 보조 사이트에 복제** | **VMware VM을 Azure에 복제** |
| --- | --- | --- | --- | --- |---|
| Active Directory, DNS |Y |Y |Y |Y |Y|
| 웹앱(IIS, SQL) |Y |Y |Y |Y |Y|
| System Center Operations Manager |Y |Y |Y |Y |Y|
| SharePoint |Y |Y |Y |Y |Y|
| SAP<br/><br/>비 클러스터에 대해 Azure에 SAP 사이트 복제 |예(Microsoft에서 테스트) |예(Microsoft에서 테스트) |예(Microsoft에서 테스트) |예(Microsoft에서 테스트) |예(Microsoft에서 테스트)|
| Exchange(비 DAG) |Y |Y |Y |Y |Y|
| 원격 데스크톱/VDI |Y |Y |Y |Y |Y|
| Linux(운영 체제 및 앱) |예(Microsoft에서 테스트) |예(Microsoft에서 테스트) |예(Microsoft에서 테스트) |예(Microsoft에서 테스트) |예(Microsoft에서 테스트)|
| Dynamics AX |Y |Y |Y |Y |Y|
| Windows 파일 서버 |Y |Y |Y |Y |Y|
| Citrix XenApp 및 XenDesktop |Y|N/A |Y |N/A |Y |

## <a name="replicate-active-directory-and-dns"></a>Active Directory 및 DNS 복제
Active Directory 및 DNS 인프라는 대부분의 엔터프라이즈 앱에 필수적입니다. 재해 복구 중에 워크로드 및 앱을 복구하기 전에 이러한 인프라 구성 요소를 보호하고 복구해야 합니다.

사이트 복구를 사용하여 Active Directory 및 DNS에 대한 완전히 자동화된 재해 복구 계획을 만들 수 있습니다. 예를 들어 기본 사이트에서 보조 사이트로 SharePoint 및 SAP를 장애 조치하려면 먼저 Active Directory를 장애 조치하는 복구 계획을 설정한 후 Active Directory에 의존하는 다른 앱을 장애 조치하는 추가 앱별 복구 계획을 설정할 수 있습니다.

Active Directory 및 DNS 보호에 대하여 [자세히 알아봅니다](site-recovery-active-directory.md).

## <a name="protect-sql-server"></a>SQL Server 보호
SQL Server는 온-프레미스 데이터 센터에서 많은 비즈니스 앱에 대한 데이터 서비스의 데이터 서비스 기반을 제공합니다.  Site Recovery와 SQL Server HA/DR 기술을 함께 사용하여 SQL Server를 사용하는 다중 계층 엔터프라이즈 앱을 보호할 수 있습니다. 사이트 복구는 다음을 제공합니다.

* SQL Server에 대해 간단하고 비용 효율적인 재해 복구 솔루션. SQL Server 독립 실행형 서버 및 클러스터의 여러 버전 및 에디션을 Azure 또는 보조 사이트로 복제합니다.  
* SQL AlwaysOn 가용성 그룹 통합은 Azure Site Recovery 복구 계획을 통해 장애 조치 및 장애 복구를 관리합니다.
* SQL Server 데이터베이스를 포함한 애플리케이션의 모든 계층에 대한 종단간 복구 계획.
* Azure에서 더 큰 IaaS 가상 머신으로 "폭발적으로 증가"하여 Site Recovery와 함께 최대 부하에 대한 SQL Server의 규모 증가.
* SQL Server 재해 복구의 간편한 테스트. 테스트 장애 조치를 실행하여 프로덕션 환경에 영향을 주지 않고 데이터를 분석하고 규정 준수 검사를 실행할 수 있습니다.

[자세히 알아봅니다](site-recovery-sql.md) .

## <a name="protect-sharepoint"></a>SharePoint 보호
Azure Site Recovery를 사용하면 다음과 같이 SharePoint 배포를 보호할 수 있습니다.

* 재해 복구를 위한 대기 팜에 대한 필요성 및 관련된 인프라 비용을 제거합니다. 사이트 복구를 사용하여 전체 팜(웹, 앱 및 데이터베이스 계층)을 Azure 또는 보조 사이트로 복제합니다.
* 애플리케이션 배포 및 관리를 간소화합니다. 기본 사이트에 배포된 업데이트가 자동으로 복제되므로 보조 사이트에서 팜의 장애 조치 및 복구 후 사용할 수 있습니다. 또한 대기 팜을 최신으로 유지하는 관리 복잡성 및 비용을 줄여줍니다.
* 테스트 및 디버깅에 프로덕션 환경과 유사한 복사본 주문형 복사본 환경을 만들어 SharePoint 애플리케이션 개발 및 테스트를 용이하게 합니다.
* 사이트 복구를 사용하여 SharePoint 배포를 Azure에 마이그레이션하여 전환을 클라우드로 단순화합니다.

[자세히 알아봅니다](site-recovery-sharepoint.md) .

## <a name="protect-dynamics-ax"></a>Dynamics AX 보호
Azure Site Recovery를 사용하면 다음과 같이 Dynamics AX ERP 솔루션을 보호하도록 합니다.

* Azure 또는 보조 사이트에 대한 전체 Dynamics AX 환경(웹 및 AOS 계층, 데이터베이스 계층, SharePoint)의 복제를 오케스트레이션합니다.
* Dynamics AX 배포의 클라우드(Azure)로 마이그레이션을 간소화합니다.
* 테스트 및 디버깅에 프로덕션 환경과 유사한 복사본 주문형을 만들어 Dynamics AX 애플리케이션 개발 및 테스트를 용이하게 합니다.

[자세히 알아봅니다](site-recovery-dynamicsax.md) .

## <a name="protect-rds"></a>RDS 보호
원격 데스크톱 서비스(RDS)는 사용자가 원하는 위치에서 사용할 수 있도록 가상 데스크톱 인프라(VDI), 세션 기반의 데스크톱 및 애플리케이션을 활성화합니다. Azure Site Recovery를 통해 다음을 수행할 수 있습니다.

* 관리되거나 관리되지 않은 풀링된 가상 데스크톱을 보조 사이트에 복제하고 원격 애플리케이션 및 세션을 보조 사이트 또는 Azure에 복제합니다.

* 복제할 수 있는 항목은 다음과 같습니다.

| **RDS** |**Azure VM을 Azure에 복제** | **Hyper-V VM을 보조 사이트로 복제** | **Hyper-V VM을 Azure에 복제** | **VMware VM을 보조 사이트에 복제** | **VMware VM을 Azure에 복제** | **물리적 서버를 보조 사이트에 복제** | **물리적 서버를 Azure에 복제** |
|---| --- | --- | --- | --- | --- | --- | --- |
| **풀링된 가상 데스크톱(관리되지 않음)** |아닙니다.|사용자 계정 컨트롤 |no |사용자 계정 컨트롤 |no |사용자 계정 컨트롤 |아닙니다. |
| **풀링된 가상 데스크톱(관리됨/UPD 없음)** |아닙니다.|사용자 계정 컨트롤 |no |사용자 계정 컨트롤 |no |사용자 계정 컨트롤 |아닙니다. |
| **원격 애플리케이션 및 데스크톱 세션(UPD 없음)** |예|예 |예 |예 |예 |예 |예 |

[Azure Site Recovery를 사용하여 RDS에 대한 재해 복구를 설정합니다](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-disaster-recovery-with-azure).

[자세히 알아봅니다](https://gallery.technet.microsoft.com/Remote-Desktop-DR-Solution-bdf6ddcb) .

## <a name="protect-exchange"></a>Exchange 보호
Site Recovery를 사용하면 다음과 같이 Exchange를 보호할 수 있습니다.

* 단일 또는 독립 실행형 서버와 같은 소규모 Exchange 배포의 경우 Site Recovery가 Azure 또는 보조 사이트로 복제하고 장애 조치(failover)할 수 있습니다.
* 대규모 배포의 경우 Site Recovery가 Exchange DAGS와 통합됩니다.
* Exchange DAG는 엔터프라이즈에서 Exchange 재해 복구를 위한 권장된 솔루션입니다.  Site Recovery의 복구 계획은 DAG를 포함하여 사이트에 걸쳐 DAG 장애 조치를 오케스트레이션할 수 있습니다.

[자세히 알아봅니다](https://gallery.technet.microsoft.com/Exchange-DR-Solution-using-11a7dcb6) .

## <a name="protect-sap"></a>SAP 보호
Site Recovery를 사용하여 다음과 같이 SAP 배포를 보호합니다.

* 구성 요소를 Azure에 복사하여 온-프레미스로 실행 중인 SAP NetWeaver 및 비-NetWeaver 프로덕션 애플리케이션 보호를 활성화합니다.
* 구성 요소를 다른 Azure 데이터 센터에 복사하여 Azure를 실행 중인 SAP NetWeaver 및 비-NetWeaver 프로덕션 애플리케이션 보호를 활성화합니다.
* Site Recovery를 사용하여 클라우드 마이그레이션을 간소화하여 Azure에 SAP 배포를 마이그레이션합니다.
* SAP 애플리케이션 테스팅을 위해 온디맨드로 프로덕션 클론을 만들어 SAP 프로젝트 업그레이드, 테스트 및 프로토타입 생성을 단순화합니다.

[자세히 알아봅니다](site-recovery-sap.md) .

## <a name="protect-iis"></a>IIS 보호
Site Recovery를 사용하여 다음과 같이 IIS 배포를 보호합니다.

Azure Site Recovery는 환경에서 중요한 구성 요소를 콜드 원격 사이트 또는 Microsoft Azure와 같은 공용 클라우드에 복제하여 재해 복구를 제공합니다. 웹 서버와 데이터베이스를 사용하는 가상 머신이 복구 사이트에 복제되므로 구성 파일 또는 인증서를 별도로 백업할 필요가 없습니다. 변경된 사후 장애 조치인 환경 변수에 따라 달라지는 애플리케이션 매핑 및 바인딩은 재해 복구 계획에 통합되는 스크립트를 통해 업데이트될 수 있습니다. 가상 머신은 장애 조치(failover) 시에만 복구 사이트에서 사용됩니다. 이 뿐만 아니라 Azure Site Recovery를 통해 다음과 같은 기능을 제공하여 종단 간 장애 조치를 오케스트레이션할 수 있습니다.

-   다양한 계층에서 가상 머신의 종료 및 시작 순서를 지정합니다.
-   가상 머신이 시작된 후에 애플리케이션 종속성 및 바인딩을 업데이트할 수 있는 스크립트를 추가합니다. 스크립트는 복구 사이트를 가리키기 위해 DNS 서버를 업데이트하는 데 사용할 수 있습니다.
-   기본 및 복구 네트워크를 매핑하여 가상 머신 사전 장애 조치에 IP 주소를 할당하고 사후 장애 조치가 업데이트되지 않아도 되는 스크립트를 사용합니다.
-   웹 서버의 여러 웹 애플리케이션에서 한 번의 클릭으로 장애 조치하는 기능은 재해가 발생한 경우 혼동을 일으키는 범위를 제거합니다.
-   DR 드릴의 격리된 환경에서 복구 계획을 테스트하는 기능입니다.

IIS 웹 팜을 보호하는 방법에 대한 [자세한 내용](https://aka.ms/asr-iis)

## <a name="protect-citrix-xenapp-and-xendesktop"></a>Citrix XenApp 및 XenDesktop 보호
Site Recovery를 사용하여 음과 같이 Citrix XenApp와 XenDesktop 배포를 보호합니다.

* 여러 배포 계층(AD DNS 서버, SQL 데이터베이스 서버, Citrix Delivery Controller, StoreFront 서버, XenApp Master(VDA), Citrix XenApp License Server)을 Azure에 복제하여 Citrix XenApp 및 XenDesktop 배포를 보호합니다.
* Citrix XenApp 및 XenDesktop 배포를 Azure로 마이그레이션하기 위해 Site Recovery를 사용하여 클라우드 마이그레이션을 간소화합니다.
* 테스트 및 디버깅을 위해 프로덕션 환경과 유사한 주문형 복사본을 만들어 Citrix XenApp/XenDesktop 테스트를 간소화합니다.
* 이 솔루션은 Windows Server 운영 체제 가상 데스크톱에만 적용할 수 있으며 Azure의 라이선스에 대해 아직 지원되지 않는 클라이언트 가상 데스크톱에는 적용할 수 없습니다.
Azure의 클라이언트/서버 데스크톱용 라이선스에 대해 [자세히 알아보세요](https://azure.microsoft.com/pricing/licensing-faq/).

Citrix XenApp 및 XenDesktop 배포 보호에 대해 [자세히 알아보세요](site-recovery-citrix-xenapp-and-xendesktop.md). 또는 세부 사항을 동일하게 설명하는 [Citrix의 백서](https://aka.ms/citrix-xenapp-xendesktop-with-asr)를 참조하세요.

## <a name="next-steps"></a>다음 단계

Azure VM 복제를 사용하여 [시작](azure-to-azure-quickstart.md)합니다.
