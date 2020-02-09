---
title: Azure에서 Linux VM의 셸 스크립트 실행
description: 이 항목에서는 명령 실행 기능을 사용 하 여 Azure Linux 가상 컴퓨터에서 스크립트를 실행 하는 방법에 대해 설명 합니다.
services: automation
ms.service: automation
author: bobbytreed
ms.author: robreed
ms.date: 04/26/2019
ms.topic: article
manager: carmonm
ms.openlocfilehash: 21787854590d3ca0be2cbd6e9d167de33482c787
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/18/2019
ms.locfileid: "72597891"
---
# <a name="run-shell-scripts-in-your-linux-vm-by-using-run-command"></a>Run 명령을 사용 하 여 Linux VM에서 셸 스크립트 실행

명령 실행 기능은 VM (가상 컴퓨터) 에이전트를 사용 하 여 Azure Linux VM 내에서 셸 스크립트를 실행 합니다. 이러한 스크립트는 일반 컴퓨터 또는 응용 프로그램 관리에 사용할 수 있습니다. VM 액세스 및 네트워크 문제를 신속 하 게 진단 하 고 수정 하 고 VM을 다시 정상 상태로 전환 하는 데 도움을 받을 수 있습니다.

## <a name="benefits"></a>혜택

여러 가지 방법으로 가상 컴퓨터에 액세스할 수 있습니다. 실행 명령은 VM 에이전트를 사용 하 여 가상 컴퓨터에서 스크립트를 원격으로 실행할 수 있습니다. Linux Vm에 대 한 Azure Portal, [REST API](/rest/api/compute/virtual%20machines%20run%20commands/runcommand)또는 [Azure CLI](/cli/azure/vm/run-command?view=azure-cli-latest#az-vm-run-command-invoke) 를 통해 실행 명령을 사용 합니다.

이 기능은 가상 컴퓨터 내에서 스크립트를 실행 하려는 모든 시나리오에서 유용 합니다. 네트워크 또는 관리 사용자 구성이 잘못 되어 RDP 또는 SSH 포트가 열려 있지 않은 가상 컴퓨터의 문제를 해결 하 고 수정 하는 유일한 방법 중 하나입니다.

## <a name="restrictions"></a>제한

Run 명령을 사용 하는 경우 다음 제한 사항이 적용 됩니다.

* 출력은 최근 4096 바이트로 제한 됩니다.
* 스크립트를 실행 하는 최소 시간은 약 20 초입니다.
* 기본적으로 스크립트는 Linux에서 관리자 권한으로 실행 됩니다.
* 한 번에 하나의 스크립트를 실행할 수 있습니다.
* 정보를 요청하는 스크립트(대화형 모드)는 지원되지 않습니다.
* 실행 중인 스크립트는 취소할 수 없습니다.
* 스크립트를 실행할 수 있는 최대 시간은 90 분입니다. 그런 다음 스크립트 시간이 초과 됩니다.
* 스크립트의 결과를 반환하려면 VM에서의 아웃바운드 연결이 필요합니다.

> [!NOTE]
> 제대로 작동 하려면 명령 실행에 Azure 공용 IP 주소에 대 한 연결 (포트 443)이 있어야 합니다. 확장에 이러한 엔드포인트에 대 한 액세스 권한이 없는 경우 스크립트가 성공적으로 실행 될 수 있지만 결과는 반환 되지 않습니다. 가상 머신에서 트래픽을 차단 하는 경우 [서비스 태그](../../virtual-network/security-overview.md#service-tags) 를 사용 하 여 `AzureCloud` 태그를 통해 AZURE 공용 IP 주소에 대 한 트래픽을 허용할 수 있습니다.

## <a name="available-commands"></a>사용 가능한 명령

이 표에서는 Linux VM에 대해 사용할 수 있는 명령 목록을 보여줍니다. **Runshellscript** 명령을 사용 하 여 원하는 사용자 지정 스크립트를 실행할 수 있습니다. Azure CLI 또는 PowerShell을 사용 하 여 명령을 실행 하는 경우 `--command-id` 또는 `-CommandId` 매개 변수에 대해 제공 하는 값은 다음 목록 값 중 하나 여야 합니다. 사용할 수 있는 명령이 아닌 값을 지정 하면 다음과 같은 오류가 표시 됩니다.

```error
The entity was not found in this Azure location
```

|**Name**|**설명**|
|---|---|
|**RunShellScript**|Linux 셸 스크립트를 실행 합니다.|
|**ifconfig**| 모든 네트워크 인터페이스의 구성을 가져옵니다.|

## <a name="azure-cli"></a>Azure CLI

다음 예제에서는 [az vm run 명령을](/cli/azure/vm/run-command?view=azure-cli-latest#az-vm-run-command-invoke) 사용 하 여 AZURE Linux vm에서 셸 스크립트를 실행 합니다.

```azurecli-interactive
az vm run-command invoke -g myResourceGroup -n myVm --command-id RunShellScript --scripts "sudo apt-get update && sudo apt-get install -y nginx"
```

> [!NOTE]
> 다른 사용자로 명령을 실행 하려면 `sudo -u`를 입력 하 여 사용자 계정을 지정 합니다.

## <a name="azure-portal"></a>Azure Portal

[Azure Portal](https://portal.azure.com) 에서 VM으로 이동 하 고 **작업**아래에서 **명령 실행** 을 선택 합니다. VM에서 실행할 수 있는 명령 목록이 표시 됩니다.

![명령 목록](./media/run-command/run-command-list.png)

실행할 명령을 선택하세요. 일부 명령에는 선택적 또는 필수 입력 매개 변수가 있을 수 있습니다. 이러한 명령의 경우 입력 값을 입력할 수 있도록 매개 변수가 텍스트 필드로 제공 됩니다. 각 명령에 대해 **보기 스크립트**를 확장 하 여 실행 중인 스크립트를 볼 수 있습니다. **Runshellscript** 는 고유한 사용자 지정 스크립트를 제공할 수 있기 때문에 다른 명령과 다릅니다.

> [!NOTE]
> 기본 제공된 명령은 편집할 수 없습니다.

명령을 선택한 후 **실행** 을 선택 하 여 스크립트를 실행 합니다. 스크립트가 완료 되 면 출력 창에 출력 및 오류가 반환 됩니다. 다음 스크린샷은 **ifconfig** 명령을 실행하는 작업의 예제 출력을 보여줍니다.

![명령 실행 스크립트 출력](./media/run-command/run-command-script-output.png)

### <a name="powershell"></a>PowerShell

다음 예제에서는 [AzVMRunCommand](https://docs.microsoft.com/powershell/module/az.compute/invoke-azvmruncommand) cmdlet을 사용 하 여 Azure VM에서 PowerShell 스크립트를 실행 합니다. cmdlet에서는 `-ScriptPath` 매개 변수에서 참조되는 스크립트가 cmdlet이 실행되는 위치의 로컬이어야 합니다.

```powershell-interactive
Invoke-AzVMRunCommand -ResourceGroupName '<myResourceGroup>' -Name '<myVMName>' -CommandId 'RunPowerShellScript' -ScriptPath '<pathToScript>' -Parameter @{"arg1" = "var1";"arg2" = "var2"}
```

## <a name="limiting-access-to-run-command"></a>명령 실행에 대한 액세스 제한

실행 명령을 나열 하거나 명령의 세부 정보를 표시 하려면 구독 수준에서 `Microsoft.Compute/locations/runCommands/read` 권한이 필요 합니다. 기본 제공 [판독기](../../role-based-access-control/built-in-roles.md#reader) 역할 및 상위 수준에이 권한이 있습니다.

명령을 실행 하려면 구독 수준에서 `Microsoft.Compute/virtualMachines/runCommand/action` 권한이 필요 합니다. [가상 컴퓨터 참가자](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) 역할 및 상위 수준에이 권한이 있습니다.

[기본 제공 역할](../../role-based-access-control/built-in-roles.md) 중 하나를 사용 하거나 실행 명령을 사용할 [사용자 지정 역할](../../role-based-access-control/custom-roles.md) 을 만들 수 있습니다.

## <a name="next-steps"></a>다음 단계

VM에서 스크립트 및 명령을 원격으로 실행 하는 다른 방법에 대해 알아보려면 [LINUX VM에서 스크립트 실행](run-scripts-in-vm.md)을 참조 하세요.
