---
title: Azure 변경 내용 추적을 사용하여 문제 해결
description: 이 문서에서는 변경 내용 추적 문제 해결에 대한 정보를 제공합니다.
services: automation
ms.service: automation
ms.subservice: change-inventory-management
author: georgewallace
ms.author: gwallace
ms.date: 01/31/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 2a6610b5cb3f01fc70b1737fc4492e09d9a7637b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61085332"
---
# <a name="troubleshoot-change-tracking-and-inventory"></a>변경 내용 추적 및 인벤토리 문제 해결

## <a name="windows"></a>Windows

### <a name="records-not-showing-windows"></a>시나리오: 변경 내용 추적 레코드가 Windows 머신에 표시되지 않음

#### <a name="issue"></a>문제

변경 내용 추적을 위해 등록된 Windows 머신에 대한 인벤토리 또는 변경 내용 추적 결과가 표시되지 않습니다.

#### <a name="cause"></a>원인

이 오류는 다음과 같은 원인으로 인해 발생할 수 있습니다.

1. **Microsoft Monitoring Agent**가 실행되고 있지 않습니다.
2. Automation 계정과의 통신이 차단되었습니다.
3. 변경 내용 추적용 관리 팩이 다운로드되지 않았습니다.
4. 온보딩 중인 VM이 현재 설치된 Microsoft Monitoring Agent를 사용하여 sysprep 되지 않은 머신에서 복제된 것일 수 있습니다.

#### <a name="resolution"></a>해결 방법

1. **Microsoft Monitoring Agent**(HealthService.exe)가 머신에서 실행되고 있는지 확인합니다.
1. 머신에서 **이벤트 뷰어**를 확인하여 단어가 `changetracking`인 이벤트를 검색합니다.
1. [네트워크 계획](../automation-hybrid-runbook-worker.md#network-planning)을 방문하여 변경 내용 추적이 작동하려면 어떤 주소 및 포트를 허용해야 하는지 알아봅니다.
1. 다음 변경 내용 추적 및 인벤토리 관리 팩이 로컬에 있는지 확인합니다.
    * Microsoft.IntelligencePacks.ChangeTrackingDirectAgent.*
    * Microsoft.IntelligencePacks.InventoryChangeTracking.*
    * Microsoft.IntelligencePacks.SingletonInventoryCollection.*
1. 복제된 이미지를 사용하는 경우 이미지에 sysprep을 수행한 후에 Microsoft Monitoring Agent 에이전트를 설치합니다.

이러한 해결 방법으로 문제가 해결되지 않았으며 고객 지원팀에 문의하는 경우 다음 명령을 실행하여 에이전트에 대한 진단을 수집할 수 있습니다.

에이전트 머신에서 `C:\Program Files\Microsoft Monitoring Agent\Agent\Tools`로 이동하여 다음 명령을 실행합니다.

```cmd
set stop healthservice
StopTracing.cmd
StartTracing.cmd VER
net start healthservice
```

> [!NOTE]
> 기본적으로 오류 추적이 사용되며, 이전 예제와 같은 자세한 오류 메시지를 표시하려는 경우 `VER` 매개 변수를 사용합니다. 정보 추적의 경우 `StartTracing.cmd`를 호출할 때 `INF`를 사용합니다.

## <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

* [Azure 포럼](https://azure.microsoft.com/support/forums/)을 통해 Azure 전문가로부터 답변을 얻습니다.
* [@AzureSupport](https://twitter.com/azuresupport)를 사용하여 연결 – Azure 커뮤니티를 적절한 리소스(답변, 지원 및 전문가)에 연결하여 고객 환경을 개선하는 공식 Microsoft Azure 계정입니다.
* 추가 지원이 필요한 경우, Azure 기술 지원 인시던트를 제출할 수 있습니다. [Azure 지원 사이트](https://azure.microsoft.com/support/options/) 로 가서 **지원 받기**를 선택합니다.
