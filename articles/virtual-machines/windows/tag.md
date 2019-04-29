---
title: Azure에서 Windows VM 리소스에 태그 지정 | Microsoft Docs
description: 리소스 관리자 배포 모델을 사용하여 Azure에서 만든 Windows 가상 컴퓨터에 태그를 지정하는 방법을 알아봅니다.
services: virtual-machines-windows
documentationcenter: ''
author: mmccrory
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: 56d17f45-e4a7-4d84-8022-b40334ae49d2
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/05/2016
ms.author: memccror
ms.openlocfilehash: eef4681626c5e0aa0c5d8a67dbd0d19bcfd7121e
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62108320"
---
# <a name="how-to-tag-a-windows-virtual-machine-in-azure"></a>Azure에서 Windows 가상 머신에 태그를 지정하는 방법
이 문서에서는 리소스 관리자 배포 모델을 통해 Azure의 Windows 가상 머신에 태그를 지정하는 다양한 방법에 대해 설명합니다. 태그는 리소스 또는 리소스 그룹에 직접 배치할 수 있는 사용자 정의 키/값 쌍입니다. Azure는 현재 리소스 및 리소스 그룹당 최대 15개의 태그를 지원합니다. 태그를 만들 때 리소스에 배치하거나 기존 리소스에 추가할 수 있습니다. 태그는 리소스 관리자 배포 모델을 통해 만든 리소스에 대해서만 지원됩니다. Linux 가상 머신에 태그를 지정하려는 경우 [Azure에서 Linux 가상 머신에 태그를 지정하는 방법](../linux/tag.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)을 참조하세요.

[!INCLUDE [virtual-machines-common-tag](../../../includes/virtual-machines-common-tag.md)]

## <a name="tagging-with-powershell"></a>PowerShell을 사용한 태그 지정
PowerShell을 통해 태그를 만들고 추가 및 삭제하려면 먼저 [Azure Resource Manager를 사용하여 PowerShell 환경][PowerShell environment with Azure Resource Manager]을 설정해야 합니다. 설정을 완료한 후 Compute, Network 및 Storage 리소스를 만들 때 또는 PowerShell을 통해 리소스를 만든 후에 태그를 배치할 수 있습니다. 이 문서에서는 Virtual Machines에 배치된 태그 보기/편집을 중점적으로 살펴봅니다.

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

먼저 `Get-AzVM` cmdlet을 통해 Virtual Machine으로 이동합니다.

        PS C:\> Get-AzVM -ResourceGroupName "MyResourceGroup" -Name "MyTestVM"

Virtual Machine에 이미 태그가 포함되어 있는 경우 해당 리소스의 모든 태그가 표시됩니다.

        Tags : {
                "Application": "MyApp1",
                "Created By": "MyName",
                "Department": "MyDepartment",
                "Environment": "Production"
               }

PowerShell을 통해 태그를 추가하려는 경우 `Set-AzResource` 명령을 사용할 수 있습니다. PowerShell을 통해 태그를 업데이트하는 경우 태그가 전체적으로 업데이트됩니다. 따라서 이미 태그가 있는 리소스에 하나의 태그를 추가하는 경우 리소스에 배치하려는 모든 태그를 포함해야 합니다. 다음은 PowerShell Cmdlet을 통해 리소스에 태그를 더 추가하는 방법의 예입니다.

첫 번째 cmdlet은 `Get-AzResource` 및 `Tags` 속성을 사용하여 *MyTestVM*에 배치된 모든 태그를 *$tags* 변수로 설정합니다.

        PS C:\> $tags = (Get-AzResource -ResourceGroupName MyResourceGroup -Name MyTestVM).Tags

두 번째 명령은 지정된 변수에 대한 태그를 표시합니다.

```
    PS C:\> $tags
    
    Key           Value
    ----          -----
    Department    MyDepartment
    Application   MyApp1
    Created By    MyName
    Environment   Production
```

세 번째 명령은 *$tags* 변수에 태그를 더 추가합니다.  **+=** 을 사용하여 *$tags* 목록에 새로운 키/값 쌍을 추가합니다.

        PS C:\> $tags += @{Location="MyLocation"}

네 번째 명령은 *$tags* 변수에 정의된 모든 태그를 지정된 리소스로 설정합니다. 이 경우 MyTestVM입니다.

        PS C:\> Set-AzResource -ResourceGroupName MyResourceGroup -Name MyTestVM -ResourceType "Microsoft.Compute/VirtualMachines" -Tag $tags

다섯 번째 명령은 리소스의 모든 태그를 표시합니다. 여기서 볼 수 있듯이 *위치*가 이제 값이 *MyLocation*인 태그로 정의되었습니다.

```
    PS C:\> (Get-AzResource -ResourceGroupName MyResourceGroup -Name MyTestVM).Tags

    Key           Value
    ----          -----
    Department    MyDepartment
    Application   MyApp1
    Created By    MyName
    Environment   Production
    Location      MyLocation
```

PowerShell을 통해 태그를 지정하는 방법에 대한 자세한 내용은 [Azure 리소스 Cmdlet][Azure Resource Cmdlets]을 참조하세요.

[!INCLUDE [virtual-machines-common-tag-usage](../../../includes/virtual-machines-common-tag-usage.md)]

## <a name="next-steps"></a>다음 단계
* Azure 리소스에 태그를 지정하는 방법에 대한 자세한 내용은 [Azure Resource Manager 개요][Azure Resource Manager Overview] 및 [태그를 사용하여 Azure 리소스 구성][Using Tags to organize your Azure Resources]을 참조하세요.
* 태그로 Azure 리소스의 사용을 관리하는 방법은 [Azure 청구서 이해][Understanding your Azure Bill] 및 [Microsoft Azure 리소스 소비에 대한 정보 얻기][Gain insights into your Microsoft Azure resource consumption]를 참조하세요.

[PowerShell environment with Azure Resource Manager]: ../../azure-resource-manager/manage-resources-powershell.md
[Azure Resource Cmdlets]: https://docs.microsoft.com/powershell/module/az.resources/
[Azure Resource Manager Overview]: ../../azure-resource-manager/resource-group-overview.md
[Using Tags to organize your Azure Resources]: ../../azure-resource-manager/resource-group-using-tags.md
[Understanding your Azure Bill]: ../../billing/billing-understand-your-bill.md
[Gain insights into your Microsoft Azure resource consumption]: ../../billing/billing-usage-rate-card-overview.md
