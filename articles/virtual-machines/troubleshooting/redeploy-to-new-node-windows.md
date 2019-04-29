---
title: Azure에서 Windows 가상 머신 다시 배포 | Microsoft Docs
description: RDP 연결 문제를 완화하도록 Azure에서 Windows 가상 머신을 다시 배포하는 방법입니다.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: genlin
manager: jeconnoc
tags: azure-resource-manager,top-support-issue
ms.assetid: 0ee456ee-4595-4a14-8916-72c9110fc8bd
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: b71ca93ac3e3e6c77c5f87b4859a2e3e0e1040d9
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62104064"
---
# <a name="redeploy-windows-virtual-machine-to-new-azure-node"></a>새 Azure 노드로 Windows 가상 머신 다시 배포
RDP(원격 데스크톱) 연결 문제 또는 Windows 기반 Azure VM(가상 머신)에 대한 애플리케이션 액세스 문제를 해결하는 데 어려움이 있는 경우 VM을 다시 배포하는 것이 도움이 될 수 있습니다. VM을 다시 배포하는 경우 Azure에서는 VM을 종료하고 VM을 Azure 인프라 내의 새 노드로 이동한 다음, 전원을 다시 켜고 모든 구성 옵션 및 관련 리소스를 그대로 유지합니다. 이 문서에서는 Azure PowerShell 또는 Azure 포털을 사용하여 VM을 다시 배포하는 방법을 보여 줍니다.

> [!NOTE]
> VM을 다시 배포한 후에 임시 디스크가 손실되고 가상 네트워크 인터페이스와 연결된 동적 IP 주소가 업데이트됩니다. 


## <a name="using-azure-powershell"></a>Azure PowerShell 사용
최신 Azure PowerShell 1.x를 컴퓨터에 설치했는지 확인합니다. 자세한 내용은 [Azure PowerShell을 설치 및 구성하는 방법](/powershell/azure/overview)을 참조하세요.

다음 예제에서는 리소스 그룹 `myResourceGroup`의 VM `myVM`을 배포합니다.

```powershell
Set-AzVM -Redeploy -ResourceGroupName "myResourceGroup" -Name "myVM"
```

[!INCLUDE [virtual-machines-common-redeploy-to-new-node](../../../includes/virtual-machines-common-redeploy-to-new-node.md)]

## <a name="next-steps"></a>다음 단계
VM에 연결하는 데 문제가 있는 경우 [RDP 연결 문제 해결](troubleshoot-rdp-connection.md) 또는 [자세한 RDP 문제 해결 단계](detailed-troubleshoot-rdp.md)에서 특정 도움말을 찾을 수 있습니다. VM에서 실행 중인 애플리케이션에 액세스할 수 없는 경우 [애플리케이션 문제 해결](../windows/troubleshoot-app-connection.md)을 읽어볼 수도 있습니다.

