---
title: Azure Log Analytics에 저장된 개인 데이터에 대한 지침 | Microsoft Docs
description: 이 문서에서는 Azure Log Analytics에 저장된 개인 데이터를 관리하고 이를 식별하고 제거하는 방법을 설명합니다.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/18/2018
ms.author: magoedte
ms.openlocfilehash: 0cf5a80e3eedbe7efb8463162b5b3ed489ac08c8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61087290"
---
# <a name="guidance-for-personal-data-stored-in-log-analytics-and-application-insights"></a>Log Analytics 및 Application Insights에 저장된 개인 데이터에 대한 지침

Log Analytics는 개인 데이터를 찾을 수 있는 데이터 저장소입니다. Application Insights는 해당 데이터를 Log Analytics 파티션에 저장합니다. 이 문서에서는 Log Analytics 및 Application Insights에서 일반적으로 이러한 데이터가 발견되는 위치와 이러한 데이터를 처리하는 데 사용할 수 있는 기능에 대해 설명합니다.

> [!NOTE]
> 이 문서의 목적을 위해, _로그 데이터_는 Log Analytics 작업 영역으로 전송된 데이터를 가리키고, _애플리케이션 데이터_는 Application Insights에 의해 수집된 데이터를 가리킵니다.

[!INCLUDE [gdpr-dsr-and-stp-note](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="strategy-for-personal-data-handling"></a>개인 데이터 처리 전략

개인 데이터를 처리할 전략을 결정하는 것은 궁극적으로 사용자 및 사용자의 회사에 달려 있지만(가능한 경우) 몇 가지 가능한 방법은 다음과 같습니다. 기술적인 관점에서 우선 순위의 순서대로 나열되어 있습니다.

* 가능한 경우 데이터를 수집 중지, 난독 처리, 익명화하거나, 그렇지 않으면 수집 중인 데이터를 "개인"으로 간주되지 않도록 조정합니다. 이는 _훨씬_ 선호되는 방법이므로 비용이 많이 들고 영향력 있는 데이터 처리 전략을 만들 필요가 없습니다.
* 가능하지 않은 경우 데이터를 정규화하여 데이터 플랫폼 및 성능에 미치는 영향을 줄입니다. 예를 들어 명시적 사용자 ID를 로깅하는 대신 사용자 이름과 해당 세부 정보를 다른 곳에 기록할 수 있는 내부 ID와 상관 관계가 있는 조회 데이터를 만듭니다. 이렇게 하면 사용자 중 한 명이 자신의 개인 정보를 삭제하도록 요청하면 사용자에게 해당하는 조회 테이블에서 해당 행만 삭제하는 것으로 충분할 수 있습니다. 
* 마지막으로 개인 데이터를 수집해야 하는 경우 제거 API 경로 및 기존 쿼리 API 경로 주변의 프로세스를 빌드하여 사용자와 연결된 개인 데이터를 내보내고 삭제해야 하는 모든 의무를 충족시킵니다. 

## <a name="where-to-look-for-private-data-in-log-analytics"></a>Log Analytics에서 개인 데이터를 찾는 위치

Log Analytics는 스키마를 데이터에 지정하는 동안 모든 필드를 사용자 지정 값으로 재정의할 수 있는 유연한 저장소입니다. 또한 모든 사용자 지정 스키마도 수집할 수 있습니다. 따라서 특정 작업 영역에서 개인 데이터가 있는 위치를 정확하게 가리킬 수는 없습니다. 그러나 다음 위치는 인벤토리에서 훌륭한 시작 지점입니다.

### <a name="log-data"></a>로그 데이터

* *IP 주소*: Log Analytics는 여러 테이블에서 다양한 IP 정보를 수집합니다. 예를 들어 다음 쿼리에서는 지난 24시간 동안 IPv4 주소가 수집된 모든 테이블을 보여 줍니다.
    ```
    search * 
    | where * matches regex @'\b((25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)(\.|$)){4}\b' //RegEx originally provided on https://stackoverflow.com/questions/5284147/validating-ipv4-addresses-with-regexp
    | summarize count() by $table
    ```
* *사용자 ID*: 사용자 ID는 매우 다양한 솔루션과 테이블에서 찾을 수 있습니다. 검색 명령을 사용하여 전체 데이터 세트에서 특정 사용자 이름을 찾을 수 있습니다.
    ```
    search "[username goes here]"
    ```
  사람이 읽을 수 있는 사용자 이름뿐만 아니라 직접 특정 사용자에게 다시 추적할 수 있는 GUID도 검색해야 합니다.
* *디바이스 ID*: 사용자 ID와 마찬가지로 디바이스 ID는 '개인'으로 간주되기도 합니다. 사용자 ID에 대해 위에서 나열한 것과 동일한 방법을 사용하여 이 문제가 될 수 있는 테이블을 식별합니다. 
* *사용자 지정 데이터*: Log Analytics를 사용하면 사용자 지정 로그와 사용자 지정 필드, [HTTP 데이터 수집기 API](../../azure-monitor/platform/data-collector-api.md) 및 시스템 이벤트 로그의 일부로 수집된 사용자 지정 데이터 등 다양한 방법으로 수집할 수 있습니다. 이러한 방법은 모두 개인 데이터를 포함하는 데 취약하고 이러한 정보가 있는지 여부를 확인하기 위해 조사해야 합니다.
* *솔루션에서 캡처된 데이터*: 솔루션 메커니즘은 제한되지 않으므로 솔루션에서 생성된 모든 테이블을 검토하여 규정 준수 여부를 확인하는 것이 좋습니다.

### <a name="application-data"></a>애플리케이션 데이터

* *IP 주소*: Application Insights는 기본적으로 모든 IP 주소 필드를 "0.0.0.0"으로 난독 처리하지만, 세션 정보를 유지하기 위해 이 값을 실제 사용자로 재정의하는 것이 매우 일반적인 패턴입니다. 아래의 Analytics 쿼리는 지난 24 시간 동안 "0.0.0.0"이 아닌 IP 주소 열의 값을 포함한 모든 테이블을 찾는 데 사용될 수 있습니다.
    ```
    search client_IP != "0.0.0.0"
    | where timestamp > ago(1d)
    | summarize numNonObfuscatedIPs_24h = count() by $table
    ```
* *사용자 ID*: Application Insights는 기본적으로 사용자 및 세션 추적을 위해 임의로 생성된 ID를 사용합니다. 그러나 애플리케이션에 더 많은 관련이 있는 ID를 저장하려면 이러한 재정의된 필드를 확인하는 것이 일반적입니다. 예: 사용자 이름, AAD GUID 등. 이러한 ID는 자주 범위 내 개인 데이터로 간주되므로 적절하게 처리되어야 합니다. 이러한 ID는 항상 난독 처리하거나 익명화하는 것이 좋습니다. 이러한 값이 보통 발견되는 필드에는 session_Id, user_Id, user_AuthenticatedId, user_AccountId 및 customDimensions이 포함됩니다.
* *사용자 지정 데이터*: Application Insights를 사용하면 모든 데이터 형식에 사용자 지정 차원 세트를 추가할 수 있습니다. 이러한 차원은 *모든* 데이터일 수 있습니다. 다음 쿼리를 사용하여 지난 24시간 동안 수집된 모든 사용자 지정 크기를 식별합니다.
    ```
    search * 
    | where isnotempty(customDimensions)
    | where timestamp > ago(1d)
    | project $table, timestamp, name, customDimensions 
    ```
* *메모리 내 및 전송 중 데이터*: Application Insights는 예외, 요청, 종속성 호출 및 추적을 추적합니다. 개인 데이터는 자주 코드 및 HTTP 호출 수준에서 수집될 수 있습니다. 이러한 데이터를 식별하려면 예외, 요청, 종속성 및 추적 테이블을 검토합니다. 이 데이터를 난독 처리할 수 있는 경우 [원격 분석 이니셜라이저](https://docs.microsoft.com/azure/application-insights/app-insights-api-filtering-sampling)를 사용합니다.
* *스냅숏 디버거 캡처*: Application Insights의 [스냅숏 디버거](https://docs.microsoft.com/azure/application-insights/app-insights-snapshot-debugger) 기능을 사용하면 애플리케이션의 프로덕션 인스턴스에서 예외가 catch될 때마다 디버그 스냅숏을 수집할 수 있습니다. 스냅숏은 예외 및 스택의 모든 단계에서 로컬 변수에 대한 값으로 이끄는 전체 스택 추적을 공개합니다. 그러나 이 기능은 맞춤 지점의 선택적 삭제 또는 스냅숏 내에서 데이터에 프로그래밍 방식의 액세스를 허용하지 않습니다. 따라서 기본 스냅숏 보존 속도가 규정 준수 요구 사항을 충족하지 못하는 경우 기능을 해제하는 것이 좋습니다.

## <a name="how-to-export-and-delete-private-data"></a>개인 데이터를 내보내고 삭제하는 방법

앞서의 [개인 데이터 처리 전략](#strategy-for-personal-data-handling) 섹션에서 설명한 대로, 가능한 경우 데이터 수집 정책을 재구성하여 개인 데이터 수집을 사용하지 않도록 설정하거나 개인 데이터를 난독 처리, 익명화하거나, 그렇지 않으면 개인 데이터를 "개인"으로 간주되지 않도록 수정하는 것이 __가장__ 좋습니다. 데이터를 처리하면 먼저 사용자와 사용자 팀이 전략을 정의 및 자동화하고, 고객이 데이터를 통해 상호 작용할 수 있는 인터페이스를 구축함으로써 비용이 발생하고, 지속적인 유지 관리 비용도 발생됩니다. 또한 Log Analytics 및 Application Insights에 대한 컴퓨팅 비용이 많이 들고, 많은 양의 동시 쿼리 또는 제거 API 호출로 인해 Log Analytics 기능과의 다른 모든 상호 작용에 부정적인 영향을 미칠 수 있습니다. 즉, 실제로 개인 데이터를 수집해야 하는 몇 가지 유효한 시나리오가 있습니다. 이러한 경우 데이터는 이 섹션에서 설명한 대로 처리해야 합니다.

[!INCLUDE [gdpr-intro-sentence](../../../includes/gdpr-intro-sentence.md)]

### <a name="view-and-export"></a>보기 및 내보내기

데이터 보기 및 내보내기 요청 둘 다에서 [Log Analytics 쿼리 API](https://dev.loganalytics.io/) 또는 [Application Insights 쿼리 API](https://dev.applicationinsights.io/quickstart)를 사용해야 합니다. 데이터의 모양을 적절한 형식으로 변환하여 사용자에게 전달하는 논리의 구현은 사용자에게 달려 있습니다. [Azure Functions](https://azure.microsoft.com/services/functions/)는 이러한 논리를 호스팅하는 데 적합합니다.

> [!IMPORTANT]
>  대부분 제거 작업의 SLA 보다 훨씬 더 빠르게 완료할 수 있습니다 하는 동안 **정식 제거 작업의 완료에 대 한 SLA은 30 일로 설정 되어** 사용 되는 데이터 플랫폼에 대 한 과도 한 영향 때문입니다. 이 자동화 된 프로세스입니다. 작업을 더 빠르게 처리 하도록 요청 하려면 방법이 없습니다.

### <a name="delete"></a>삭제

> [!WARNING]
> Log Analytics에서 삭제하는 작업은 파괴적이고 되돌릴 수 없습니다! 실행에 각별히 주의하세요.

*제거* API 경로를 처리하는 개인 정보의 일부로 사용할 수 있게 되었습니다. 이 경로는 이러한 작업과 관련된 위험, 잠재적인 성능 영향 및 Log Analytics 데이터의 총 집계, 측정 및 다른 측면을 왜곡할 수 있는 가능성으로 인해 드물게 사용해야 합니다. 개인 데이터를 처리하는 다른 방법은 [개인 데이터 처리 전략](#strategy-for-personal-data-handling) 섹션을 참조하세요.

제거는 높은 수준의 권한이 필요한 작업으로 Azure의 사용자(리소스 소유자도 포함) 또는 앱이 Azure Resource Manager에서 명시적으로 역할을 부여받아야 실행할 수 있습니다. 이 역할은 _데이터 제거자_이며, 데이터 손실 가능성 때문에 신중하게 위임해야 합니다. 

Azure Resource Manager 역할이 할당되면 두 개의 새 API 경로를 사용할 수 있습니다. 

#### <a name="log-data"></a>로그 데이터

* [게시 제거](https://docs.microsoft.com/rest/api/loganalytics/workspaces%202015-03-20/purge) - 삭제할 데이터의 매개 변수를 지정하는 개체를 사용하고 참조 GUID를 반환합니다. 
* GET 상태 가져오기 - POST 제거 호출은 제거 API의 상태를 결정하기 위해 호출할 수 있는 URL이 포함된 'x-ms-status-location' 헤더를 반환합니다. 예를 들면 다음과 같습니다.

    ```
    x-ms-status-location: https://management.azure.com/subscriptions/[SubscriptionId]/resourceGroups/[ResourceGroupName]/providers/Microsoft.OperatonalInsights/workspaces/[WorkspaceName]/operations/purge-[PurgeOperationId]?api-version=2015-03-20
    ```

> [!IMPORTANT]
>  대부분의 제거 작업은 SLA보다 훨씬 빨리 완료될 것으로 예상하지만, Log Analytics에서 사용하는 데이터 플랫폼에 많은 영향을 미치기 때문에 **제거 작업 완료에 대한 공식 SLA는 30일로 설정됩니다**. 

#### <a name="application-data"></a>애플리케이션 데이터

* [게시 제거](https://docs.microsoft.com/rest/api/application-insights/components/purge) - 삭제할 데이터의 매개 변수를 지정하는 개체를 사용하고 참조 GUID를 반환합니다.
* GET 상태 가져오기 - POST 제거 호출은 제거 API의 상태를 결정하기 위해 호출할 수 있는 URL이 포함된 'x-ms-status-location' 헤더를 반환합니다. 예를 들면 다음과 같습니다.

   ```
   x-ms-status-location: https://management.azure.com/subscriptions/[SubscriptionId]/resourceGroups/[ResourceGroupName]/providers/microsoft.insights/components/[ComponentName]/operations/purge-[PurgeOperationId]?api-version=2015-05-01
   ```

> [!IMPORTANT]
>  대부분의 제거 작업은 Application Insights에서 사용하는 데이터 플랫폼에 대한 많은 영향으로 인해 SLA보다 훨씬 빨리 완료할 수 있는 반면 **제거 작업 완료를 위한 공식 SLA는 30일로 설정됩니다**.

## <a name="next-steps"></a>다음 단계
- Log Analytics 데이터 수집, 처리 및 보안 방법에 대한 자세한 내용은 [Log Analytics 데이터 보안](../../azure-monitor/platform/data-security.md)을 참조하세요.
- Application Insights 데이터 수집, 처리 및 보안 방법에 대한 자세한 내용은 [Application Insights 데이터 보안](../../azure-monitor/app/data-retention-privacy.md)을 참조하세요.
