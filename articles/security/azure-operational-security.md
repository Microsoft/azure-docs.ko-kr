---
title: Azure 운영 보안 | Microsoft Docs
description: Microsoft Azure Monitor 로그, 해당 서비스 및 작동 하는 방법에 대해 알아봅니다.
services: security
documentationcenter: na
author: UnifyCloud
manager: barbkess
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: TomSh
ms.openlocfilehash: ab5b50433b85416ff471546171998e992293b0ea
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60586991"
---
# <a name="azure-operational-security"></a>Azure 운영 보안
## <a name="introduction"></a>소개

### <a name="overview"></a>개요
보안은 클라우드의 작업이며 Azure 보안에 대한 정확하고 시기 적절한 정보를 찾는 것이 얼마나 중요한지 알고 있습니다. 애플리케이션 및 서비스에 Azure를 사용하는 가장 좋은 이유 중 하나는 제공하는 광범위한 보안 도구 및 기능을 활용한다는 점입니다. 이러한 도구 및 기능을 통해 안전한 Azure 플랫폼에서 안전한 솔루션을 만들 수 있습니다. Microsoft Azure는 고객 데이터의 기밀성, 무결성 및 가용성을 제공해야 하는 한편 투명한 책임도 가능하게 해야 합니다.

고객 관점과 Microsoft 운영 관점 모두에서 Microsoft Azure에서 구현된 일련의 보안 제어를 보다 잘 이해할 수 있도록 Microsoft Azure에서 제공되는 운영 보안을 포괄적으로 살펴볼 수 있는 "Azure 운영 보안" 백서가 작성되었습니다.

### <a name="azure-platform"></a>Azure 플랫폼
Azure는 다양한 운영 체제, 프로그래밍 언어, 프레임워크, 도구, 데이터베이스 및 디바이스를 지원하는 공용 클라우드 서비스 플랫폼입니다. Docker 통합으로 Linux 컨테이너를 실행할 수 있습니다. JavaScript, Python, .NET, PHP, Java 및 Node.js를 사용하여 앱을 빌드할 수 있습니다. iOS, Android 및 Windows 디바이스용 백 엔드를 빌드할 수 있습니다. Azure 클라우드 서비스는 수백만의 개발자와 IT 전문가가 이미 믿고 사용하고 있는 동일한 수준의 기술을 지원합니다.

IT 자산을 만들거나 공용 클라우드 서비스 공급자로 마이그레이션하는 경우 조직에서 제공하는 서비스와 제어를 통해 애플리케이션과 데이터를 보호할 수 있는 해당 조직의 능력에 의존하여 클라우드 기반 자산의 보안을 관리합니다.

Azure의 인프라는 수백만 고객을 동시에 호스팅하는 애플리케이션에 맞게 시설로부터 설계되었으며, 비즈니스에서 보안 요구 사항을 충족할 수 있는 신뢰할 수 있는 기반을 제공합니다. 또한 Azure는 다양하게 구성할 수 있는 보안 옵션과 이를 제어하는 기능을 제공하므로 보안을 사용자 지정하여 조직의 고유한 배포 요구 사항을 충족할 수 있습니다. 이 문서는 Azure 보안 기능이 이러한 요구 사항을 충족하는 방법을 이해하는 데 도움이 됩니다.

### <a name="abstract"></a>요약
Azure 운영 보안은 사용자가 Microsoft Azure에서 자신의 데이터, 애플리케이션 및 기타 자산을 보호할 수 있는 서비스, 제어 및 기능을 나타냅니다. Azure 운영 보안은 Microsoft SDL(Security Development Lifecycle), Microsoft 보안 대응 센터 프로그램 및 사이버 보안 위협 상황에 대한 심층 인식을 포함하여 Microsoft 고유의 다양한 기능을 통해 얻은 지식을 통합한 프레임워크를 기반으로 합니다.

이 백서는 Microsoft Azure 클라우드 플랫폼 내에서 Azure 운영 보안에 대한 Microsoft 접근 방식을 간략히 설명하고 다음 서비스를 다룹니다.
1.  [Azure Monitor](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview)

2.  [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)

3.  [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview)

4.  [Azure Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview)

5.  [Azure Storage 분석](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics)

6.  [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis)


## <a name="microsoft-azure-monitor-logs"></a>Microsoft Azure Monitor 로그

Microsoft Azure Monitor 로그는 하이브리드 클라우드를 위한 IT 관리 솔루션입니다. 단독으로 사용 하거나 Azure Monitor 로그는 기존 System Center 배포 확장을 사용 하면 최대 유연성 및 제어 인프라의 클라우드 기반 관리에 대 한 합니다.

