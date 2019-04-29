---
title: Azure Automation Runbook 형식
description: 'Azure Automation에서 사용할 수 있는 다양한 형식의 Runbook을 설명하고 사용할 형식을 결정할 때 고려해야 하는 사항을 설명합니다. '
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/05/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 7a3990366814beda83852fc7c07c896445388c23
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60737162"
---
# <a name="azure-automation-runbook-types"></a>Azure Automation Runbook 형식

Azure Automation는 여러 가지 유형의 Runbook을 지원합니다. 아래 테이블에서는 각 유형에 대해 간략하게 설명합니다.  아래 섹션은 각각을 사용할 경우에 대한 고려 사항을 포함하여 각 형식에 대해 자세한 정보를 제공합니다.

| Type | 설명 |
|:--- |:--- |
| [그래픽](#graphical-runbooks)|Windows PowerShell을 기반으로 하며 Azure 포털의 그래픽 편집기로 완전하게 생성 및 편집됩니다. |
| [그래픽 PowerShell 워크플로](#graphical-runbooks)|Windows PowerShell 워크플로를 기반으로 하며 Azure 포털의 그래픽 편집기로 완전하게 생성 및 편집됩니다. |
| [PowerShell](#powershell-runbooks) |Windows PowerShell 스크립트를 기반으로 하는 텍스트 Runbook |
| [PowerShell 워크플로](#powershell-workflow-runbooks)|Windows PowerShell 워크플로를 기반으로 하는 텍스트 Runbook |
| [Python](#python-runbooks) |Python을 기반으로 하는 텍스트 Runbook입니다. |

## <a name="graphical-runbooks"></a>그래픽 Runbook

[그래픽](automation-runbook-types.md#graphical-runbooks) 및 그래픽 PowerShell 워크플로 Runbook은 Azure 포털의 그래픽 편집기로 생성 및 편집됩니다.  파일로 내보낸 후 다른 자동화 계정으로 가져올 수 있지만 다른 도구에서 만들거나 편집할 수는 없습니다. 그래픽 Runbook은 PowerShell 코드를 생성하지만 코드를 직접 보거나 수정할 수 없습니다. 그래픽 Runbook은 [텍스트 형식](automation-runbook-types.md) 중 하나로 변환할 수 없고, 텍스트 Runbook은 그래픽 형식으로 변환할 수 없습니다. 가져오는 동안 그래픽 Runbook을 그래픽 PowerShell 워크플로 Runbook으로 변환하거나 그 반대로 변환할 수 있습니다.

### <a name="advantages"></a>장점

* 시각적 삽입-링크-구성 제작 모델  
* 프로세스를 통해 데이터가 흐르는 방식에 집중  
* 관리 프로세스를 시각적으로 표시  
* 다른 Runbook을 자식 Runbook으로 포함시켜 고급 워크플로 만들기  
* 모듈식 프로그래밍 장려  

### <a name="limitations"></a>제한 사항

* Azure 포털 외부에서 Runbook을 편집할 수 없습니다.
* 복잡한 논리를 실행하기 위해 PowerShell 코드를 포함하는 코드 작업이 필요할 수 있습니다.
* 그래픽 워크플로에 의해 만들어진 PowerShell 코드를 보거나 직접 편집할 수 없습니다. 코드 작업에서 만든 코드를 볼 수 있습니다.
* Linux Hybrid Runbook Worker에서 실행할 수 없습니다.

## <a name="powershell-runbooks"></a>PowerShell Runbook

PowerShell Runbook은 Windows PowerShell을 기반으로 합니다.  Azure 포털의 텍스트 편집기를 사용하여 Runbook을 직접 편집합니다.  오프라인 텍스트 편집기도 사용할 수 있고 Azure Automation으로 [Runbook 가져오기](manage-runbooks.md) 가 가능합니다.

### <a name="advantages"></a>장점

* PowerShell 워크플로의 부가적인 복잡성 없이 PowerShell 코드로 모든 복잡한 로직을 구현합니다.
* Runbook은 실행 전에 컴파일이 필요 없기 때문에 PowerShell 워크플로 Runbook보다 빨리 시작됩니다.
* Azure에서 또는 Linux와 Windows Hybrid Runbook Worker에서 실행할 수 있습니다.

### <a name="limitations"></a>제한 사항

* PowerShell 스크립팅에 대해 잘 알아야 합니다.
* [병렬 처리](automation-powershell-workflow.md#parallel-processing)를 사용하여 여러 작업을 병렬로 실행할 수 없습니다.
* 오류 발생 시 [검사점](automation-powershell-workflow.md#checkpoints)을 사용하여 Runbook을 다시 시작할 수 없습니다.
* PowerShell 워크플로 Runbook 및 그래픽 Runbook은 새 작업을 만드는 Start-AzureAutomationRunbook cmdlet을 사용해야만 자식 Runbook으로 포함할 수 있습니다.

### <a name="known-issues"></a>알려진 문제

PowerShell Runbook에 대해 현재 알려진 문제는 다음과 같습니다.

* PowerShell Runbook이 null 값을 갖는 암호화되지 않은 [변수 자산](automation-variables.md)을 가져올 수 없습니다.
* PowerShell Runbook은 이름에 *~* 가 포함된 [변수 자산](automation-variables.md)을 검색할 수 없습니다.
* PowerShell Runbook의 반복적인 Get-Process는 80회 반복 후에 작동이 중단될 수 있습니다.
* PowerShell Runbook이 한 번에 대량의 데이터를 출력 스트림에 쓰려고 하면 실패할 수 있습니다.   일반적으로 큰 개체로 작업하는 경우 필요한 정보만 출력하면 이 문제를 극복할 수 있습니다.  예를 들어 *Get-Process* 같은 출력 대신 *Get-Process | Select ProcessName, CPU*를 사용하여 필요한 필드만 출력할 수 있습니다.

## <a name="powershell-workflow-runbooks"></a>PowerShell 워크플로 Runbook

PowerShell 워크플로 Runbook은 [Windows PowerShell 워크플로](automation-powershell-workflow.md)를 기반으로 하는 텍스트 Runbook입니다.  Azure 포털의 텍스트 편집기를 사용하여 Runbook을 직접 편집합니다.  오프라인 텍스트 편집기도 사용할 수 있고 Azure Automation으로 [Runbook 가져오기](manage-runbooks.md) 가 가능합니다.

### <a name="advantages"></a>장점

* PowerShell 워크플로 코드로 모든 복잡한 로직을 구현합니다.
* 오류 발생 시 [검사점](automation-powershell-workflow.md#checkpoints)을 사용하여 Runbook을 다시 시작합니다.
* [병렬 처리](automation-powershell-workflow.md#parallel-processing) 를 사용하여 여러 작업을 병렬로 수행합니다.
* 다른 그래픽 Runbook과 PowerShell 워크플로 Runbook을 자식 Runbook으로 포함시켜 고급 워크플로를 만들 수 있습니다.

### <a name="limitations"></a>제한 사항

* 작성자는 PowerShell 워크플로를 잘 알아야 합니다.
* Runbook은 [역직렬화된 개체](automation-powershell-workflow.md#code-changes)와 같은 PowerShell 워크플로의 부가적인 복잡성을 다루어야 합니다.
* Runbook은 실행 전에 컴파일이 필요하기 때문에 PowerShell Runbook보다 시작 시간이 깁니다.
* PowerShell Runbook은 새 작업을 만드는 Start-AzureAutomationRunbook cmdlet을 사용해야만 자식 Runbook으로 포함할 수 있습니다.
* Linux Hybrid Runbook Worker에서 실행할 수 없습니다.

## <a name="python-runbooks"></a>Python Runbook

Python Runbook은 Python 2에서 컴파일됩니다.  Azure Portal의 텍스트 편집기 또는 오프라인 텍스트 편집기를 사용하여 직접 Runbook 코드를 편집한 다음, Azure Automation으로 [Runbook을 가져올 수](manage-runbooks.md) 있습니다.

### <a name="advantages"></a>장점

* 유용한 Python 라이브러리를 활용할 수 있습니다.
* Azure에서 또는 모두 Linux Hybrid Runbook Worker에서 실행할 수 있습니다. Windows Hybrid Runbook Worker를 사용 하 여 지 [python2.7](https://www.python.org/downloads/release/latest/python2) 설치 합니다.

### <a name="limitations"></a>제한 사항

* Python 스크립팅에 대해 잘 알아야 합니다.
* 현재는 Python 2만 지원되므로 Python 3 특정 함수는 실패합니다.
* 타사 라이브러리를 사용하려면 해당 라이브러리를 사용할 Automation 계정으로 [패키지를 가져와야](python-packages.md) 합니다.

## <a name="considerations"></a>고려 사항

특정 Runbook에 사용할 형식을 결정할 때 다음과 같은 사항을 추가로 고려합니다.

* Runbook를 그래픽에서 텍스트 형식으로 또는 그 반대로 변환할 수 없습니다.
* 형식이 다른 Runbook을 자식 Runbook으로 사용하는 경우 제한 사항이 있습니다. 자세한 내용은 [Azure Automation의 자식 Runbook](automation-child-runbooks.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

* 그래픽 Runbook 작성에 대해 자세히 알아보려면 [Azure Automation에서 그래픽 작성](automation-graphical-authoring-intro.md)
* Runbook용 PowerShell 및 PowerShell 워크플로 간의 차이점을 이해하려면 [Windows PowerShell 워크플로 학습](automation-powershell-workflow.md)
* Runbook을 만들거나 가져오는 방법에 대한 자세한 내용은 [Runbook 만들기 또는 가져오기](manage-runbooks.md)

