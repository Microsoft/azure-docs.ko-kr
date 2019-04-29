---
title: Azure의 Linux VM에서 셸 스크립트 실행
description: 이 항목에서는 명령 실행을 사용하여 Azure Linux 가상 머신 내에서 스크립트를 실행하는 방법 설명
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 10/25/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: e865d4e9cbad2c2064d961bc6e407440ce8556fc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60772073"
---
# <a name="run-shell-scripts-in-your-linux-vm-with-run-command"></a>명령 실행을 사용하여 Linux VM에서 셸 스크립트 실행

명령 실행은 VM 에이전트를 사용하여 Azure Linux VM 내에서 셸 스크립트를 실행합니다. 이러한 스크립트는 일반 컴퓨터 또는 애플리케이션 관리에 사용할 수 있으며, VM 액세스 및 네트워크 문제를 신속하게 진단 및 수정하고 VM을 정상 상태로 되돌리는 데 사용할 수 있습니다.

## <a name="benefits"></a>이점

가상 머신에 액세스하는 데 사용할 수 있는 여러 옵션이 있습니다. 명령 실행은 VM 에이전트를 사용하여 원격으로 가상 머신에서 스크립트를 실행할 수 있습니다. 명령 실행은 Linux VM용 Azure Portal, [REST API](/rest/api/compute/virtual%20machines%20run%20commands/runcommand) 또는 [Azure CLI](/cli/azure/vm/run-command?view=azure-cli-latest#az-vm-run-command-invoke)를 통해 사용할 수 있습니다.

이 기능은 가상 머신 내에서 스크립트를 실행하려는 경우 모든 시나리오에서 유용하며, 부적절한 네트워크 또는 관리 사용자 구성으로 인해 RDP 또는 SSH를 개방하지 않은 가상 머신의 문제를 해결하고 수정할 수 있는 유일한 방법입니다.

## <a name="restrictions"></a>제한

명령 실행을 사용하는 경우 제공되는 제한 사항 목록은 다음과 같습니다.

* 출력은 마지막 4096바이트로 제한됨
* 스크립트를 실행하는 최소 시간 약 20초
* 기본적으로 스크립트는 Linux에서 관리자 권한으로 실행
* 한 번에 한 스크립트씩 실행 가능
* 정보를 요청하는 스크립트(대화형 모드)는 지원되지 않습니다.
* 실행 중인 스크립트는 취소할 수 없음
* 스크립트를 실행할 수 있는 최대 시간은 90분으로 이후는 시간 초과
* 스크립트의 결과를 반환하려면 VM에서의 아웃바운드 연결이 필요합니다.

> [!NOTE]
> 제대로 작동하려면 실행 명령이 Azure 공용 IP 주소에 연결(포트 443)되어야 합니다. 확장이 이러한 엔드포인트에 대해 액세스 권한이 없는 경우 스크립트는 성공적으로 실행되지만 결과를 반환하지는 않습니다. 가상 머신에서 트래픽을 차단하는 경우 `AzureCloud` 태그를 사용하여 Azure 공용 IP 주소로 트래픽을 허용하려면 [서비스 태그](../../virtual-network/security-overview.md#service-tags)를 사용할 수 있습니다.

## <a name="azure-cli"></a>Azure CLI

Azure Linux VM에서 셸 스크립트를 실행하기 위해 [az vm run-command](/cli/azure/vm/run-command?view=azure-cli-latest#az-vm-run-command-invoke) 명령을 사용하는 예제는 다음과 같습니다.

```azurecli-interactive
az vm run-command invoke -g myResourceGroup -n myVm --command-id RunShellScript --scripts "sudo apt-get update && sudo apt-get install -y nginx"
```

> [!NOTE]
> 다른 사용자로 명령을 실행하려면 `sudo -u`를 사용하여 사용할 사용자 계정을 지정할 수 있습니다.

## <a name="azure-portal"></a>Azure portal

[Azure](https://portal.azure.com)에서 VM으로 이동하여 **작업** 아래에서 **명령 실행**을 선택합니다. VM에서 실행에 사용할 수 있는 명령 목록이 표시됩니다.

![명령 실행 목록](./media/run-command/run-command-list.png)

실행할 명령을 선택하세요. 명령 중 일부에는 선택 또는 필수 입력 매개 변수가 있을 수 있습니다. 이러한 명령의 경우 매개 변수는 입력 값을 제공하기 위한 텍스트 필드로 표시됩니다. 각 명령의 경우 **스크립트 보기**를 확장하여 실행되는 스크립트를 볼 수 있습니다. **RunShellScript**는 사용자 고유의 사용자 지정 스크립트를 제공할 수 있으므로 다른 명령과 다릅니다.

> [!NOTE]
> 기본 제공된 명령은 편집할 수 없습니다.

명령을 선택하면 **실행**을 클릭하여 스크립트를 실행합니다. 스크립트가 실행되고 완료되면 출력 창에 출력 및 모든 오류를 반환합니다. 다음 스크린샷은 **ifconfig** 명령을 실행하는 작업의 예제 출력을 보여줍니다.

![명령 실행 스크립트 출력](./media/run-command/run-command-script-output.png)

## <a name="available-commands"></a>사용할 수 있는 명령

이 표에서는 Linux VM에 대해 사용할 수 있는 명령 목록을 보여줍니다. **RunShellScript** 명령은 원하는 모든 사용자 지정 스크립트를 실행하는 데 사용할 수 있습니다.

|**Name**|**설명**|
|---|---|
|**RunShellScript**|Linux 셸 스크립트를 실행합니다.|
|**ifconfig**| 모든 네트워크 인터페이스의 구성을 가져옵니다.|

## <a name="limiting-access-to-run-command"></a>명령 실행에 대한 액세스 제한

명령 실행을 나열하거나 명령의 세부 정보를 보여주는 것은 기본 제공 [판독기](../../role-based-access-control/built-in-roles.md#reader) 역할 이상이 가진 `Microsoft.Compute/locations/runCommands/read`의 사용 권한이 필요합니다.

명령 실행은 [기여자](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) 역할 이상이 가진 `Microsoft.Compute/virtualMachines/runCommand/action`의 권한이 필요합니다.

명령 실행을 사용하려면 [기본 제공](../../role-based-access-control/built-in-roles.md) 역할 중 하나를 사용하거나 [사용자 지정](../../role-based-access-control/custom-roles.md) 역할을 만들 수 있습니다.

## <a name="next-steps"></a>다음 단계

VM에서 원격으로 스크립트 및 명령을 실행하는 다른 방법을 알려면 [Linux VM에서 스크립트 실행](run-scripts-in-vm.md)을 참조합니다.
