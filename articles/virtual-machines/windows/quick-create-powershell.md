---
title: 빠른 시작 - Azure PowerShell을 사용하여 Windows VM 만들기
description: 이 빠른 시작에서는 Azure PowerShell을 사용하여 Windows 가상 머신을 만드는 방법을 배웁니다.
author: cynthn
ms.service: virtual-machines-windows
ms.topic: quickstart
ms.workload: infrastructure
ms.date: 07/02/2019
ms.author: cynthn
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: d23bc83d9cfd60585a63df0acbfb2b4076cd83f4
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89077327"
---
# <a name="quickstart-create-a-windows-virtual-machine-in-azure-with-powershell"></a>빠른 시작: PowerShell을 사용하여 Azure에서 Windows 가상 머신 만들기

PowerShell 명령줄 또는 스크립트에서 Azure 리소스를 만들고 관리하는 데 Azure PowerShell 모듈이 사용됩니다. 이 빠른 시작에서는 Azure PowerShell 모듈을 사용하여 Windows Server 2016을 실행하는 Azure에서 VM(가상 머신)을 배포하는 방법을 보여줍니다. 또한 VM에 RDP를 실행하고 IIS 웹 서버를 설치하여 실행 중인 VM을 표시할 수 있습니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell 시작

Azure Cloud Shell은 이 항목의 단계를 실행하는 데 무료로 사용할 수 있는 대화형 셸입니다. 공용 Azure 도구가 사전 설치되어 계정에서 사용하도록 구성되어 있습니다. 

Cloud Shell을 열려면 코드 블록의 오른쪽 위 모서리에 있는 **사용해 보세요**를 선택하기만 하면 됩니다. 또한 [https://shell.azure.com/powershell](https://shell.azure.com/powershell)로 이동하여 별도의 브라우저 탭에서 Cloud Shell을 시작할 수도 있습니다. **복사**를 선택하여 코드 블록을 복사하여 Cloud Shell에 붙여넣고, Enter 키를 눌러 실행합니다.


## <a name="create-resource-group"></a>리소스 그룹 만들기

[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)을 사용하여 Azure 리소스 그룹을 만듭니다. 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다.

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-virtual-machine"></a>가상 머신 만들기

[New-AzVM](/powershell/module/az.compute/new-azvm)으로 VM을 만듭니다. 각 리소스의 이름을 제공하고 아직 없는 경우 `New-AzVM` cmdlet에서 만듭니다.

메시지가 표시되면 VM에 대한 로그인 자격 증명으로 사용할 사용자 이름과 암호를 입력합니다.

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVM" `
    -Location "East US" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -OpenPorts 80,3389
```

## <a name="connect-to-virtual-machine"></a>가상 머신에 연결

배포가 완료되면 VM에 RDP를 수행합니다. 작업에서 VM을 확인하기 위해 IIS 웹 서버가 설치됩니다.

VM의 공용 IP 주소를 보려면 [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) cmdlet을 사용합니다.

```powershell
Get-AzPublicIpAddress -ResourceGroupName "myResourceGroup" | Select "IpAddress"
```

다음 명령을 사용하여 로컬 컴퓨터에서 원격 데스크톱 세션을 만듭니다. IP 주소를 VM의 공용 IP 주소로 바꿉니다. 

```powershell
mstsc /v:publicIpAddress
```

**Windows 보안** 창에서 **기타 선택 사항**을 선택한 다음, **다른 계정 사용**을 선택합니다. 사용자 이름으로 **localhost**\\*username*을 입력하고, 가상 머신에 대해 만든 암호를 입력한 다음, **확인**을 클릭합니다.

로그인 프로세스 중에 인증서 경고가 나타날 수 있습니다. **예** 또는 **계속**을 클릭하여 연결을 만듭니다.

## <a name="install-web-server"></a>웹 서버 설치

실제로 작동하는 VM을 보려면 IIS 웹 서버를 설치합니다. VM에서 PowerShell 프롬프트를 열고 다음 명령을 실행합니다.

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

여기까지 마쳤으면 VM에 대한 RDP 연결을 닫습니다.

## <a name="view-the-web-server-in-action"></a>실제로 작동 중인 웹 서버 보기

IIS를 설치하고 현재 포트 80이 인터넷에서 VM에 열려 있으면 사용자가 선택한 웹 브라우저를 사용하여 기본 IIS 시작 페이지를 봅니다. 이전 단계에서 가져온 VM의 공용 IP 주소를 사용합니다. 다음 예제는 기본 IIS 웹 사이트를 보여줍니다.

![IIS 기본 사이트](./media/quick-create-powershell/default-iis-website.png)

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) cmdlet을 사용하여 리소스 그룹, VM 및 모든 관련 리소스를 제거할 수 있습니다.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 간단한 가상 머신을 배포하고, 웹 트래픽에 대한 네트워크 포트를 열고, 기본 웹 서버를 설치했습니다. Azure 가상 머신에 대한 자세한 내용을 알아보려면 Windows VM의 자습서를 계속 진행합니다.

> [!div class="nextstepaction"]
> [Azure Windows 가상 머신 자습서](./tutorial-manage-vm.md)
