---
title: 서비스 공급자의 Azure Monitor | Microsoft Docs
description: Azure Monitor는 MSPs (관리 서비스 공급자), 대기업, Isv (독립 소프트웨어 공급 업체) 및 호스팅 서비스 공급자가 고객의 온-프레미스 또는 클라우드 인프라에서 서버를 관리 및 모니터링 하는 데 도움이 될 수 있습니다.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: MeirMen
ms.author: meirm
ms.date: 08/06/2019
ms.openlocfilehash: b0f25d01421edd329b03d8f2b7e1aafaa2ba67d5
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72932072"
---
# <a name="azure-monitor-for-service-providers"></a>서비스 공급자 Azure Monitor
Azure Monitor의 Log Analytics 작업 영역을 통해 MSPs (관리 서비스 공급자), 대기업, Isv (독립 소프트웨어 공급 업체) 및 호스팅 서비스 공급자가 고객의 온-프레미스 또는 클라우드 인프라에서 서버를 관리 하 고 모니터링할 수 있습니다. 

대기업은 서비스 공급자와 많은 유사성을 공유하는데, 특히 중앙 IT 팀이 다양한 사업부의 IT 관리를 담당한다는 점이 그렇습니다. 간단한 설명을 위해 이 문서에서는 *서비스 공급자*라는 용어를 사용하지만 기업 및 기타 고객에 대해서도 같은 기능을 사용할 수 있습니다.

