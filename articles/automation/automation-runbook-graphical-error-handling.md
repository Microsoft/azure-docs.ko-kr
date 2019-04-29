---
title: Azure Automation 그래픽 runbook의 오류 처리
description: 이 문서에서는 Azure Automation 그래픽 runbook에서 오류 처리 논리를 구현하는 방법에 대해 설명합니다.
services: automation
documentationcenter: ''
author: yunan2016
manager: digimobile
editor: tysonn
ms.assetid: ''
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
origin.date: 03/16/2018
ms.date: 05/14/2018
ms.author: v-nany
ms.openlocfilehash: d7fe38334b71334d4dae9235643117efdf5fbd5d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61233123"
---
# <a name="error-handling-in-azure-automation-graphical-runbooks"></a>Azure Automation 그래픽 runbook의 오류 처리

고려해야 할 주요 runbook 디자인 보안 주체는 runbook이 경험할 수 있는 다른 문제를 식별합니다. 이러한 문제는 성공, 예상된 오류 상태 및 예기치 않은 오류 조건을 포함할 수 있습니다.

runbook은 오류 처리를 포함해야 합니다. 활동의 출력에 대한 유효성을 검사하거나 그래픽 runbook으로 오류를 처리하려면 Windows PowerShell 코드 활동을 사용하거나 활동 출력 링크에서 조건부 논리를 정의하거나 다른 방법을 적용할 수 있습니다.          

종종 runbook 작업으로 발생하는 종료되지 않는 오류가 있는 경우 따르는 모든 활동은 오류에 관계 없이 처리됩니다. 오류는 예외를 생성할 수 있지만 다음 작업이 계속 실행되도록 허용됩니다. PowerShell이 오류를 처리하도록 설계된 방법입니다.    

실행 중에 발생할 수 있는 PowerShell 오류 유형으로 종료 오류 또는 종료되지 않는 오류가 있습니다. 종료 오류 및 종료되지 않는 오류 간의 차이점은 다음과 같습니다.

* **종료 오류**: 실행 중에 명령 (또는 스크립트 실행)을 완전히 중단하는 심각한 오류입니다. 예를 들어 존재하지 않는 cmdlet, cmdlet을 실행하지 못하도록 하는 구문 오류 또는 기타 심각한 오류를 포함합니다.

* **종료되지 않는 오류**: 실패에도 불구하고 실행을 계속할 수 있는 심각하지 않은 오류입니다. 예를 들어 찾을 수 없는 파일 오류 및 권한 문제 등의 작동 오류가 있습니다.

Azure Automation 그래픽 runbook은 오류 처리를 포함하는 기능으로 개선되었습니다. 이제 예외를 종료되지 않는 오류로 전환하고 활동 간에 오류 링크를 만들 수 있습니다. 이 프로세스를 통해 runbook 작성자가 오류를 포착하고 실현되거나 예기치 않은 조건을 관리할 수 있습니다.  

## <a name="when-to-use-error-handling"></a>오류 처리를 사용하는 경우

오류 또는 예외를 throw하는 중요한 활동이 있을 때마다 runbook의 다음 활동을 처리하지 못하도록 하거나 적절하게 오류를 처리하는 것이 중요합니다. 이는 runbook에서 비즈니스 또는 서비스 운영 프로세스를 지원할 때 특히 중요합니다.

runbook 작성자는 오류를 발생시킬 수 있는 각 활동에 대해 다른 활동을 가리키는 오류 링크를 추가할 수 있습니다. 대상 활동은 코드 활동, cmdlet 호출, 다른 runbook 호출 등을 포함하는 다른 어떤 유형이 될 수 있습니다.

또한 대상 활동에 나가는 링크가 있을 수도 있습니다. 이러한 링크는 일반 링크 또는 오류 링크일 수 있습니다. 즉, runbook 작성자는 코드 활동에 문의하지 않고 복잡한 오류 처리 논리를 구현할 수 있습니다. 권장되는 방식은 일반적인 기능으로 전용 오류 처리 runbook을 만드는 것이지만 필수는 아닙니다. PowerShell 코드 활동에서 오류 처리 논리는 유일한 옵션이 아닙니다.  

