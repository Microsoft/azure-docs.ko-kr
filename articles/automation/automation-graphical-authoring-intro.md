---
title: Azure Automation에서 그래픽 작성
description: 그래픽 작성을 통해 코드 작업 없이 Azure Automation에 대한 Runbook을 만들 수 있습니다. 이 문서에서는 그래픽 작성을 소개하고 그래픽 Runbook 만들기를 시작하는 데 필요한 모든 세부 정보를 제공합니다.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: ae732ab5c73dbec4a2aef6521b9edb490079112e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60740821"
---
# <a name="graphical-authoring-in-azure-automation"></a>Azure Automation에서 그래픽 작성

그래픽 작성을 통해 기본 Windows PowerShell 또는 PowerShell 워크플로 코드의 복잡성 없이 Azure Automation용 Runbook을 만들 수 있습니다. cmdlet 및 runbook의 라이브러리의 캔버스에 작업을 추가하고 이를 함께 연결하여 하나의 워크플로를 구성할 수 있습니다. System Center Orchestrator 또는 SMA(서비스 관리 Automation)로 작업해 본 경우에 익숙하게 느껴지실 것입니다.

이 문서에서는 그래픽 작성을 소개하고 그래픽 Runbook 만들기를 시작하는 데 필요한 개념을 설명합니다.

## <a name="graphical-runbooks"></a>그래픽 Runbook

Azure Automation의 모든 Runbook은 Windows PowerShell 워크플로입니다. 그래픽 및 그래픽 PowerShell 워크플로 runbook은 자동화 작업자가 실행하는 PowerShell 코드를 생성하지만 사용자가 이를 보거나 직접 수정할 수는 없습니다. 그래픽 runbook을 그래픽 PowerShell 워크플로 runbook으로 변환할 수 있고 그 반대의 경우도 가능하지만 텍스트 runbook으로 변환할 수는 없습니다. 기존 텍스트 runbook을 그래픽 편집기로 가져올 수 없습니다.

## <a name="overview-of-graphical-editor"></a>그래픽 편집기 개요

그래픽 Runbook을 만들거나 편집하여 Azure 포털에서 그래픽 편집기를 열 수 있습니다.

![그래픽 작업 영역](media/automation-graphical-authoring-intro/runbook-graphical-editor.png)

다음 섹션에서는 그래픽 편집기의 컨트롤에 대해 설명합니다.

### <a name="canvas"></a>캔버스

캔버스는 Runbook을 디자인하는 곳입니다. 라이브러리 컨트롤의 노드에서 Runbook에 활동을 추가하고 Runbook의 논리를 정의하는 링크와 연결합니다.

확대 및 축소할 캔버스의 맨 아래에서 컨트롤을 사용할 수 있습니다.

### <a name="library-control"></a>라이브러리 컨트롤

