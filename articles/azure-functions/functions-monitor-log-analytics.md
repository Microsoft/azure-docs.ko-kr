---
title: Azure Monitor 로그를 사용 하 여 Azure Functions 모니터링
description: Azure Functions에서 Azure Monitor 로그를 사용 하 여 함수 실행을 모니터링 하는 방법에 대해 알아봅니다.
author: craigshoemaker
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: 51c611b2565ae0a5a054a45f0aedcb039351b46b
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88208364"
---
# <a name="monitoring-azure-functions-with-azure-monitor-logs"></a>Azure Monitor 로그를 사용 하 여 Azure Functions 모니터링

Azure Functions는 함수를 모니터링 하기 위해 [Azure Monitor 로그](../azure-monitor/platform/data-platform-logs.md) 와의 통합을 제공 합니다. 이 문서에서는 시스템 생성 및 사용자 생성 로그를 Azure Monitor 로그에 보내도록 Azure Functions를 구성 하는 방법을 보여 줍니다.

Azure Monitor 로그는 동일한 작업 영역에 있는 여러 리소스의 로그를 통합 하는 기능을 제공 합니다 .이를 통해 [쿼리](../azure-monitor/log-query/log-query-overview.md) 를 사용 하 여 분석 하 여 수집 된 데이터를 신속 하 게 검색, 통합 및 분석할 수 있습니다.  Azure Portal에서 [Log Analytics](../azure-monitor/log-query/log-query-overview.md)를 사용하여 쿼리를 만들고 테스트한 다음, 이러한 도구를 사용하여 데이터를 직접 분석하거나 [시각화](../azure-monitor/visualizations.md) 또는 [경고 규칙](../azure-monitor/platform/alerts-overview.md)에 사용하기 위해 쿼리를 저장할 수 있습니다.

Azure Monitor는 Azure Data Explorer에서 사용되는 [Kusto 쿼리 언어](/azure/kusto/query/)의 한 버전을 사용합니다. Kusto 쿼리 언어는 단순 로그 쿼리에 적합하지만 집계, 조인, 스마트 분석 등의 고급 기능도 포함합니다. [여러 강좌](../azure-monitor/log-query/get-started-queries.md)를 통해 쿼리 언어를 빠르게 배울 수 있습니다.

> [!NOTE]
> Azure Monitor 로그와의 통합은 현재 Windows 사용량, 프리미엄 및 전용 호스팅 계획에서 실행 되는 함수 앱에 대 한 공개 미리 보기로 제공 됩니다.

## <a name="setting-up"></a>설치

1. [Azure Portal](https://portal.azure.com)의 함수 앱 **모니터링** 섹션에서 **진단 설정**을 선택한 다음 **진단 설정 추가**를 선택 합니다.

   :::image type="content" source="media/functions-monitor-log-analytics/diagnostic-settings-add.png" alt-text="진단 설정 선택":::

1. **진단 설정** 페이지의 **범주 세부 정보** 및 **로그**에서 **FunctionAppLogs**를 선택 합니다.

   **FunctionAppLogs** 테이블은 원하는 로그를 포함 합니다.

1. **대상 세부 정보**에서 **Log Analytics 보내기를**선택 하 고 **Log Analytics 작업 영역**을 선택 합니다. 

1. **진단 설정 이름을**입력 하 고 **저장**을 선택 합니다.

   :::image type="content" source="media/functions-monitor-log-analytics/choose-table.png" alt-text="진단 설정 추가":::

## <a name="user-generated-logs"></a>사용자 생성 로그

사용자 지정 로그를 생성 하려면 해당 언어와 관련 된 로깅 문을 사용 합니다. 샘플 코드 조각은 다음과 같습니다.


# <a name="c"></a>[C#](#tab/csharp)

```csharp
log.LogInformation("My app logs here.");
```

# <a name="java"></a>[Java](#tab/java)

```java
context.getLogger().info("My app logs here.");
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
context.log('My app logs here.');
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
Write-Host "My app logs here."
```

# <a name="python"></a>[Python](#tab/python)

```python
logging.info('My app logs here.')
```

---

## <a name="querying-the-logs"></a>로그 쿼리

생성 된 로그를 쿼리하려면:
 
1. 함수 앱에서 **진단 설정**을 선택 합니다. 

1. **진단 설정** 목록에서 함수 로그를 보내도록 구성 된 Log Analytics 작업 영역을 선택 합니다. 

1. **Log Analytics 작업 영역** 페이지에서 **로그**를 선택 합니다.

   Azure Functions은 **Logmanagement**의 **FunctionAppLogs** 테이블에 모든 로그를 기록 합니다. 

   :::image type="content" source="media/functions-monitor-log-analytics/querying.png" alt-text="Log Analytics 작업 영역의 쿼리 창":::

다음은 몇 가지 샘플 쿼리입니다.

### <a name="all-logs"></a>모든 로그

```

FunctionAppLogs
| order by TimeGenerated desc

```

### <a name="specific-function-logs"></a>특정 함수 로그

```

FunctionAppLogs
| where FunctionName == "<Function name>" 

```

### <a name="exceptions"></a>예외

```

FunctionAppLogs
| where ExceptionDetails != ""  
| order by TimeGenerated asc

```

## <a name="next-steps"></a>다음 단계

- [Azure Functions 개요](functions-overview.md)를 검토 합니다.
- [Azure Monitor 로그](../azure-monitor/platform/data-platform-logs.md)에 대해 자세히 알아보세요.
- [쿼리 언어](../azure-monitor/log-query/get-started-queries.md)에 대해 자세히 알아보기
