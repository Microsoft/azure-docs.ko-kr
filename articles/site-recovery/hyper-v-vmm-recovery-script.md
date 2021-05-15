---
title: Azure Site Recovery에서 복구 계획에 스크립트 추가
description: VMM 클라우드에서 Hyper-V VM의 재해 복구를 위해 복구 계획에 VMM 스크립트를 추가하는 방법을 알아봅니다.
author: Sharmistha-Rai
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: sharrai
ms.openlocfilehash: 3217c30737a133c1c1092fc4a8a8caaa0338e980
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "89425877"
---
# <a name="add-a-vmm-script-to-a-recovery-plan"></a>복구 계획에 VMM 스크립트 추가

이 문서에서는 [Azure Site Recovery](site-recovery-overview.md)에서 System Center Virtual Machine Manager(VMM) 스크립트를 생성하여 복구 계획에 추가하는 방법을 설명합니다.

이 문서의 맨 아래쪽 또는 [Azure Recovery Services의 Microsoft Q&A 질문 페이지](/answers/topics/azure-site-recovery.html)에 의견이나 질문을 게시할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

복구 계획에서 PowerShell 스크립트를 사용할 수 있습니다. 복구 계획에서 스크립트를 액세스할 수 있도록 스크립트를 작성하여 VMM 라이브러리에 배치해야 합니다. 스크립트를 작성할 때는 다음 사항에 유의하세요.

* 예외가 정상적으로 처리되도록 스크립트에서 try-catch 블록을 사용합니다.
    - 스크립트에서 예외가 발생하면 실행이 중지되고 작업이 실패한 것으로 표시됩니다.
    - 오류가 발생하면 스크립트의 나머지 부분이 실행되지 않습니다.
    - 계획되지 않은 장애 조치를 실행할 때 오류가 발생하면 복구 계획이 계속됩니다.
    - 계획된 장애 조치를 실행할 때 오류가 발생하면 복구 계획이 중단됩니다. 스크립트를 수정하고 예상대로 실행되는지 확인한 다음, 복구 계획을 다시 실행합니다.
        - 복구 계획 스크립트에서는 `Write-Host` 명령이 작동하지 않습니다. 스크립트에서 `Write-Host` 명령을 사용하면 스크립트가 제대로 실행되지 않습니다. 출력을 만들려면 기본 스크립트를 실행하는 프록시 스크립트를 만듭니다. 모든 출력이 표시되도록 **\>\>** 명령을 사용합니다.
        - 600초 이내에 반환하지 않는 경우 스크립트 시간이 초과됩니다.
        - STDERR에 기록되는 항목이 하나라도 존재하면 스크립트가 실패로 분류됩니다. 이 정보는 스크립트 실행 세부 정보에 표시됩니다.

* 복구 계획의 스크립트는 VMM 서비스 계정 컨텍스트에서 실행됩니다. 따라서 VMM 서비스 계정에 스크립트가 위치한 원격 공유에 대한 읽기 권한이 있어야 합니다. 스크립트가 VMM 서비스 계정에 부여된 사용자 권한과 동일한 수준으로 실행되는지 테스트합니다.
* VMM cmdlet은 Windows PowerShell 모듈로 배달됩니다. 모듈은 VMM 콘솔을 설치할 때 설치됩니다. 스크립트에 모듈을 로드하려면 스크립트에서 다음 명령을 사용합니다. 

    `Import-Module -Name virtualmachinemanager`

    자세한 내용은 [Windows PowerShell과 VMM 시작](/previous-versions/system-center/system-center-2012-R2/hh875013(v=sc.12))을 참조하세요.
