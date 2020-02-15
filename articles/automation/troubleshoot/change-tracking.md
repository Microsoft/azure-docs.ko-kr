---
title: Azure 변경 내용 추적을 사용하여 문제 해결
description: Azure Automation 변경 내용 추적 및 인벤토리 기능을 사용 하 여 문제를 해결 하 고 문제를 해결 하는 방법을 알아봅니다.
services: automation
ms.service: automation
ms.subservice: change-inventory-management
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 6cadaea1a20743071acbe8860df02ca7bbdde954
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77198533"
---
# <a name="troubleshoot-change-tracking-and-inventory"></a>변경 내용 추적 및 인벤토리 문제 해결

## <a name="windows"></a>Windows

### <a name="records-not-showing-windows"></a>시나리오: Windows 컴퓨터에 대 한 변경 내용 추적 레코드가 표시 되지 않음

#### <a name="issue"></a>문제

변경 내용 추적 등록 된 Windows 컴퓨터에 대 한 변경 내용 추적 또는 인벤토리 결과가 표시 되지 않습니다.

#### <a name="cause"></a>원인

이 오류는 다음과 같은 경우에 발생할 수 있습니다.

* Microsoft Monitoring Agent 실행 되 고 있지 않습니다.
* Automation 계정에 대 한 다시 통신이 차단 되 고 있습니다.
* 변경 내용 추적 관리 팩이 다운로드 되지 않았습니다.
* 등록 중인 VM이 설치 된 Microsoft Monitoring Agent sysprep 되지 않은 복제 된 컴퓨터에서 가져온 것일 수 있습니다.

#### <a name="resolution"></a>해결 방법

아래에 설명 된 솔루션은 문제를 해결 하는 데 도움이 될 수 있습니다. 여전히 도움이 필요한 경우 진단 정보를 수집 하 고 지원 담당자에 게 문의할 수 있습니다. 에이전트 컴퓨터에서 C:\Program Files\Microsoft Monitoring Agent\Agent\Tools로 이동 하 고 다음 명령을 실행 합니다.

```cmd
net stop healthservice
StopTracing.cmd
StartTracing.cmd VER
net start healthservice
```

> [!NOTE]
> 기본적으로 오류 추적이 사용 됩니다. 앞의 예제와 같이 자세한 오류 메시지를 사용 하려면 *VER* 매개 변수를 사용 합니다. 정보 추적의 경우 **Starttracing**를 호출할 때 *INF* 를 사용 합니다.

##### <a name="microsoft-monitoring-agent-not-running"></a>Microsoft Monitoring Agent 실행 되 고 있지 않음

Microsoft Monitoring Agent (Health service)가 컴퓨터에서 실행 되 고 있는지 확인 합니다.

##### <a name="communication-to-automation-account-blocked"></a>Automation 계정에 대 한 통신이 차단 됨

컴퓨터에서 이벤트 뷰어를 확인 하 고 "changetracking" 라는 단어가 포함 된 이벤트를 찾습니다.

