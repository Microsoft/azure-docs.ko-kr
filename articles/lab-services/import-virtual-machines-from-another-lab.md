---
title: Azure DevTest Labs의 다른 랩에서 가상 컴퓨터 가져오기
description: 이 문서에서는 Azure DevTest Labs에서 현재 랩으로 다른 랩에서 가상 컴퓨터를 가져오는 방법을 설명 합니다.
services: devtest-lab, lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: 299d5c8758a13edded63b99abb2f12ddf9fa14be
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/26/2020
ms.locfileid: "76759519"
---
# <a name="import-virtual-machines-from-another-lab-in-azure-devtest-labs"></a>Azure DevTest Labs의 다른 랩에서 가상 컴퓨터 가져오기
이 문서에서는 다른 랩에서 랩으로 가상 컴퓨터를 가져오는 방법에 대 한 정보를 제공 합니다.

## <a name="scenarios"></a>시나리오
한 랩에서 다른 랩으로 Vm을 가져와야 하는 몇 가지 시나리오는 다음과 같습니다.

- 팀의 개인이 기업 내 다른 그룹으로 이동 하 고 개발자 데스크톱을 새로운 팀의 DevTest Labs로 이동 하려고 합니다.
- 그룹이 [구독 수준 할당량](../azure-resource-manager/management/azure-subscription-service-limits.md) 에 도달 하 여 팀을 몇 개 구독으로 분할 하려고 합니다.
- 회사에서 새 인프라를 사용 하도록 Virtual Machines 이동 하려고 합니다 (또는 다른 새 네트워킹 토폴로지).

## <a name="solution-and-constraints"></a>해결 방법 및 제약 조건
이 기능을 사용 하면 하나의 랩 (원본)에서 다른 랩 (대상)으로 Vm을 가져올 수 있습니다. 필요에 따라 프로세스에서 대상 VM에 대 한 새 이름을 지정할 수 있습니다. 가져오기 프로세스에는 디스크, 일정, 네트워크 설정 등과 같은 모든 종속성이 포함 됩니다.

프로세스는 다소 시간이 걸리며 다음과 같은 요인의 영향을 받습니다.

- 원본 컴퓨터에 연결 된 디스크의 수/크기 (복사 작업 이므로 이동 작업이 아님)
- 대상에 대 한 거리 (예: 미국 동부 지역에서 동남 아시아로).

프로세스가 완료 되 면 원본 가상 컴퓨터는 종료 된 상태로 유지 되 고 새 가상 컴퓨터는 대상 랩에서 실행 됩니다.

의 단일 랩에서 다른 랩으로 Vm을 가져올 계획인 경우 다음 두 가지 주요 제약 조건이 있습니다.

- 구독 및 지역 간 가상 컴퓨터 가져오기는 지원 되지만 동일한 Azure Active Directory 테넌트에 구독을 연결 해야 합니다.
- Virtual Machines은 원본 랩에서 클레임 할 수 있는 상태가 아니어야 합니다.
- 대상 랩에서 랩의 원본 랩 및 소유자에서 VM의 소유자입니다.
- 현재이 기능은 Powershell 및 REST API을 통해서만 지원 됩니다.

## <a name="use-powershell"></a>PowerShell 사용
[GitHub](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImportVirtualMachines)에서 ImportVirtualMachines 파일을 다운로드 합니다. 스크립트를 사용 하 여 원본 랩에서 단일 VM 또는 모든 Vm을 대상 랩으로 가져올 수 있습니다.

### <a name="use-powershell-to-import-a-single-vm"></a>PowerShell을 사용 하 여 단일 VM 가져오기
이 powershell 스크립트를 실행 하려면 원본 VM과 대상 랩을 식별 하 고 대상 컴퓨터에 사용할 새 이름을 선택적으로 제공 해야 합니다.

```powershell
./ImportVirtualMachines.ps1 -SourceSubscriptionId "<ID of the subscription that contains the source lab>" `
                            -SourceDevTestLabName "<Name of the source lab>" `
                            -SourceVirtualMachineName "<Name of the VM to be imported from the source lab> " `
                            -DestinationSubscriptionId "<ID of the subscription that contians the destination lab>" `
                            -DestinationDevTestLabName "<Name of the destination lab>" `
                            -DestinationVirtualMachineName "<Optional: specify a new name for the imported VM in the destination lab>"
```

### <a name="use-powershell-to-import-all-vms-in-the-source-lab"></a>PowerShell을 사용 하 여 원본 랩에서 모든 Vm 가져오기
원본 가상 컴퓨터를 지정 하지 않으면 스크립트는 DevTest Labs의 모든 Vm을 자동으로 가져옵니다.  예:

```powershell
./ImportVirtualMachines.ps1 -SourceSubscriptionId "<ID of the subscription that contains the source lab>" `
                            -SourceDevTestLabName "<Name of the source lab>" `
                            -DestinationSubscriptionId "<ID of the subscription that contians the destination lab>" `
                            -DestinationDevTestLabName "<Name of the destination lab>"
```

## <a name="use-http-rest-to-import-a-vm"></a>HTTP REST를 사용 하 여 VM 가져오기
REST 호출은 간단 합니다. 원본 및 대상 리소스를 식별 하는 데 충분 한 정보를 제공 합니다. 작업은 대상 랩 리소스에서 수행 됩니다.

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
