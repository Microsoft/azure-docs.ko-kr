---
title: Azure Automation Graphical Runbook SDK의 개요
description: 이 문서에서는 Azure Automation Graphical Runbook SDK를 사용하는 방법을 설명합니다.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 07/20/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: bc9f1feff6d8d45e52c8621d1ec4b36e0a4a4a76
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61303219"
---
# <a name="use-the-azure-automation-graphical-runbook-sdk"></a>Azure Automation Graphical Runbook SDK 사용

[Graphical Runbook](automation-graphical-authoring-intro.md)은 기본 Windows PowerShell 또는 PowerShell 워크플로 코드의 복잡성을 관리하는 데 도움이 되는 Runbook입니다. Microsoft Azure Automation Graphical Authoring SDK를 통해 개발자는 Azure Automation 서비스 사용을 위한 Graphical Runbook을 만들고 편집할 수 있습니다. 다음 코드 조각은 코드에서 Graphical Runbook 만들기의 기본 흐름을 보여줍니다.

## <a name="pre-requisites"></a>필수 조건

시작하려면 프로젝트로 `Microsoft.Azure.Automation.GraphicalRunbook.Model` 패키지를 가져옵니다.

## <a name="create-a-runbook-object-instance"></a>Runbook 개체 인스턴스 만들기

`Orchestrator.GraphRunbook.Model` 어셈블리를 참조하고 `Orchestrator.GraphRunbook.Model.GraphRunbook` 클래스의 인스턴스를 만듭니다.

```csharp
using Orchestrator.GraphRunbook.Model;
using Orchestrator.GraphRunbook.Model.ExecutableView;

var runbook = new GraphRunbook();
```

## <a name="add-runbook-parameters"></a>Runbook 매개 변수 추가

`Orchestrator.GraphRunbook.Model.Parameter` 개체를 인스턴스화하고 Runbook에 추가합니다.

```csharp
runbook.AddParameter(
 new Parameter("YourName") {
  TypeName = typeof(string).FullName,
   DefaultValue = "World",
   Optional = true
 });

runbook.AddParameter(
 new Parameter("AnotherParameter") {
  TypeName = typeof(int).FullName, ...
 });
```

## <a name="add-activities-and-links"></a>작업 및 링크 추가

적절한 유형의 작업을 인스턴스화하고 Runbook에 추가합니다.

```csharp
var writeOutputActivityType = new CommandActivityType {
 CommandName = "Write-Output",
  ModuleName = "Microsoft.PowerShell.Utility",
 InputParameterSets = new [] {
  new ParameterSet {
   Name = "Default",
    new [] {
     new Parameter("InputObject"), ...
    }
  },
  ...
 }
};

var outputName = runbook.AddActivity(
 new CommandActivity("Output name", writeOutputActivityType) {
  ParameterSetName = "Default",
   Parameters = new ActivityParameters {
    {
     "InputObject",
     new RunbookParameterValueDescriptor("YourName")
    }
   },
   PositionX = 0,
   PositionY = 0
 });

var initializeRunbookVariable = runbook.AddActivity(
 new WorkflowScriptActivity("Initialize variable") {
  Process = "$a = 0",
   PositionX = 0,
   PositionY = 100
 });
```

작업은 `Orchestrator.GraphRunbook.Model` 네임스페이스에서 다음 클래스에 의해 구현됩니다.

|클래스  |작업  |
|---------|---------|
|CommandActivity     | PowerShell 명령(cmdlet, 함수 등)을 호출합니다.        |
|InvokeRunbookActivity     | 다른 Runbook 인라인을 호출합니다.        |
|JunctionActivity     | 들어오는 모든 분기가 완료될 때까지 기다립니다.        |
|WorkflowScriptActivity     | Runbook의 컨텍스트에서 PowerShell 또는 PowerShell 워크플로 코드의 블록을 실행합니다(Runbook 유형에 따라). 강력한 도구이지만 과도하게 사용하지 마십시오. UI는 이 스크립트 블록을 텍스트로 표시합니다. 실행 엔진은 제공된 블록을 검은색 상자로 처리하고 기본 구문 검사를 제외하고 해당 콘텐츠를 분석하는 시도를 하지 않습니다. 단일 PowerShell 명령을 호출해야 하는 경우 CommandActivity를 사용합니다.        |

