---
title: 리소스 로그 수집 & 분석
description: 인증, 이미지 푸시, 이미지 풀 등의 Azure Container Registry에 대한 리소스 로그 이벤트를 기록 하 고 분석 합니다.
ms.topic: article
ms.date: 01/03/2020
ms.openlocfilehash: 72d03149cd24636ba2086dfaaff0dbba16d30f1e
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/08/2020
ms.locfileid: "75748007"
---
# <a name="azure-container-registry-logs-for-diagnostic-evaluation-and-auditing"></a>진단 평가 및 감사에 대한 Azure Container Registry 로그

이 문서에서는 [Azure Monitor](../azure-monitor/overview.md)기능을 사용 하 여 Azure container registry에 대한 로그 데이터를 수집 하는 방법을 설명 합니다. Azure Monitor는 레지스트리의 사용자 기반 이벤트에 대한 [리소스 로그](../azure-monitor/platform/platform-logs-overview.md) (이전의 *진단 로그*)를 수집 합니다. 이 데이터를 수집 하 고 사용 하 여 다음과 같은 요구 사항을 충족 합니다.

* 보안 및 규정 준수를 보장 하는 레지스트리 인증 이벤트 감사 

* 레지스트리를 사용 하 여 운영 문제를 진단할 수 있도록 끌어오기 및 끌어오기 이벤트와 같은 레지스트리 아티팩트의 전체 작업 내역을 제공 합니다. 