![Azure Monitor 로그](./media/azure-operational-security/azure-operational-security-fig1.png)

Azure Monitor 로그를 사용 하 여 모든 클라우드, 온-프레미스, Azure, AWS, Windows Server, Linux, VMware 및 OpenStack을 포함 하 여 경쟁 솔루션 보다 저렴 한 비용의 모든 인스턴스를 관리할 수 있습니다. 클라우드 우선 세계 용으로 작성 된, Azure Monitor 로그는 새로운 비즈니스 과제를 충족 하기 위해 새 워크 로드, 응용 프로그램 및 클라우드 환경을 수용할 수 있는 가장 빠르고 비용 효율적인 방법인 엔터프라이즈를 관리 하는 새로운 방법을 제공 합니다.

### <a name="azure-monitor-services"></a>Azure Monitor 서비스

Azure Monitor 로그의 핵심 기능은 Azure에서 실행 되는 서비스 집합으로 제공 됩니다. 각 서비스는 고유의 관리 기능을 제공하며, 사용자는 서비스를 결합하여 다양한 관리 시나리오를 해결할 수 있습니다.

| 서비스  | 설명|
| :------------- | :-------------|
| Azure Monitor 로그 | 실제 컴퓨터와 가상 머신을 포함하여 다양한 리소스의 가용성 및 성능을 모니터링하고 분석합니다. |
|Automation | 수동 프로세스를 자동화하고 실제 컴퓨터와 가상 머신에 대한 구성을 적용합니다. |
| Backup | 중요한 데이터를 백업하고 복원합니다. |
| Site Recovery | 중요한 애플리케이션에 고가용성을 제공합니다. |

### <a name="azure-monitor-logs"></a>Azure Monitor 로그

