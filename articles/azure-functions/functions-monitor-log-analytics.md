---
title: Azure Monitor 로그를 사용 하 여 Azure Functions 모니터링
description: Azure Functions에서 Azure Monitor 로그를 사용 하 여 함수 실행을 모니터링 하는 방법에 대해 알아봅니다.
author: ahmedelnably
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: aelnably
ms.openlocfilehash: f4af646569edc8a9274af752e7e4f2a36585ae4d
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75769101"
---
# <a name="monitoring-azure-functions-with-azure-monitor-logs"></a>Azure Monitor 로그를 사용 하 여 Azure Functions 모니터링

Azure Functions는 함수를 모니터링 하기 위해 [Azure Monitor 로그](../azure-monitor/platform/data-platform-logs.md) 와의 통합을 제공 합니다. 이 문서에서는 시스템 생성 및 사용자 생성 로그를 Azure Monitor 로그에 보내도록 Azure Functions를 구성 하는 방법을 보여 줍니다.

Azure Monitor 로그는 동일한 작업 영역에 있는 여러 리소스의 로그를 통합 하는 기능을 제공 합니다 .이를 통해 [쿼리](../azure-monitor/log-query/log-query-overview.md) 를 사용 하 여 분석 하 여 수집 된 데이터를 신속 하 게 검색, 통합 및 분석할 수 있습니다.  Azure Portal에서 [Log Analytics](../azure-monitor/log-query/portals.md)를 사용하여 쿼리를 만들고 테스트한 다음, 이러한 도구를 사용하여 데이터를 직접 분석하거나 [시각화](../azure-monitor/visualizations.md) 또는 [경고 규칙](../azure-monitor/platform/alerts-overview.md)에 사용하기 위해 쿼리를 저장할 수 있습니다.

Azure Monitor는 Azure Data Explorer에서 사용되는 [Kusto 쿼리 언어](/azure/kusto/query/)의 한 버전을 사용합니다. Kusto 쿼리 언어는 단순 로그 쿼리에 적합하지만 집계, 조인, 스마트 분석 등의 고급 기능도 포함합니다. [여러 강좌](../azure-monitor/log-query/get-started-queries.md)를 통해 쿼리 언어를 빠르게 배울 수 있습니다.

> [!NOTE]
> Azure Monitor 로그와의 통합은 현재 Windows 사용량, 프리미엄 및 전용 호스팅 계획에서 실행 되는 함수 앱에 대한 공개 미리 보기로 제공 됩니다.

## <a name="setting-up"></a>설치

**모니터링** 섹션에서 **진단 설정** 을 선택 하 고 진단 설정 **추가**를 클릭 합니다.

![진단 설정 추가](media/functions-monitor-log-analytics/diagnostic-settings-add.png)

**진단 설정** 페이지에서 **Log Analytics 보내기**를 선택 하 고 Log Analytics 작업 영역을 선택 합니다. **로그** 아래에서 **FunctionAppLogs**를 선택 합니다 .이 테이블에는 원하는 로그가 포함 됩니다.

![진단 설정 추가](media/functions-monitor-log-analytics/choose-table.png)

## <a name="user-generated-logs"></a>사용자 생성 로그

사용자 지정 로그를 생성 하려면 해당 언어에 따라 특정 로깅 문을 사용할 수 있습니다. 샘플 코드 조각은 다음과 같습니다.


# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
log.LogInformation("My app logs here.");
```

# <a name="javatabjava"></a>[Java](#tab/java)

```java
context.getLogger().info("My app logs here.");
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
context.log('My app logs here.');
```

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

```powershell
Write-Host "My app logs here."
```

# <a name="pythontabpython"></a>[Python](#tab/python)

```python
logging.info('My app logs here.')
```

---

## <a name="querying-the-logs"></a>로그 쿼리

생성 된 로그를 쿼리하려면 함수 로그를 보내도록 구성 된 Log Analytics 작업 영역으로 이동한 후 **로그**를 클릭 합니다.

![LA 작업 영역의 쿼리 창](media/functions-monitor-log-analytics/querying.png)

모든 로그를 **FunctionAppLogs** 테이블에 기록 하는 Azure Functions 다음은 몇 가지 샘플 쿼리입니다.

### <a name="all-logs"></a>모든 로그

```

FunctionAppLogs
| order by TimeGenerated desc

```

### <a name="a-specific-function-logs"></a>특정 함수 로그

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

- [Azure Functions 개요](functions-overview.md) 를 검토 합니다.
- [Azure Monitor 로그](../azure-monitor/platform/data-platform-logs.md) 에 대해 자세히 알아보기
- [쿼리 언어](../azure-monitor/log-query/get-started-queries.md)에 대해 자세히 알아보기
