---
title: 비호환 Azure Automation 상태 구성 서버 재구성
description: 구성 상태가 데이터베이스가 드리프트 서버에 요청 시 구성을 다시 적용 하는 방법
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.topic: conceptual
ms.date: 07/17/2019
manager: nirb
ms.openlocfilehash: a6c7639cb4988eb13dfaa1c151085cda6e53b5d3
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68614499"
---
# <a name="remediate-non-compliant-dsc-servers"></a>비준수 DSC 서버 수정

서버를 Azure Automation 상태 구성으로 등록 하면 ' 구성 모드 '가 ApplyOnly, ApplyandMonitor 또는 Applyand자동 고침으로 설정 됩니다.
모드가 자동으로 설정 되지 않은 경우 어떤 이유로 든 호환 상태에서 드리프트 한 서버는 수동으로 수정할 때까지 비규격 상태로 유지 됩니다.

Azure compute는 고객이 가상 머신 내에서 스크립트를 실행할 수 있도록 하는 실행 명령 이라는 기능을 제공 합니다.
이 문서에서는 구성 드리프트를 수동으로 수정할 때이 기능에 대한 예제 스크립트를 제공 합니다.

## <a name="correct-drift-of-windows-virtual-machines-using-powershell"></a>PowerShell을 사용 하 여 Windows 가상 머신의 정확한 드리프트

Windows 가상 머신에서 명령 실행 기능을 사용 하는 단계별 지침은 [실행 명령을 사용 하 여 WINDOWS VM에서 PowerShell 스크립트 실행](/azure/virtual-machines/windows/run-command)문서를 참조 하세요.

Azure Automation 상태 구성 노드에서 최신 구성을 다운로드 하 고 적용 하려면 `Update-DscConfiguration` cmdlet을 사용 합니다.
자세한 내용은 [start-dscconfiguration](/powershell/module/psdesiredstateconfiguration/update-dscconfiguration)cmdlet 설명서를 참조 하세요.

```powershell
Update-DscConfiguration -Wait -Verbose
```

## <a name="correct-drift-of-linux-virtual-machines"></a>Linux 가상 머신의 정확한 드리프트

Linux 서버에는 현재 유사한 기능을 사용할 수 없습니다.
유일한 옵션은 등록 프로세스를 반복 하는 것입니다.
Azure 노드의 경우 포털에서 또는 Az Automation cmdlet을 사용 하 여 드리프트 수정 작업을 수행할 수 있습니다.
이 프로세스에 대한 세부 정보는 [Azure Automation 상태 구성을 통해 관리할 수 있도록 컴퓨터](/azure/automation/automation-dsc-onboarding#azure-portal)를 등록 하는 페이지에 설명 되어 있습니다.
하이브리드 노드의 경우 포함 된 Python 스크립트를 사용 하 여 드리프트 수정 작업을 수행할 수 있습니다.
[Linux 리포지토리의 POWERSHELL DSC에 대한](https://github.com/Microsoft/PowerShell-DSC-for-Linux#performing-dsc-operations-from-the-linux-computer)설명서를 참조 하세요.

## <a name="next-steps"></a>다음 단계

- PowerShell cmdlet 참조는 [Azure Automation 상태 구성 cmdlet](/powershell/module/azurerm.automation/#automation)을 참조하세요.
- 지속적인 배포 파이프라인에서 Azure Automation 상태 구성을 사용하는 예제는 [Azure Automation 상태 구성 및 Chocolatey를 사용한 지속적인 배포](automation-dsc-cd-chocolatey.md)를 참조하세요.