[CSP (클라우드 솔루션 공급자)](https://partner.microsoft.com/Solutions/cloud-reseller-overview) 프로그램의 일부인 파트너 및 서비스 공급자의 경우 Azure Monitor의 Log Analytics는 [azure CSP 구독](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview)에서 사용할 수 있는 azure 서비스 중 하나입니다. 

## <a name="architectures-for-service-providers"></a>서비스 공급자의 아키텍처

Log Analytics 작업 영역에서는 관리자가 [로그](data-platform-logs.md) 데이터의 흐름과 분리를 제어 하 고 특정 비즈니스 요구 사항을 해결 하는 아키텍처를 만들 수 있는 방법을 제공 합니다. [이 문서](design-logs-deployment.md) 에서는 작업 영역에 대 한 디자인, 배포 및 마이그레이션 고려 사항을 설명 하 고, [액세스 관리](manage-access.md) 문서는 로그 데이터에 대 한 사용 권한을 적용 하 고 관리 하는 방법을 설명 합니다. 서비스 공급자에게는 추가 고려 사항이 있습니다.

Log Analytics 작업 영역에 관련된 서비스 공급자에 대해 세 가지 아키텍처를 사용할 수 있습니다.

### <a name="1-distributed---logs-are-stored-in-workspaces-located-in-the-customers-tenant"></a>1. 분산 로그는 고객의 테넌트에 있는 작업 영역에 저장 됩니다. 

이 아키텍처에서 작업 영역은 해당 고객의 모든 로그에 사용되는 고객의 테넌트에 배포됩니다. 서비스 공급자 관리자는 [Azure Active Directory 게스트 사용자(B2B)](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b)를 사용하여 이 작업 영역에 대한 액세스 권한이 부여되었습니다. 서비스 공급자 관리자는 Azure Portal에서 고객 디렉터리로 전환해야 이러한 작업 영역에 액세스할 수 있습니다.

이 아키텍처의 장점은 다음과 같습니다.
* 고객은 고유한 [역할 기반 액세스](https://docs.microsoft.com/azure/role-based-access-control/overview)를 사용하여 로그에 대한 액세스 권한을 관리할 수 있습니다.
* 각 고객은 보존 및 데이터 제한과 같은 해당 작업 영역의 설정이 서로 다를 수 있습니다.
* 규정 및 준수를 위해 고객을 서로 격리합니다.
* 각 작업 영역에 대한 요금이 고객의 구독에 롤인됩니다.
* 로그는 에이전트 기반이 아닌 모든 형식의 리소스에서 수집될 수 있습니다. Azure 감사 로그 등을 예로 들 수 있습니다.

이 아키텍처의 단점은 다음과 같습니다.
* 서비스 공급자의 경우 한 번에 많은 수의 고객 테넌트를 관리하기가 더 어렵습니다.
* 서비스 공급자 관리자는 고객 디렉터리에서 프로비전되어야 합니다.
* 서비스 공급자는 해당 고객 간에 데이터를 분석할 수 없습니다.

### <a name="2-central---logs-are-stored-in-a-workspace-located-in-the-service-provider-tenant"></a>2. 중앙 로그는 서비스 공급자 테넌트에 있는 작업 영역에 저장 됩니다.

이 아키텍처에서 로그는 고객의 테넌트에 저장되지 않고 서비스 공급자의 구독 내의 중앙 위치에만 저장됩니다. 고객의 VM에 설치된 에이전트는 작업 영역 ID 및 비밀 키를 사용하여 이 작업 영역에 해당 로그를 보내도록 구성됩니다.

이 아키텍처의 장점은 다음과 같습니다.
* 쉽게 다수의 고객을 관리하고 다양한 백 엔드 시스템에 통합할 수 있습니다.

* 서비스 공급자에게는 함수 및 저장된 쿼리와 같은 로그 및 다양한 아티팩트에 대한 전체 소유권이 있습니다.

* 서비스 공급자는 모든 고객에 대해 분석을 수행할 수 있습니다.

이 아키텍처의 단점은 다음과 같습니다.
* 이 아키텍처는 에이전트 기반 VM 데이터에만 적용 가능하고, PaaS, SaaS 및 Azure 패브릭 데이터 원본을 다루지 않습니다.

* 단일 작업 영역으로 병합된 고객 간에 데이터를 구분하기가 어려울 수 있습니다. 이렇게 하려면 유일한 방법은 컴퓨터의 FQDN(정규화된 도메인 이름)을 사용하거나 Azure 구독 ID를 통해서 수행하는 것입니다. 

* 모든 고객의 모든 데이터는 단일 청구서 및 동일한 보존 및 구성 설정을 사용하여 동일한 지역에 저장됩니다.

* Azure Diagnostics 및 Azure 감사 로그와 같은 Azure 패브릭 및 PaaS 서비스에는 리소스와 동일한 테넌트에 있는 작업 영역이 필요합니다. 따라서 중앙 작업 영역에 로그를 보낼 수 없습니다.

* 모든 고객의 모든 VM 에이전트는 동일한 작업 영역 ID 및 키를 사용하여 중앙 작업 영역에 인증됩니다. 다른 고객을 방해하지 않고 특정 고객의 로그를 차단하는 방법은 없습니다.

### <a name="3-hybrid---logs-are-stored-in-workspace-located-in-the-customers-tenant-and-some-of-them-are-pulled-to-a-central-location"></a>3. 하이브리드 로그는 고객의 테넌트에 있는 작업 영역에 저장 되 고 그 중 일부는 중앙 위치로 끌어옵니다.

두 가지 옵션 중 세 번째 아키텍처 조합입니다. 로그가 각 고객에게 로컬인 첫 번째 분산 아키텍처에 기반하지만 로그의 중앙 리포지토리를 만드는 일부 메커니즘을 사용합니다. 보고 및 분석을 위해 중앙 위치로 로그의 일부를 끌어옵니다. 이 부분은 적은 수의 데이터 형식 또는 일별 통계와 같은 작업 요약일 수 있습니다.

중앙 위치에 로그를 구현 하는 두 가지 옵션이 있습니다.

1. 중앙 작업 영역: 서비스 공급자는 해당 테넌트에서 작업 영역을 만들고 [데이터 수집 API](../../azure-monitor/platform/data-collector-api.md)와 함께 [쿼리 API](https://dev.loganalytics.io/)를 활용하는 스크립트를 사용하여 데이터를 다양한 작업 영역에서 중앙 위치로 가져올 수 있습니다. 스크립트 외에 사용 가능한 또 다른 옵션은 [Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview)를 사용하는 것입니다.

2. 중앙 위치로 Power BI: Power BI 여러 작업 영역에서 Log Analytics 작업 영역과 [Power BI](../../azure-monitor/platform/powerbi.md)간의 통합을 사용 하 여 데이터를 내보낼 때 중앙 위치로 작동할 수 있습니다. 

## <a name="next-steps"></a>다음 단계

* [Resource Manager 템플릿](template-workspace-configuration.md)을 사용하여 작업 영역 생성 및 구성 자동화

* [PowerShell](../../azure-monitor/platform/powershell-workspace-configuration.md)을 사용하여 작업 영역 생성 자동화 

* [경고](../../azure-monitor/platform/alerts-overview.md)를 사용하여 기존 시스템과 통합

* [Power BI](../../azure-monitor/platform/powerbi.md)를 사용하여 요약 보고서 생성

* [여러 CSP 고객을 모니터링하도록 Log Analytics 및 Power BI를 구성](https://docs.microsoft.com/azure/cloud-solution-provider/support/monitor-multiple-customers)하는 프로세스 검토
