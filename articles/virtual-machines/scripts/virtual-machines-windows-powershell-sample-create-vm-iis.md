---
title: Azure PowerShell 스크립트 샘플 - IIS
description: Azure PowerShell 스크립트 샘플 - IIS
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 12/12/2017
ms.author: cynthn
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 63789f79dee2e7a7ed95608631d24069c6111ac8
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89077456"
---
# <a name="create-an-iis-vm-with-powershell"></a>PowerShell을 사용하여 IIS VM 만들기

이 스크립트는 Windows Server 2016을 실행하는 Azure Virtual Machine을 만들고 Azure Virtual Machine 사용자 지정 스크립트 확장을 사용하여 IIS를 설치합니다. 스크립트를 실행하면 가상 머신의 공용 IP 주소에서 기본 IIS 웹 사이트에 액세스할 수 있습니다.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 

## <a name="sample-script"></a>샘플 스크립트

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-vm-iis/create-windows-vm-iis.ps1 "Create VM IIS")]

## <a name="clean-up-deployment"></a>배포 정리

다음 명령을 실행하여 리소스 그룹, VM 및 모든 관련된 리소스를 제거할 수 있습니다.

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용하여 배포합니다. 테이블에 있는 각 항목은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | 모든 리소스가 저장되는 리소스 그룹을 만듭니다. |
| [New-AzVM](/powershell/module/az.compute/new-azvm) | 가상 머신을 만들고 네트워크 카드, 가상 네트워크, 서브넷 및 네트워크 보안 그룹에 연결합니다. 이 명령은 포트 80을 열고 관리자 자격 증명도 설정합니다. |
| [Set-AzVMExtension](/powershell/module/az.compute/set-azvmextension) | 가상 머신에 VM 확장을 추가합니다. 이 샘플에서 사용자 지정 스크립트 확장은 IIS를 설치하는 데 사용됩니다. |
|[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | 리소스 그룹 및 포함된 모든 리소스를 제거합니다. |

## <a name="next-steps"></a>다음 단계

Azure PowerShell 모듈에 대한 자세한 내용은 [Azure PowerShell 설명서](/powershell/azure/)를 참조하세요.

추가 가상 머신 PowerShell 스크립트 샘플은 [Azure Windows VM 설명서](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)에서 확인할 수 있습니다.