Azure Monitor를 사용 하 여 리소스 로그 데이터를 수집 하면 추가 비용이 발생할 수 있습니다. [Azure Monitor 가격 책정](https://azure.microsoft.com/pricing/details/monitor/)을 참조 하세요. 


> [!IMPORTANT]
> 이 기능은 현재 미리 보기로 제공 되며 일부 [제한 사항이](#preview-limitations) 적용 됩니다. [부속 사용 약관][terms-of-use]에 동의하면 미리 보기를 사용할 수 있습니다. 이 기능의 몇 가지 측면은 일반 공급(GA) 전에 변경될 수 있습니다.

## <a name="preview-limitations"></a>미리 보기 제한 사항

현재 이미지 및 기타 아티팩트에 대해 다음과 같은 리포지토리 수준 이벤트가 기록 됩니다.

* **푸시 이벤트**
* **끌어오기 이벤트**
* **태그 지정 해제 이벤트**
* **이벤트 삭제** (리포지토리 삭제 이벤트 포함)

현재 기록 되지 않은 리포지토리 수준 이벤트: 제거 이벤트

## <a name="registry-resource-logs"></a>레지스트리 리소스 로그

리소스 로그는 내부 작업을 설명 하는 Azure 리소스에서 내보낸 정보를 포함 합니다. Azure container registry의 경우 로그에는 다음 표에 저장 된 인증 및 리포지토리 수준 이벤트가 포함 됩니다. 

* **ContainerRegistryLoginEvents** -들어오는 ID 및 IP 주소를 포함 하는 레지스트리 인증 이벤트 및 상태
* **ContainerRegistryRepositoryEvents** -이미지 및 레지스트리 리포지토리의 기타 아티팩트에 대한 푸시 및 가져오기와 같은 작업
* **Azuremetrics** 집계 된 푸시 및 끌어오기 수와 같은 [컨테이너 레지스트리 메트릭을](../azure-monitor/platform/metrics-supported.md#microsoftcontainerregistryregistries) - 합니다.

작업의 경우 로그 데이터에는 다음이 포함 됩니다.
  * 성공 또는 실패 상태
  * 시작 및 종료 타임 스탬프

Azure는 리소스 로그 외에도 컨테이너 레지스트리의 생성 또는 삭제와 같은 Azure 관리 이벤트의 단일 구독 수준 레코드인 [활동 로그](../azure-monitor/platform/platform-logs-overview.md)를 제공 합니다.

## <a name="enable-collection-of-resource-logs"></a>리소스 로그 수집 사용

컨테이너 레지스트리에 대한 리소스 로그 컬렉션은 기본적으로 사용 하도록 설정 되어 있지 않습니다. 모니터링할 각 레지스트리에 대해 진단 설정을 명시적으로 사용 하도록 설정 합니다. 진단 설정을 사용 하도록 설정 하는 옵션은 [Azure에서 플랫폼 로그 및 메트릭을 수집 하는 진단 설정 만들기](../azure-monitor/platform/diagnostic-settings.md)를 참조 하세요.

예를 들어 Azure Monitor의 거의 실시간으로 컨테이너 레지스트리에 대한 로그 및 메트릭을 보려면 Log Analytics 작업 영역에서 리소스 로그를 수집 합니다. Azure Portal를 사용 하 여이 진단 설정을 사용 하려면:

1. 작업 영역이 아직 없는 경우 [Azure Portal](../azure-monitor/learn/quick-create-workspace.md)를 사용 하 여 작업 영역을 만듭니다. 데이터 수집의 대기 시간을 최소화 하려면 작업 영역이 container registry와 **동일한 지역** 에 있어야 합니다.
1. 포털에서 레지스트리를 선택 하 고 **모니터링 > 진단 설정 > 진단 설정 추가**를 선택 합니다.
1. 설정에 대한 이름을 입력 하 고 **Log Analytics 보내기를**선택 합니다.
1. 레지스트리 진단 로그의 작업 영역을 선택 합니다.
1. 수집 하려는 로그 데이터를 선택 하 고 **저장**을 클릭 합니다.

다음 이미지는 포털을 사용 하 여 레지스트리에 대한 진단 설정 생성을 보여 줍니다.

![진단 설정을 사용하도록 설정](media/container-registry-diagnostics-audit-logs/diagnostic-settings.png)

> [!TIP]
> 필요한 데이터만 수집 하 고, 비용 및 모니터링 요구 사항을 분산 합니다. 예를 들어 인증 이벤트만 감사 해야 하는 경우 **ContainerRegistryLoginEvents** 로그만 선택 합니다. 

## <a name="view-data-in-azure-monitor"></a>Azure Monitor에서 데이터 보기

Log Analytics에서 진단 로그의 컬렉션을 사용 하도록 설정 하면 데이터가 Azure Monitor에 표시 되는 데 몇 분 정도 걸릴 수 있습니다. 포털에서 데이터를 보려면 레지스트리를 선택 하 고 **모니터링 > 로그**를 선택 합니다. 레지스트리에 대한 데이터가 포함 된 테이블 중 하나를 선택 합니다. 

쿼리를 실행 하 여 데이터를 봅니다. 몇 가지 샘플 쿼리가 제공 되거나 직접 실행 됩니다. 예를 들어 다음 쿼리는 **ContainerRegistryRepositoryEvents** 테이블에서 최근 24 시간 동안의 데이터를 검색 합니다.

```Kusto
ContainerRegistryRepositoryEvents
| where TimeGenerated > ago(1d) 
```

다음 이미지는 샘플 출력을 보여 줍니다.

![로그 데이터 쿼리](media/container-registry-diagnostics-audit-logs/azure-monitor-query.png)

Azure Portal에서 Log Analytics를 사용 하는 방법에 대한 자습서는 [Azure Monitor Log Analytics 시작](../azure-monitor/log-query/get-started-portal.md)또는 Log Analytics [Demo 환경](https://portal.loganalytics.io/demo)사용을 참조 하세요. 

로그 쿼리에 대한 자세한 내용은 Azure Monitor의 [로그 쿼리 개요](../azure-monitor/log-query/log-query-overview.md)를 참조 하세요.

### <a name="additional-query-examples"></a>추가 쿼리 예제

#### <a name="100-most-recent-registry-events"></a>100 가장 최근 레지스트리 이벤트

```Kusto
ContainerRegistryRepositoryEvents
| union ContainerRegistryLoginEvents
| top 100 by TimeGenerated
| project TimeGenerated, LoginServer , OperationName , Identity , Repository , DurationMs , Region , ResultType
```

## <a name="additional-log-destinations"></a>추가 로그 대상

로그를 Log Analytics 또는 다른 방법으로 전송 하는 것 외에도 Azure Storage 계정을 로그 대상으로 선택 하는 것이 일반적인 시나리오입니다. Azure Storage에서 로그를 보관 하려면 진단 설정을 통해 보관을 사용 하도록 설정 하기 전에 저장소 계정을 만듭니다.

진단 로그 이벤트를 [Azure 이벤트 허브](../event-hubs/event-hubs-what-is-event-hubs.md)로 스트리밍할 수도 있습니다. Event Hubs는 초당 수백 건의 이벤트를 수집하여 모든 실시간 분석 공급자를 통해 변환 및 저장할 수 있습니다. 

## <a name="next-steps"></a>다음 단계

* [Log Analytics](../azure-monitor/log-query/get-started-portal.md) 사용 및 [로그 쿼리](../azure-monitor/log-query/get-started-queries.md)만들기에 대해 자세히 알아보세요.
* Azure [플랫폼 로그 개요](../azure-monitor/platform/platform-logs-overview.md) 를 참조 하 여 다양 한 azure 계층에서 사용할 수 있는 플랫폼 로그에 대해 알아보세요.

<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
