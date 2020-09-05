---
title: 계획 보고서 & 배포 모니터링-Azure AD
description: 보고 및 모니터링의 implmentation을 계획 하 고 실행 하는 방법을 설명 합니다.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: baselden
ms.reviewer: plenzke
ms.collection: M365-identity-device-management
ms.openlocfilehash: 20b83291dc37c6248761214654f99d3ce214b551
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89229755"
---
# <a name="plan-an-azure-active-directory-reporting-and-monitoring-deployment"></a>Azure Active Directory 보고 및 모니터링 배포 계획

사용자의 Azure Active Directory (Azure AD) 보고 및 모니터링 솔루션은 법적, 보안 및 운영 요구 사항과 기존 환경 및 프로세스에 따라 달라 집니다. 이 문서에서는 다양 한 디자인 옵션을 제공 하 고 올바른 배포 전략에 대 한 지침을 제공 합니다.

### <a name="benefits-of-azure-ad-reporting-and-monitoring"></a>Azure AD 보고 및 모니터링의 이점

Azure AD reporting은 로그인 이벤트, 감사 이벤트 및 디렉터리 변경을 포함 하 여 사용자 환경에서 Azure AD 활동의 포괄적인 보기 및 로그를 제공 합니다.

제공된 데이터를 통해 다음을 수행할 수 있습니다.

* 앱과 서비스를 사용 하는 방법을 결정 합니다.

* 환경의 상태에 영향을 주는 잠재적 위험을 감지 합니다.

* 사용자가 작업을 수행 하지 못하게 하는 문제를 해결 합니다.

* Azure AD 디렉터리의 변경 내용에 대 한 감사 이벤트를 확인 하 여 통찰력을 얻습니다.

> [!IMPORTANT]
> Azure AD 모니터링을 사용 하면 Azure AD reporting에 의해 생성 된 로그를 다른 대상 시스템으로 라우팅할 수 있습니다. 그런 다음, 활동 로그를 장기간 사용할 수 있도록 보존할 수도 있고, 타사 SIEM(보안 정보 및 이벤트 관리) 도구와 통합하여 환경에 대한 인사이트를 얻을 수도 있습니다.

Azure AD 모니터링을 사용 하면 로그를 다음으로 라우팅할 수 있습니다.

* 보관을 위해 Azure 저장소 계정.
* Azure Monitor 로그 (이전에는 Azure Log Analytics 작업 영역 이라고 함)-데이터를 분석 하 고 대시보드를 만들고 특정 이벤트에 대해 경고할 수 있습니다.
* Splunk, Sumologic 또는 QRadar과 같은 기존 SIEM 도구와 통합할 수 있는 Azure 이벤트 허브.

> [!NOTE]
최근 Log Analytics 대신 Azure Monitor 로그 라는 용어를 사용 하기 시작 했습니다. 로그 데이터는 여전히 Log Analytics 작업 영역에 저장되며 동일한 Log Analytics 서비스에 의해 계속 수집 및 분석됩니다. [Azure Monitor에서 로그](../../azure-monitor/platform/data-platform.md)의 역할을 보다 잘 반영하기 위해 용어를 업데이트하고 있습니다. 자세한 내용은 [Azure Monitor 용어 변경](../../azure-monitor/terminology.md)을 참조하세요.

[보고서 보존 정책에 대해 자세히 알아보세요](./reference-reports-data-retention.md).

### <a name="licensing-and-prerequisites-for-azure-ad-reporting-and-monitoring"></a>Azure AD 보고 및 모니터링을 위한 라이선스 및 필수 조건

Azure AD 로그인 로그에 액세스 하려면 Azure AD premium 라이선스가 필요 합니다.

