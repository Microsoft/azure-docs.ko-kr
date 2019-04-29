---
title: Azure에서 VHD의 스냅숏 만들기 | Microsoft Docs
description: 백업 또는 문제 해결을 위해 사용할 Azure VM의 복사본을 만드는 방법을 알아봅니다.
documentationcenter: ''
author: roygara
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: 15eb778e-fc07-45ef-bdc8-9090193a6d20
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/08/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: b3b9095cd7ee3fa12523b14f59cc06820b9e4382
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63763940"
---
# <a name="create-a-snapshot"></a>스냅숏 만들기

스냅숏은 VHD(가상 하드 드라이브)의 전체 읽기 전용 복사본입니다. 백업으로 사용 또는 VM(가상 머신) 문제 해결을 위해 OS 또는 데이터 디스크 VHD의 스냅숏을 만들 수 있습니다.

스냅숏을 사용하여 새 VM을 만들려는 경우 진행 중인 모든 프로세스를 제거하려면 스냅숏을 만들기 전에 완전히 VM을 종료하는 것이 좋습니다.

## <a name="use-the-azure-portal"></a>Azure Portal 사용 

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 왼쪽 메뉴에서 **리소스 만들기**를 선택한 후 **스냅숏**을 검색하여 선택합니다.
3. **스냅숏** 창에서 만들기 **만들기**를 선택합니다. **스냅숏 만들기** 창이 나타납니다.
4. 스냅숏의 **이름**을 입력합니다.
5. 기존 [리소스 그룹](../../azure-resource-manager/resource-group-overview.md#resource-groups)을 선택하거나 새 리소스 그룹의 이름을 입력합니다. 
6. Azure 데이터 센터 **위치**를 선택합니다.  
7. **원본 디스크**에서 스냅숏을 만들 관리 디스크를 선택합니다.
8. 스냅숏 저장에 사용할 **계정 유형**을 선택합니다. 스냅숏이 고성능 디스크에 저장되어야 하는 경우가 아니면 **Standard_HDD**를 선택합니다.
9. **만들기**를 선택합니다.

## <a name="use-powershell"></a>PowerShell 사용

다음 단계에서는 [New-AzSnapshot](https://docs.microsoft.com/powershell/module/az.compute/new-azsnapshot) cmdlet을 사용하여 VHD 디스크를 복사하고, 스냅숏 구성을 만들고, 디스크의 스냅숏을 만드는 방법을 보여 줍니다. 

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

1. 일부 매개 변수를 설정합니다. 

   ```azurepowershell-interactive
   $resourceGroupName = 'myResourceGroup' 
   $location = 'eastus' 
   $vmName = 'myVM'
   $snapshotName = 'mySnapshot'  
   ```

2. VM을 가져옵니다.

   ```azurepowershell-interactive
   $vm = get-azvm `
   -ResourceGroupName $resourceGroupName 
   -Name $vmName
   ```

3. 스냅숏 구성을 만듭니다. 이 예제에서 스냅숏은 OS 디스크의 스냅숏입니다.

   ```azurepowershell-interactive
   $snapshot =  New-AzSnapshotConfig 
   -SourceUri $vm.StorageProfile.OsDisk.ManagedDisk.Id 
   -Location $location 
   -CreateOption copy
   ```
   
   > [!NOTE]
   > 스냅숏을 영역 중복 저장소에 저장하려는 경우 [가용성 영역](../../availability-zones/az-overview.md)을 지원하고 `-SkuName Standard_ZRS` 매개 변수를 포함하는 지역에 만듭니다.   
   
4. 스냅숏을 만듭니다.

   ```azurepowershell-interactive
   New-AzSnapshot 
   -Snapshot $snapshot 
   -SnapshotName $snapshotName 
   -ResourceGroupName $resourceGroupName 
   ```


## <a name="next-steps"></a>다음 단계

스냅숏에서 관리되는 디스크를 만들고 새 관리되는 디스크를 OS 디스크로 연결하여 스냅숏에서 가상 머신을 만듭니다. 자세한 내용은 [PowerShell을 사용하여 스냅숏에서 VM 만들기](./../scripts/virtual-machines-windows-powershell-sample-create-vm-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json)에 있는 샘플을 참조하세요.
