---
title: Azure 보안 소개 | Microsoft Docs
description: Azure 보안, 해당 서비스 및 작동 방법에 대해 알아봅니다.
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
ms.openlocfilehash: ed57d72d32ba82a37036c9af77590bd4e93db8d9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60610504"
---
# <a name="introduction-to-azure-security"></a>Azure 보안 소개
## <a name="overview"></a>개요
보안은 클라우드의 작업이며 Azure 보안에 대한 정확하고 시기 적절한 정보를 찾는 것이 얼마나 중요한지 알고 있습니다. Azure를 애플리케이션과 서비스에 사용하는 가장 좋은 이유 중 하나는 광범위한 보안 도구와 기능을 활용한다는 점입니다. 이러한 도구 및 기능을 통해 안전한 Azure 플랫폼에서 안전한 솔루션을 만들 수 있습니다. Microsoft Azure는 고객 데이터의 기밀성, 무결성 및 가용성을 제공하는 한편 투명한 책임도 가능하게 합니다.

고객 관점과 Microsoft 작업 관점 모두에서 Microsoft Azure에서 구현된 보안 제어 모음을 더 잘 이해할 수 있도록 Microsoft Azure에서 제공되는 보안을 포괄적으로 살펴볼 수 있는 "Azure 보안 소개" 백서가 작성되었습니다.

### <a name="azure-platform"></a>Azure 플랫폼
Azure는 다양한 운영 체제, 프로그래밍 언어, 프레임워크, 도구, 데이터베이스 및 디바이스를 지원하는 공용 클라우드 서비스 플랫폼입니다. Docker 통합으로 Linux 컨테이너를 실행할 수 있습니다. JavaScript, Python, .NET, PHP, Java 및 Node.js를 사용하여 앱을 빌드할 수 있습니다. iOS, Android 및 Windows 디바이스용 백 엔드를 빌드할 수 있습니다.

Azure 공용 클라우드 서비스는 수백만의 개발자 및 IT 전문가가 이미 믿고 사용하고 있는 동일한 수준의 기술을 지원합니다. IT 자산을 만들거나 공용 클라우드 서비스 공급자로 마이그레이션하는 경우 조직에서 제공하는 서비스와 제어를 통해 애플리케이션과 데이터를 보호할 수 있는 해당 조직의 능력에 의존하여 클라우드 기반 자산의 보안을 관리합니다.

Azure의 인프라는 수백만 고객을 동시에 호스팅하는 애플리케이션에 맞게 시설로부터 설계되었으며, 비즈니스에서 보안 요구 사항을 충족할 수 있는 신뢰할 수 있는 기반을 제공합니다.

또한 Azure는 다양하게 구성할 수 있는 보안 옵션과 이를 제어하는 기능을 제공하므로 보안을 사용자 지정하여 조직의 고유한 배포 요구 사항을 충족할 수 있습니다. 이 문서는 Azure 보안 기능이 이러한 요구 사항을 충족하는 방법을 이해하는 데 도움이 됩니다.