[Azure Monitor 로그](https://azure.microsoft.com/documentation/services/log-analytics) 를 중앙 리포지토리로 관리 되는 리소스의 데이터를 수집 하 여 모니터링 서비스를 제공 합니다. 이 데이터에는 이벤트, 성능 데이터 또는 API를 통해 제공되는 사용자 지정 데이터가 포함될 수 있습니다. 수집된 데이터는 경고, 분석 및 내보내기에 사용할 수 있습니다.


이 방법을 사용하면 다양한 원본의 데이터를 통합할 수 있으므로 Azure 서비스의 데이터를 기존 온-프레미스 환경과 결합할 수 있습니다. 또한 모든 종류의 데이터에 모든 작업을 제공할 수 있도록 데이터 컬렉션을 해당 데이터에서 수행된 작업과 명확하게 구분합니다.


![Azure Monitor 로그](./media/azure-operational-security/azure-operational-security-fig2.png)

Azure Monitor 서비스 다음 메서드를 사용 하 여 클라우드 기반 데이터를 안전 하 게 관리 합니다.
-   데이터 분리
-   데이터 보존
-   물리적 보안
-   인시던트 관리
-   규정 준수
-   보안 표준 인증

### <a name="azure-backup"></a>Azure Backup

[Azure Backup](https://azure.microsoft.com/documentation/services/backup) 백업 및 복원 서비스 및 Azure Monitor 제품군 제품 및 서비스의 일부인 데이터를 제공 합니다.
자본 투자 없이 최소한의 운영 비용으로 애플리케이션 데이터를 보호하고 수년 간 보관합니다. SQL Server, SharePoint와 같은 애플리케이션 워크로드 외에도 물리적 및 가상 Windows 서버의 데이터를 백업할 수 있습니다. 또한 중복 및 장기 저장을 위해 [System Center DPM(Data Protection Manager)](https://en.wikipedia.org/wiki/System_Center_Data_Protection_Manager)에서 보호된 데이터를 Azure로 복제하는 데 사용할 수 있습니다.


Azure Backup의 보호 데이터는 특정 지리적 지역에 있는 백업 저장소에 저장됩니다. 데이터는 동일 지역 내에서 복제되며, 보관 유형에 따라 복원력을 높이기 위해 다른 지역에 복제될 수 있습니다.

### <a name="management-solutions"></a>관리 솔루션
[Azure Monitor](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started) 는 Microsoft의 클라우드 기반 IT 관리 솔루션 보호할 온-프레미스 및 클라우드 인프라 및 관리할 수 있도록 합니다.


[관리 솔루션](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-solutions) 미리 패키지 된 집합이 하나 이상의 Azure Monitor 서비스를 사용 하 여 특정 관리 시나리오를 구현 하는 논리입니다. 다른 솔루션은 Microsoft에서 파트너를 쉽게 추가할 수 있습니다 Azure Monitor에 대 한 투자의 가치를 높일 Azure 구독에 사용할 수 있습니다. 파트너는 자체 솔루션을 만들어 애플리케이션과 서비스를 지원하고, Azure Marketplace 또는 빠른 시작 템플릿을 통해 사용자에게 제공할 수 있습니다.


![관리 솔루션](./media/azure-operational-security/azure-operational-security-fig4.png)

여러 서비스를 사용하여 추가 기능을 제공하는 솔루션의 좋은 예로 [업데이트 관리 솔루션](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-update-management)이 있습니다. 이 솔루션에서는 합니다 [Azure Monitor 로그](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) 에이전트에 대 한 정보를 수집 하는 Windows 및 Linux 용 필요한 각 에이전트에 업데이트 합니다. 이 데이터는 포함 된 대시보드를 사용 하 여 분석할 수 있는 Azure Monitor 로그 저장소에 씁니다.

배포를 만들 때 [Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro)의 runbook을 사용하여 필요한 업데이트를 설치합니다. 이 전체 프로세스를 포털에서 관리할 수 있으며 기본적인 세부 사항에 대해 걱정할 필요가 없습니다.

## <a name="azure-security-center"></a>Azure Security Center

Azure Security Center는 Azure 리소스를 보호하는 데 도움이 됩니다. Azure 구독에서 통합된 보안 모니터링 및 정책 관리를 제공합니다. 서비스 내에서 Azure 구독에 대한 정책뿐만 아니라 [리소스 그룹](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups)에 대한 정책도 정의할 수 있으므로 더욱 세분화할 수 있습니다.

### <a name="security-policies-and-recommendations"></a>보안 정책 및 권장 사항

보안 정책은 지정된 구독 또는 리소스 그룹 내에서 리소스에 대해 권장되는 제어 집합을 정의합니다.

Security Center에서 회사의 보안 요구 사항 및 애플리케이션 유형 또는 데이터 민감도에 따라 정책을 정의합니다.

![보안 정책 및 권장 사항](./media/azure-operational-security/azure-operational-security-fig5.png)


구독 수준에서 사용할 수 있는 정책은 다이어그램의 오른쪽과 같이 구독 내 모든 리소스 그룹에 자동으로 전파됩니다.


### <a name="data-collection"></a>데이터 수집

Security Center는 해당 보안 상태를 평가하고 보안 권장 사항을 제공하며 위협에 경고하기 위해 VM(가상 머신)에서 데이터를 수집합니다. Security Center에 처음 액세스하는 경우 구독의 모든 VM에서 데이터 수집이 활성화됩니다. 데이터 수집을 사용하는 것이 좋지만 Security Center 정책에서 데이터 수집 해제를 설정할 수 있습니다.

### <a name="data-sources"></a>데이터 원본

- Azure Security Center에서는 다음 소스의 데이터를 분석하여 보안 상태에 대한 가시성을 제공하고, 취약점을 식별하고, 완화 방법을 권장하고, 활성 위협을 감지합니다.

-   Azure 서비스: 해당 서비스의 리소스 공급자와 통신하여 배포한 Azure 서비스의 구성에 대한 정보를 사용합니다.

- 네트워크 트래픽: 원본/대상 IP/포트, 패킷 크기 및 네트워크 프로토콜과 같은 Microsoft의 인프라에서 샘플링된 네트워크 트래픽 메타데이터를 사용합니다.

-   파트너 솔루션: 방화벽 및 맬웨어 방지 솔루션과 같은 통합된 파트너 솔루션의 보안 경고를 사용합니다.

-   사용자의 Virtual Machines: Windows 이벤트 및 감사 로그, IIS 로그, syslog 메시지 및 가상 머신의 크래시 덤프 파일과 같은 보안 이벤트에 대한 구성 정보 및 정보를 사용합니다.

### <a name="data-protection"></a>데이터 보호

고객이 위협을 방지, 감지 및 대응하는 데 도움을 주기 위해 Azure Security Center에서는 구성 정보, 메타데이터, 이벤트 로그, 크래시 덤프 파일 등을 포함한 보안 관련 데이터를 수집하고 처리합니다. Microsoft는 코딩부터 서비스에 이르기까지 엄격한 규정 준수 및 보안 지침을 따릅니다.

-   **데이터 분리**: 데이터는 서비스 전체에서 각 구성 요소에 논리적으로 별도로 유지됩니다. 모든 데이터에는 조직별로 태그가 지정됩니다. 이 태그는 데이터 수명 주기 동안 유지되며 서비스의 각 계층에서 적용됩니다.

-   **데이터 액세스**: Microsoft 직원은 보안 권장 사항을 제공하고 잠재적 보안 위협을 조사하기 위해 Azure 서비스에서 수집하거나 분석한 정보에 액세스할 수 있습니다. 이러한 정보에는 크래시 덤프 파일, 프로세스 생성 이벤트, VM 디스크 스냅숏 및 아티팩트 등이 포함되며, 가상 머신의 고객 데이터 또는 개인 데이터가 의도치 않게 포함될 수도 있습니다. Microsoft는 [Microsoft Online Services 약관 및 개인정보처리방침](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31)을 준수하며, 고객 데이터를 사용하지 않거나 광고 또는 이와 유사한 상업적 목적으로 정보를 유출하지 않습니다.

-   **데이터 사용**: Microsoft는 여러 테넌트에 발생하는 패턴 및 위협 인텔리전스를 사용하여 방지 및 검색 기능을 향상시킵니다. [개인정보처리방침](https://www.microsoft.com/en-us/privacystatement/OnlineServices/)에 설명된 개인정보처리방침 약정에 따라 수행합니다.

### <a name="data-location"></a>데이터 위치

Azure Security Center는 크래시 덤프 파일의 임시 복사본을 수집하고 이용 시도 및 손상 성공의 증거를 찾기 위해 분석합니다. Azure Security Center는 작업 영역과 동일한 지역 내에서 이 분석을 수행하고 분석이 완료되면 임시 복사본을 삭제합니다. 컴퓨터 아티팩트는 VM과 동일한 지역에 중앙 집중식으로 저장됩니다.

-   **스토리지 계정**: 스토리지 계정은 가상 머신이 실행되는 각 지역에 대해 지정됩니다. 그러면 데이터를 수집하는 가상 머신과 동일한 지역에 데이터를 저장할 수 있습니다.

-   **Azure Security Center Storage**: 보안 경고(파트너 경고 포함), 권장 사항 및 보안 상태에 대한 정보는 현재 미국에 있는 Azure Security Center Storage에 중앙 집중식으로 저장됩니다. 이 정보는 필요한 가상 머신에서 수집된 관련된 구성 정보 및 보안 이벤트를 포함하여 보안 경고, 권장 사항 또는 보안 상태를 제공할 수 있습니다.


## <a name="azure-monitor"></a>Azure Monitor

합니다 [Azure Monitor에는 보안 로그](https://docs.microsoft.com/azure/operations-management-suite/oms-security-monitoring-resources) 및 감사 솔루션을 사용 하면 IT 보안 인시던트의 영향을 최소화 하는 데 도움이 되는 모든 리소스를 능동적으로 모니터링할 수 있습니다. Azure Monitor 로그 보안 및 감사 보안 도메인 리소스 모니터링에 사용할 수 있는 경우 보안 도메인을 통해 옵션에 빠르게 액세스할 수 있으며, 여기서는 보안 모니터링과 관련하여 다음 도메인에 대해 자세히 살펴봅니다.

-   맬웨어 평가
-   업데이트 평가
-   ID 및 액세스

Azure Monitor는 특정 유형의 리소스 정보에 대한 포인터를 제공합니다. Azure 인프라(활동 로그)와 개별 Azure 리소스(진단 로그)의 데이터에 대한 시각화, 쿼리, 라우팅, 경고, 자동 크기 조정 및 자동화를 제공합니다.

![Azure Monitor](./media/azure-operational-security/azure-operational-security-fig6.png)


클라우드 애플리케이션은 이동하는 부분이 많아 복잡합니다. 모니터링은 애플리케이션을 유지하고 정상 상태에서 실행할 수 있는 데이터를 제공합니다. 또한 잠재적 문제를 방지하거나 지난 문제를 해결할 수 있습니다.

또한 애플리케이션에 대해 깊이 이해하는 데 모니터링 데이터를 사용할 수 있습니다. 이러한 정보를 통해 애플리케이션 성능이나 유지 관리를 개선하거나 그렇지 않으면 수동 개입이 필요한 작업을 자동화하는 데 도움이 될 수 있습니다.

### <a name="azure-activity-log"></a>Azure 동작 로그


구독의 리소스에서 수행된 작업에 대한 정보를 제공하는 로그입니다. 활동 로그는 구독에 대한 제어 평면 이벤트를 보고하므로 이전에 "감사 로그" 또는 "작업 로그"로 알려져 있습니다.

![Azure 동작 로그](./media/azure-operational-security/azure-operational-security-fig7.png)

활동 로그를 통해 구독의 리소스에 대한 모든 쓰기 작업(PUT, POST, DELETE)에서 '무엇을, 누가, 언제'를 판단할 수 있습니다. 또한 작업 및 기타 관련 속성의 상태도 이해할 수 있습니다. 활동 로그에는 읽기(GET) 작업 또는 클래식 모델을 사용하는 리소스에 대한 작업이 포함되지 않습니다.

### <a name="azure-diagnostic-logs"></a>Azure 진단 로그

이러한 로그는 리소스에서 내보내고, 해당 리소스의 작업에 대한 풍부하고 빈번한 정보를 제공합니다. 이러한 로그의 내용은 리소스 종류에 따라 달라집니다.

예를 들어, Windows 이벤트 시스템 로그는 VM 및 Blob에 대한 진단 로그의 범주이고 테이블 및 큐 로그는 스토리지 계정에 대한 진단 로그의 범주입니다.

진단 로그는 [활동 로그(이전의 감사 로그 또는 작업 로그)](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)와 다릅니다. 활동 로그는 구독에 있는 리소스에서 수행된 작업에 대한 자세한 정보를 제공합니다. 진단 로그는 리소스 자체에서 수행하는 작업에 대한 정보를 제공합니다.

### <a name="metrics"></a>메트릭

Azure 모니터에서는 원격 분석을 사용하여 Azure에서 워크로드의 상태와 성능에 대한 정보를 구할 수 있습니다. Azure 원격 분석 데이터의 가장 중요한 유형은 대부분의 Azure 리소스에서 내보내는 메트릭(성능 카운터라고도 함)입니다. Azure Monitor는 모니터링 및 문제 해결을 위해 이러한 [메트릭](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics)을 구성하고 사용하는 몇 가지 방법을 제공합니다. 메트릭은 원격 분석의 중요한 출처로, 다음 작업을 수행할 수 있습니다.

-   포털 차트에 메트릭을 넣고 해당 차트를 대시보드에 고정하여 리소스(예: VM, 웹 사이트, 논리 앱)의 **성능을 추적**합니다.

-   메트릭이 특정 임계값을 초과할 때 리소스 성능에 영향을 미치는 **문제에 대한 알림을 받습니다**.

-   메트릭이 특정 임계값을 초과할 때 리소스 자동 크기 조정 또는 runbook 실행과 같은 **자동화된 작업을 구성합니다**.

-   리소스의 성능 또는 사용 추세에 대한 **고급 분석**이나 보고를 수행합니다.

-   규정 준수 또는 감사 목적으로 리소스의 성능 또는 상태 기록을 **보관**합니다.

### <a name="azure-diagnostics"></a>Azure Diagnostics

배포된 애플리케이션에서 진단 데이터를 수집할 수 있도록 하는 Azure의 기능입니다. 별도의 다양한 원본에서 진단 확장을 사용할 수 있습니다. [Azure 클라우드 서비스 웹 및 작업자 역할](https://docs.microsoft.com/azure/vs-azure-tools-configure-roles-for-cloud-service), Microsoft Windows를 실행하는 [Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/windows/overview) 및 [Service Fabric](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics)이 현재 지원되고 있습니다. 다른 Azure 서비스에는 별도의 자체 진단이 있습니다.

## <a name="azure-network-watcher"></a>Azure Network Watcher

네트워크 보안 감사는 네트워크 취약성을 검색하고 IT 보안 및 규제 관리 모델을 준수하는 것이 중요합니다. [보안 그룹] 보기를 사용하면 구성된 네트워크 보안 그룹과 해당 보안 규칙 및 효과적인 보안 규칙을 검색할 수 있습니다. 적용된 규칙 목록을 사용하면 열려 있는 포트를 확인하고 네트워크 취약성을 평가할 수 있습니다.

[Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview)는 Azure 내에서, Azure로, Azure로부터 네트워크 수준에서 상태를 모니터링하고 진단할 수 있게 하는 지역 서비스입니다. Network Watcher에서 제공하는 네트워크 진단 및 시각화 도구를 사용하면 Azure에서 네트워크를 파악하고, 진단하고, 정보를 얻을 수 있습니다. 이 서비스에는 패킷 캡처, 다음 홉, IP 흐름 확인, 보안 그룹 보기, NSG 흐름 로그가 포함됩니다. 시나리오 수준 모니터링에서는 개별 네트워크 리소스 모니터링과 달리 네트워크 리소스의 종단 간 보기를 제공합니다.

![Azure Network Watcher](./media/azure-operational-security/azure-operational-security-fig8.png)

Network Watcher는 현재 다음과 같은 기능을 제공합니다.

-   **<a href="https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview">감사 로그</a>** - 네트워크 구성의 일부로 수행되는 작업이 기록됩니다. 이러한 로그는 Azure Portal에서 보거나 Power BI 또는 타사 도구와 같은 Microsoft 도구를 사용하여 검색할 수 있습니다. 감사 로그는 포털, PowerShell, CLI 및 Rest API를 통해 사용할 수 있습니다. 감사 로그에 대한 자세한 내용은 Resource Manager로 작업 감사를 참조하세요. 감사 로그는 모든 네트워크 리소스에서 수행된 작업에 사용할 수 있습니다.


-   **<a href="https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview">IP 흐름 확인</a>** - 흐름 정보의 5개 튜플 패킷 매개 변수(대상 IP, 원본 IP, 대상 포트, 원본 포트 및 프로토콜)에 따라 패킷이 허용되거나 거부되는지 여부를 확인합니다. 네트워크 보안 그룹에서 패킷을 거부하면 해당 패킷을 거부한 규칙과 네트워크 보안 그룹이 반환됩니다.

-   **<a href="https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview">다음 홉</a>** - Azure 네트워크 패브릭에서 라우팅되는 패킷의 다음 홉을 결정하여 잘못 구성된 사용자 정의 경로를 진단할 수 있습니다.

-   **<a href="https://docs.microsoft.com/azure/network-watcher/network-watcher-security-group-view-overview">보안 그룹 보기</a>** - VM에 적용되는 효과적이고 실용적인 보안 규칙을 가져옵니다.

-   **<a href="https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview">NSG 흐름 로깅</a>** - 네트워크 보안 그룹의 흐름 로그를 사용하면 그룹의 보안 규칙으로 허용되거나 거부되는 트래픽과 관련된 로그를 캡처할 수 있습니다. 흐름은 원본 IP, 대상 IP, 원본 포트, 대상 포트 및 프로토콜의 5개 튜플 정보로 정의됩니다.

## <a name="azure-storage-analytics"></a>Azure Storage 분석

[스토리지 분석](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics)은 Storage 서비스 요청과 관련하여 집계된 트랜잭션 통계 및 용량 데이터를 포함하는 메트릭을 저장할 수 있습니다. 트랜잭션은 API 작업 수준과 저장소 서비스 수준에서 모두 보고되며 용량은 저장소 서비스 수준에서 보고됩니다. 메트릭 데이터를 사용하면 저장소 서비스 사용량을 분석하고 저장소 서비스에 대한 요청의 문제를 진단하며 서비스를 사용하는 애플리케이션의 성능을 개선할 수 있습니다.

[Azure Storage 분석](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics)은 로깅을 수행하며 Storage 계정에 대한 메트릭 데이터를 제공합니다. 이 데이터를 사용하여 요청을 추적하고 사용량 추세를 분석하며 저장소 계정에 대한 문제를 진단할 수 있습니다. Storage 분석 로깅은 [Blob, 큐 및 Table service](https://docs.microsoft.com/azure/storage/storage-introduction)에서 사용할 수 있습니다. 스토리지 분석은 Storage 서비스에 대해 성공한 요청과 실패한 요청 관련 상세 정보를 기록합니다.

이 정로를 사용하면 개별 요청을 모니터링하고 저장소 서비스의 문제를 진단할 수 있습니다. 요청은 최상의 노력을 기준으로 기록됩니다. 서비스 엔드포인트에 대한 요청이 있는 경우에만 로그 항목이 만들어집니다. 예를 들어 스토리지 계정의 작업이 Blob 엔드포인트에는 있지만 테이블 또는 큐 엔드포인트에는 없는 경우 Blob service와 관련된 로그만 생성됩니다.

저장소 분석을 사용하려면 모니터링할 각 서비스에 대해 저장소 분석을 개별적으로 사용하도록 설정해야 합니다. 이 작업은 [Azure Portal](https://portal.azure.com/)에서 수행할 수 있습니다. 자세한 내용은 [Azure Portal에서 저장소 계정 모니터링](https://docs.microsoft.com/azure/storage/storage-monitor-storage-account)을 참조하세요. REST API 또는 클라이언트 라이브러리를 통해 프로그래밍 방식으로 저장소 분석을 사용하도록 설정할 수도 있습니다. [서비스 속성 설정] 작업을 사용하여 각 서비스에 대해 개별적으로 저장소 분석을 사용하도록 설정합니다.

집계된 데이터는 알려진 로깅용 Blob 및 알려진 메트릭용 테이블에 저장됩니다. Blob service 및 Table service API를 사용하면 이러한 Blob와 테이블에 액세스할 수 있습니다.

스토리지 분석에 저장되는 데이터의 양은 총 Storage 계정의 총 한도와 관계없이 20TB로 제한됩니다. 모든 로그는 $logs라는 컨테이너의 [블록 Blob](https://docs.microsoft.com/azure/storage/storage-analytics)에 저장되며, Storage 계정에 대해 스토리지 분석을 사용하도록 설정하면 자동으로 만들어집니다.

저장소 분석에서 수행하는 다음 작업에 대해 요금이 청구될 수 있습니다.

-   로깅용 Blob 작성 요청
-   메트릭용 테이블 엔터티 작성 요청

> [!Note]
> 청구 및 데이터 보존 정책에 대한 자세한 내용은 [저장소 분석 및 청구](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-and-billing)를 참조하세요.
> 최적의 성능을 얻기 위해 가상 머신에 연결되어 자주 활용되는 디스크의 수를 제한하여 가능한 제한을 방지하려고 합니다. 모든 디스크가 동시에 자주 활용되지 않으면 저장소 계정에서 많은 디스크를 지원할 수 있습니다.

> [!Note]
> 스토리지 계정 제한에 대한 자세한 내용은 [Azure Storage 확장성 및 성능 목표](https://docs.microsoft.com/azure/storage/storage-scalability-targets)를 참조하세요.


다음과 같은 유형의 인증된 요청 및 익명 요청이 기록됩니다.

| 인증됨  | 익명|
| :------------- | :-------------|
| 성공한 요청 | 성공한 요청 |
|실패한 요청(시간 제한, 제한, 네트워크, 권한 부여 및 기타 오류) | SAS(공유 액세스 서명)를 사용하는 요청(실패한 요청 및 성공한 요청 포함) |
| SAS(공유 액세스 서명)를 사용하는 요청(실패한 요청 및 성공한 요청 포함) |클라이언트와 서버 모두에 대한 시간 제한 오류 |
|   분석 데이터에 대한 요청 |    오류 코드가 304(수정되지 않음)인 실패한 GET 요청 |
| 로그 만들기/삭제 등 저장소 분석 자체에서 수행한 요청은 기록되지 않습니다. 기록되는 데이터의 전체 목록은 [저장소 분석에서 기록한 작업 및 상태 메시지](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-logged-operations-and-status-messages) 및 [저장소 분석 로그 형식](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-log-format) 항목에 나와 있습니다. | 기타 모든 실패한 익명 요청은 기록되지 않습니다. 기록되는 데이터의 전체 목록은 [저장소 분석에서 기록한 작업 및 상태 메시지](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-logged-operations-and-status-messages) 및 [저장소 분석 로그 형식](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-log-format)에서 설명하고 있습니다. |

## <a name="azure-active-directory"></a>Azure Active Directory

Azure AD에는 다단계 인증, 디바이스 등록, 셀프 서비스 암호 관리, 셀프 서비스 그룹 관리, 권한 있는 계정 관리, 역할 기반 액세스 제어, 애플리케이션 사용 모니터링, 광범위한 감사, 보안 모니터링 및 경고를 포함한 완벽한 ID 관리 기능이 포함되어 있습니다.

-   Azure AD 다단계 인증 및 조건부 액세스를 사용하여 애플리케이션 보안을 개선합니다.

-   애플리케이션 사용을 모니터링하고 보안 보고 및 모니터링을 통해 비즈니스를 고급 위협으로부터 보호합니다.

Azure AD(Azure Active Directory)에는 디렉터리에 대한 보안, 활동 및 감사 보고서가 포함되어 있습니다. [Azure Active Directory 감사 보고서](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-guide)를 사용하면 고객이 자신의 Azure Active Directory에서 발생한 권한 있는 작업을 식별할 수 있습니다. 권한 있는 작업에는 권한 상승 변경(예: 역할 만들기 또는 암호 재설정), 정책 구성 변경(예: 암호 정책) 또는 디렉터리 구성 변경(예: 도메인 페더레이션 설정 변경)이 포함됩니다.

보고서는 이벤트 이름의 감사 레코드, 작업을 수행한 행위자, 변경으로 인한 영향을 받은 대상 리소스, 날짜 및 시간(UTC)을 제공합니다. 고객은 [감사 로그 보기](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal)에서 설명한 대로 [Azure Portal](https://portal.azure.com/)을 통해 Azure Active Directory에 대한 감사 이벤트 목록을 검색할 수 있습니다. 포함된 보고서 목록은 다음과 같습니다.

| 보안 보고서  | 작업 보고서| 감사 보고서 |
| :------------- | :-------------| :-------------|
|알 수 없는 원본에서 로그인 | 애플리케이션 사용: 요약 | 디렉터리 감사 보고서 |
|여러 번의 실패 후 로그인 | 애플리케이션 사용: 세부 |   |
|여러 지역에서의 로그인 | 애플리케이션 대시보드 |  |
|의심스러운 작업이 있는 IP 주소에서 로그인 |계정 프로비전 오류 |  |
|비정상적인 로그인 작업 |개별 사용자 디바이스 |  |
|감염 가능성이 있는 디바이스에서 로그인 |개별 사용자 활동 |   |
|비정상적인 로그인 활동을 포함하는 사용자 |그룹 활동 보고서 |   |
| |암호 재설정 등록 활동 보고서 |   |
| |암호 재설정 활동 |   |



이러한 보고서의 데이터는 SIEM 시스템, 감사 및 비즈니스 인텔리전스 도구와 같은 애플리케이션에 유용할 수 있습니다. Azure AD 보고 [API](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started)는 일단의 REST 기반 API를 통해 데이터에 프로그래밍 방식으로 액세스합니다. 이러한 API는 다양한 프로그래밍 언어와 도구에서 호출할 수 있습니다.

Azure AD 감사 보고서의 이벤트는 180일 동안 보존됩니다.

> [!Note]
> 보고서에 보존하는 방법에 대한 자세한 내용은 [Azure Active Directory 보고서 보존 정책](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-retention)을 참조하세요.

장기 보존 기간 동안 감사 이벤트를 저장하려는 고객의 경우 보고 API를 사용하여 [감사 이벤트](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-audit-events)를 정기적으로 별도의 데이터 저장소로 가져올 수 있습니다.

## <a name="summary"></a>요약

이 문서에서는 개인 정보와 데이터를 보호하는 동시에 조직의 IT 인프라를 관리하는 데 유용한 소프트웨어와 서비스를 제공합니다. Microsoft는 데이터를 다른 사람에게 맡길 때 해당 신뢰에는 엄격한 보안이 필요하다는 것을 알고 있습니다. Microsoft는 코딩부터 서비스에 이르기까지 엄격한 규정 준수 및 보안 지침을 따릅니다.  데이터 보안 및 보호는 Microsoft의 최우선 과제입니다.

이 문서에서 설명하는 내용은 다음과 같습니다.

-   데이터 수집, 처리 및 방식을 Azure Monitor 도구 모음에 보호 합니다.

-   여러 데이터 원본에서 이벤트를 빠르게 분석합니다. 보안 위험을 식별하고 위협과 공격의 범위와 영향을 파악하여 보안 위반의 손해를 완화합니다.

-   아웃바운드 악성 IP 트래픽과 악성 위협 유형을 시각화하여 공격 패턴을 식별합니다. 플랫폼에 상관없이 전체 환경의 보안 상태를 파악합니다.

-   보안 또는 규정 준수 감사에 필요한 모든 로그 및 이벤트 데이터를 캡처합니다. 완벽하고 검색 가능하며 내보낼 수 있는 로그 및 이벤트 데이터 집합으로 보안 감사를 제공하는 데 필요한 시간과 리소스를 줄입니다.

<ul>
<li>다음과 같은 보안 관련 이벤트, 감사 및 위반 분석을 수집하여 자산을 주의 깊게 관찰합니다.</li>
<ul>
<li>보안 상태</li>
<li>주목할 만한 문제</li>
<li>위협 요약</li>
</ul>
</ul>

## <a name="next-steps"></a>다음 단계

- [설계 및 운영 보안](https://www.microsoft.com/trustcenter/security/designopsecurity)

Microsoft는 클라우드 인프라의 탄력성을 보장하고 공격으로부터 방어할 수 있도록 보안에 주의하면서 서비스와 소프트웨어를 설계합니다.

- [Azure Monitor 로그 | 보안 및 규정 준수](https://www.microsoft.com/cloud-platform/security-and-compliance)

Microsoft 보안 데이터 및 분석을 사용하여 더 지능적이고 효과적인 위협 검색을 수행합니다.

- [Azure Security Center 계획 및 작업 가이드](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide) - 조직의 보안 요구 사항과 클라우드 관리 모델에 따라 보안 센터를 최적으로 사용하기 위해 수행할 수 있는 일단의 단계 및 작업입니다.

