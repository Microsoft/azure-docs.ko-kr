---
title: Windows VM 확장 오류 문제 해결
description: Azure Windows VM 확장 오류 문제 해결에 대해 자세히 알아봅니다.
services: virtual-machines-windows
documentationcenter: ''
author: kundanap
manager: gwallace
editor: ''
tags: top-support-issue,azure-resource-manager
ms.assetid: 878ab9b6-c3e6-40be-82d4-d77fecd5030f
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/29/2016
ms.author: kundanap
ms.openlocfilehash: ad3197f20428ec751b4e3520af72dc5f8eb9ad28
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2020
ms.locfileid: "89180358"
---
# <a name="troubleshooting-azure-windows-vm-extension-failures"></a>Azure Windows VM 확장 오류 문제 해결
[!INCLUDE [virtual-machines-common-extensions-troubleshoot](../../../includes/virtual-machines-common-extensions-troubleshoot.md)]

## <a name="viewing-extension-status"></a>확장 상태 보기
Azure Powershell에서 Azure Resource Manager 템플릿을 실행할 수 있습니다. 템플릿을 실행하면 Azure Resource Explorer 또는 명령줄 도구에서 확장 상태를 볼 수 있습니다.

예를 들면 다음과 같습니다.

Azure PowerShell:

```azurepowershell
Get-AzVM -ResourceGroupName $RGName -Name $vmName -Status
```

샘플 출력은 다음과 같습니다.

```output
Extensions:  {
  "ExtensionType": "Microsoft.Compute.CustomScriptExtension",
  "Name": "myCustomScriptExtension",
  "SubStatuses": [
    {
      "Code": "ComponentStatus/StdOut/succeeded",
      "DisplayStatus": "Provisioning succeeded",
      "Level": "Info",
      "Message": "    Directory: C:\\temp\\n\\n\\nMode                LastWriteTime     Length Name
          \\n----                -------------     ------ ----                              \\n-a---          9/1/2015   2:03 AM         11
          test.txt                          \\n\\n",
                  "Time": null
      },
    {
      "Code": "ComponentStatus/StdErr/succeeded",
      "DisplayStatus": "Provisioning succeeded",
      "Level": "Info",
      "Message": "",
      "Time": null
    }
  ]
}
```

## <a name="troubleshooting-extension-failures"></a>확장 오류 문제 해결

### <a name="rerun-the-extension-on-the-vm"></a>VM에서 확장 다시 실행
사용자 지정 스크립트 확장을 사용하여 VM에서 스크립트를 실행하는 경우 때때로 VM이 성공적으로 만들어졌지만 스크립트가 실패하는 오류가 발생할 수 있습니다. 이러한 조건에서 이 오류를 복구하는 권장 방법은 확장을 제거하고 템플릿을 다시 실행하는 것입니다.
참고: 이후에는 확장을 제거할 필요가 없도록 이 기능이 향상될 예정입니다.

#### <a name="remove-the-extension-from-azure-powershell"></a>Azure Powershell에서 확장 제거
```azurepowershell
Remove-AzVMExtension -ResourceGroupName $RGName -VMName $vmName -Name "myCustomScriptExtension"
```

확장이 제거되면 템플릿을 다시 실행하여 VM에서 스크립트를 실행할 수 있습니다.

### <a name="trigger-a-new-goalstate-to-the-vm"></a>VM에 대 한 새 GoalState 트리거
확장이 실행 되지 않았거나, "Windows Azure CRP 인증서 생성기" (해당 인증서가 확장의 보호 된 설정에 대 한 전송 보안을 유지 하는 데 사용 됨) 때문에 실행에 실패 하는 것을 알 수 있습니다.
해당 인증서는 가상 컴퓨터 내에서 Windows 게스트 에이전트를 다시 시작 하 여 자동으로 다시 생성 됩니다.
- 작업 관리자 열기
- 세부 정보 탭으로 이동
- WindowsAzureGuestAgent.exe 프로세스를 찾습니다.
- 를 마우스 오른쪽 단추로 클릭 하 고 "작업 끝내기"를 선택 합니다. 프로세스가 자동으로 다시 시작 됩니다.


"빈 업데이트"를 실행 하 여 VM에 대 한 새 GoalState을 트리거할 수도 있습니다.

Azure PowerShell:

```azurepowershell
$vm = Get-AzureRMVM -ResourceGroupName <RGName> -Name <VMName>  
Update-AzureRmVM -ResourceGroupName <RGName> -VM $vm  
```

Azure CLI:

```azurecli
az vm update -g <rgname> -n <vmname>
```

"빈 업데이트"가 작동 하지 않는 경우 Azure 관리 포털에서 VM에 새 빈 데이터 디스크를 추가한 다음, 인증서가 다시 추가 되 면 나중에 제거할 수 있습니다.