라이브러리 컨트롤은 Runbook에 추가할 [활동](#activities) 을 선택하는 곳입니다. 이를 다른 활동에 연결하는 캔버스에 추가할 수 있습니다. 라이브러리 컨트롤은 다음 표에 설명된 4개의 섹션으로 구성됩니다.

| Section | Description |
|:--- |:--- |
| Cmdlet |Runbook에서 사용할 수 있는 모든 cmdlet을 포함합니다. 이러한 cmdlet은 모듈별로 구성됩니다. 자동화 계정에 설치한 모든 모듈을 사용할 수 있습니다. |
| Runbook |자동화 계정의 Runbook을 포함합니다. 이러한 runbook을 자식 runbook으로 사용될 캔버스에 추가할 수 있습니다. 편집 중인 runbook인 동일한 코어 유형의 runbook만 표시됩니다. 그래픽 runbook의 경우 PowerShell 기반 runbook만 표시되는 반면 그래픽 PowerShell 워크플로 runbook의 경우 PowerShell 워크플로 기반 runbook만 표시됩니다. |
| 자산 |Runbook에서 사용할 수 있는 자동화 계정의 [자동화 자산](/previous-versions/azure/dn939988(v=azure.100)) 을 포함합니다. 자산을 Runbook에 추가하면 선택한 자산을 가져오는 워크플로 활동이 추가됩니다. 변수 자산의 경우 변수를 가져오는 활동을 추가할지 또는 변수를 설정하는 활동을 추가할지 선택할 수 있습니다. |
| Runbook 컨트롤 |현재 Runbook에서 사용할 수 있는 Runbook 컨트롤 활동을 포함합니다. *분기 동기화* 는 여러 입력을 사용하며 모두 완료될 때까지 기다렸다가 워크플로를 계속합니다. *코드* 작업은 그래픽 runbook 종류에 따라 하나 이상의 PowerShell 또는 PowerShell 워크플로 코드 줄을 실행합니다. 사용자 지정 코드 또는 다른 작업으로 수행하기 어려운 기능에 이 작업을 사용할 수 있습니다. |

### <a name="configuration-control"></a>구성 컨트롤

구성 컨트롤에서는 캔버스에서 선택한 개체에 대한 세부 정보를 제공합니다. 이 컨트롤에서 사용할 수 있는 속성은 선택한 개체의 유형에 따라 달라 집니다. 구성 컨트롤에서 옵션을 선택하면 추가 정보를 제공하도록 추가 블레이드가 열립니다.

### <a name="test-control"></a>테스트 컨트롤

테스트 컨트롤은 그래픽 편집기를 처음 시작할 때 표시되지 않습니다. 대화형으로 [그래픽 Runbook을 테스트](#graphical-runbook-procedures)할 때 열립니다.

## <a name="graphical-runbook-procedures"></a>그래픽 Runbook 절차

### <a name="exporting-and-importing-a-graphical-runbook"></a>그래픽 Runbook 내보내기 및 가져오기

게시된 버전의 그래픽 Runbook만 내보낼 수 있습니다. Runbook이 아직 게시되지 않은 경우 **내보내기** 단추를 사용할 수 없습니다. **내보내기** 단추를 클릭하면 Runbook이 로컬 컴퓨터에 다운로드됩니다. 파일 이름은 *graphrunbook* 확장명이 추가된 Runbook 이름과 일치합니다.

Runbook을 추가할 때 **가져오기** 옵션을 선택하여 그래픽 또는 그래픽 PowerShell 워크플로 Runbook 파일을 가져올 수 있습니다. 가져올 파일을 선택할 때 동일한 **이름** 을 유지하거나 새 이름을 제공할 수 있습니다. Runbook 형식 필드에서는 선택한 파일을 평가한 후에 runbook의 형식을 표시하고 정확하지 않은 다른 형식을 선택하려고 하면 잠재적인 충돌이 일어날 가능성이 있다는 메시지가 나타나며 변환하는 동안 구문 오류가 발생할 수 있습니다.

![Runbook 가져오기](media/automation-graphical-authoring-intro/runbook-import-revised20165.png)

### <a name="testing-a-graphical-runbook"></a>그래픽 Runbook 테스트

게시된 버전의 Runbook을 변경되지 않은 상태로 두고 Azure 포털에서 초안 버전의 Runbook을 테스트하거나, Runbook을 게시한 후 새 Runbook을 테스트할 수 있습니다. 이를 통해 게시된 버전을 바꾸기 전에 Runbook이 올바르게 작동하는지 확인할 수 있습니다. Runbook을 테스트할 때 초안 Runbook이 실행되며 해당 Runbook에서 수행하는 모든 작업이 완료됩니다. 작업 기록은 만들어지지 않지만 테스트 출력 창에 출력이 표시됩니다.

편집용 Runbook을 열고 **테스트 창** 단추를 클릭하여 Runbook의 테스트 컨트롤을 엽니다.

테스트 컨트롤에서 입력 매개 변수를 묻는 메시지가 표시되면 **시작** 단추를 클릭하여 Runbook을 시작할 수 있습니다.

### <a name="publishing-a-graphical-runbook"></a>그래픽 Runbook 게시

Azure Automation의 각 Runbook에는 초안 버전과 게시된 버전이 있습니다. 게시된 버전만 실행할 수 있으며 초안 버전만 편집할 수 있습니다. 초안 버전을 변경해도 게시된 버전은 영향을 받지 않습니다. 초안 버전을 사용할 수 있는 준비가 완료되면 이를 게시합니다. 그러면 초안 버전이 게시된 버전을 덮어씁니다.

편집용 Runbook을 열고 **게시** 단추를 클릭하여 그래픽 Runbook을 게시할 수 있습니다.

아직 게시하지 않은 Runbook은 상태가 **신규**입니다. 게시된 Runbook은 상태가 **게시됨**입니다. Runbook을 게시한 후 편집할 때 초안 버전과 게시된 버전이 다른 경우 Runbook의 상태는 **편집 중**이 됩니다.

![Runbook 상태](media/automation-graphical-authoring-intro/runbook-statuses-revised20165.png)

게시된 버전의 Runbook으로 되돌릴 수 있는 옵션도 있습니다. 이 옵션을 사용하면 Runbook이 마지막으로 게시된 이후에 적용된 모든 변경 내용이 취소되고 초안 버전이 Runbook이 게시된 버전으로 바뀝니다.

## <a name="activities"></a>활동

활동은 Runbook의 구성 요소입니다. 활동은 PowerShell cmdlet, 자식 Runbook 또는 워크플로 활동일 수 있습니다. 라이브러리 컨트롤에서 활동을 마우스 오른쪽 단추로 클릭하고 **캔버스에 추가**를 선택하여 Runbook에 활동을 추가합니다. 그런 다음 활동을 클릭하고 끌어서 캔버스의 원하는 위치에 배치할 수 있습니다. 캔버스에서 활동의 위치는 Runbook의 작업에 영향을 주지 않습니다. 해당 작업을 시각화하는 데 가장 적합한 곳에 Runbook을 배치할 수 있습니다.

![캔버스에 추가](media/automation-graphical-authoring-intro/add-to-canvas-revised20165.png)

캔버스에서 활동을 선택하여 구성 블레이드에서 해당 속성 및 매개 변수를 구성할 수 있습니다. 활동의 **레이블** 을 이해하기 쉽게 변경할 수 있습니다. 여전히 원래 cmdlet이 실행되며 그래픽 편집기에서 사용할 표시 이름만 변경됩니다. 레이블은 Runbook 내에서 고유해야 합니다.

### <a name="parameter-sets"></a>매개 변수 집합

매개 변수 집합은 특정 cmdlet에 대한 값을 허용하는 필수 및 선택적 매개 변수를 정의합니다. 모든 cmdlet에는 적어도 하나의 매개 변수 집합이 있으며, 여러 매개 변수 집합이 있는 cmdlet도 있습니다. cmdlet에 여러 매개 변수 집합이 있는 경우 매개 변수를 구성하려면 먼저 사용할 매개 변수 집합을 선택해야 합니다. 구성할 수 있는 매개 변수는 선택한 매개 변수 집합에 따라 달라집니다. **매개 변수 집합** 을 선택하고 다른 집합을 선택하여 활동에서 사용하는 매개 변수 집합을 변경할 수 있습니다. 이 경우 구성한 모든 매개 변수 값이 손실됩니다.

다음 예제에서 Get-AzureRmVM cmdlet에는 세 개의 매개 변수 집합이 있습니다. 매개 변수 집합 중 하나를 선택할 때까지 매개 변수 값을 구성할 수 없습니다. ListVirtualMachineInResourceGroupParamSet 매개 변수 집합은 리소스 그룹에 모든 가상 머신을 반환하는 데 사용되며 단일 선택적 매개 변수를 가지고 있습니다. **GetVirtualMachineInResourceGroupParamSet**은 반환할 가상 머신을 지정하는 데 사용되며, 두개의 필수 매개 변수와 하나의 선택적 매개 변수를 가집니다.

![매개 변수 집합](media/automation-graphical-authoring-intro/get-azurermvm-parameter-sets.png)

#### <a name="parameter-values"></a>매개 변수 값

매개 변수 값을 지정하려면 데이터 원본을 선택하여 값 지정 방법을 결정합니다. 특정 매개 변수에 사용할 수 있는 데이터 원본은 해당 매개 변수의 유효한 값에 따라 달라집니다. 예를 들어 Null은 null 값을 허용하지 않는 매개 변수에 사용 가능한 옵션이 아닙니다.

| 데이터 원본 | Description |
|:--- |:--- |
| 상수 값 |매개 변수 값을 입력합니다. 다음 데이터 형식에만 사용할 수 있습니다. Int32, Int64, String, Boolean, DateTime, Switch |
| 활동 출력 |워크플로에서 현재 활동 앞에 오는 활동의 출력입니다. 유효한 모든 활동이 나열됩니다. 매개 변수 값에 해당 출력을 사용할 활동만 선택하세요. 활동에서 여러 속성을 가진 개체를 출력하는 경우에는 활동을 선택한 후 속성 이름을 입력할 수 있습니다. |
| Runbook 입력 |작업 매개 변수에 대한 입력으로 runbook의 입력 매개 변수를 선택합니다. |
| 변수 자산 |입력으로 Automation 변수를 선택합니다. |
| 자격 증명 자산 |입력으로 Automation 자격 증명을 선택합니다. |
| 인증서 자산 |입력으로 Automation 인증서를 선택합니다. |
| 연결 자산 |입력으로 Automation 연결을 선택합니다. |
| PowerShell 식 |간단한 [PowerShell 식](#powershell-expressions)을 지정합니다. 활동 전에 식이 계산되며, 그 결과가 매개 변수 값에 사용됩니다. 변수를 사용하여 활동의 출력 또는 Runbook 입력 매개 변수를 참조할 수 있습니다. |
| 구성되지 않음 |이전에 구성된 모든 값을 지웁니다. |

#### <a name="optional-additional-parameters"></a>선택적 추가 매개 변수

모든 cmdlet에는 추가 매개 변수를 제공할 수 있는 옵션이 있습니다. 이는 PowerShell 일반 매개 변수 또는 기타 사용자 지정 매개 변수입니다. PowerShell 구문을 사용하여 매개 변수를 제공할 수 있는 텍스트 상자가 표시됩니다. 예를 들어 **Verbose** 일반 매개 변수를 사용하려면 **"-Verbose:$True"** 를 지정합니다.

### <a name="retry-activity"></a>작업 다시 시도

**동작 다시 시도** 를 사용하면 루프와 비슷한 특정 조건이 충족될 때까지 활동이 여러 번 실행될 수 있습니다. 여러 번 실행해야 하고 오류가 발생하기 쉬운 작업에 이 기능을 사용할 수 있으며 성공하기 위해 한 번 이상 시도하거나 유효한 데이터에 대한 작업의 출력 정보를 테스트해야 할 수 있습니다.

활동에 재시도 사용하면 지연 및 조건을 설정할 수 있습니다. 지연은 작업을 다시 실행하기 전에 Runbook이 대기하는 시간(분 또는 초 단위로 측정됨)입니다. 지연이 지정되지 않은 경우 작업은 완료된 후에 즉시 다시 실행됩니다.

![작업 다시 시도 지연](media/automation-graphical-authoring-intro/retry-delay.png)

다시 시도 조건은 작업이 실행된 이후 마다 평가되는 PowerShell 식입니다. 식이 True로 확인되면 작업을 다시 실행합니다. 식이 False로 확인되면 활동이 다시 실행되지 않고 Runbook이 다음 활동으로 이동합니다.

![작업 다시 시도 지연](media/automation-graphical-authoring-intro/retry-condition.png)

다시 시도 조건은 작업 다시 시도에 대한 정보에 액세스를 제공하는 $RetryData라는 변수를 사용할 수 있습니다. 이 변수는 다음 테이블의 속성을 가집니다.

| 자산 | Description |
|:--- |:--- |
| NumberOfAttempts |활동이 실행된 횟수입니다. |
| Output |활동의 마지막 실행에서 출력입니다. |
| TotalDuration |작업이 처음으로 시작된 이후 경과된 시간입니다. |
| StartedAt |작업을 처음으로 시작한 UTC 형식의 시간입니다. |

다음은 활동 다시 시도 조건의 예제입니다.

```powershell-interactive
# Run the activity exactly 10 times.
$RetryData.NumberOfAttempts -ge 10
```

```powershell-interactive
# Run the activity repeatedly until it produces any output.
$RetryData.Output.Count -ge 1
```

```powershell-interactive
# Run the activity repeatedly until 2 minutes has elapsed.
$RetryData.TotalDuration.TotalMinutes -ge 2
```

작업에 다시 시도 조건을 구성하면 이를 알리는 두 개의 시각 신호가 작업에 포함됩니다. 하나는 작업에 표시되고 다른 하나는 활동의 구성을 검토할 경우 표시됩니다.

![작업 다시 시도 시각적 표시기](media/automation-graphical-authoring-intro/runbook-activity-retry-visual-cue.png)

### <a name="workflow-script-control"></a>워크플로 스크립트 컨트롤

코드 컨트롤은 다른 방법으로 사용할 수 없는 기능을 제공하기 위해 작성된 그래픽 runbook의 형식에 따라 PowerShell 또는 PowerShell 워크플로 스크립트를 허용하는 특별한 작업입니다. 매개 변수를 사용할 수 없지만 활동 출력 및 Runbook 입력 매개 변수에 변수를 사용할 수는 있습니다. 활동의 출력은 나가는 링크가 없는 경우 데이터 버스에 추가되고, 나가는 링크가 있는 경우 Runbook의 출력에 추가됩니다.

예를 들어 다음 코드는 $NumberOfDays라는 Runbook 입력 변수를 사용하여 날짜 계산을 수행합니다. 그런 다음 Runbook의 후속 활동에서 사용할 출력으로 계산된 날짜 시간을 보냅니다.

```powershell-interactive
$DateTimeNow = (Get-Date).ToUniversalTime()
$DateTimeStart = ($DateTimeNow).AddDays(-$NumberOfDays)}
$DateTimeStart
```

## <a name="links-and-workflow"></a>링크 및 워크플로

그래픽 Runbook의 **링크** 는 두 개의 활동을 연결합니다. 원본 활동에서 대상 활동을 가리키는 화살표로 캔버스에 표시됩니다. 활동은 화살표 방향으로 실행되며, 원본 활동이 완료된 대상 활동이 시작됩니다.

### <a name="create-a-link"></a>링크 만들기

원본 활동을 선택하고 셰이프 아래쪽에 있는 원을 클릭하여 두 활동 간의 링크를 만듭니다. 화살표를 대상 활동으로 끌어 놓습니다.

![링크 만들기](media/automation-graphical-authoring-intro/create-link-revised20165.png)

링크를 선택하여 구성 블레이드에서 해당 속성을 구성할 수 있습니다. 여기에는 다음 표에 설명된 링크 형식이 포함됩니다.

| 링크 형식 | Description |
|:--- |:--- |
| 파이프라인 |대상 활동은 원본 활동의 각 개체 출력에 대해 한 번씩 실행됩니다. 원본 활동의 출력이 없는 경우에는 대상 활동이 실행되지 않습니다. 원본 활동의 출력은 개체로 제공됩니다. |
| 시퀀스 |대상 활동이 한 번만 실행됩니다. 원본 활동에서 개체 배열을 받습니다. 원본 활동의 출력이 개체 배열로 제공됩니다. |

### <a name="starting-activity"></a>시작 활동

그래픽 Runbook은 들어오는 링크가 없는 활동으로 시작됩니다. Runbook의 시작 활동 역할을 하는 활동이 하나뿐인 경우도 있습니다. 여러 활동에 들어오는 링크가 없는 경우 이러한 활동을 병렬로 실행하면 Runbook이 시작됩니다. 각 활동이 완료되는 대로 링크에 따라 다른 활동을 실행합니다.

### <a name="conditions"></a>조건

링크에 대한 조건을 지정하면 조건이 true로 확인된 경우에만 대상 활동이 실행됩니다. 일반적으로 조건에서 $ActivityOutput 변수를 사용하여 원본 활동의 출력을 검색합니다.

파이프라인 링크의 경우 단일 개체에 대한 조건을 지정하면 원본 활동의 각 개체 출력에 대해 조건이 평가됩니다. 그런 다음 조건을 충족하는 각 개체에 대해 대상 활동이 실행됩니다. 예를 들어 원본 활동이 Get-AzureRmVm인 경우 조건부 파이프라인 링크에 다음 구문을 사용하여 *그룹1*이라는 리소스 그룹의 가상 머신만을 검색할 수 있습니다.

```powershell-interactive
$ActivityOutput['Get Azure VMs'].Name -match "Group1"
```

시퀀스 링크의 경우 원본 활동의 모든 개체 출력이 포함된 단일 배열이 반환되므로 조건이 한 번만 평가됩니다. 따라서 시퀀스 링크는 파이프라인 링크처럼 필터링에 사용할 수 없으며 다음 활동의 실행 여부만 결정합니다. 시작 VM runbook에서 다음 작업 집합에 예를 들어 보겠습니다.

![시퀀스와 조건부 링크](media/automation-graphical-authoring-intro/runbook-conditional-links-sequence.png)

값을 확인하는 세 개의 시퀀스 링크는 적절한 조치(단일 VM 시작, 리소스 그룹의 모든 VM 또는 구독의 모든 VM 시작 등)가 무엇인지 결정하기 위해 VM 이름 및 리소스 그룹 이름을 나타내는 두 개의 runbook 입력 매개 변수를 제공했습니다. Azure에 연결과 단일 VM 가져오기 간의 시퀀스 링크의 경우 조건 논리는 다음과 같습니다.

```powershell-interactive
<#
Both VMName and ResourceGroupName runbook input parameters have values
#>
(
(($VMName -ne $null) -and ($VMName.Length -gt 0))
) -and (
(($ResourceGroupName -ne $null) -and ($ResourceGroupName.Length -gt 0))
)
```

조건부 링크를 사용하면 원본 활동에서 해당 분기의 다른 활동까지 사용 가능한 데이터가 조건별로 필터링됩니다. 활동이 여러 링크의 원본인 경우에는 각 분기의 활동에 사용할 수 있는 데이터가 해당 분기에 연결된 링크의 조건에 따라 달라집니다.

예를 들어 아래 Runbook의 **Start-AzureRmVm** 작업은 모든 가상 머신을 시작합니다. 두 개의 조건부 링크가 있습니다. 첫 번째 조건부 링크는 *$ActivityOutput['Start-AzureRmVM'].IsSuccessStatusCode -eq $true* 식을 사용하여 Start-AzureRmVm 작업이 성공적으로 완료된 경우 필터링합니다. 두 번째 조건부 링크는 *$ActivityOutput['Start-AzureRmVM'].IsSuccessStatusCode -ne $true* 식을 사용하여 Start-AzureRmVm 작업이 가상 머신을 시작하는 데 실패한 경우 필터링합니다.

![조건부 링크 예제](media/automation-graphical-authoring-intro/runbook-conditional-links.png)

첫 번째 링크를 따르고 Get-AzureVM의 활동 출력을 사용하는 모든 활동은 Get-AzureVM이 실행될 당시에 시작된 가상 머신만 가져옵니다. 두 번째 링크를 따르는 모든 활동은 Get-AzureVM이 실행될 당시에 중지된 가상 머신만 가져옵니다. 세 번째 링크를 따르는 모든 활동은 실행 상태에 상관없이 모든 가상 머신을 가져옵니다.

### <a name="junctions"></a>분기 동기화

분기 동기화는 들어오는 모든 분기가 완료될 때까지 대기하는 특별한 활동입니다. 이를 통해 여러 활동을 병렬로 실행하고 모든 활동이 완료된 후 다음 단계를 진행할 수 있습니다.

분기 동기화에서는 들어오는 링크의 개수에 제한이 없지만 이러한 링크가 두 개 이상이 아닌 경우에는 파이프라인일 수 있습니다. 들어오는 시퀀스 링크의 수는 제한되지 않습니다. 여러 개의 들어오는 파이프라인 링크가 있는 분기 동기화를 만들고 Runbook을 저장할 수 있지만 이를 실행하면 오류가 발생합니다.

아래 예제는 가상 머신 집합을 시작하고 이와 동시에 해당 컴퓨터에 적용할 패치를 다운로드하는 Runbook의 일부입니다. 두 프로세스 모두 완료된 후 Runbook을 계속하기 위해 분기 동기화가 사용되었습니다.

![분기 동기화](media/automation-graphical-authoring-intro/runbook-junction.png)

### <a name="cycles"></a>주기

주기는 대상 활동이 해당 원본 활동에 다시 연결되거나 최종적으로 해당 원본에 다시 연결되는 다른 활동에 연결된 경우를 의미합니다. 주기는 그래픽 작성에서 현재 허용되지 않습니다. Runbook에 주기가 있는 경우 정상적으로 저장되지만 실행하면 오류가 발생합니다.

![주기](media/automation-graphical-authoring-intro/runbook-cycle.png)

### <a name="sharing-data-between-activities"></a>활동 간의 데이터 공유

나가는 링크가 있는 활동에서 출력된 모든 데이터는 Runbook의 *데이터 버스* 에 기록됩니다. Runbook의 모든 활동은 데이터 버스에 있는 데이터를 사용하여 매개 변수 값을 채우거나 스크립트 코드에 포함할 수 있습니다. 활동은 워크플로의 모든 이전 활동에 대한 출력에 액세스할 수 있습니다.

데이터가 데이터 버스에 기록되는 방식은 활동의 링크 형식에 따라 달라집니다. **파이프라인**의 경우 데이터는 여러 개체로 출력됩니다. **시퀀스** 링크의 경우 데이터는 배열로 출력됩니다. 값이 하나만 있으면 단일 요소 배열로 출력됩니다.

두 가지 방법 중 하나를 사용하여 데이터 버스의 데이터에 액세스할 수 있습니다. 첫째는 **활동 출력** 데이터 원본을 사용하여 다른 활동의 매개 변수를 채우는 것입니다. 출력이 개체인 경우에는 단일 속성을 지정할 수 있습니다.

![활동 출력](media/automation-graphical-authoring-intro/activity-output-datasource-revised20165.png)

**PowerShell 식** 데이터 원본에서 활동 출력을 검색하거나 ActivityOutput 변수를 사용하여 **워크플로 스크립트** 활동에서 활동 출력을 검색할 수도 있습니다. 출력이 개체인 경우에는 단일 속성을 지정할 수 있습니다. ActivityOutput 변수는 다음 구문을 사용합니다.

```powershell-interactive
$ActivityOutput['Activity Label']
$ActivityOutput['Activity Label'].PropertyName
```

### <a name="checkpoints"></a>검사점

작업에서 [검사점 Runbook](automation-powershell-workflow.md#checkpoints) 을 선택하여 그래픽 PowerShell 워크플로 Runbook에서 *검사점* 을 설정할 수 있습니다. 활동을 실행한 후에 검사점을 설정하게 됩니다.

![검사점](media/automation-graphical-authoring-intro/set-checkpoint.png)

검사점은 그래픽 PowerShell 워크플로 runbook에서 사용할 수 있고 그래픽 runbook에서는 사용할 수 없습니다. Runbook이 Azure cmdlet를 사용하면 다른 작업자의 이 검사점에서 Runbook이 일시 중지되었다가 다시 시작되는 경우 Connect-AzureRmAccount를 사용하여 검사점이 지정된 작업을 수행해야 합니다.

## <a name="authenticating-to-azure-resources"></a>Azure 리소스 인증

Azure 리소스를 관리하는 Azure Automation의 Runbook에는 Azure에 대한 인증이 필요합니다. [실행 계정](automation-create-runas-account.md)(서비스 주체라고도 함)은 Automation runbook을 사용하여 구독의 Azure Resource Manager 리소스에 액세스하는 기본 방법입니다. **AzureRunAsConnection** 연결 자산을 추가하여 그래픽 runbook에 추가 이 기능을 추가할 수 있습니다. 이는 PowerShell [Get-AutomationConnection](https://technet.microsoft.com/library/dn919922%28v=sc.16%29.aspx) cmdlet을 사용하고 캔버스에 [Connect-AzureRmAccount](/powershell/module/azurerm.profile/connect-azurermaccount) cmdlet을 사용합니다. 다음 예제에 이 내용이 나와 있습니다.

![실행 인증 작업](media/automation-graphical-authoring-intro/authenticate-run-as-account.png)

실행 연결 가져오기 작업(즉, Get-AutomationConnection)은 AzureRunAsConnection이라는 상수 값 데이터 원본으로 구성됩니다.

![실행 연결 구성](media/automation-graphical-authoring-intro/authenticate-runas-parameterset.png)

다음 활동인 Connect-AzureRmAccount는 Runbook에서 사용하기 위해 인증된 실행 계정을 추가합니다.

![Connect-AzureRmAccount 매개 변수 집합](media/automation-graphical-authoring-intro/authenticate-conn-to-azure-parameter-set.png)

> [!IMPORTANT]
> **Add-AzureRmAccount**는 이제 **Connect-AzureRMAccount**에 대한 별칭입니다. 라이브러리를 항목을 검색할 때 **Connect-AzureRMAccount**가 표시되지 않는 경우 **Add-AzureRmAccount**를 사용하거나 Automation 계정에서 모듈을 업데이트할 수 있습니다.

**APPLICATIONID**, **CERTIFICATETHUMBPRINT** 및 **TENANTID** 매개 변수의 경우에는 활동이 여러 속성이 있는 개체를 출력하기 때문에 필드 경로에 대한 속성 이름을 지정해야 합니다. 그렇지 않은 경우 runbook을 실행하면 인증하려는 시도가 실패합니다. 실행 계정을 사용하여 runbook을 인증하는데 필요한 최소한의 사항입니다.

Azure 클래식 배포 또는 Azure Resource Manager 리소스를 관리하기 위해 [Azure AD 사용자 계정](automation-create-aduser-account.md)을 사용하여 Automation 계정을 만든 구독자가 이전 버전과 호환성을 유지하려면 인증하는 방법으로 Azure 계정에 액세스할 수 있는 Active Directory 사용자를 나타내는 [자격 증명 자산](automation-credentials.md)이 있는 Add-AzureAccount cmdlet을 사용합니다.

Add-AzureAccount 활동이 따르는 캔버스에 자격 증명 자산을 추가하여 그래픽 Runbook에 이 기능을 추가할 수 있습니다. Add-AzureAccount는 자격 증명 활동을 해당 입력으로 사용합니다. 다음 예제에 이 내용이 나와 있습니다.

![인증 활동](media/automation-graphical-authoring-intro/authentication-activities.png)

Runbook을 시작할 때와 각 검사점 이후에 인증해야 합니다. 즉, 모든 Checkpoint-Workflow 활동 뒤에 Add-AzureAccount 활동을 추가해야 합니다. 동일한 자격 증명 활동을 사용할 수 있으므로 추가 자격 증명 활동은 필요하지 않습니다.

![활동 출력](media/automation-graphical-authoring-intro/authentication-activity-output.png)

## <a name="runbook-input-and-output"></a>Runbook 입력 및 출력

### <a name="runbook-input"></a>Runbook 입력

Runbook에는 사용자의 입력(사용자가 Azure 포털을 통해 Runbook을 시작하는 경우) 또는 다른 Runbook의 입력(현재 Runbook이 자식 Runbook으로 사용되는 경우)이 필요할 수 있습니다.
예를 들어 가상 머신을 만드는 Runbook이 있는 경우 Runbook을 시작할 때마다 가상 머신의 이름 및 기타 속성과 같은 정보를 제공해야 할 수 있습니다.

하나 이상의 입력 매개 변수를 정의하여 Runbook에 대한 입력을 허용합니다. Runbook을 시작할 때마다 이러한 매개 변수의 값을 제공합니다. Azure Portal을 사용하여 Runbook을 시작하는 경우 Runbook의 각 입력 매개 변수 값을 제공하라는 메시지가 표시됩니다.

Runbook 도구 모음에서 **입력 및 출력** 단추를 클릭하여 Runbook의 입력 매개 변수에 액세스할 수 있습니다.

이렇게 하면 **입력 및 출력** 컨트롤이 열리며, 여기에서 기존 입력 매개 변수를 편집하거나 **입력 추가**를 클릭하여 새 입력 매개 변수를 만들 수 있습니다.

![입력 추가](media/automation-graphical-authoring-intro/runbook-edit-add-input.png)

각 입력 매개 변수는 다음 표의 속성으로 정의됩니다.

| 자산 | Description |
|:--- |:--- |
| Name |매개 변수의 고유한 이름입니다. 영숫자 문자만 포함할 수 있으며, 공백을 포함할 수 없습니다. |
| Description |입력 매개 변수에 대한 선택적 설명입니다. |
| Type |매개 변수 값에 필요한 데이터 형식입니다. Azure Portal에서는 입력 메시지를 표시할 때 각 매개 변수의 데이터 형식에 대한 적절한 컨트롤을 제공합니다. |
| Mandatory |매개 변수에 대해 값을 제공해야 하는지 여부를 지정합니다. 기본값이 정의되지 않은 각 필수 매개 변수의 값을 제공하지 않으면 Runbook을 시작할 수 없습니다. |
| Default Value |값을 제공하지 않은 경우 매개 변수에 사용되는 값을 지정합니다. Null 또는 특정 값일 수 있습니다. |

### <a name="runbook-output"></a>Runbook 출력

나가는 링크가 없는 모든 작업에서 생성된 데이터는 [Runbook의 출력](https://docs.microsoft.com/azure/automation/automation-runbook-output-and-messages)에 저장됩니다. 출력은 Runbook 작업과 함께 저장되며, 해당 Runbook이 자식으로 사용되는 경우 부모 Runbook에서 사용할 수 있습니다.

## <a name="powershell-expressions"></a>PowerShell 식

그래픽 제작의 이점 중 하나는 PowerShell에 대한 최소한의 지식만으로 runbook을 작성하는 기능을 제공한다는 점입니다. 그러나 현재 특정 [매개 변수 값](#activities)을 채우고 [연결 조건](#links-and-workflow)을 설정하기 위해 PowerShell을 조금은 알아야 합니다. 이 섹션에서는 PowerShell 식에 대해 친숙하지 못한 사용자에게 이를 간략히 소개합니다. PowerShell의 세부 사항 전체는 [Windows PowerShell을 사용하는 스크립트](https://technet.microsoft.com/library/bb978526.aspx)에서 사용 가능합니다.

### <a name="powershell-expression-data-source"></a>PowerShell 식 데이터 원본
PowerShell 식을 데이터 원본으로 사용하여 [동작 매개 변수](#activities) 의 값을 일부 PowerShell 코드의 결과로 채울 수 있습니다. 간단한 함수를 수행하는 코드 한 줄이거나 또는 복잡한 함수를 수행하는 여러 줄의 코드일 수 있습니다. 변수에 할당되지 않은 명령의 출력은 매개 변수 값에 대한 출력입니다.

예를 들어 다음 명령은 현재 날짜를 출력합니다.

예를 들어 다음 명령은 현재 날짜를 출력합니다.

```powershell-interactive
Get-Date
```

다음 명령은 현재 날짜에서 문자열을 작성하고 변수에 할당합니다. 변수의 내용은 출력으로 보내집니다.

```powershell-interactive
$string = "The current date is " + (Get-Date)
$string
```

다음 명령은 현재 날짜를 평가하고 현재 날짜가 주말 또는 평일인지를 나타내는 문자열을 반환합니다.

```powershell-interactive
$date = Get-Date
if (($date.DayOfWeek = "Saturday") -or ($date.DayOfWeek = "Sunday")) { "Weekend" }
else { "Weekday" }
```

### <a name="activity-output"></a>활동 출력

Runbook의 이전 작업에서 출력을 사용하려면 다음 구문을 사용하여 $ActivityOutput 변수를 사용합니다.

```powershell-interactive
$ActivityOutput['Activity Label'].PropertyName
```

예를 들어 가상 머신의 이름이 필요한 속성을 사용하는 활동이 있는 경우 다음 식을 사용할 수 있습니다.

```powershell-interactive
$ActivityOutput['Get-AzureVm'].Name
```

속성이 속성 대신 가상 머신 개체를 필요로 하는 경우 다음 구문을 사용하여 전체 개체를 반환합니다.

```powershell-interactive
$ActivityOutput['Get-AzureVm']
```

또한 가상 머신 이름에 텍스트를 연결하는 다음과 같은 보다 복잡한 식에서 활동의 출력을 사용할 수 있습니다.

```powershell-interactive
"The computer name is " + $ActivityOutput['Get-AzureVm'].Name
```

### <a name="conditions"></a>조건

[비교 연산자](https://technet.microsoft.com/library/hh847759.aspx) 를 사용하여 값을 비교 하거나 값이 지정된 패턴과 일치하는지를 확인합니다. 비교는 $true 또는 $false의 값을 반환합니다.

예를 들어 다음 조건은 *Get-AzureVM*이라는 활동에서 가상 머신이 현재 *중지*되었는지 확인합니다.

```powershell-interactive
$ActivityOutput["Get-AzureVM"].PowerState –eq "Stopped"
```

다음 조건은 동일한 가상 머신이 *중지*이외의 상태인지를 확인합니다.

```powershell-interactive
$ActivityOutput["Get-AzureVM"].PowerState –ne "Stopped"
```

**-and** 또는 **-or**와 같은 [논리 연산자](https://technet.microsoft.com/library/hh847789.aspx)를 사용하여 여러 조건을 조인할 수 있습니다. 예를 들어 다음 조건은 이전 예제에서 동일한 가상 머신이 *중지됨* 또는 *중지 중* 중에 어떤 상태인지 확인합니다.

```powershell-interactive
($ActivityOutput["Get-AzureVM"].PowerState –eq "Stopped") -or ($ActivityOutput["Get-AzureVM"].PowerState –eq "Stopping")
```

### <a name="hashtables"></a>해시 테이블

[해시 테이블](https://technet.microsoft.com/library/hh847780.aspx) 은 값 집합을 반환하는 데 유용한 이름/값 쌍입니다. 특정 활동에 대한 속성은 단순 값 대신 해시 테이블을 필요로 할 수 있습니다. 또한 해시 테이블은 사전이라고 표시될 수 있습니다.

다음 구문을 사용하여 해시 테이블을 만듭니다. 해시 테이블은 항목을 몇 개이든 포함할 수 있지만 각각은 이름 및 값으로 정의됩니다.

```powershell-interactive
@{ <name> = <value>; [<name> = <value> ] ...}
```

예를 들어 다음 식은 데이터 원본에서 인터넷을 검색에 대한 값이 있는 해시 테이블을 예상하는 활동 매개 변수에 사용될 해시 테이블을 만듭니다.

```powershell-interactive
$query = "Azure Automation"
$count = 10
$h = @{'q'=$query; 'lr'='lang_ja';  'count'=$Count}
$h
```

다음 예제에서는 *Twitter 연결 가져오기* 라는 작업에서 출력을 사용하여 해시 테이블을 채움니다.

```powershell-interactive
@{'ApiKey'=$ActivityOutput['Get Twitter Connection'].ConsumerAPIKey;
    'ApiSecret'=$ActivityOutput['Get Twitter Connection'].ConsumerAPISecret;
    'AccessToken'=$ActivityOutput['Get Twitter Connection'].AccessToken;
    'AccessTokenSecret'=$ActivityOutput['Get Twitter Connection'].AccessTokenSecret}
```

## <a name="next-steps"></a>다음 단계

* PowerShell 워크플로 Runbook을 시작하려면 [내 첫 번째 PowerShell 워크플로 Runbook](automation-first-runbook-textual.md)
* 그래픽 Runbook을 시작하려면 [내 첫 번째 그래픽 Runbook](automation-first-runbook-graphical.md)
* Runbook의 형식, 장점 및 제한 사항에 대해 자세히 알아보려면 [Azure Automation Runbook 형식](automation-runbook-types.md)
* Automation 실행 계정을 사용하여 인증하는 방법을 알아보려면 [Azure 실행 계정 구성](automation-sec-configure-azure-runas-account.md)
