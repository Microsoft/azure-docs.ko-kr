---
title: Security Center의 사용 중지 기능 (2019 년 7 월) | Microsoft Docs
description: 이 문서는 2019 년 7 월 31 일에 만료 됩니다는 Security Center의 기능을 설명 합니다.
services: security-center
author: yoavfrancis
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.date: 4/16/2019
ms.author: yoafr
ms.openlocfilehash: 1d364da9506124a35c724209c68ff72db4243e80
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/24/2019
ms.locfileid: "67341564"
---
# <a name="retirement-of-security-center-features-july-2019"></a>Security Center의 사용 중지 기능 (2019 년 7 월)

여러 만들었습니다 [개선 사항](https://azure.microsoft.com/updates/?product=security-center) 는 지난 6 달 동안 Azure Security Center에 있습니다.
이러한 향상 된 기능을 사용 하 여 2019 년 7 월 31 일에 일부 중복 기능 및 관련된 Api Security Center에서를 제거 하는 것입니다.  

Azure Security Center 및 Azure Log Analytics에 새 기능을 사용 하 여 대부분의 이러한 사용 되지 않는 기능을 대체할 수 있습니다. 사용 하 여 다른 기능을 구현할 수 있습니다 [Azure (미리 보기) Sentinel](https://azure.microsoft.com/services/azure-sentinel/)합니다.

Security Center 기능 사용 중지에 다음과 같습니다.

- [이벤트 대시보드](#menu_events)
- [검색 메뉴 항목](#menu_search)
- [클래식 Id 및 액세스 링크 보기에서 Id 및 액세스 (미리 보기)](#menu_classicidentity)
- [보안 이벤트에서 보안 경고 맵 (미리 보기) 단추를 매핑합니다.](#menu_securityeventsmap)
- [사용자 지정 경고 규칙 (미리 보기)](#menu_customalerts)
- [단추 위협 보호 보안 경고를 조사 합니다.](#menu_investigate)
- [보안 솔루션의 하위 집합](#menu_solutions)
- [보안 정책에 대 한 보안 구성 편집](#menu_securityconfigurations)
- [Log Analytics 작업 영역에 대 한 보안 및 감사 대시보드 (원래 OMS 포털에서 사용)](#menu_securityomsdashboard)

이 문서에서는 대체 기능을 구현 하기 위해 취할 수 사용 중지 된 각 기능 및 단계에 대 한 자세한 정보를 제공 합니다.

## 이벤트 대시보드<a name="menu_events"></a>

Security Center는 컴퓨터에서 다양 한 보안 관련 구성과 이벤트를 수집 하려면 Microsoft Monitoring Agent를 사용 합니다. 작업 영역에 이러한 이벤트를 저장 합니다. 합니다 [이벤트 대시보드](https://docs.microsoft.com/azure/security-center/security-center-events-dashboard) 이 데이터를 볼 수 있습니다 하 고 Log Analytics에는 진입점을 제공 합니다.

작업 영역을 선택 하면 표시 되는 이벤트 대시보드를 사용 중지 하는 것:

![이벤트 대시보드][2]

### <a name="events-dashboard---the-new-experience"></a>이벤트 대시보드-새로운 환경

에서는 작업 영역에서 주목할 만한 이벤트를 보려면 Azure Log Analytics의 기본 기능을 사용 하는 것이 좋습니다.

Security Center에서 사용자 지정 주요 이벤트를 만든 경우 액세스할 수 됩니다. Log Analytics로 이동 **작업 영역 선택** > **저장 된 검색**합니다. 데이터를 손실 하거나 수정할 수 없습니다. 네이티브 주목할 만한 이벤트를 Log Analytics에서 동일한 화면에서 사용할 수 있습니다.

![작업 영역 저장 된 검색][3]

## 검색 메뉴 항목<a name="menu_search"></a>

Azure Security Center는 현재 검색 및 보안 데이터를 분석 하려면 Azure Monitor의 로그 검색을 사용 합니다. 이 화면 Log Analytics 검색 페이지를 창으로 역할 및 사용자가 선택한 작업 영역에서 검색 쿼리를 실행할 수 있습니다. 자세한 내용은 [Azure Security Center 검색](https://docs.microsoft.com/azure/security-center/security-center-search)합니다. 이 검색 창을 사용 중지 하는 것:

![검색 페이지][4]

### <a name="search-menu-entry---the-new-experience"></a>검색 메뉴 항목-새로운 환경

작업 영역에서 검색 쿼리를 수행 하려면 기본 Azure Log Analytics 기능을 사용 하는 것이 좋습니다. Azure Log Analytics로 이동 및 선택 **로그**합니다.

![Log Analytics 로그 페이지][5]

## 클래식 Id 및 액세스 (미리 보기)<a name="menu_classicidentity"></a>

현재 Security Center에서 클래식 Id 및 액세스 환경을 Log Analytics의 대시보드 id 및 액세스 정보를 보여 줍니다. 이 대시보드를 보려면:

1. 선택 **클래식 Id 및 액세스 보기**합니다.

   ![Id 페이지][6]

1. 보기는 **Id 및 액세스 대시보드**합니다.

    ![Id 페이지-작업 영역 선택][7]

1. 열려면 작업 영역을 선택 합니다 **Id 및 액세스** id 보려면 작업 영역에 대 한 정보를 액세스 하는 Log Analytics에서 대시보드.

   ![Id 페이지-대시보드][8]

이전 단계에서 표시 하는 모든 세 개의 화면이 사용 중지 하는 것입니다. 데이터는 Log Analytics 보안 솔루션에서 사용할 수 있는 남아 수정 하거나 제거할 수 없습니다.

### <a name="classic-identity--access-preview---the-new-experience"></a>클래식 Id 및 액세스 (미리 보기)-새로운 환경

Log Analytics 대시보드를 사용 하는 단일 작업 영역에서 insights 보여 주었습니다. 그러나 기본 Security Center 기능 모든 구독 및 연결 된 모든 작업 영역에 대 한 가시성을 제공 합니다. 쉽게 액세스할 수 있습니다-수 있는 뷰를 사용 하도록 집중 중요 한 권장 사항과 해당 보안 점수에 따라 순위가 지정 합니다.

모든 기능을 **Id 및 액세스** Log Analytics에서 대시보드를 선택 하 여 연락할 수 있습니다 **Id 및 액세스 (미리 보기)** Security Center에서.

![Id 페이지-클래식 경험 사용 중지][9]

## 보안 이벤트 맵<a name="menu_securityeventsmap"></a>

Security Center를 사용 하면 사용 하 여는 [보안 경고 맵](https://docs.microsoft.com/azure/security-center/security-center-threat-intel) 보안 위협을 식별할 수 있도록 합니다. 합니다 **보안 이벤트 맵으로 이동** 단추에 매핑되는 선택한 작업 영역에서 원시 보안 이벤트를 볼 수 있도록 대시보드를 엽니다.

제거 하는 것은 **보안 이벤트 맵으로 이동** 단추 및 당 작업 영역 대시보드.

![보안 경고 맵-단추][10]

선택 하는 경우는 **보안 이벤트 맵으로 이동** 단추를 위협 인텔리전스 대시보드를 엽니다. 위협 인텔리전스 대시보드에 사용 중지 하는 것입니다.  

![위협 인텔리전스 대시보드][11]

위협 인텔리전스 대시보드를 볼 작업 영역을 선택 하면 Log Analytics에서 보안 경고 맵 (미리 보기) 화면을 엽니다. 이 화면 사용 중지 하는 것입니다.

![Log Analytics에서 보안 경고 맵][12]

기존 데이터 Log Analytics 보안 솔루션에서 사용할 수 있는 남아 수정도 제거할 수 없습니다.

### <a name="security-events-map---the-new-experience"></a>보안 이벤트 맵-새로운 환경

Security Center에 기본 제공 경고 맵 기능을 사용 하는 것이 좋습니다. **보안 경고 맵 (미리 보기)** 합니다. 이 기능은 최적화 된 환경을 제공 하 고 모든 구독 및 연결 된 작업 영역에서 작동 합니다. 환경에서 상위 수준 보기를 제공 하 고 단일 작업 영역에 집중 하지 않습니다.

## 사용자 지정 경고 규칙 (미리 보기)<a name="menu_customalerts"></a>

우리가 [경고 환경 사용자 지정을 사용 중지](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) 2019 년 6 월 30 일에 있으므로 기본 인프라 사용을 중지 합니다. 그때까지 기존 사용자 지정 경고 규칙을 편집할 수 있지만 새로 추가할 수 없는 합니다. 사용 중지 날짜를 다음 정의 된 모든 사용자 지정 경고 적용 되지 않습니다 하 고 이러한 규칙을 기반으로 하는 보안 경고를 생성 되지 않습니다.
사용할 수 있도록 하는 것이 좋습니다 [Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/) 하 고 있는 사용자 지정 경고를 다시 만듭니다. 또는 Azure Monitor의 로그 경고를 사용 하 여 경고를 만들 수 있습니다.

기존 경고를 유지 및 Azure Sentinel 만듭니다.

1. [Azure Sentinel 엽니다](https://portal.azure.com/#create/Microsoft.ASI/preview) 사용자 지정 경고를 저장할 작업 영역 선택
1. 선택 **Analytics** 메뉴에서
1. 다음 지침을 따릅니다 [자습서](https://docs.microsoft.com/azure/sentinel/tutorial-detect-threats) Azure Sentinel 사용자 지정 경고를 생성 하는 방법에

Azure Sentinel 사용에 관심이 있는 모르는 경우에 Azure Monitor의 로그 경고를 사용 하 여 경고를 만들 수 있습니다. 지침은 [만들기, 보기 및 Azure Monitor를 사용 하 여 로그 경고를 관리](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log) 하 고 [Azure Monitor의 로그 경고](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log)합니다.

![사용자 지정 경고][13]

사용자 지정 경고 사용 중지에 대 한 자세한 내용은 참조 하세요. [Azure 보안 센터 (미리 보기)에서 사용자 지정 경고 규칙](https://docs.microsoft.com/azure/security-center/security-center-custom-alert)합니다.

## 보안 경고 조사<a name="menu_investigate"></a>

[조사 기능](https://docs.microsoft.com/azure/security-center/security-center-investigation) 잠재적인 보안 인시던트를 심사 하에서 Security Center는 데 도움이 됩니다. 기능을 사용 하면 인시던트에 대 한 범위를 파악 하 고 근본 원인을 추적할 수 있습니다. 향상된 된 환경을 사용 하 여 바뀝니다 때문에 Security Center에서이 기능을 제거 하는 것 [Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/)합니다.

![보안 인시던트][14]

선택 하는 경우는 **조사** 에서 단추를 **보안 인시던트** 화면에서 Log Analytics에서 조사 대시보드 (미리 보기)를 엽니다. 조사 대시보드를 사용 중지 하는 것입니다.  

기존 데이터 Log Analytics 보안 솔루션에서 사용할 수 있는 남아 수정도 제거할 수 없습니다.

![Log Analytics를 조사 대시보드][15]

### <a name="investigation---the-new-experience"></a>조사-새로운 환경

전환 좋습니다 [Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/) 풍부한 조사 환경에 대 한 합니다. Azure Sentinel 조직의 데이터 원본에서 보안 위협에 대 한 hunt에 강력한 검색 및 쿼리 도구를 제공 합니다.  

## 보안 솔루션의 하위 집합<a name="menu_solutions"></a>

Security Center를 사용 하도록 설정할 수 [Azure의 보안 솔루션 통합](https://docs.microsoft.com/azure/security-center/security-center-partner-integration)합니다. Security Center에서 다음 파트너 솔루션 사용을 중지 하는 것입니다. 이러한 솔루션에서 사용 될 [Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/) 추가 데이터 원본의 수와 함께 합니다.

- [다음 세대 방화벽 및 웹 응용 프로그램 방화벽 솔루션](https://docs.microsoft.com/azure/sentinel/connect-data-sources)
- [일반적인 이벤트 형식 (CEF)를 지 원하는 보안 솔루션 통합](https://docs.microsoft.com/azure/sentinel/connect-common-event-format)
- [Microsoft Advanced Threat Analytics](https://docs.microsoft.com/azure/sentinel/connect-azure-atp)
- [Azure AD ID 보호](https://docs.microsoft.com/azure/sentinel/connect-azure-ad-identity-protection)

사용 중지 한 후 추가 하거나 UI 또는 API에서 위의 목록에서 언급 한 솔루션 형식 중 하나를 수정할 수 없습니다.

기존 연결 된 솔루션에 있는 경우 좋습니다 Azure Sentinel을 이동할 수 있습니다.

![보안 솔루션 센터][16]

## 보안 정책에 대 한 보안 구성 편집<a name="menu_securityconfigurations"></a>

Azure Security Center의 집합을 적용 하 여 보안 구성을 모니터링 [150 개 이상의 권장 규칙](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335)합니다. OS를 강화 합니다. 이러한 규칙 방화벽, 감사, 암호 정책 및 더 관련이 있습니다. 컴퓨터에 취약한 구성이 있는 것으로 확인되면 Security Center에서 보안 권장 사항이 생성됩니다. 합니다 [보안 구성 편집 화면](https://docs.microsoft.com/azure/security-center/security-center-customize-os-security-config) 고객은 Security Center에서 기본 OS 보안 구성을 사용자 지정할 수 있습니다.

이 미리 보기 기능 사용을 중지 하는 것입니다. 사용 중지 날짜 이후에 원하는 보안 구성을 기본값으로 다시 설정 하려면 경우 해도 사용 하 여 API 또는 Powershell을 통해는 [지침](https://aka.ms/ascresetsecurityconfigurations)

![보안 구성 편집][17]

### <a name="edit-security-configurations---the-new-experience"></a>새 환경 보안 구성 편집

지원 하기 위해 Security Center를 사용 하도록 설정 하려고 합니다 [게스트 구성 에이전트가](https://docs.microsoft.com/azure/governance/policy/concepts/guest-configuration)합니다. 이러한 업데이트는 더 많은 운영 체제 및 게스트 구성에 대 한 Azure 게스트 내 정책의 통합에 대 한 지원을 비롯 하 여 보다 다양 한 기능 집합을 수 있습니다. 이러한 변경 내용을 사용 하도록 설정 규모의 구성을 제어 하 고 자동으로 새 리소스에 적용할 수가 있습니다.

## Log Analytics 작업 영역에 대 한 보안 및 감사 대시보드<a name="menu_securityomsdashboard"></a>

보안 및 감사 대시보드는 OMS 포털에서 원래 사용 되었습니다. Log Analytics 대시보드 주목할 만한 보안 이벤트 및 위협, 위협 인텔리전스 맵 및 작업 영역에 저장 하는 보안 이벤트의 id 및 액세스 평가 당 작업 영역 개요를 제공 합니다. 대시보드를 제거 하는 것입니다. 이미 대시보드 UI의에서 권장 것이 좋습니다는 Azure Security Center를 전환할 수 있습니다.

![Log Analytics 보안 대시보드][18]

### <a name="security-and-audit-dashboard---the-new-experience"></a>보안 및 감사 대시보드-새로운 환경

Azure Security Center로 전환 하는 것이 좋습니다. 여러 구독에서 동일한 보안 개요를 제공 및 작업 영역 그와 관련 된 다양 한 기능 집합입니다.

채우기의 보안 및 감사 대시보드는 원래 Log Analytics 쿼리를 가져올 수 있습니다 합니다 [GitHub 리포지토리](https://github.com/Azure/Azure-Security-Center/tree/master/Legacy%20Log%20Analytics%20dashboards) Security Center에 대 한 합니다.

## <a name="next-steps"></a>다음 단계

- [Azure Security Center](https://docs.microsoft.com/azure/security-center/)에 대해 자세히 알아봅니다.
- 에 대해 자세히 알아보세요 [Azure Sentinel](https://docs.microsoft.com/azure/sentinel)합니다.

<!--Image references - events-->
[1]: ./media/security-center-features-retirement-july2019/asc_events_dashboard.png
[2]: ./media/security-center-features-retirement-july2019/asc_events_dashboard_inner.png
[3]: ./media/security-center-features-retirement-july2019/workspace_saved_searches.png
<!--Image references - search-->
[4]: ./media/security-center-features-retirement-july2019/asc_search.png
[5]: ./media/security-center-features-retirement-july2019/workspace_logs.png
<!--Image references - classic identity and access-->
[6]: ./media/security-center-features-retirement-july2019/asc_identity.png
[7]: ./media/security-center-features-retirement-july2019/asc_identity_workspace_selection.png
[8]: ./media/security-center-features-retirement-july2019/loganalytics_dashboard_identity.png
[9]: ./media/security-center-features-retirement-july2019/asc_identity_nobuttonhighlight.png
<!--Image references - alerts map-->
[10]: ./media/security-center-features-retirement-july2019/asc_security_alerts_map.png
[11]: ./media/security-center-features-retirement-july2019/asc_threat_intellignece_dashboard.png
[12]: ./media/security-center-features-retirement-july2019/loganalytics_security_alerts_map.png
<!--Image references - custom alerts-->
[13]: ./media/security-center-features-retirement-july2019/asc_custom_alerts.png
<!--Image references - Investigation-->
[14]: ./media/security-center-features-retirement-july2019/asc-security-incident.png
[15]: ./media/security-center-features-retirement-july2019/loganalytics_investigation_dashboard.png
<!--Image references - Solutions-->
[16]: ./media/security-center-features-retirement-july2019/asc_security_solutions.png
<!--Image references - Edit security configurations-->
[17]: ./media/security-center-features-retirement-july2019/asc_edit_security_configurations.png
<!--Image references - Security dashboard in log analytics-->
[18]: ./media/security-center-features-retirement-july2019/loganalytics_security_dashboard.png