* VMM 배포에 최소 1개의 라이브러리 서버가 있어야 합니다. VMM 서버의 라이브러리 공유 경로는 기본적으로 VMM 서버 로컬에 위치합니다. 폴더 이름은 MSCVMMLibrary입니다.

  라이브러리 공유 경로가 원격 위치에 있거나 로컬에 있으나 MSCVMMLibrary와 공유되어 있지 않다면 다음과 같이 공유를 구성합니다. 여기서는 공유의 예로 \\libserver2.contoso.com\share\를 사용합니다.
  
  1. 레지스트리 편집기를 열고 **HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\Azure Site Recovery\Registration** 으로 이동합니다.

  1. **ScriptLibraryPath** 의 값을 **\\\libserver2.contoso.com\share\\** 로 변경합니다. 전체 FQDN을 지정합니다. 공유 위치에 대한 사용 권한을 제공합니다. 이것은 공유의 루트 노드입니다. 루트 노드를 확인하려면 VMM에서 라이브러리의 루트 노드로 이동합니다. 이때 열리는 경로가 경로의 루트입니다. 이 경로를 변수에 사용해야 합니다.

  1. VMM 서비스 계정과 동일한 사용자 권한 수준을 갖는 사용자 계정으로 스크립트를 테스트합니다. 이렇게 하면 스크립트를 독립형 테스트 환경에서 실행할 때도 복구 계획과 동일한 환경으로 테스트할 수 있습니다. VMM 서버에서 실행 정책을 다음과 같이 우회로 설정합니다.

     a. **64비트 Windows PowerShell** 콘솔을 권리자 권한으로 엽니다.
     
     b. **Set-executionpolicy bypass** 를 입력합니다. 자세한 내용은 [Set-ExecutionPolicy cmdlet 사용](/previous-versions/windows/it-pro/windows-powershell-1.0/ee176961(v=technet.10))을 참조하세요.

     > [!IMPORTANT]
     > **Set-executionpolicy bypass** 는 64비트 PowerShell 콘솔에서만 설정해야 합니다. 32비트 PowerShell 콘솔에서 설정하면 스크립트가 실행되지 않습니다.

## <a name="add-the-script-to-the-vmm-library"></a>VMM 라이브러리에 스크립트 추가

VMM 소스 사이트가 있다면 VMM 서버에서 스크립트를 생성할 수 있습니다. 그런 다음, 복구 계획에 스크립트를 추가하면 됩니다.

1. 라이브러리 공유에서 새 폴더를 만듭니다. 예: \<VMM server name>\MSSCVMMLibrary\RPScripts. 폴더를 VMM 서버의 소스와 대상에 배치합니다.
1. 스크립트를 작성합니다. 여기서는 스크립트의 이름을 RPScript라고 지정하겠습니다. 스크립트가 예상대로 작동하는지 확인합니다.
1. 스크립트를 소스 및 대상 VMM 서버의 \<VMM server name>\MSSCVMMLibrary 폴더에 배치합니다.

## <a name="add-the-script-to-a-recovery-plan"></a>복구 계획에 스크립트 추가

복구 계획에 VM 또는 복제 그룹을 추가하고 계획을 생성했으면 그룹에 스크립트를 추가합니다.

1. 복구 계획을 엽니다.
1. **단계** 목록에서 항목을 하나 선택합니다. 그런 다음, **스크립트** 와 **수동 작업** 중 하나를 선택합니다.
1. 스크립트 또는 작업을 선택한 항목의 앞에 추가할 것인지 뒤에 추가할 것인지 지정합니다. 스크립트를 위나 아래로 이동하려면 **위로 이동** 및 **아래로 이동** 단추를 선택합니다.
1. VMM 스크립트를 추가하면 **VMM 스크립트에 대한 장애 조치** 를 선택합니다. **스크립트 경로** 에 공유의 상대 경로를 입력합니다. 예: **\RPScripts\RPScript.PS1**.
1. Azure Automation Runbook을 추가하는 경우에는 Runbook이 있는 Automation 계정을 지정합니다. 그런 다음, 사용하려는 Azure Runbook 스크립트를 선택합니다.
1. 스크립트가 예상대로 작동하는지 확인하려면 복구 계획의 장애 조치(failover)를 테스트합니다.


## <a name="next-steps"></a>다음 단계
* [장애 조치(failover) 실행](site-recovery-failover.md)에 대해 자세히 알아보세요.