> [!NOTE]
> 제공된 클래스에서 사용자 고유의 작업을 파생하지 마십시오. Azure Automation은 사용자 지정 작업 유형과 함께 Runbook을 사용할 수 없습니다.

CommandActivity 및 InvokeRunbookActivity 매개 변수는 직접 값이 아닌 값 설명자로 제공되어야 합니다. 값 설명자는 실제 매개 변수 값이 생성되어야 하는 방법을 지정합니다. 다음 값 설명자가 현재 제공됩니다.


|설명자  |정의  |
|---------|---------|
|ConstantValueDescriptor     | 하드 코드된 상수 값을 나타냅니다.        |
|RunbookParameterValueDescriptor     | 이름으로 Runbook 매개 변수를 나타냅니다.        |
|ActivityOutputValueDescriptor     | 하나의 작업이 다른 작업에 의해 생성된 데이터를 "구독"하도록 허용하는 업스트림 작업 출력을 나타냅니다.        |
|AutomationVariableValueDescriptor     | 이름으로 Automation 변수 자산을 나타냅니다.         |
|AutomationCredentialValueDescriptor     | 이름으로 Automation 인증서 자산을 나타냅니다.        |
|AutomationConnectionValueDescriptor     | 이름으로 Automation 연결 자산을 나타냅니다.        |
|PowerShellExpressionValueDescriptor     | 작업을 호출하기 직전에 평가될 자유 양식 PowerShell 식을 지정합니다.  <br/>강력한 도구이지만 과도하게 사용하지 마십시오. UI는 이 식을 텍스트로 표시합니다. 실행 엔진은 제공된 블록을 검은색 상자로 처리하고 기본 구문 검사를 제외하고 해당 콘텐츠를 분석하는 시도를 하지 않습니다. 가능한 경우 보다 구체적인 값 설명자를 사용합니다.      |

> [!NOTE]
> 제공된 클래스에서 사용자 고유의 값 설명자를 파생하지 마십시오. Azure Automation은 사용자 지정 값 설명자 유형과 함께 Runbook을 사용할 수 없습니다.

작업을 연결하는 링크를 인스턴스화하고 Runbook에 추가합니다.

```csharp
runbook.AddLink(new Link(activityA, activityB, LinkType.Sequence));

runbook.AddLink(
 new Link(activityB, activityC, LinkType.Pipeline) {
  Condition = string.Format("$ActivityOutput['{0}'] -gt 0", activityB.Name)
 });
```

## <a name="save-the-runbook-to-a-file"></a>파일에 Runbook을 저장합니다.

`Orchestrator.GraphRunbook.Model.Serialization.RunbookSerializer`를 사용하여 문자열에 Runbook을 직렬화합니다.

```csharp
var serialized = RunbookSerializer.Serialize(runbook);
```

**.graphrunbook** 확장을 사용하여 이 문자열을 파일에 저장할 수 있으며 이 파일을 Azure Automation으로 가져올 수 있습니다.
직렬화된 형식은 `Orchestrator.GraphRunbook.Model.dll`의 향후 버전에서 변경될 수 있습니다. 이전 버전과의 호환성을 약속 드립니다. `Orchestrator.GraphRunbook.Model.dll`의 이전 버전으로 직렬화된 모든 Runbook은 모든 새 버전으로 역직렬화될 수 있습니다. 이후 버전과의 호환성은 보장되지 않습니다. 최신 버전으로 직렬화된 Runbook을 이전 버전으로 역직렬화할 수 없습니다.

## <a name="next-steps"></a>다음 단계

Azure Automation에서 Graphical Runbook에 대해 자세히 알아보려면 [그래픽 작성 소개](automation-graphical-authoring-intro.md)를 참조하세요.