자세한 기능 및 라이선스 정보는 [Azure Active Directory 가격 책정 가이드를 참조](https://azure.microsoft.com/pricing/details/active-directory/)하세요.

Azure AD 모니터링 및 보고를 배포 하려면 Azure AD 테 넌 트의 전역 관리자 또는 보안 관리자 인 사용자가 필요 합니다.

로그 데이터의 최종 대상에 따라 다음 중 하나가 필요 합니다.

* ListKeys 권한이 있는 Azure Storage 계정. Blob Storage 계정이 아닌 일반 스토리지 계정을 사용하는 것이 좋습니다. 스토리지 가격 책정 정보는 [Azure Storage 가격 책정 계산기](https://azure.microsoft.com/pricing/calculator/?service=storage)를 참조하세요.

* 타사 SIEM 솔루션과 통합할 Azure Event Hubs 네임 스페이스입니다.

* Azure Monitor 로그에 로그를 보내는 Azure Log Analytics 작업 영역

## <a name="plan-an-azure-reporting-and-monitoring-deployment-project"></a>Azure 보고 및 모니터링 배포 프로젝트 계획

이 프로젝트에서는 보고서를 사용 및 모니터링 하 고 Azure AD 모니터링 아키텍처를 정의 하는 대상을 정의 합니다.

### <a name="engage-the-right-stakeholders"></a>올바른 관련자 참여

기술 프로젝트가 실패하는 이유는 일반적으로 영향, 결과 및 책임에 대한 기대 수준이 일치하지 않기 때문입니다. 이러한 문제를 방지 하려면 [올바른 관련자에 게 관심이 있는지 확인](https://aka.ms/deploymentplans)합니다. 또한 관련자와 해당 프로젝트 입력 및 accountabilities를 문서화 하 여 프로젝트의 관련자 역할을 잘 이해 해야 합니다.

### <a name="plan-communications"></a>통신 계획

통신은 새 서비스의 성공에 대단히 중요합니다. 사용자의 경험이 어떻게 변화하고 언제 변할 것인지, 문제가 발생할 경우 지원을 받는 방법에 대해 사용자와 사전에 소통합니다.

### <a name="document-your-current-infrastructure-and-policies"></a>현재 인프라 및 정책 문서화

현재 인프라 및 정책을 통해 보고 및 모니터링 설계를 추진 합니다. 알고 있어야 합니다.

* 사용 중인 SIEM 도구 (있는 경우)

* 기존 저장소 계정 및 모니터링을 포함 하는 Azure 인프라

* 해당 하는 준수 프레임 워크를 비롯 한 로그에 대 한 조직 보존 정책. 

## <a name="plan-an-azure-ad-reporting-and-monitoring-deployment"></a>Azure AD 보고 및 모니터링 배포 계획

보고 및 모니터링은 비즈니스 요구 사항을 충족 하 고, 사용 패턴에 대 한 통찰력을 얻고, 조직 보안 상태를 높이는 데 사용 됩니다.

### <a name="business-use-cases"></a>비즈니스 사용 사례

* 비즈니스 요구를 충족 하는 솔루션에 필요 합니다.
* 비즈니스 요구를 충족 하는 데 유용 합니다.
* 해당 사항 없음

|영역 |Description |
|-|-|
|보존| **30 일 넘게 로그 보존** 법률 또는 비즈니스 요구 사항으로 인해 Azure AD의 감사 로그 및 로그인 로그를 30 일 보다 길게 저장 해야 합니다. |
|분석| **로그를 검색할 수 있어야**합니다. 저장 된 로그는 분석 도구를 사용 하 여 검색할 수 있어야 합니다. |
| Operational Insights| **다양 한 팀에 대 한 정보**입니다. 응용 프로그램 사용, 로그인 오류, 셀프 서비스 사용, 추세 등의 작업 정보를 얻기 위해 다른 사용자에 게 액세스 권한을 부여 해야 합니다. |
| 보안 정보| **다양 한 팀에 대 한 정보**입니다. 응용 프로그램 사용, 로그인 오류, 셀프 서비스 사용, 추세 등의 작업 정보를 얻기 위해 다른 사용자에 게 액세스 권한을 부여 해야 합니다. |
| SIEM 시스템의 통합      | **Siem 통합**. Azure AD 로그인 로그 및 감사 로그를 기존 SIEM 시스템에 통합 하 고 스트리밍할 필요가 있습니다. |

### <a name="choose-a-monitoring-solution-architecture"></a>모니터링 솔루션 아키텍처 선택

Azure AD 모니터링을 사용 하 여 Azure AD 활동 로그를 비즈니스 요구에 가장 잘 맞는 시스템으로 라우팅할 수 있습니다. 그런 다음 장기 보고 및 분석을 위해 유지 하 여 사용자 환경에 대 한 통찰력을 얻고 SIEM 도구와 통합할 수 있습니다.

#### <a name="decision-flow-chartan-image-showing-what-is-described-in-subsequent-sections"></a>의사 결정 흐름 차트![이후 섹션에서 설명 하는 내용을 보여 주는 이미지](media/reporting-deployment-plan/deploy-reporting-flow-diagram.png)

#### <a name="archive-logs-in-a-storage-account"></a>저장소 계정에 로그 보관

로그를 Azure storage 계정에 라우팅하여 [보존 정책](./reference-reports-data-retention.md)에 설명 된 기본 보존 기간 보다 오래 유지할 수 있습니다. 로그를 보관 해야 하지만 SIEM 시스템과 통합 하지 않아도 되 고 진행 중인 쿼리 및 분석을 수행 하지 않아도 되는 경우이 방법을 사용 합니다. 요청 시 검색을 계속 수행할 수 있습니다.

[데이터를 스토리지 계정으로 라우팅](./quickstart-azure-monitor-route-logs-to-storage-account.md)하는 방법을 알아보세요.

#### <a name="send-logs-to-azure-monitor-logs"></a>Azure Monitor 로그에 로그 보내기

[Azure Monitor 로그](../../azure-monitor/log-query/log-query-overview.md) 는 서로 다른 원본의 모니터링 데이터를 통합 합니다. 또한 응용 프로그램의 작업 및 리소스 사용에 대 한 정보를 제공 하는 쿼리 언어와 분석 엔진을 제공 합니다. Azure AD 활동 로그를 Azure Monitor 로그에 보내서 수집 된 데이터를 신속 하 게 검색, 모니터링 및 경고할 수 있습니다. 데이터를 직접 보내려는 기존 SIEM 솔루션이 없고 쿼리 및 분석을 수행 하려는 경우이 방법을 사용 합니다. 데이터가 Azure Monitor 로그에 있으면 이벤트 허브로 전송 하 고 원하는 경우 SIEM에 전송할 수 있습니다.

[데이터를 Azure Monitor 로그에 보내는 방법](./howto-integrate-activity-logs-with-log-analytics.md)에 대해 알아보세요.

Azure AD 활동 로그에 대해 미리 작성 된 보기를 설치 하 여 로그인 및 감사 이벤트와 관련 된 일반적인 시나리오를 모니터링할 수도 있습니다.

[Azure AD 활동 로그에 대한 로그 분석 보기 설치 및 사용](./howto-install-use-log-analytics-views.md) 방법에 대해 알아보세요.

#### <a name="stream-logs-to-your-azure-event-hub"></a>Azure 이벤트 허브에 로그 스트림

로그를 Azure 이벤트 허브로 라우팅하는 경우 타사 SIEM 도구와 통합할 수 있습니다. 이 통합을 통해 Azure AD 활동 로그 데이터를 SIEM에서 관리하는 다른 데이터와 결합하여 환경에 보다 풍부한 인사이트를 얻을 수 있습니다. 

[로그를 이벤트 허브로 스트리밍](//azure/active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub)하는 방법을 알아보세요.

## <a name="plan-operations-and-security-for-azure-ad-reporting-and-monitoring"></a>Azure AD 보고 및 모니터링을 위한 계획 작업 및 보안

관련자는 operational insights를 얻기 위해 Azure AD 로그에 액세스 해야 합니다. 사용자에 게 보안 팀 구성원, 내부 또는 외부 감사자, id 및 액세스 관리 운영 팀이 포함 될 가능성이 있습니다.

Azure AD 역할을 사용 하면 사용자의 역할에 따라 Azure AD 보고서를 구성 하 고 볼 수 있는 기능을 위임할 수 있습니다. 조직에서 Azure AD 보고서를 읽을 수 있는 권한이 필요한 사용자와 해당 사용자에 게 적합 한 역할을 식별 합니다. 

다음 역할은 Azure AD 보고서를 읽을 수 있습니다.

* 전역 관리자

* 보안 관리자

* 보안 판독기

* 보고서 읽기 권한자

[AZURE AD 관리 역할](../users-groups-roles/directory-assign-admin-roles.md)에 대해 자세히 알아보세요.

*계정 손상의 위험을 줄이려면 항상 최소 권한 개념을 적용*합니다. [Privileged Identity Management](../privileged-identity-management/pim-configure.md) 를 구현 하 여 조직의 보안을 강화 하는 것이 좋습니다.

##  

## <a name="deploy-azure-ad-reporting-and-monitoring"></a>Azure AD 보고 및 모니터링 배포

위의 디자인 지침을 사용 하 여 이전에 결정 한 사항에 따라이 섹션에서는 다양 한 배포 옵션에 대 한 설명서를 안내 합니다.

### <a name="consume-and-archive-azure-ad-logs"></a>Azure AD 로그 사용 및 보관

[Azure Portal에서 작업 보고서 찾기](./howto-find-activity-reports.md)

[Azure AD 로그를 Azure Storage 계정에 보관](./quickstart-azure-monitor-route-logs-to-storage-account.md)

### <a name="implement-monitoring-and-analytics"></a>모니터링 및 분석 구현

[Azure Monitor로 로그 보내기](./howto-integrate-activity-logs-with-log-analytics.md)

[Azure Active Directory용 로그 분석 보기 설치 및 사용](./howto-install-use-log-analytics-views.md)

[Azure Monitor 로그를 사용하여 Azure AD 활동 로그 분석](./howto-analyze-activity-logs-log-analytics.md)

* [Azure Monitor에서 감사 로그 스키마 해석](./reference-azure-monitor-audit-log-schema.md)

* [Azure Monitor에서 로그인 로그 스키마 해석](./reference-azure-monitor-sign-ins-log-schema.md)

 * [Azure 이벤트 허브로 Azure AD 로그 스트림](./tutorial-azure-monitor-stream-logs-to-event-hub.md)

* [Azure Monitor를 사용하여 Splunk와 Azure AD 로그 통합](./howto-integrate-activity-logs-with-splunk.md)

* [Azure Monitor를 사용하여 SumoLogic과 Azure AD 로그 통합](./howto-integrate-activity-logs-with-sumologic.md)

 

 

## <a name="next-steps"></a>다음 단계

구현 고려 사항 [Privileged Identity Management](../privileged-identity-management/pim-configure.md) 

[Azure 역할 기반 access control 구현 고려 (AZURE RBAC)](../../role-based-access-control/overview.md)