예를 들어 VM을 시작하고 애플리케이션을 설치하려고 하는 runbook을 살펴보세요. VM이 제대로 시작되지 않는 경우 두 가지 작업을 수행합니다.

1. 이 문제에 대한 알림을 보냅니다.
2. 대신 새 VM을 자동으로 프로비전하는 다른 runbook을 시작합니다.

한 가지 해결 방법은 1단계를 처리하는 활동을 가리키는 오류 링크를 갖는 것입니다. 예를 들어 **Start-AzureRmAutomationRunbook** cmdlet과 같은 2단계에 대한 활동에 **Write-Warning** cmdlet을 연결할 수 있습니다.

또한 이 두 가지 활동을 별도의 오류 처리 runbook에 배치하고 앞에서 제안한 지침에 따라 많은 runbook에서 사용하기 위해 이 동작을 일반화할 수 있습니다. 이 오류 처리 runbook을 호출하기 전에 원래 runbook의 데이터에서 사용자 지정 메시지를 생성한 다음 이 메시지를 오류 처리 runbook에 매개 변수로 전달할 수 있습니다.

## <a name="how-to-use-error-handling"></a>오류 처리 사용 방법

각 활동에는 예외를 종료되지 않는 오류로 전환하는 구성 설정이 있습니다. 이 설정은 기본적으로 사용하지 않도록 설정됩니다. 오류를 처리하려는 활동에서 이 설정을 사용하도록 설정하는 것이 좋습니다.  

구성을 사용하면 활동의 종료 오류 및 종료되지 않는 오류는 모두 종료되지 않는 오류로 처리되고 오류 링크로 처리할 수 있습니다.  

이 설정을 구성한 후에는 오류를 처리하는 활동을 만듭니다. 활동이 오류를 생성하면 활동에서 일반 출력을 생성했더라도 일반 링크가 아니라 나가는 오류 링크를 따릅니다.<br><br> ![Automation runbook 오류 링크 예제](media/automation-runbook-graphical-error-handling/error-link-example.png)

다음 예제에서 runbook은 가상 머신의 컴퓨터 이름을 포함하는 변수를 검색합니다. 다음 작업으로 가상 머신을 시작하려고 시도합니다.<br><br> ![Automation runbook 오류 처리 예제](media/automation-runbook-graphical-error-handling/runbook-example-error-handling.png)<br><br>      

**Get-AutomationVariable** 활동과 **Start-AzureRmVm**은 예외를 오류로 변환하도록 구성됩니다. 변수를 가져오거나 VM을 시작하는 데 문제가 있으면 오류가 생성됩니다.<br><br> ![Automation runbook 오류 처리 활동 설정](media/automation-runbook-graphical-error-handling/activity-blade-convertexception-option.png)

오류 링크는 이러한 활동에서 단일 **오류 관리** 활동(코드 활동)으로 이동합니다. 이 활동은 *Throw* 키워드를 사용하여 간단한 PowerShell 식으로 구성되어 *$Error.Exception.Message*와 함께 처리를 중지하고 현재 예외를 설명하는 메시지를 가져옵니다.<br><br> ![Automation runbook 오류 처리 코드 예제](media/automation-runbook-graphical-error-handling/runbook-example-error-handling-code.png)


## <a name="next-steps"></a>다음 단계

* 그래픽 runbook의 링크 및 링크 유형에 대해 자세히 알아보려면 [Azure Automation에서 그래픽 작성](automation-graphical-authoring-intro.md#links-and-workflow)을 참조하세요.

* runbook 실행, runbook 작업 모니터링 방법 및 기타 기술 세부 정보를 알아보려면 [runbook 작업 추적](automation-runbook-execution.md)을 참조하세요.
