---
title: Azure에서 Windows VM 리소스 이동 | Microsoft Docs
description: Resource Manager 배포 모델에서 다른 Azure 구독 또는 리소스 그룹으로 Windows VM을 이동합니다.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 4e383427-4aff-4bf3-a0f4-dbff5c6f0c81
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/12/2018
ms.author: cynthn
ms.openlocfilehash: ede2092be4e4eaf201e15307a7d9934ea267ae37
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61481516"
---
# <a name="move-a-windows-vm-to-another-azure-subscription-or-resource-group"></a>다른 Azure 구독 또는 리소스 그룹으로 Windows VM 이동
이 문서에서는 리소스 그룹 또는 구독 간에 Windows VM(가상 머신)을 이동하는 방법을 안내합니다. 개인 구독에서 원래 VM을 만들고 회사 구독으로 이동한 후 작업을 계속하려는 경우에 구독 간의 이동이 편리할 수 있습니다.

> [!IMPORTANT]
>새 리소스 ID는 이동의 일부로 생성됩니다. VM을 이동한 후에는 새 리소스 ID를 사용하도록 도구와 스크립트를 업데이트해야 합니다. 
> 
> 

[!INCLUDE [virtual-machines-common-move-vm](../../../includes/virtual-machines-common-move-vm.md)]

## <a name="use-powershell-to-move-a-vm"></a>PowerShell을 사용하여 VM 이동

가상 머신을 다른 리소스 그룹에 이동하려면 모든 종속 리소스를 이동하는지 확인해야 합니다. 이러한 리소스 각각에 대한 리소스 ID의 목록을 가져오려면 [Get-AzResource](https://docs.microsoft.com/powershell/module/az.resources/get-azresource) cmdlet을 사용합니다.

```azurepowershell-interactive
 Get-AzResource -ResourceGroupName <sourceResourceGroupName> | Format-table -Property ResourceId 
```

이전 명령의 출력을 [Move-AzResource](https://docs.microsoft.com/powershell/module/az.resources/move-azresource)에 대한 쉼표로 구분된 리소스 ID 목록으로 사용하여 각 리소스를 대상으로 이동할 수 있습니다. 

```azurepowershell-interactive
Move-AzResource -DestinationResourceGroupName "<myDestinationResourceGroup>" `
    -ResourceId <myResourceId,myResourceId,myResourceId>
```
    
리소스를 다른 구독으로 이동하기 위해 **-DestinationSubscriptionId** 매개 변수를 포함합니다. 

```azurepowershell-interactive
Move-AzResource -DestinationSubscriptionId "<myDestinationSubscriptionID>" `
    -DestinationResourceGroupName "<myDestinationResourceGroup>" `
    -ResourceId <myResourceId,myResourceId,myResourceId>
```


지정된 리소스를 이동할지 확인하는 메시지가 표시되면 **Y**를 입력하여 확인합니다.

## <a name="next-steps"></a>다음 단계
리소스 그룹 및 구독 간의 다양한 유형의 리소스를 이동할 수 있습니다. 자세한 내용은 [새 리소스 그룹 또는 구독으로 리소스 이동](../../resource-group-move-resources.md)을 참조하세요.    

