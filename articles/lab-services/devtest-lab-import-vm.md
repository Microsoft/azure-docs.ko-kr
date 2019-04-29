---
title: Azure DevTest Labs에서 다른 랩의 VM 가져오기 | Microsoft Docs
description: Azure DevTest Labs의 현재 랩으로 다른 랩의 가상 머신을 가져오는 방법을 설명합니다.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 8460f09e-482f-48ba-a57a-c95fe8afa001
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/11/2018
ms.author: spelluru
ms.openlocfilehash: cb4a3ec9be82957b4c0366ec232f1147c52d0251
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62125902"
---
# <a name="import-vms-from-another-lab-in-azure-devtest-labs"></a>Azure DevTest Labs에서 다른 랩의 VM 가져오기
Azure DevTest Labs 서비스를 사용하면 개발 및 테스트 활동을 위한 VM(가상 머신) 관리 방식을 크게 개선할 수 있습니다. 이 서비스에서는 팀 또는 인프라 요구 사항 변경 시 랩 간에 VM을 이동할 수 있습니다. VM을 이동해야 할 수 있는 몇 가지 일반적인 시나리오는 다음과 같습니다.

- 한 팀원이 기업 내의 다른 그룹으로 이동하면서 개발 VM을 새 팀의 랩으로 가져가려는 경우
- 그룹의 구동 수준 할당량이 소진되어 팀을 여러 구독으로 분할하려는 경우
- 회사에서 Express 경로 또는 기타 신규 네트워킹 토폴로지로 이동할 계획이어서 팀이 해당 신규 인프라를 사용하기 위해 VM을 이동하려는 경우

## <a name="solution-and-constraints"></a>해결 방법 및 제약 조건
랩 소유자는 Azure DevTest Labs를 통해 원본 랩의 VM을 대상 랩으로 가져올 수 있습니다. 그리고 필요한 경우에는 가져오기 프로세스에서 대상 VM의 새 이름을 입력할 수도 있습니다. 가져오기 프로세스에는 디스크, 일정, 네트워크 설정 등의 모든 종속성이 포함됩니다. 해당 프로세스는 수행하는 데 시간이 다소 걸리며, 원본 컴퓨터에 연결된 디스크의 수/크기 및 대상까지의 거리(예: 미국 동부 지역에서 동남 아시아 지역으로의 가져오기)에 영향을 받습니다. 가져오기 프로세스가 완료되면 원본 VM은 종료된 상태로 유지되며 새 VM이 대상 랩에서 실행됩니다.

다른 랩으로 VM 가져오기를 계획할 때는 다음의 두 가지 주요 제약 조건을 고려해야 합니다.

- 여러 구독과 지역 간에 VM을 가져올 수는 있지만, 이 경우 구독이 같은 Azure Active Directory 테넌트에 연결되어 있어야 합니다.
- 원본 랩에서 VM이 클레임할 수 있는 상태가 아니어야 합니다.

또한 랩 간에 VM을 가져오려면 원본 랩에서는 VM의 소유자여야 하며 대상 랩에서는 랩의 소유자여야 합니다.

## <a name="steps-to-import-a-vm-from-another-lab"></a>다른 랩의 VM을 가져오는 단계
현재는 Azure PowerShell 및 REST API를 통해서만 특정 랩의 VM을 다른 랩으로 가져올 수 있습니다.

### <a name="use-powershell"></a>PowerShell 사용
[Azure DevTest Lab Git 리포지토리](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImportVirtualMachines)에서 PowerShell 스크립트 파일 ImportVirtualMachines.ps1을 로컬 드라이브에 다운로드합니다.

#### <a name="import-a-single-vm"></a>단일 VM 가져오기
원본 랩의 단일 VM을 대상 랩으로 가져오려면 ImportVirtualMachines.ps1 스크립트를 실행합니다. DestinationVirtualMachineName 매개 변수를 사용하면 복사하는 VM의 새 이름을 지정할 수 있습니다.

```powershell
./ImportVirtualMachines.ps1 -SourceSubscriptionId "<ID of the subscription that contains the source VM>" `
                            -SourceDevTestLabName "<Name of the lab that contains the source VM>" `
                            -SourceVirtualMachineName "<Name of the machine. Optional. If not specified, all VMs are copied>" `
                            -DestinationSubscriptionId "<ID of the target/destination subscription>" `
                            -DestinationDevTestLabName "<Name of the lab to which the VM is copied>" `
                            -DestinationVirtualMachineName "<New name for the VM. Optional>"
```


#### <a name="importing-all-vms"></a>모든 VM 가져오기
ImportVirtualMachines.ps1 스크립트를 실행할 때 원본 랩의 VM을 지정하지 않으면 스크립트는 원본 랩의 모든 VM을 대상 랩으로 가져옵니다.

```powershell
./ImportVirtualMachines.ps1 -SourceSubscriptionId "<ID of the subscription that contains the source VM>" `
                            -SourceDevTestLabName "<Name of the lab that contains the source VM>" `
                            -DestinationSubscriptionId "<ID of the target/destination subscription>" `
                            -DestinationDevTestLabName "<Name of the lab to which the VMs are copied>"
```

### <a name="use-rest-api"></a>REST API 사용
다음 예제에 나와 있는 것처럼 대상 랩에 대해 REST API를 호출하고 소스 랩, 구독 및 VM 정보를 매개 변수로 전달합니다.

```json
POST https://management.azure.com/subscriptions/<ID of the target/destination subscription>/resourceGroups/<Name of the resource group that contains the destination lab>/providers/Microsoft.DevTestLab/labs/<Name of the lab to which the VMs are copied>/ImportVirtualMachine?api-version=2017-04-26-preview
{
   sourceVirtualMachineResourceId: "/subscriptions/<ID of the subscription that contains the source VM>/resourcegroups/<Name of the resource group that contains the source lab>/providers/microsoft.devtestlab/labs/<Name of the lab that contains the source VM>/virtualmachines/MyVm",
   destinationVirtualMachineName: "MyVmNew"
}
```

## <a name="next-steps"></a>다음 단계

- VM 크기 조정에 대한 자세한 내용은 [VM 크기 조정](devtest-lab-resize-vm.md)을 참조하세요.
- VM 다시 배포에 대한 자세한 내용은 [VM 다시 배포](devtest-lab-redeploy-vm.md)를 참조하세요.
