---
title: Azure DevTest Labs에서 다른 lab에서 가상 컴퓨터 가져오기 | Microsoft Docs
description: 현재 랩에 다른 랩의 가상 컴퓨터를 가져오는 방법에 알아봅니다.
services: devtest-lab, lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/21/2019
ms.author: spelluru
ms.openlocfilehash: ca6ed58cfabb5027830828812c4820c1b586875c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61322878"
---
# <a name="import-virtual-machines-from-another-lab-in-azure-devtest-labs"></a>Azure DevTest Labs에서 다른 lab에서 가상 컴퓨터 가져오기
이 문서에서는 다른 lab에서 랩에 가상 컴퓨터를 가져오는 방법에 대 한 정보를 제공 합니다.

## <a name="scenarios"></a>시나리오
다른 랩에 하나의 랩의 Vm을 가져와야 하는 몇 가지 시나리오는 다음과 같습니다.

- 팀의 개별 엔터프라이즈 내 다른 그룹으로 이동 하 고 새 팀의 DevTest Labs에 개발자 데스크톱을 사용 하려고 합니다.
- 그룹에 도달한를 [구독 수준 할당량](../azure-subscription-service-limits.md) 구독이 몇 개를 팀 분할 하려고 합니다.
- Express Route (또는 일부 다른 새 네트워킹 토폴로지)로 이동 하는 회사 및 팀이 새로운 인프라를 사용 하도록 가상 컴퓨터를 이동 하려고 합니다.

## <a name="solution-and-constraints"></a>해결 방법 및 제약 조건
이 기능을 사용 하면 다른 랩 (대상)에 하나의 랩 (원본)에서 Vm을 가져올 수 있습니다. 필요에 따라 프로세스에서 대상 VM에 대 한 새 이름을 지정할 수 있습니다. 가져오기 프로세스는 디스크, 일정, 네트워크 설정 등 모든 종속성을 포함합니다.

프로세스는 다소 시간이 걸리며 다음 요인의 영향:

- (이므로 복사 작업 및 이동 작업 아님) 원본 컴퓨터에 연결 된 디스크의 수/크기
- 대상 (예: 미국 동부 지역 동남 아시아로) 까지의 거리입니다.

프로세스가 완료 되 면 원본 가상 머신을 종료 하 고 새 대상 환경에서 실행 중인 하나 남아 있습니다.

다른 랩에 Vm에 하나의 랩 가져올 계획할 때 알아야 할 키 제약 조건을 두 가지 있습니다.

- 구독 및 지역에서 가상 머신 가져오기 사용할 수 있지만 구독은 동일한 Azure Active Directory 테 넌 트에 연결 되어야 합니다.
- 가상 컴퓨터 원본 랩에 클레임 할 수 있는 상태가 아니어야 합니다.
- 대상 환경에서 랩의 소유자를 원본 랩 VM의 소유자 인 합니다.
- 현재이 기능은 Powershell 및 REST API를 통해서만 지원 됩니다.

## <a name="use-powershell"></a>PowerShell 사용
ImportVirtualMachines.ps1 파일을 다운로드 합니다 [GitHub](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImportVirtualMachines)합니다. 대상 랩에 단일 VM 또는 원본 랩의 모든 Vm을 가져오는 스크립트를 사용할 수 있습니다.

### <a name="use-powershell-to-import-a-single-vm"></a>PowerShell을 사용 하 여 단일 VM을 가져오려면
이 powershell 스크립트를 실행 필요 VM 원본 및 대상 랩을 식별 하 고 필요에 따라 대상 컴퓨터에 사용할 새 이름을 제공 합니다.

```powershell
./ImportVirtualMachines.ps1 -SourceSubscriptionId "<ID of the subscription that contains the source lab>" `
                            -SourceDevTestLabName "<Name of the source lab>" `
                            -SourceVirtualMachineName "<Name of the VM to be imported from the source lab> " `
                            -DestinationSubscriptionId "<ID of the subscription that contians the destination lab>" `
                            -DestinationDevTestLabName "<Name of the destination lab>" `
                            -DestinationVirtualMachineName "<Optional: specify a new name for the imported VM in the destination lab>"
```

### <a name="use-powershell-to-import-all-vms-in-the-source-lab"></a>PowerShell를 사용 하 여 원본 랩의 모든 Vm 가져오기
원본 가상 컴퓨터를 지정 하지 않으면 스크립트 DevTest 랩에서 모든 Vm을 자동으로 가져옵니다.  예를 들면 다음과 같습니다.

```powershell
./ImportVirtualMachines.ps1 -SourceSubscriptionId "<ID of the subscription that contains the source lab>" `
                            -SourceDevTestLabName "<Name of the source lab>" `
                            -DestinationSubscriptionId "<ID of the subscription that contians the destination lab>" `
                            -DestinationDevTestLabName "<Name of the destination lab>"
```

## <a name="use-http-rest-to-import-a-vm"></a>HTTP REST를 사용 하 여 VM을 가져오려면
REST 호출을 하는 것은 간단 합니다. 원본 및 대상 리소스를 식별 하려면 충분 한 정보를 제공 합니다. 한 작업에서 발생 대상 랩 리소스를 기억 합니다.

```REST
POST https://management.azure.com/subscriptions/<DestinationSubscriptionID>/resourceGroups/<DestinationResourceGroup>/providers/Microsoft.DevTestLab/labs/<DestinationLab>/ImportVirtualMachine?api-version=2017-04-26-preview
{
   sourceVirtualMachineResourceId: "/subscriptions/<SourceSubscriptionID>/resourcegroups/<SourceResourceGroup>/providers/microsoft.devtestlab/labs/<SourceLab>/virtualmachines/<NameofVMTobeImported>",
   destinationVirtualMachineName: "<NewNameForImportedVM>"
}
```

## <a name="next-steps"></a>다음 단계
다음 문서를 참조하세요.

- [랩에 대한 정책 설정](devtest-lab-get-started-with-lab-policies.md)
- [질문과 대답](devtest-lab-faq.md)
