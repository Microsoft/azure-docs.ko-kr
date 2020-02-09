---
title: 진단 로깅
titleSuffix: Azure Cognitive Services
description: 이 가이드에서는 Azure 인식 서비스에 대한 진단 로깅을 사용 하도록 설정 하는 단계별 지침을 제공 합니다. 이러한 로그는 문제를 식별 하 고 디버깅 하는 데 사용 되는 리소스 작업에 대한 풍부 하 고 빈번한 데이터를 제공 합니다.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.topic: article
ms.date: 06/14/2019
ms.author: erhopf
ms.openlocfilehash: 539a35f170b2ee0c94762a30ed9376ca4a416210
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/02/2019
ms.locfileid: "71827909"
---
# <a name="enable-diagnostic-logging-for-azure-cognitive-services"></a>Azure Cognitive Services에 대한 진단 로깅 사용

이 가이드에서는 Azure 인식 서비스에 대한 진단 로깅을 사용 하도록 설정 하는 단계별 지침을 제공 합니다. 이러한 로그는 문제를 식별 하 고 디버깅 하는 데 사용 되는 리소스 작업에 대한 풍부 하 고 빈번한 데이터를 제공 합니다. 계속 하기 전에 [Bing Web Search](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/overview), [Speech Services](https://docs.microsoft.com/azure/cognitive-services/speech-service/overview)또는 [LUIS](https://docs.microsoft.com/azure/cognitive-services/luis/what-is-luis)와 같은 하나 이상의 인식 서비스에 대한 구독이 있는 Azure 계정이 있어야 합니다.

## <a name="prerequisites"></a>사전 요구 사항

진단 로깅을 사용 하도록 설정 하려면 로그 데이터를 저장 하는 위치에 있어야 합니다. 이 자습서에서는 Azure Storage와 Log Analytics를 사용 합니다.

* [Azure storage](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-archive-diagnostic-logs) -정책 감사, 정적 분석 또는 백업에 대한 진단 로그를 유지 합니다. 설정을 구성하는 사용자가 두 구독에 대한 적절한 RBAC 액세스를 가진 경우 스토리지 계정은 로그를 내보내는 리소스와 동일한 구독을 가지고 있지 않아도 됩니다.
* [Log Analytics](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-stream-diagnostic-logs-log-analytics) -Azure 리소스에서 생성 된 원시 로그를 분석할 수 있는 유연한 로그 검색 및 분석 도구입니다.

> [!NOTE]
> 추가 구성 옵션을 사용할 수 있습니다. 자세히 알아보려면 [Azure 리소스에서 로그 데이터 수집 및 사용](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview)을 참조 하세요.

## <a name="enable-diagnostic-log-collection"></a>진단 로그 수집 사용  

먼저 Azure Portal를 사용 하 여 진단 로깅을 사용 하도록 설정 합니다.

> [!NOTE]
> PowerShell 또는 Azure CLI를 사용 하 여이 기능을 사용 하도록 설정 하려면 [Azure 리소스에서 로그 데이터 수집 및](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview)사용에 제공 된 지침을 사용 합니다.

1. Azure Portal로 이동합니다. 그런 다음 Cognitive Services 리소스를 찾아 선택 합니다. 예를 들어 구독을 Bing Web Search 합니다.   
2. 그런 다음 왼쪽 탐색 메뉴에서 **모니터링** 을 찾아 **진단 설정**을 선택 합니다. 이 화면에는이 리소스에 대해 이전에 만든 모든 진단 설정이 포함 되어 있습니다.
3. 이전에 만든 리소스를 사용 하려는 경우 지금 선택할 수 있습니다. 그렇지 않으면 **+ 진단 설정 추가**를 선택 합니다.
4. 설정의 이름을 입력 합니다. 그런 다음 **저장소 계정에 보관** 을 선택 하 고 **Log Analytics에 보냅니다**.
5. 구성 하 라는 메시지가 표시 되 면 진단 로그를 저장 하는 데 사용할 저장소 계정 및 OMS 작업 영역을 선택 합니다. **참고**: 저장소 계정 또는 OMS 작업 영역이 없는 경우 표시 되는 메시지에 따라 하나를 만듭니다.
6. **감사**, **RequestResponse**및 **allmetrics**을 선택 합니다. 그런 다음 진단 로그 데이터에 대한 보존 기간을 설정 합니다. 보존 정책이 0으로 설정 된 경우 해당 로그 범주에 대한 이벤트는 무기한 저장 됩니다.
7. **저장**을 클릭합니다.

로깅 데이터를 쿼리 및 분석에 사용할 수 있을 때까지 최대 2 시간이 걸릴 수 있습니다. 바로 표시 되지 않는 경우 걱정 하지 마세요.

## <a name="view-and-export-diagnostic-data-from-azure-storage"></a>Azure Storage에서 진단 데이터 보기 및 내보내기

Azure Storage는 대량의 구조화 되지 않은 데이터를 저장 하기 위해 최적화 된 강력한 개체 저장소 솔루션입니다. 이 섹션에서는 30 일 기간 동안 총 트랜잭션을 위해 저장소 계정을 쿼리하고 excel로 데이터를 내보내는 방법을 배웁니다.

1. Azure Portal에서 마지막 섹션에서 만든 Azure Storage 리소스를 찾습니다.
2. 왼쪽 탐색 메뉴에서 **모니터링** 을 찾아 **메트릭**을 선택 합니다.
3. 사용 가능한 드롭다운을 사용 하 여 쿼리를 구성 합니다. 이 예에서는 시간 범위를 **최근 30 일** 으로 설정 하 고 메트릭을 **트랜잭션으로**설정 해 보겠습니다.
4. 쿼리가 완료 되 면 지난 30 일 동안의 트랜잭션 시각화가 표시 됩니다. 이 데이터를 내보내려면 페이지 맨 위에 있는 **Excel로 내보내기** 단추를 사용 합니다.

[Azure Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction)에서 진단 데이터로 수행할 수 있는 작업에 대해 자세히 알아보세요.

## <a name="view-logs-in-log-analytics"></a>Log Analytics에서 로그 보기

다음 지침에 따라 리소스에 대한 log analytics 데이터를 탐색 합니다.

1. Azure Portal의 왼쪽 탐색 메뉴에서 **Log Analytics** 을 찾아 선택 합니다.
2. 진단을 사용 하도록 설정할 때 만든 리소스를 찾아 선택 합니다.
3. **일반**에서 **로그**를 찾아서 선택 합니다. 이 페이지에서 로그에 대해 쿼리를 실행할 수 있습니다.

### <a name="sample-queries"></a>샘플 쿼리

로그 데이터를 탐색 하는 데 사용할 수 있는 몇 가지 기본 Kusto 쿼리는 다음과 같습니다.

지정 된 기간 동안 Azure Cognitive Services의 모든 진단 로그에 대해이 쿼리를 실행 합니다.

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
```

이 쿼리를 실행 하 여 최근 10 개 로그를 확인 합니다.

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| take 10
```

다음 쿼리를 실행 하 여 **리소스**별로 작업을 그룹화 합니다.

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES" |
summarize count() by Resource
```
다음 쿼리를 실행 하 여 작업을 수행 하는 데 걸리는 평균 시간을 찾습니다.

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| summarize avg(DurationMs)
by OperationName
```

이 쿼리를 실행 하 여 모든 10 초에 대한 개수를 기준으로 하는 OperationName으로 분할 된 시간에 따라 작업 볼륨을 볼 수 있습니다.

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| summarize count()
by bin(TimeGenerated, 10s), OperationName
| render areachart kind=unstacked
```

## <a name="next-steps"></a>다음 단계

* 로깅을 사용 하도록 설정 하는 방법 및 다양 한 Azure 서비스에서 지 원하는 메트릭 및 로그 범주를 이해 하려면 Microsoft Azure [메트릭 개요](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics) 및 [Azure 진단 로그 개요](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview) 문서를 참조 하세요.
* Event Hubs에 대한 자세한 내용은 다음 문서를 참조하세요.
  * [Azure Event Hubs 정의](https://docs.microsoft.com/azure/event-hubs/event-hubs-what-is-event-hubs)
  * [Event Hubs 시작](https://docs.microsoft.com/azure/event-hubs/event-hubs-csharp-ephcs-getstarted)
* [Azure Storage에서 메트릭 및 진단 로그 다운로드](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-dotnet#download-blobs)를 읽어보세요.
* [Azure Monitor 로그의 로그 검색 이해를](https://docs.microsoft.com/azure/log-analytics/log-analytics-log-search-new)참조 하세요.
