---
title: Azure 애플리케이션 Insights 에이전트 API 참조
description: 에이전트 API 참조를 Application Insights 합니다. Set-ApplicationInsightsMonitoringConfig. 웹 사이트를 다시 배포 하지 않고 웹 사이트 성능을 모니터링 합니다. 온-프레미스, Vm 또는 Azure에서 호스트 되는 ASP.NET 웹 앱에서 작동 합니다.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: 45dcd2374fc5be40f86d403f8daccf4a6f1d6997
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77189435"
---
# <a name="application-insights-agent-api-set-applicationinsightsmonitoringconfig"></a>Application Insights 에이전트 API: ApplicationInsightsMonitoringConfig

이 문서에서는 [Az. ApplicationMonitor PowerShell 모듈](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/)의 멤버인 cmdlet에 대해 설명 합니다.

## <a name="description"></a>Description

전체를 다시 설치 하지 않고 구성 파일을 설정 합니다.
IIS를 다시 시작 하 여 변경 내용을 적용 합니다.

> [!IMPORTANT] 
> 이 cmdlet을 사용 하려면 관리자 권한이 있는 PowerShell 세션이 있어야 합니다.


## <a name="examples"></a>예

### <a name="example-with-a-single-instrumentation-key"></a>단일 계측 키를 사용 하는 예제
이 예제에서는 현재 컴퓨터의 모든 앱에 단일 계측 키가 할당 됩니다.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

### <a name="example-with-an-instrumentation-key-map"></a>계측 키 맵이 있는 예제
이 예제에서:
- `MachineFilter`은 `'.*'` 와일드 카드를 사용 하 여 현재 컴퓨터와 일치 합니다.
- `AppFilter='WebAppExclude'` `null` 계측 키를 제공 합니다. 지정 된 앱은 계측 되지 않습니다.
- `AppFilter='WebAppOne'` 지정 된 앱에 고유한 계측 키를 할당 합니다.
- `AppFilter='WebAppTwo'` 지정 된 앱에 고유한 계측 키를 할당 합니다.
- 마지막으로 `AppFilter`는 `'.*'` 와일드 카드를 사용 하 여 이전 규칙과 일치 하지 않는 모든 웹 앱을 일치 시키고 기본 계측 키를 할당 합니다.
- 가독성을 위해 공백을 추가 합니다.

```powershell
Enable-ApplicationInsightsMonitoring -InstrumentationKeyMap `
       @(@{MachineFilter='.*';AppFilter='WebAppExclude'},
          @{MachineFilter='.*';AppFilter='WebAppOne';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx1'}},
          @{MachineFilter='.*';AppFilter='WebAppTwo';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2'}},
          @{MachineFilter='.*';AppFilter='.*';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault'}})
```

## <a name="parameters"></a>매개 변수

### <a name="-instrumentationkey"></a>-InstrumentationKey
**필수** 이 매개 변수를 사용 하 여 대상 컴퓨터의 모든 앱에서 사용할 단일 계측 키를 제공 합니다.

### <a name="-instrumentationkeymap"></a>-InstrumentationKeyMap
**필수** 이 매개 변수를 사용 하 여 여러 계측 키를 제공 하 고 각 앱에서 사용 하는 계측 키의 매핑을 제공 합니다.
`MachineFilter`를 설정 하 여 여러 컴퓨터에 대해 단일 설치 스크립트를 만들 수 있습니다.

> [!IMPORTANT]
> 앱은 규칙이 제공 된 순서 대로 규칙에 대해 일치 합니다. 따라서 가장 구체적인 규칙을 먼저 지정 하 고 가장 일반적인 규칙을 마지막에 지정 해야 합니다.

#### <a name="schema"></a>스키마
`@(@{MachineFilter='.*';AppFilter='.*';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'})`

- **Machinefilter** 는 컴퓨터 또는 C# VM 이름의 필수 regex입니다.
    - '. * '는 모두 일치 합니다.
    - ' ComputerName '은 지정한 이름을 가진 컴퓨터에만 일치 합니다.
- **Appfilter** 는 컴퓨터 또는 C# VM 이름의 필수 regex입니다.
    - '. * '는 모두 일치 합니다.
    - ' ApplicationName '은 지정 된 이름의 IIS 앱에만 일치 합니다.
- **InstrumentationKey** 는 위의 두 필터와 일치 하는 앱의 모니터링을 사용 하도록 설정 하는 데 필요 합니다.
    - 모니터링을 제외 하는 규칙을 정의 하려면이 값을 null로 둡니다.


### <a name="-verbose"></a>-Verbose
**일반 매개 변수입니다.** 이 스위치를 사용 하 여 자세한 로그를 표시 합니다.


## <a name="output"></a>출력

기본적으로 출력은 없습니다.

#### <a name="example-verbose-output-from-setting-the-config-file-via--instrumentationkey"></a>-InstrumentationKey를 통해 구성 파일을 설정 하는 방법의 예제 세부 정보 출력

```
VERBOSE: Operation: InstallWithIkey
VERBOSE: InstrumentationKeyMap parsed:
Filters:
0)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx AppFilter: .* MachineFilter: .*
VERBOSE: set config file
VERBOSE: Config File Path:
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\applicationInsights.ikey.config
```

#### <a name="example-verbose-output-from-setting-the-config-file-via--instrumentationkeymap"></a>-InstrumentationKeyMap를 통해 구성 파일을 설정 하는 방법의 예제 세부 정보 출력

```
VERBOSE: Operation: InstallWithIkeyMap
VERBOSE: InstrumentationKeyMap parsed:
Filters:
0)InstrumentationKey:  AppFilter: WebAppExclude MachineFilter: .*
1)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2 AppFilter: WebAppTwo MachineFilter: .*
2)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault AppFilter: .* MachineFilter: .*
VERBOSE: set config file
VERBOSE: Config File Path:
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\applicationInsights.ikey.config
```

## <a name="next-steps"></a>다음 단계

  원격 분석 보기:
 - [메트릭을 탐색](../../azure-monitor/app/metrics-explorer.md) 하 여 성능 및 사용량을 모니터링 합니다.
- [이벤트와 로그를 검색](../../azure-monitor/app/diagnostic-search.md) 하 여 문제를 진단 합니다.
- 고급 쿼리를 위해 [분석을 사용](../../azure-monitor/app/analytics.md) 합니다.
- [대시보드를 만듭니다](../../azure-monitor/app/overview-dashboard.md).
 
 원격 분석 더 추가:
 - [웹 테스트를 만들어](monitor-web-app-availability.md) 사이트가 라이브 상태로 유지 되는지 확인 합니다.
- 웹 [클라이언트 원격 분석을 추가](../../azure-monitor/app/javascript.md) 하 여 웹 페이지 코드에서 예외를 확인 하 고 추적 호출을 사용 하도록 설정 합니다.
- 추적 및 로그 호출을 삽입할 수 있도록 [APPLICATION INSIGHTS SDK를 코드에 추가 합니다](../../azure-monitor/app/asp-net.md) .
 
 Application Insights 에이전트를 사용 하 여 더 많은 작업 수행:
 - 가이드를 사용 하 여 Application Insights 에이전트 [문제를 해결할](status-monitor-v2-troubleshoot.md) 수 있습니다.
 - [구성을 가져와서](status-monitor-v2-api-get-config.md) 설정이 올바르게 기록 되었는지 확인 합니다.
 - 모니터링을 검사할 [상태를 가져옵니다](status-monitor-v2-api-get-status.md) .