[Hybrid Runbook Worker를 사용 하 여 데이터 센터 또는 클라우드에서 리소스 자동화](../automation-hybrid-runbook-worker.md#network-planning) 를 참조 하 여 변경 내용 추적 작동 하는 데 허용 해야 하는 주소 및 포트에 대해 알아보세요.

##### <a name="management-packs-not-downloaded"></a>다운로드 되지 않은 관리 팩

다음 변경 내용 추적 및 인벤토리 관리 팩이 로컬로 설치 되어 있는지 확인 합니다.

* Microsoft.IntelligencePacks.ChangeTrackingDirectAgent.*
* Microsoft.IntelligencePacks.InventoryChangeTracking.*
* Microsoft.IntelligencePacks.SingletonInventoryCollection.*

##### <a name="vm-from-cloned-machine-that-has-not-been-sysprepped"></a>복제 된 컴퓨터에서 sysprep 되지 않은 VM

복제 된 이미지를 사용 하는 경우 먼저 이미지를 sysprep 한 다음 Microsoft Monitoring Agent를 설치 합니다.

## <a name="linux"></a>Linux

### <a name="scenario-no-change-tracking-or-inventory-results-on-linux-machines"></a>시나리오: Linux 컴퓨터에서 변경 내용 추적 또는 인벤토리 결과 없음

#### <a name="issue"></a>문제

변경 내용 추적 등록 된 Linux 컴퓨터에 대 한 인벤토리 또는 변경 내용 추적 결과가 표시 되지 않습니다. 

#### <a name="cause"></a>원인
이 문제와 관련 된 가능한 원인은 다음과 같습니다.
* Linux 용 Log Analytics 에이전트가 실행 되 고 있지 않습니다.
* Linux 용 Log Analytics 에이전트가 올바르게 구성 되지 않았습니다.
* FIM (파일 무결성 모니터링) 충돌이 있습니다.

#### <a name="resolution"></a>해결 방법 

##### <a name="log-analytics-agent-for-linux-not-running"></a>Linux 용 Log Analytics 에이전트가 실행 되 고 있지 않음

Linux 용 Log Analytics 에이전트 (omsagent)에 대 한 데몬이 사용자의 컴퓨터에서 실행 되 고 있는지 확인 합니다. Automation 계정에 연결 된 Log Analytics 작업 영역에서 다음 쿼리를 실행 합니다.

```loganalytics Copy
Heartbeat
| summarize by Computer, Solutions
```

쿼리 결과에 컴퓨터가 표시 되지 않으면 최근에 체크 인 되지 않은 것입니다. 로컬 구성 문제가 있는 것 이므로 에이전트를 다시 설치 해야 합니다. 설치 및 구성에 대 한 자세한 내용은 [Log Analytics 에이전트를 사용 하 여 로그 데이터 수집](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent)을 참조 하세요. 

컴퓨터가 쿼리 결과에 표시 되 면 범위 구성을 확인 합니다. [Azure Monitor의 모니터링 솔루션을 대상으로 지정을](https://docs.microsoft.com/azure/azure-monitor/insights/solution-targeting)참조 하세요.

이 문제에 대 한 자세한 문제 해결 방법은 [문제: Linux 데이터가 표시 되지 않음](https://docs.microsoft.com/azure/azure-monitor/platform/agent-linux-troubleshoot#issue-you-are-not-seeing-any-linux-data)을 참조 하세요.

##### <a name="log-analytics-agent-for-linux-not-configured-correctly"></a>Linux 용 Log Analytics 에이전트가 올바르게 구성 되지 않음

OMS 로그 수집기 도구를 사용 하 여 로그 및 명령줄 출력 컬렉션에 대해 Linux 용 Log Analytics 에이전트가 올바르게 구성 되어 있지 않을 수 있습니다. [변경 내용 추적 솔루션을 사용 하 여 사용자 환경에서 변경 내용 추적을](../change-tracking.md)참조 하세요.

##### <a name="fim-conflicts"></a>FIM 충돌

Azure Security Center의 FIM 기능이 Linux 파일의 무결성에 대해 잘못 된 유효성 검사를 할 수 있습니다. FIM이 작동 하 고 Linux 파일 모니터링에 대해 올바르게 구성 되어 있는지 확인 합니다. [변경 내용 추적 솔루션을 사용 하 여 사용자 환경에서 변경 내용 추적을](../change-tracking.md)참조 하세요.

## <a name="next-steps"></a>다음 단계

문제가 표시 되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 사용 하 여 더 많은 지원을 받을 수 있습니다.

* Azure [포럼](https://azure.microsoft.com/support/forums/)을 통해 azure 전문가 로부터 답변을 받으세요.
* [@AzureSupport](https://twitter.com/azuresupport)를 사용하여 연결 – Azure 커뮤니티를 적절한 리소스(답변, 지원 및 전문가)에 연결하여 고객 환경을 개선하는 공식 Microsoft Azure 계정입니다.
* 추가 지원이 필요한 경우, Azure 기술 지원 인시던트를 제출할 수 있습니다. [Azure 지원 사이트](https://azure.microsoft.com/support/options/) 로 가서 **지원 받기**를 선택합니다.