> [!Note]
> 이 문서는 주로 애플리케이션과 서비스의 보안을 사용자 지정하고 향상시키는 데 사용할 수 있는 고객 관련 제어에 집중하고 있습니다.
>
> 여기서는 일부 개요 정보를 제공하지만, Microsoft에서 Azure 플랫폼 자체를 보호하는 방법에 대한 자세한 내용은 [Microsoft Trust Center](https://www.microsoft.com/TrustCenter/default.aspx)에서 설명하는 정보를 참조하세요.

### <a name="abstract"></a>요약
처음에는 공용 클라우드 마이그레이션이 비용 절감과 민첩성을 통해 추진되면서 혁신되었습니다. 보안은 한동안 공용 클라우드 마이그레이션에 대한 중요한 관심사로 간주되었으며, 심지어는 쇼 스토퍼로도 간주되었습니다. 그러나 공용 클라우드 보안은 주요 관심사에서 클라우드 마이그레이션을 위한 드라이버 중 하나로 전환되었습니다. 이 배경의 근거는 대형 공용 클라우드 서비스 공급자가 클라우드 기반 자산의 애플리케이션과 데이터를 보호하는 뛰어난 기능에 있습니다.

Azure의 인프라는 수백만 명의 고객을 동시에 호스팅하기 위한 시설에서 애플리케이션에 이르는 인프라를 설계하며 비즈니스의 보안 요구 사항을 충족하는 신뢰할 수 있는 기반을 제공합니다. 또한 Azure는 다양하게 구성 가능한 보안 옵션 및 이를 제어하는 기능을 제공하므로 IT 제어 정책을 충족하고 외부 규정을 준수하도록 배포에 대한 고유한 요구 사항에 맞게 보안을 사용자 지정할 수 있습니다.

이 백서에서는 다음과 같은 Microsoft Azure 클라우드 플랫폼 내 보안 방법에 대해 간략히 설명합니다.
* Azure 인프라, 고객 데이터 및 애플리케이션 보안을 유지하기 위해 Microsoft에서 구현된 보안 기능
* Azure 구독 내에서 서비스와 데이터의 보안을 관리할 수 있는 Azure 서비스 및 보안 기능

## <a name="summary-azure-security-capabilities"></a>Azure 보안 기능 요약
다음 표에서는 Azure 인프라, 고객 데이터 및 보안 애플리케이션 보안을 유지하기 위해 Microsoft에서 구현된 보안 기능에 대해 간략히 설명하고 있습니다.
### <a name="security-features-implemented-to-secure-the-azure-platform"></a>Azure 플랫폼 보안을 유지하기 위해 구현된 보안 기능:
다음에 나열된 기능은 Azure 플랫폼을 안전하게 관리한다는 확신을 보장하기 위해 검토할 수 있는 기능입니다. Microsoft에서 보안 플랫폼, 개인 정보 보호 및 제어, 규정 준수 및 투명성의 네 가지 영역에서 고객의 신뢰 문제를 해결하는 방법에 대해 추가로 드릴다운할 수 있는 링크를 제공하고 있습니다.


| [보안 플랫폼](https://www.microsoft.com/en-us/trustcenter/Security/default.aspx)  | [개인 정보 보호 및 제어](https://www.microsoft.com/en-us/trustcenter/Privacy/default.aspx)  |[규정 준수](https://www.microsoft.com/en-us/trustcenter/Compliance/default.aspx)   | [투명성](https://www.microsoft.com/en-us/trustcenter/Transparency/default.aspx) |
| :-- | :-- | :-- | :-- |
| [보안 개발 주기](https://www.microsoft.com/en-us/sdl/)(영문), 내부 감사 | [항상 사용자 데이터 관리](https://www.microsoft.com/en-us/trustcenter/Privacy/You-own-your-data)(영문) | [보안 센터](https://www.microsoft.com/en-us/trustcenter/default.aspx) |[Microsoft Azure 서비스에서 고객 데이터를 보호하는 방법](https://www.microsoft.com/en-us/trustcenter/Transparency/default.aspx)(영문) |
| [필수 보안 교육, 백그라운드 검사](https://downloads.cloudsecurityalliance.org/star/self-assessment/StandardResponsetoRequestforInformationWindowsAzureSecurityPrivacy.docx)(영문) |  [데이터 위치에서 제어](https://www.microsoft.com/en-us/trustcenter/Privacy/Where-your-data-is-located) |  [일반 컨트롤 허브](https://www.microsoft.com/en-us/trustcenter/Common-Controls-Hub)(영문) |[Microsoft Azure 서비스에서 데이터 위치를 관리하는 방법](https://azuredatacentermap.azurewebsites.net/)(영문)|
| [침투 테스트](https://downloads.cloudsecurityalliance.org/star/self-assessment/StandardResponsetoRequestforInformationWindowsAzureSecurityPrivacy.docx), [침입 검색, DDoS](https://www.microsoft.com/en-us/trustcenter/Security/ThreatManagement), [감사 및 로깅](https://www.microsoft.com/en-us/trustcenter/Security/AuditingAndLogging) | [조건부 데이터 액세스 제공](https://www.microsoft.com/en-us/trustcenter/Privacy/Who-can-access-your-data-and-on-what-terms) |  [Cloud Services 실사 검사 목록](https://www.microsoft.com/en-us/trustcenter/Compliance/Due-Diligence-Checklist)(영문) |[데이터에 누가 그리고 어떤 조건으로 액세스할 수 있는가](https://www.microsoft.com/en-us/trustcenter/Privacy/Who-can-access-your-data-and-on-what-terms)|
| [세계 첨단의 데이터 센터](https://www.microsoft.com/en-us/cloud-platform/global-datacenters), 물리적 보안, [네트워크 보안](https://docs.microsoft.com/azure/security/security-network-overview) | [사법 기관에 대한 응답](https://www.microsoft.com/en-us/trustcenter/Privacy/Responding-to-govt-agency-requests-for-customer-data)(영문) |  [서비스별, 지역별 및 산업별 규정 준수](https://www.microsoft.com/en-us/trustcenter/Compliance/default.aspx) |[Microsoft Azure 서비스에서 고객 데이터를 보호하는 방법](https://www.microsoft.com/en-us/trustcenter/Transparency/default.aspx)(영문)|
|  [보안 사고 대응](https://aka.ms/SecurityResponsepaper)(영문), [공동 책임](https://aka.ms/sharedresponsibility)(영문) |[엄격한 개인 정보 보호 표준](https://www.microsoft.com/en-us/TrustCenter/Privacy/We-set-and-adhere-to-stringent-standards) |  | [Azure 서비스, 투명성 허브에 대한 인증 검토](https://www.microsoft.com/en-us/trustcenter/Compliance/default.aspx)(영문)|



### <a name="security-features-offered-by-azure-to-secure-data-and-application"></a>데이터와 애플리케이션 보안을 유지하기 위해 Azure에서 제공하는 보안 기능
클라우드 서비스 모델에 따라 애플리케이션 또는 서비스에 대한 보안 관리 담당자의 책임이 달라질 수 있습니다. Azure 플랫폼에는 기본 제공 기능 및 Azure 구독에 배포할 수 있는 파트너 솔루션을 통해 이러한 책임을 이행하는 데 도움이 되는 기능이 있습니다.

기본 제공 기능은 6개의 기능 영역인 작업, 애플리케이션, 스토리지, 네트워킹, 컴퓨팅 및 ID로 구성되어 있습니다. Azure 플랫폼에서 사용할 수 있는 이러한 6가지 영역의 기능에 대한 자세한 추가 정보는 요약 정보를 통해 제공됩니다.

## <a name="operations"></a>작업
이 섹션에서는 보안 작업의 주요 기능에 대한 추가 정보와 이러한 기능에 대한 요약 정보를 제공합니다.

### <a name="security-and-audit-dashboard"></a>보안 및 감사 대시보드
[보안 및 감사 솔루션](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started)은 주의가 필요한 주요 문제에 대한 [기본 제공 검색 쿼리](https://blogs.technet.microsoft.com/msoms/2016/01/21/easy-microsoft-operations-management-suite-search-queries/)를 통해 조직의 IT 보안 상태에 대한 포괄적인 보기를 제공합니다. 합니다 [보안 및 감사](https://technet.microsoft.com/library/mt484091.aspx) 대시보드 Azure Monitor 로그에는 보안과 관련 된 모든 항목에 대 한 홈 화면입니다. 이 대시보드에서 컴퓨터의 보안 상태를 대략적으로 확인할 수 있으며, 지난 24시간, 7일, 기타 사용자 지정 기간 동안 발생한 모든 이벤트를 확인할 수도 있습니다.

또한 특정 이벤트가 검색되면 [특정 작업을 자동으로 수행](https://blogs.technet.microsoft.com/robdavies/2016/04/20/simple-look-at-oms-alert-remediation-with-runbooks-part-1/)하도록 보안 및 규정 준수를 구성할 수 있습니다.

### <a name="azure-resource-manager"></a>Azure 리소스 관리자
[Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model)를 사용하면 솔루션에서 리소스를 그룹으로 사용할 수 있습니다. 조정된 단일 작업에서 솔루션에 대한 모든 리소스를 배포, 업데이트 또는 삭제할 수 있습니다. 배포용 [Azure Resource Manager 템플릿](https://blogs.technet.microsoft.com/canitpro/2015/06/29/devops-basics-infrastructure-as-code-arm-templates/)을 사용하고, 해당 템플릿은 테스트, 스테이징 및 프로덕션과 같은 여러 환경에서 사용할 수 있습니다. 리소스 관리자는 보안, 감사 및 태그 기능을 제공하여 배포 후에 리소스를 관리할 수 있습니다.

Azure Resource Manager 템플릿 기반 배포를 사용하면 표준 보안 제어 설정을 표준화된 템플릿 기반 배포에 통합할 수 있기 때문에 Azure에 배포된 솔루션의 보안을 향상시킬 수 있습니다. 이렇게 하면 수동 배포 중에 발생할 수 있는 보안 구성 오류의 위험이 줄어듭니다.

### <a name="application-insights"></a>Application Insights
[Application Insights](https://docs.microsoft.com/azure/application-insights/)는 웹 개발자를 위한 확장 가능한 APM(애플리케이션 성능 관리) 서비스입니다. Application Insights를 사용하면 라이브 웹 애플리케이션을 모니터링하고 성능 이상을 자동으로 검색할 수 있습니다. 사용자가 문제를 진단하고 사용자가 실제로 앱을 사용하여 수행할 작업을 이해하는 데 도움이 되는 강력한 분석 도구가 포함되어 있습니다. 테스트 중인 경우 및 게시하거나 배포한 후에 실행 중인 모든 시간 동안 애플리케이션을 모니터링합니다.

예를 들어 Application Insights는 많은 사용자를 가져오는 시간, 앱이 얼마나 반응하는지, 종속된 외부 서비스에서 얼마나 잘 제공되는지를 보여주는 차트 및 테이블을 만듭니다.

충돌, 오류 또는 성능 문제가 있는 경우 세부 정보에서 원격 분석 데이터를 통해 검색하여 원인을 진단할 수 있습니다. 그리고 앱의 가용성과 성능에 변경 사항이 있는 경우 서비스에서 사용자에게 전자 메일을 보냅니다. 따라서 Application Insight는 기밀성, 무결성 및 가용성 보안 3요소를 한 조로 묶어 가용성을 높이는 데 도움이 되므로 중요한 보안 도구가 됩니다.

### <a name="azure-monitor"></a>Azure Monitor
[Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) – Azure 인프라([활동 로그](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs))와 개별 Azure 리소스([진단 로그](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)) 모두의 데이터에 대한 시각화, 쿼리, 라우팅, 경고, 자동 크기 조정 및 자동화를 제공합니다. Azure Monitor를 사용하여 Azure 로그에서 생성된 보안 관련 이벤트에 대해 사용자에게 알릴 수 있습니다.

### <a name="azure-monitor-logs"></a>Azure Monitor 로그
[Azure Monitor 로그](https://azure.microsoft.com/documentation/services/log-analytics/) – 온-프레미스 및 타사 클라우드 기반 인프라 (예: AWS) 외에도 Azure 리소스에 대 한 IT 관리 솔루션을 제공 합니다. Azure Monitor의 데이터를 한 곳에서 전체 환경에 대 한 메트릭 및 로그를 볼 수 있도록 Azure Monitor 로그로 직접 라우팅할 수 있습니다.

Azure Monitor 로그 도구를 사용 하면 신속 하 게 많은 양의 유연한 쿼리 방법으로 보안 관련 항목을 검색할 법정 분석 및 기타 보안 분석에 유용한 도구가 될 수 있습니다. 또한 온-프레미스 [방화벽 및 프록시 로그를 Azure로 내보내고 Azure Monitor 로그를 사용 하 여 분석에 사용할 수 있도록 합니다.](https://docs.microsoft.com/azure/log-analytics/log-analytics-proxy-firewall)

### <a name="azure-advisor"></a>Azure Advisor
[Azure Advisor](https://docs.microsoft.com/azure/advisor/)는 Azure 배포를 최적화하는 데 도움이 되는 개인 설정된 클라우드 컨설턴트입니다. 리소스 구성 및 사용 원격 분석을 분석 합니다. 그런 다음 [전체 Azure 사용을 줄일](https://docs.microsoft.com/azure/advisor/advisor-cost-recommendations) 수 있는 기회를 찾는 동안 [성능](https://docs.microsoft.com/azure/advisor/advisor-performance-recommendations), [보안](https://docs.microsoft.com/azure/advisor/advisor-security-recommendations) 및 [고가용성](https://docs.microsoft.com/azure/advisor/advisor-high-availability-recommendations)을 향상시키는 데 도움이 되는 솔루션을 사용하는 것이 좋습니다. Azure Advisor는 보안 권장 사항을 제공하므로 Azure에 배포하는 솔루션의 전반적인 보안 상태를 크게 향상시킬 수 있습니다. 이러한 권장 사항은 [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)에서 수행한 보안 분석에서 가져온 것입니다.

### <a name="azure-security-center"></a>Azure Security Center
[Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)를 사용하면 Azure 리소스의 보안에 대한 향상된 가시성과 제어를 통해 위협을 예방, 검색 및 대응할 수 있습니다. 이는 Azure 구독에 대해 통합된 보안 모니터링 및 정책 관리를 제공하고 다른 방법으로 발견되지 않을 수 있는 위협을 감지하는 데 도움이 되며 보안 솔루션의 광범위한 환경에서 작동합니다.

또한 Azure Security Center는 즉시 수행할 수 있는 경고와 권장 사항을 보여 주는 단일 대시보드를 제공하여 보안 운영을 지원합니다. 종종 Azure Security Center 콘솔에서 한 번의 클릭으로 문제를 해결할 수 있습니다.
## <a name="applications"></a>애플리케이션
이 섹션에서는 애플리케이션 보안의 주요 기능에 대한 추가 정보와 이러한 기능에 대한 요약 정보를 제공합니다.

### <a name="web-application-vulnerability-scanning"></a>웹 애플리케이션 취약성 스캔
[App Service 앱](https://docs.microsoft.com/azure/app-service/overview)에서 취약성 테스트를 시작하는 가장 쉬운 방법 중 하나는 [Tinfoil Security와 통합](https://azure.microsoft.com/blog/web-vulnerability-scanning-for-azure-app-service-powered-by-tinfoil-security/)을 사용하여 앱에서 한 번의 클릭으로 취약성 스캔을 수행하는 것입니다. 이해하기 쉬운 보고서에서 테스트 결과를 확인하고 단계별 지침에 따라 각 취약점을 수정하는 방법을 알아볼 수 있습니다.

### <a name="penetration-testing"></a>침투 테스트
사용자 고유의 침투 테스트를 수행하거나 다른 스캐너 도구 모음 또는 공급자를 사용하려면 [Azure 침투 테스트 승인 프로세스](https://docs.microsoft.com/azure/security/azure-security-pen-testing ) 에 따라 원하는 침투 테스트를 수행하기 위한 사전 승인을 얻어야 합니다.

### <a name="web-application-firewall"></a>웹 애플리케이션 방화벽
[Azure Application Gateway](https://azure.microsoft.com/services/application-gateway/)의 WAF(웹 애플리케이션 방화벽)는 SQL 삽입, 사이트 간 스크립팅 공격, 세션 하이재킹과 같은 일반적인 웹 기반 공격으로부터 웹 애플리케이션을 보호합니다. [OWASP(Open Web Application Security Project)에서 상위 10개 일반 취약성](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project)으로 확인된 위협으로부터 보호하는 기능이 미리 구성되어 있습니다.

### <a name="authentication-and-authorization-in-azure-app-service"></a>Azure App Service의 인증 및 권한 부여
[App Service 인증/권한 부여](https://docs.microsoft.com/azure/app-service/overview-authentication-authorization)는 앱 백 엔드에서 코드를 변경할 필요가 없도록 사용자가 애플리케이션에 로그인하는 방법을 제공하는 기능입니다. 애플리케이션을 보호하고 사용자 단위당 데이터로 작업하는 쉬운 방법을 제공합니다.

### <a name="layered-security-architecture"></a>계층화된 보안 아키텍처
[App Service 환경](https://docs.microsoft.com/azure/app-service/environment/app-service-app-service-environment-intro)이 [Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)에 배포된 격리된 런타임 환경을 제공하므로 개발자는 각 애플리케이션 계층에 서로 다른 수준의 네트워크 액세스를 제공하는 계층화된 보안 아키텍처를 만들 수 있습니다. 일반적으로 일반 인터넷 액세스로부터 API 백 엔드를 숨기거나 API가 업스트림 웹앱에서 호출될 수 있도록 하기 원합니다. [NSG(네트워크 보안 그룹)](https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/)은 App Service 환경을 포함하는 Azure Virtual Network 서브넷에서 사용하여 API 애플리케이션에 대한 공용 액세스를 제한할 수 있습니다.

### <a name="web-server-diagnostics-and-application-diagnostics"></a>웹 서버 진단 및 애플리케이션 진단
App Service 웹앱은 웹 서버와 웹 애플리케이션 모두의 정보를 로깅할 수 있도록 진단 기능을 제공합니다. 이는 논리적으로 [웹 서버 진단](https://docs.microsoft.com/azure/app-service/troubleshoot-diagnostic-logs) 및 [애플리케이션 진단](https://technet.microsoft.com/library/hh530058(v=sc.12).aspx)으로 구분됩니다. 웹 서버에서 사이트와 애플리케이션을 진단하고 문제를 해결하는 두 가지의 큰 발전이 이루어졌습니다.

첫째, 새로운 기능으로 애플리케이션 풀, 작업자 프로세스, 사이트, 애플리케이션 도메인 및 실행 중인 요청에 대한 실시간 상태 정보가 있습니다. 둘째, 새로운 이점으로 요청-응답 프로세스 전체에서 요청을 추적하는 자세한 추적 이벤트가 있습니다.

이러한 추적 이벤트의 수집을 사용하기 위해 IIS 7에서 경과 시간 또는 오류 응답 코드를 기반으로 하는 특정 요청에 대해 XML 형식의 전체 추적 로그를 자동으로 캡처하도록 구성할 수 있습니다.

#### <a name="web-server-diagnostics"></a>웹 서버 진단
다음과 같은 종류의 로그를 사용하거나 사용하지 않도록 설정할 수 있습니다.

-   자세한 오류 로깅 - 오류를 나타내는 HTTP 상태 코드(상태 코드 400 이상)에 대한 자세한 오류 정보입니다. 여기에는 서버에서 오류 코드를 반환한 이유를 확인하는 데 도움이 되는 정보가 포함될 수 있습니다.

-   실패한 요청 추적 - 요청을 처리하는 데 사용된 IIS 구성 요소 추적 및 각 구성 요소에서 소요된 시간을 포함하여 실패한 요청에 대한 자세한 정보입니다. 이는 사이트 성능을 개선하거나 반환된 특정 HTTP 오류를 유발한 항목을 격리하려는 경우에 유용합니다.

-   웹 서버 로깅 - W3C 확장 로그 파일 형식을 사용하는 HTTP 트랜잭션에 대한 정보입니다. 이는 처리된 요청 수, 특정 IP 주소에서 들어온 요청 수 등의 전체 사이트 메트릭을 확인하는 경우에 유용합니다.

#### <a name="application-diagnostics"></a>애플리케이션 진단
[애플리케이션 진단](https://docs.microsoft.com/azure/app-service/troubleshoot-diagnostic-logs)을 사용하면 웹 애플리케이션에서 생성된 정보를 캡처할 수 있습니다. ASP.NET 애플리케이션은 [System.Diagnostics.Trace](https://msdn.microsoft.com/library/system.diagnostics.trace) 클래스를 사용하여 애플리케이션 진단 로그에 정보를 로깅할 수 있습니다. 애플리케이션 진단에는 두 가지 주요 유형의 이벤트, 즉 애플리케이션 성능과 관련된 이벤트와 애플리케이션 실패 및 오류와 관련된 이벤트가 있습니다. 실패와 오류는 연결, 보안 및 오류 문제로 더 자세히 구분할 수 있습니다. 오류 문제는 일반적으로 애플리케이션 코드에서 발생하는 문제와 관련이 있습니다.

애플리케이션 진단에서 다음과 같은 방법으로 그룹화된 이벤트를 볼 수 있습니다.

-   모두(모든 이벤트 표시)
-   애플리케이션 오류(예외 이벤트 표시)
-   성능(성능 이벤트 표시)

## <a name="storage"></a>Storage
이 섹션에서는 Azure 저장소 보안의 주요 기능에 대한 추가 정보와 이러한 기능에 대한 요약 정보를 제공합니다.

### <a name="role-based-access-control-rbac"></a>역할 기반 Access Control(RBAC)
RBAC(역할 기반 Access Control)를 사용하여 저장소 계정의 보안을 유지할 수 있습니다. [알아야 할 사항](https://en.wikipedia.org/wiki/Need_to_know) 및 [최소 권한](https://en.wikipedia.org/wiki/Principle_of_least_privilege) 보안 원칙을 기반으로 하여 액세스를 제한하는 것은 데이터 액세스에 대한 보안 정책을 시행하려는 조직에서 반드시 필요합니다. 특정 범위에서 그룹 및 애플리케이션에 적절한 RBAC 역할을 할당하여 이러한 액세스 권한을 부여합니다. Storage 계정 참여자와 같은 [기본 제공 RBAC 역할](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)을 사용하여 사용자에게 권한을 할당할 수 있습니다. [Azure Resource Manager](https://docs.microsoft.com/azure/storage/storage-security-guide) 모델을 사용하는 저장소 계정의 저장소 키에 대한 액세스는 RBAC(역할 기반 Access Control)를 통해 제어할 수 있습니다.

### <a name="shared-access-signature"></a>공유 액세스 서명
[SAS(공유 액세스 서명)](https://docs.microsoft.com/azure/storage/storage-dotnet-shared-access-signature-part-1)는 저장소 계정의 리소스에 대한 위임된 권한을 제공합니다. SAS는 지정된 권한 집합을 사용하여 지정된 기간 동안 저장소 계정의 개체로 제한된 권한을 클라이언트에 부여할 수 있다는 것입니다. 계정 선택키를 공유하지 않고도 제한된 권한을 부여할 수 있습니다.

### <a name="encryption-in-transit"></a>전송 중 암호화
전송 중 암호화는 네트워크를 통해 전송되는 경우 데이터 보호의 메커니즘입니다. Azure Storage를 사용하면 다음을 사용하여 데이터를 보호할 수 있습니다.
-   [전송 수준 암호화](https://docs.microsoft.com/azure/storage/storage-security-guide#encryption-in-transit)(예: Azure Storage 안팎으로 데이터를 전송하는 경우 HTTPS)

-   [실시간 암호화](https://docs.microsoft.com/azure/storage/storage-security-guide#using-encryption-during-transit-with-azure-file-shares)(예: [Azure 파일 공유](https://docs.microsoft.com/azure/storage/storage-dotnet-how-to-use-files)에 대한 [SMB 3.0 암호화](https://docs.microsoft.com/azure/storage/storage-security-guide))

-   클라이언트 쪽 암호화 - 데이터를 저장소로 전송하기 전에 암호화하고, 저장소에서 전송한 후에 암호를 해독합니다.

### <a name="encryption-at-rest"></a>휴지 상태의 암호화
많은 조직에서 미사용 데이터 암호화는 데이터 개인 정보 보호, 규정 준수 및 데이터 주권을 위한 필수 단계입니다. "미사용" 데이터 암호화를 제공하는 세 가지 Azure 저장소 보안 기능이 있습니다.

-   [Storage 서비스 암호화](https://docs.microsoft.com/azure/storage/storage-service-encryption) 를 사용하면 Storage 서비스가 Azure Storage에 데이터를 쓸 때 데이터를 자동으로 암호화하도록 요청할 수 있습니다.

-   [Client-side Encryption](https://docs.microsoft.com/azure/storage/storage-client-side-encryption) 는 휴지 상태의 암호화 기능을 제공합니다.

-   [Azure 디스크 암호화](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) 를 사용하면 IaaS 가상 머신에서 사용되는 OS 디스크 및 데이터 디스크를 암호화할 수 있습니다.

### <a name="storage-analytics"></a>저장소 분석
[Azure Storage 분석](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics)은 로깅을 수행하며 Storage 계정에 대한 메트릭 데이터를 제공합니다. 이 데이터를 사용하여 요청을 추적하고 사용량 추세를 분석하며 저장소 계정에 대한 문제를 진단할 수 있습니다. 스토리지 분석은 Storage 서비스에 대해 성공한 요청과 실패한 요청 관련 상세 정보를 기록합니다. 이 정로를 사용하면 개별 요청을 모니터링하고 저장소 서비스의 문제를 진단할 수 있습니다. 요청은 최상의 노력을 기준으로 기록됩니다. 다음과 같은 유형의 인증된 요청이 기록됩니다.
-   성공한 요청

-   실패한 요청(제한 시간, 제한, 네트워크, 권한 부여 및 기타 오류)

-   실패한 요청 및 성공한 요청을 포함하는 SAS(공유 액세스 서명)를 사용하는 요청

-   분석 데이터에 대한 요청

### <a name="enabling-browser-based-clients-using-cors"></a>CORS를 통해 브라우저 기반 클라이언트를 사용하도록 설정
[CORS(원본 간 리소스 공유)](https://docs.microsoft.com/rest/api/storageservices/fileservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services)는 도메인에서 서로의 리소스에 액세스할 수 있는 권한을 상호 간에 부여할 수 있게 하는 메커니즘입니다. 사용자 에이전트는 특정 도메인에서 로드된 JavaScript 코드가 다른 도메인에 있는 리소스에 액세스할 수 있도록 허용하기 위해 추가 헤더를 보냅니다. 그런 다음 후자의 도메인은 리소스에 대한 원래 도메인의 액세스를 허용하거나 거부하는 추가 헤더로 응답합니다.

Azure 저장소 서비스는 이제 CORS를 지원하므로 서비스에 대한 CORS 규칙을 설정하면 다른 도메인의 서비스에 대해 올바르게 인증된 요청을 평가하여 지정된 규칙에 따라 해당 요청이 허용되는지 여부를 결정합니다.
## <a name="networking"></a>네트워킹
이 섹션에서는 Azure 네트워크 보안의 주요 기능에 대한 추가 정보와 이러한 기능에 대한 요약 정보를 제공합니다.

### <a name="network-layer-controls"></a>네트워크 계층 제어
네트워크 액세스 제어는 특정 디바이스 또는 서브넷 간의 연결을 제한하는 행위이며, 네트워크 보안의 핵심을 나타냅니다. 네트워크 액세스 제어의 목표는 가상 머신 및 서비스가 사용자가 액세스하길 원하는 사용자 및 디바이스에만 액세스 가능하도록 하는 것입니다.

#### <a name="network-security-groups"></a>네트워크 보안 그룹
[NSG(네트워크 보안 그룹)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)은 기본적인 상태 저장 패킷 필터링 방화벽이며, [5개 튜플](https://www.techopedia.com/definition/28190/5-tuple)에 기반하여 액세스를 제어할 수 있게 합니다. NSG는 애플리케이션 계층 검사 또는 인증된 액세스 제어를 제공하지 않습니다. 이 그룹은 Azure Virtual Network 내의 서브넷 간에 이동하는 트래픽 및 Azure Virtual Network와 인터넷 간 트래픽을 제어하는 데 사용할 수 있습니다.

#### <a name="route-control-and-forced-tunneling"></a>경로 제어 및 터널링 적용
Azure Virtual Network에 라우팅 동작을 제어하는 기능은 중요한 네트워크 보안 및 액세스 제어 기능입니다. 예를 들어 Azure Virtual Network에 들어오고 나가는 모든 트래픽이 해당 가상 보안 어플라이언스를 통과하는지 확인하려면 라우팅 동작을 제어하고 사용자 지정할 수 있어야 합니다. 이렇게 하려면 Azure에서 사용자 정의 경로를 구성하면 됩니다.

[사용자 정의 경로](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview)를 사용하면 개별 가상 머신 또는 서브넷으로 들어오고 나가는 트래픽의 인바운드 및 아웃바운드 경로를 사용자 지정하여 가능한 가장 안전한 경로를 보장할 수 있습니다. [터널링 적용](https://www.petri.com/azure-forced-tunneling)은 사용자의 서비스가 인터넷에서 디바이스에 대한 연결을 개시하지 못하게 하도록 사용할 수 있는 메커니즘입니다.

이는 들어오는 연결을 수락한 다음 응답을 받을 수 있는 것과 다릅니다. 프런트 엔드 웹 서버는 인터넷 호스트의 요청에 응답해야 하므로 인터넷 소싱 트래픽은 이러한 웹 서버에 허용되는 인바운드이며 웹 서버에서 응답할 수 있습니다.

강제 적용 터널링은 일반적으로 온-프레미스 보안 프록시 및 방화벽을 통과하도록 인터넷으로의 아웃바운드 트래픽을 강제 적용하는 데 사용됩니다.

#### <a name="virtual-network-security-appliances"></a>Virtual Network 보안 어플라이언스
네트워크 보안 그룹, 사용자 정의 경로 및 강제 적용 터널링으로 [OSI 모델](https://en.wikipedia.org/wiki/OSI_model) 네트워크 계층 및 전송 계층에서 보안 수준을 제공할 수 있지만, 더 높은 수준의 스택에서 보안을 사용하려는 경우가 있을 수 있습니다. Azure 파트너 네트워크 보안 어플라이언스 솔루션을 사용하여 이러한 향상된 네트워크 보안 기능에 액세스할 수 있습니다. [Azure Marketplace](https://azure.microsoft.com/marketplace/)를 방문하여 "보안" 및 "네트워크 보안"을 검색하여 가장 최신의 Azure 파트너 네트워크 보안 솔루션을 찾을 수 있습니다.

### <a name="azure-virtual-network"></a>Azure Virtual Network

Azure 가상 네트워크(VNet)는 클라우드의 사용자 네트워크를 나타내는 표현입니다. 구독 전용 Azure 네트워크 패브릭의 논리적 격리입니다. 사용자는 이 네트워크 내부의 IP 주소 블록, DNS 설정, 보안 정책 및 경로 테이블을 완벽하게 제어할 수 있습니다. Azure Virtual Networks에서 VNet을 서브넷으로 분할하고 Azure IaaS VM(가상 머신) 및/또는 [Cloud services(PaaS 역할 인스턴스)](https://docs.microsoft.com/azure/cloud-services/cloud-services-choose-me)를 배치할 수 있습니다.

뿐만 아니라 Azure에서 제공하는 [연결 옵션](https://docs.microsoft.com/azure/vpn-gateway/) 중 하나를 사용하여 가상 네트워크를 온-프레미스 네트워크에 연결할 수 있습니다. 기본적으로 네트워크를 Azure로 확장하여 IP 주소 블록을 완벽하게 제어하고, Azure가 제공하는 엔터프라이즈급 솔루션의 혜택을 누릴 수 있습니다.

Azure 네트워킹은 다양한 보안 원격 액세스 시나리오를 지원합니다. 그 중 일부는 다음과 같습니다.

-   [Azure Virtual Network에 개별 워크스테이션 연결](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps)

-   [VPN으로 Azure Virtual Network에 온-프레미스 네트워크 연결](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design)

-   [전용 WAN 링크로 Azure Virtual Network에 온-프레미스 네트워크 연결](https://docs.microsoft.com/azure/expressroute/expressroute-introduction)

-   [Azure Virtual Networks 상호 연결](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-vnet-vnet-rm-ps)

### <a name="vpn-gateway"></a>VPN Gateway
Azure Virtual Network와 온-프레미스 사이트 간에 네트워크 트래픽을 보내려면 Azure Virtual Network에 대한 가상 VPN Gateway를 만들어야 합니다. [VPN Gateway](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)는 공용 연결을 통해 암호화된 트래픽을 보내는 가상 네트워크 게이트웨이의 유형입니다. 또한 VPN Gateway를 사용하여 Microsoft 네트워크 패브릭을 통해 Azure Virtual Network 간에 트래픽을 보낼 수도 있습니다.

### <a name="express-route"></a>Express 경로
Microsoft Azure [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction)는 연결 공급자로 쉽게 처리된 전용 개인 연결을 통해 온-프레미스 네트워크를 Microsoft 클라우드로 확장할 수 있는 전용 WAN 링크입니다.

![Express 경로](./media/azure-security/azure-security-fig1.png)

ExpressRoute를 사용하면 Microsoft Azure, Office 365, CRM Online과 같은 Microsoft 클라우드 서비스에 대한 연결을 설정하거나, 공동 배치 시설에서 연결 공급자를 통해 임의의(IP VPN) 네트워크, 지점간 이더넷 네트워크 또는 가상 간 연결에서 연결할 수 있습니다.

ExpressRoute 연결은 공용 인터넷을 거치지 않으므로 VPN 기반 솔루션보다 안전합니다. 이 기능을 사용하면 ExpressRoute 연결은 인터넷을 통한 일반 연결보다 안정적이고 속도가 빠르며 대기 시간이 짧고 보안성이 높습니다.


### <a name="application-gateway"></a>Application Gateway
Microsoft [Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction)는 [ADC(Application Delivery Controller)](https://en.wikipedia.org/wiki/Application_delivery_controller)를 서비스로 제공하여 애플리케이션에 다양한 계층 7 부하 분산 기능을 제공합니다.

![Application Gateway](./media/azure-security/azure-security-fig2.png)

Application Gateway에 CPU 집약적인 SSL 종료를 오프로드하여("SSL 오프로드"또는 "SSL 브리징"이라고도 함) 웹 팜 생산성을 최적화할 수 있습니다. 또한 들어오는 트래픽의 라운드 로빈 배포, 쿠키 기반 세션 선호도, URL 경로 기반 라우팅 및 단일 Application Gateway 뒤에 여러 웹 사이트를 호스팅할 수 있는 기능을 포함한 다른 계층 7 라우팅 기능도 제공합니다. Azure Application Gateway는 계층 7 부하 분산 장치입니다.

클라우드 또는 온-프레미스이든 상관없이 서로 다른 서버 간에 장애 조치(Failover), 성능 라우팅 HTTP 요청을 제공합니다.

애플리케이션은 HTTP 부하 분산, 쿠키 기반 세션 선호도, [SSL(Secure Sockets Layer)](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-powershell) 오프로드, 사용자 지정 상태 프로브, 다중 사이트 지원 및 기타를 포함하여 많은 ADC 기능을 제공합니다.

### <a name="web-application-firewall"></a>웹 애플리케이션 방화벽
웹 애플리케이션 방화벽은 표준 ADC 기능을 위해 애플리케이션 게이트웨이를 사용하는 웹 애플리케이션에 보호를 제공하는 [Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction)의 기능입니다. 웹 애플리케이션 방화벽은 대부분의 OWASP Top 10 일반 웹 취약점으로부터 보호함으로써 이 기능을 수행합니다.

![웹 애플리케이션 방화벽](./media/azure-security/azure-security-fig1.png)

-   SQL 삽입 공격 보호

-   명령 삽입, HTTP 요청 밀반입, HTTP 응답 분할, 원격 파일 포함 공격 등의 일반 웹 공격 보호

-   HTTP 프로토콜 위반 보호

-   누락된 호스트 사용자-에이전트 및 수락 헤더 같은 HTTP 프로토콜 이상 보호

-   보트, 크롤러 및 스캐너 방지

-   일반적인 애플리케이션 구성 오류(즉 Apache, IIS 등) 검색


웹 공격으로부터 보호하는 중앙 집중식 웹 애플리케이션 방화벽은 보안 관리가 훨씬 간단하고 침입 위협으로부터 애플리케이션을 더욱 효과적으로 보호합니다. 또한 WAF 솔루션은 각각의 웹 애플리케이션을 보호하는 대신 중앙의 위치에서 알려진 취약점에 패치를 적용하여 보다 신속하게 보안 위협에 대응할 수 있습니다. 기존 애플리케이션 게이트웨이는 웹 애플리케이션 방화벽을 통해 애플리케이션 게이트웨이로 쉽게 변환될 수 있습니다.
### <a name="traffic-manager"></a>Traffic Manager
Microsoft [Azure Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-overview)를 사용하면 여러 데이터 센터에 있는 서비스 엔드포인트에 대한 사용자 트래픽의 배포를 제어할 수 있습니다. Traffic Manager에서 지원하는 서비스 엔드포인트에는 Azure VM, Web Apps 및 클라우드 서비스가 포함됩니다. 또한 외부, Azure가 아닌 엔드포인트로 Traffic Manager를 사용할 수 있습니다. Traffic Manager는 DNS(Domain Name System)를 사용하여 클라이언트 요청을 [트래픽 라우팅 메서드](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-routing-methods) 및 엔드포인트의 상태를 기반으로 가장 적절한 엔드포인트로 리디렉션합니다.

Traffic Manager는 다양한 애플리케이션 요구 사항, 엔드포인트 상태 [모니터링](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-monitoring) 및 자동 장애 조치에 잘 맞는 트래픽 라우팅 메서드를 제공합니다. Traffic Manager는 전체 Azure 지역의 오류를 포함한, 오류에 대해 복원력을 갖습니다.
### <a name="azure-load-balancer"></a>Azure Load Balancer
[Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)는 애플리케이션에 고가용성 및 네트워크 성능을 제공합니다. 이 장치는 부하 분산 장치 집합에 정의된 서비스의 정상 인스턴스 간에 들어오는 트래픽을 분산하는 계층 4(TCP, UDP) 부하 분산 장치입니다. Azure Load Balancer는 다음과 같이 구성할 수 있습니다.

-   들어오는 인터넷 트래픽을 가상 머신에 부하 분산합니다. 이 구성을 [인터넷 연결 부하 분산](https://docs.microsoft.com/azure/load-balancer/load-balancer-internet-overview)이라고 합니다.

-   가상 네트워크의 가상 머신 간, 클라우드 서비스의 가상 머신 간, 또는 크로스-프레미스 가상 네트워크의 온-프레미스 컴퓨터와 가상 머신 간에 트래픽을 부하 분산합니다. 이 구성을 [내부 부하 분산](https://docs.microsoft.com/azure/load-balancer/load-balancer-internal-overview)이라고 합. 

- 외부 트래픽을 특정 가상 머신으로 전달

### <a name="internal-dns"></a>내부 DNS
관리 포털 또는 네트워크 구성 파일에서 VNet에 사용된 DNS 서버 목록을 관리할 수 있습니다. 고객은 각 VNet마다 최대 12개의 DNS 서버를 추가할 수 있습니다. DNS 서버를 지정할 때 고객 환경에 맞는 올바른 순서로 고객의 DNS 서버를 나열하는지 확인하는 것이 중요합니다. DNS 서버 목록은 라운드 로빈 방식으로 작동하지 않으며, 지정된 순서로 사용됩니다. 목록의 첫 번째 DNS 서버에 연결할 수 있으면 DNS 서버가 적절하게 작동하는지 여부와 관계없이 클라이언트에서 해당 DNS 서버를 사용합니다. 고객의 가상 네트워크에 대한 DNS 서버 순서를 변경하려면 목록에서 DNS 서버를 제거하고 고객이 원하는 순서대로 다시 추가합니다. DNS는 "CIA" 보안 3요소를 한 조로 묶어 가용성 측면을 지원합니다.

### <a name="azure-dns"></a>Azure DNS
[Domain Name System](https://technet.microsoft.com/library/bb629410.aspx), 즉 DNS는 웹 사이트 또는 서비스 이름을 해당 IP 주소로 변환(또는 확인)합니다. [Azure DNS](https://docs.microsoft.com/azure/dns/dns-overview)는 Microsoft Azure 인프라를 사용하여 이름 확인을 제공하는 DNS 도메인에 대한 호스팅 서비스입니다. Azure에 도메인을 호스트하면 다른 Azure 서비스와 동일한 자격 증명, API, 도구 및 대금 청구를 사용하여 DNS 레코드를 관리할 수 있습니다. DNS는 "CIA" 보안 3요소를 한 조로 묶어 가용성 측면을 지원합니다.
### <a name="azure-monitor-logs-nsgs"></a>Azure Monitor Nsg 로그
NSG에 대한 다음 진단 로그 범주를 활성화할 수 있습니다.
-   이벤트: VM 및 MAC 주소 기반 인스턴스 역할에 적용된 NSG 규칙에 대한 항목을 포함합니다. 이러한 규칙에 대한 상태는 60초마다 수집됩니다.

-   규칙 카운터: 트래픽을 허용하거나 거부하기 위해 각 NSG 규칙이 적용된 횟수에 대한 항목을 포함합니다.

### <a name="azure-security-center"></a>Azure Security Center
Security Center는 위협을 예방, 검색 및 대응하는 데 도움이 되며, Azure 리소스의 보안에 대한 향상된 가시성과 제어를 제공합니다. Azure 구독을 통해 통합된 보안 모니터링 및 정책 관리를 제공하고, 달리 발견되지 않을 수도 있는 위협을 검색하는 데 도움이 되며, 보안 솔루션의 광범위한 에코시스템에서 작동합니다. 네트워크 권장 사항은 방화벽, 네트워크 보안 그룹, 인바운드 트래픽 규칙 구성 등에 초점을 맞추고 있습니다.

사용 가능한 네트워크 권장 사항은 다음과 같습니다.

-   [차세대 방화벽 추가](https://docs.microsoft.com/azure/security-center/security-center-add-next-generation-firewall) - Microsoft 파트너의 차세대 방화벽(NGFW)을 추가하여 보안 보호를 증가시키는 것이 좋습니다.

-   [NGFW를 통해서만 트래픽 라우팅](https://docs.microsoft.com/azure/security-center/security-center-add-next-generation-firewall#route-traffic-through-ngfw-only) - NGFW를 통해 VM에 인바운드 트래픽을 강제 적용하는 NSG(네트워크 보안 그룹) 규칙을 구성하는 것이 좋습니다.

-   [서브넷 또는 가상 머신에서 네트워크 보안 그룹 사용](https://docs.microsoft.com/azure/security-center/security-center-enable-network-security-groups) - 서브넷 또는 VM에서 NSG를 사용하는 것이 좋습니다.

-   [인터넷 연결 엔드포인트를 통한 액세스 제한](https://docs.microsoft.com/azure/security-center/security-center-restrict-access-through-internet-facing-endpoints) - NSG에 대한 인바운드 트래픽 규칙을 구성하는 것이 좋습니다.


## <a name="compute"></a>컴퓨팅

이 섹션에서는 이 영역의 주요 기능에 대한 추가 정보와 이러한 기능에 대한 요약 정보를 제공합니다.

### <a name="antimalware--antivirus"></a>맬웨어 방지 및 바이러스 백신
Azure IaaS를 사용하면 Microsoft, Symantec, Trend Micro, McAfee 및 Kaspersky와 같은 보안 공급업체의 맬웨어 방지 소프트웨어를 통해 악성 파일, 애드웨어 및 기타 위협으로부터 가상 머신을 보호할 수 있습니다. Azure Cloud Services 및 Virtual Machines를 위한 [Microsoft 맬웨어 방지 프로그램](https://docs.microsoft.com/azure/security/azure-security-antimalware)은 바이러스, 스파이웨어 및 기타 악성 소프트웨어를 식별하고 제거하는 데 도움이 되는 보호 기능입니다. Microsoft 맬웨어 방지 프로그램은 알려진 악성 또는 원치 않는 소프트웨어가 Azure 시스템에서 스스로의 설치나 실행을 시도할 때 구성 가능한 경고를 제공합니다. 또한 Microsoft 맬웨어 방지 프로그램은 Azure Security Center를 통해 배포할 수도 있습니다.

### <a name="hardware-security-module"></a>하드웨어 보안 모듈
암호화 및 인증은 키 자체가 보호되지 않는 한 보안을 향상시키지 않습니다. 중요한 비밀과 키를 [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis)에 저장하여 관리와 보안을 단순화할 수 있습니다. Key Vault는 FIPS 140-2 Level 2 기준에 따라 인증된 HSM(하드웨어 보안 모듈)에 키를 저장할 수 있는 옵션을 제공합니다. 백업 또는 [투명한 데이터 암호화](https://msdn.microsoft.com/library/bb934049.aspx)를 위한 SQL Server 암호화 키는 애플리케이션의 키 또는 암호와 함께 주요 자격 증명 모음에 저장됩니다. 이러한 보호된 항목에 대한 사용 권한 및 액세스는 [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/)를 통해 관리됩니다.

### <a name="virtual-machine-backup"></a>가상 머신 백업
[Azure Backup](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup)은 자본 투자 없이 최소의 운영 비용으로 애플리케이션 데이터를 보호하는 솔루션입니다. 애플리케이션 오류로 인해 데이터가 손상될 수 있으며, 사용자 실수로 인해 애플리케이션에 버그가 발생하여 보안 문제가 발생할 수 있습니다. Azure Backup은 Windows 및 Linux를 실행하는 가상 머신의 보호에 도움이 됩니다.

### <a name="azure-site-recovery"></a>Azure Site Recovery
계획되거나 계획되지 않은 중단 상태가 발생하는 경우 조직 [BCDR(비즈니스 연속성/재해 복구)](https://docs.microsoft.com/azure/best-practices-availability-paired-regions) 전략의 중요한 부분은 회사 워크로드 및 앱을 가동 및 실행하도록 유지하는 방법을 파악하는 것입니다. [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview)를 사용하면 기본 위치가 중단되는 경우 보조 위치에서 사용할 수 있도록 워크로드 및 앱의 복제, 장애 조치 및 복구를 오케스트레이션할 수 있습니다.

### <a name="sql-vm-tde"></a>SQL VM TDE
[TDE(투명한 데이터 암호화)](https://docs.microsoft.com/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-ps-sql-keyvault) 및 CLE(열 수준 암호화)는 SQL Server 암호화 기능입니다. 이러한 형태의 암호화를 사용하려면 고객이 암호화에 사용되는 암호화 키를 관리하고 저장해야 합니다.

AKV(Azure Key Vault) 서비스는 안전하고 가용성이 높은 위치에서 이러한 키의 보안 및 관리를 개선하도록 설계되었습니다. SQL Server 커넥터는 SQL Server에서 Azure Key Vault의 키를 사용할 수 있게 합니다.

온-프레미스 컴퓨터에서 SQL Server를 실행하는 경우 온-프레미스 SQL Server 컴퓨터에서 Azure Key Vault에 액세스할 수 있는 단계가 있습니다. 하지만 Azure VM의 SQL Server에서는 Azure Key Vault 통합 기능을 사용하여 시간을 절약할 수 있습니다. 이 기능을 지원하는 Azure PowerShell cmdlet 몇 개만 있으면 SQL VM이 키 자격 증명 모음에 액세스하는 데 필요한 구성을 자동화할 수 있습니다.

### <a name="vm-disk-encryption"></a>VM 디스크 암호화
[Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption)은 Windows 및 Linux IaaS 가상 머신 디스크를 암호화할 수 있게 하는 새로운 기능입니다. Windows의 업계 표준 BitLocker 기능과 Linux의 DM-Crypt 기능을 적용하여 OS 및 데이터 디스크에 볼륨 암호화를 제공합니다. 이 솔루션은 Azure Key Vault와 통합되어 Key Vault 구독에서 디스크 암호화 키와 비밀을 제어하고 관리 할 수 있습니다. 이 솔루션은 가상 머신 디스크에 있는 모든 데이터가 미사용 시 Azure Storage에 암호화되도록 합니다.

### <a name="virtual-networking"></a>가상 네트워킹
가상 머신은 네트워크 연결이 필요합니다. 이 요구 사항을 지원하기 위해 Azure에서는 가상 머신을 Azure Virtual Network에 연결해야 합니다. Azure Virtual Network는 물리적 Azure 네트워크 패브릭 위에 구축되는 논리적 구조체입니다. 논리적 [Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) 각각은 다른 모든 Azure Virtual Network와 격리됩니다. 이 격리를 통해 사용자 배포의 네트워크 트래픽이 다른 Microsoft Azure 고객에게 액세스되지 않게 해줍니다.

### <a name="patch-updates"></a>패치 업데이트
패치 업데이트는 기업에서 배포해야 하는 소프트웨어 업데이트의 수를 줄이고 규정 준수를 모니터링하는 기능을 향상시킴으로써 잠재적인 문제를 찾아 수정하고 소프트웨어 업데이트 관리 프로세스를 간소화할 수 있는 기반을 제공합니다.

### <a name="security-policy-management-and-reporting"></a>보안 정책 관리 및 보고
[Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)는 위협을 예방, 검색 및 대응하는 데 도움이 되며, Azure 리소스의 보안에 대한 향상된 가시성과 제어를 제공합니다. Azure 구독을 통해 통합된 보안 모니터링 및 정책 관리를 제공하고, 달리 발견되지 않을 수도 있는 위협을 검색하는 데 도움이 되며, 보안 솔루션의 광범위한 에코시스템에서 작동합니다.

### <a name="azure-security-center"></a>Azure Security Center
보안 센터는 Azure 리소스의 보안에 대한 향상된 가시성과 제어권을 통해 위협을 예방하고 감지하며 위협에 대응하는 데 도움이 됩니다. 이는 Azure 구독에 대해 통합된 보안 모니터링 및 정책 관리를 제공하고 다른 방법으로 발견되지 않을 수 있는 위협을 감지하는 데 도움이 되며 보안 솔루션의 광범위한 환경에서 작동합니다.

## <a name="identity-and-access-management"></a>ID 및 액세스 관리

시스템, 애플리케이션 및 데이터 보안은 ID 기반 액세스 제어로 시작합니다. Microsoft 비즈니스 제품 및 서비스에 기본 제공되는 ID 및 액세스 관리 기능을 사용하면, 권한이 없는 액세스로부터 조직 및 개인 정보를 보호하는 한편, 합법적인 사용자가 필요할 때마다 어디서나 사용할 수 있습니다.

### <a name="secure-identity"></a>보안 ID
Microsoft는 제품 및 서비스 전반에 여러 가지 보안 사례와 기술을 사용하여 ID와 액세스를 관리합니다.
-   [Multi-Factor Authentication](https://azure.microsoft.com/services/multi-factor-authentication/) - 사용자가 액세스, 온-프레미스 및 클라우드에서 여러 가지 방법을 사용해야 합니다. 간단한 로그인 프로세스를 통해 사용자를 수용하면서 편리한 확인 옵션을 통해 강력한 인증을 제공합니다.

-   [Microsoft Authenticator](https://aka.ms/authenticator) - Microsoft Azure Active Directory 및 Microsoft 계정 모두에서 작동하는 친숙한 Multi-Factor Authentication 환경을 제공하고, 착용식 장치 및 지문 기반 승인을 지원합니다.

-   [암호 정책 적용](https://azure.microsoft.com/documentation/articles/active-directory-passwords-policy/) - 길이 및 복잡성 요구 사항, 정기적 강제 회전, 실패한 인증 시도 후 계정 잠금을 강제 적용하여 기존 암호의 보안을 강화합니다.

-   [토큰 기반 인증](https://azure.microsoft.com/documentation/articles/active-directory-authentication-scenarios/)을 사용하여 Azure Active Directory를 통한 인증을 설정할 수 있습니다.

-   [RBAC(역할 기반 액세스 제어)](https://azure.microsoft.com/documentation/articles/role-based-access-built-in-roles/) - 사용자에게 할당된 역할에 따라 액세스 권한을 부여함으로써 사용자에게 자신의 업무를 수행하는 데 필요한 액세스 권한만 부여할 수 있게 합니다. 조직의 비즈니스 모델 및 위험 허용 범위에 따라 RBAC를 사용자 지정할 수 있습니다.

-   [통합 ID 관리(하이브리드 ID)](https://azure.microsoft.com/documentation/articles/active-directory-hybrid-identity-design-considerations-overview/) - 모든 리소스에 대한 인증 및 권한 부여를 위한 단일 사용자 ID를 만들어 내부 데이터 센터 및 클라우드 플랫폼에서 사용자 액세스에 대한 제어를 유지할 수 있게 합니다.

### <a name="secure-apps-and-data"></a>보안 앱 및 데이터
포괄적인 ID 및 액세스 관리 클라우드 솔루션인 [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)는 사이트 및 클라우드의 애플리케이션에서 데이터에 대한 보안 액세스를 지원하고, 사용자 및 그룹 관리를 간소화합니다. 핵심 디렉터리 서비스, 고급 ID 관리, 보안 및 애플리케이션 액세스 관리를 결합하고, 개발자가 정책 기반 ID 관리를 자신의 앱에 쉽게 만들 수 있습니다. Azure Active Directory를 강화하려면 Azure Active Directory Basic, Premium P1 및 Premium P2 Edition을 사용하여 유료 기능을 추가할 수 있습니다.

| 평가판/일반 기능     | Basic 기능    |Premium P1 기능 |Premium P2 기능 | Azure Active Directory 조인 – Windows 10 전용 관련 기능|
| :------------- | :------------- |:------------- |:------------- |:------------- |
|   [디렉터리 개체](https://docs.microsoft.com/azure/active-directory/active-directory-editions), [사용자/그룹 관리(추가/업데이트/삭제)/사용자 기반 프로비전, 디바이스 등록](https://docs.microsoft.com/azure/active-directory/active-directory-editions), [SSO(Single Sign-On)](https://docs.microsoft.com/azure/active-directory/active-directory-editions), [클라우드 사용자를 위한 셀프 서비스 암호 변경](https://docs.microsoft.com/azure/active-directory/active-directory-editions), [연결(온-프레미스 디렉터리를 Azure Active Directory로 확장하는 동기화 엔진)](https://docs.microsoft.com/azure/active-directory/active-directory-editions), [보안/사용량 보고서](https://docs.microsoft.com/azure/active-directory/active-directory-editions)       |     [그룹 기반 액세스 관리/프로비전](https://docs.microsoft.com/azure/active-directory/active-directory-editions), [클라우드 사용자를 위한 셀프 서비스 암호 재설정](https://docs.microsoft.com/azure/active-directory/active-directory-editions), [회사 브랜딩(로그온 페이지/액세스 패널 사용자 지정)](https://docs.microsoft.com/azure/active-directory/active-directory-editions), [애플리케이션 프록시](https://docs.microsoft.com/azure/active-directory/active-directory-editions), [SLA 99.9%](https://docs.microsoft.com/azure/active-directory/active-directory-editions) |  [셀프 서비스 그룹 및 앱 관리/셀프 서비스 애플리케이션 추가/동적 그룹](https://docs.microsoft.com/azure/active-directory/active-directory-editions), [셀프 서비스 암호 재설정/변경/온-프레미스 쓰기 저장을 통한 잠금 해제](https://docs.microsoft.com/azure/active-directory/active-directory-editions), [Multi-Factor Authentication(클라우드 및 온-프레미스(MFA 서버))](https://docs.microsoft.com/azure/active-directory/active-directory-editions), [MIM CAL + MIM 서버](https://docs.microsoft.com/azure/active-directory/active-directory-editions), [Cloud App Discovery](https://docs.microsoft.com/azure/active-directory/active-directory-editions), [Connect Health](https://docs.microsoft.com/azure/active-directory/active-directory-editions), [그룹 계정에 대한 자동 암호 롤오버](https://docs.microsoft.com/azure/active-directory/active-directory-editions)|  [ID 보호](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection), [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure)|  [디바이스를 Azure AD, 데스크톱 SSO, Azure AD용 Microsoft Passport, 관리자 BitLocker 복구에 조인](https://docs.microsoft.com/azure/active-directory/active-directory-editions), [MDM 자동 등록, 셀프 서비스 BitLocker 복구, Azure AD 조인을 통해 Windows 10 디바이스에 로컬 관리자 추가](https://docs.microsoft.com/azure/active-directory/active-directory-editions)|


- [클라우드 앱 검색](https://docs.microsoft.com/azure/active-directory/active-directory-cloudappdiscovery-whatis) - 조직의 직원이 사용하는 클라우드 애플리케이션을 식별할 수 있게 하는 Azure Active Directory의 프리미엄 기능입니다.

- [Azure Active Directory ID 보호](https://azure.microsoft.com/documentation/articles/active-directory-identityprotection/) - Azure Active Directory 변칙 검색 기능을 사용하여 조직의 ID에 영향을 줄 수 있는 위험 이벤트 및 잠재적 취약성에 대한 통합된 보기를 제공하는 보안 서비스입니다.

- [Azure Active Directory Domain Services](https://azure.microsoft.com/services/active-directory-ds/) - 도메인 컨트롤러를 배포하지 않아도 Azure VM을 도메인에 조인할 수 있게 합니다. 사용자는 회사 Active Directory 자격 증명을 사용하여 이러한 VM에 로그인하고 리소스에 원활하게 액세스할 수 있습니다.

- [Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) - 수백만 개의 ID로 확장하고 모바일 및 웹 플랫폼에 통합할 수 있는 소비자 지향 응용 프로그램을 위한 전역적인 고가용성 ID 관리 서비스입니다. 고객은 기존 소셜 미디어 계정을 사용하는 사용자 지정 가능한 환경을 통해 모든 앱에 로그인하거나 새 독립 실행 형 자격 증명을 만들 수 있습니다.

- [Azure Active Directory B2B 공동 작업](https://aka.ms/aad-b2b-collaboration) - 파트너가 자체 관리 ID를 사용하여 회사 애플리케이션 및 데이터에 선택적으로 액세스할 수 있게 하여 회사 간 관계를 지원하는 안전한 파트너 통합 솔루션입니다.

- [Azure Active Directory 조인](https://azure.microsoft.com/documentation/articles/active-directory-azureadjoin-overview/) - 중앙 집중식 관리를 위해 클라우드 기능을 Windows 10 디바이스로 확장할 수 있게 합니다. 사용자가 Azure Active Directory를 통해 회사 또는 조직의 클라우드에 연결할 수 있게 하며, 앱과 리소스에 대한 액세스를 간소화합니다.

- [Azure Active Directory 애플리케이션 프록시](https://azure.microsoft.com/documentation/articles/active-directory-application-proxy-get-started/) - 온-프레미스에 호스팅되는 웹 애플리케이션에 대해 SSO(Single Sign-On) 및 보안된 원격 액세스를 제공합니다.

## <a name="next-steps"></a>다음 단계
- [Microsoft Azure 보안 시작](https://docs.microsoft.com/azure/security/azure-security-getting-started)

Azure 내에서 서비스와 데이터를 보호 하기 위해 사용할 수 있는 Azure 서비스 및 기능

- [Azure Security Center](https://azure.microsoft.com/services/security-center/)

Azure 리소스의 보안에 대한 향상된 가시성과 제어를 통해 위협을 예방, 검색 및 대응합니다.

- [Azure Security Center에서 보안 상태 모니터링](https://docs.microsoft.com/azure/security-center/security-center-monitoring)

Azure Security Center의 모니터링 기능을 사용하여 정책 준수를 모니터링합니다.
