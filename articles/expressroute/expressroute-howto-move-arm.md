---
title: 'Azure Express 경로: 클래식 회로를 리소스 관리자 이동'
description: 이 페이지는 PowerShell을 사용하여 클래식 회로를 Resource Manager 배포 모델로 이동하는 방법을 설명합니다.
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: how-to
ms.date: 02/25/2019
ms.author: charwen
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 1e1096fcb0f1175df67f47d4ca6c8caf4dc1a0e4
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89079696"
---
# <a name="move-expressroute-circuits-from-classic-to-resource-manager-deployment-model-using-powershell"></a>PowerShell을 사용하여 클래식에서 Resource Manager 배포 모델로 ExpressRoute 회로 이동

클래식 및 Resource Manager 배포 모델 모두에서 사용할 수 있도록 ExpressRoute 회로를 사용하려면 회로를 Resource Manager 배포 모델로 이동해야 합니다. 다음 섹션에서는 PowerShell을 사용하여 회로를 이동할 수 있습니다.

## <a name="before-you-begin"></a>시작하기 전에

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

* 클래식 및 Az Azure PowerShell 모듈을 모두 컴퓨터에 로컬로 설치 했는지 확인 합니다. 자세한 내용은 [Azure PowerShell을 설치 및 구성하는 방법](/powershell/azure/)을 참조하세요.
* 구성을 시작하기 전에 [필수 조건](expressroute-prerequisites.md), [라우팅 요구 사항](expressroute-routing.md) 및 [워크플로](expressroute-workflows.md)를 검토했는지 확인합니다.
* [클래식에서 Resource Manager로 ExpressRoute 회로 이동](expressroute-move.md)에서 제공되는 정보를 검토합니다. 제한 및 제한 사항을 완전히 이해해야 합니다.
* 클래식 배포 모델에서 회로가 완벽하게 작동되는지 확인합니다.
* 리소스 관리자 배포 모델에 만든 리소스 그룹이 있는지 확인합니다.

## <a name="move-an-expressroute-circuit"></a>ExpressRoute 회로 이동

### <a name="step-1-gather-circuit-details-from-the-classic-deployment-model"></a>1단계: 클래식 배포 모델에서 회로 세부 정보 수집

Azure 클래식 환경에 로그인하고 서비스 키를 수집합니다.

1. Azure 계정에 로그인합니다.

   ```powershell
   Add-AzureAccount
   ```

2.  적절한 Azure 구독을 선택합니다.

   ```powershell
   Select-AzureSubscription "<Enter Subscription Name here>"
   ```

3. Azure 및 ExpressRoute에 대한 PowerShell 모듈을 가져옵니다.

   ```powershell
   Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\Azure\Azure.psd1'
   Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\ExpressRoute\ExpressRoute.psd1'
   ```

4. 아래 cmdlet을 사용하여 모든 ExpressRoute 회로에 대한 서비스 키를 가져옵니다. 키를 가져온 후에 Resource Manager 배포 모델로 이동하려는 회로의 **서비스 키**를 복사합니다.

   ```powershell
   Get-AzureDedicatedCircuit
   ```

### <a name="step-2-sign-in-and-create-a-resource-group"></a>2단계: 로그인하여 리소스 그룹 만들기

Resource Manager 환경에 로그인하고 새 리소스 그룹을 만듭니다.

1. Azure Resource Manager 환경으로 로그인합니다.

   ```powershell
   Connect-AzAccount
   ```

2.  적절한 Azure 구독을 선택합니다.

   ```powershell
   Get-AzSubscription -SubscriptionName "<Enter Subscription Name here>" | Select-AzSubscription
   ```

3. 리소스 그룹이 아직 없는 경우 아래 코드 조각을 수정하여 새 리소스 그룹을 만듭니다.

   ```powershell
   New-AzResourceGroup -Name "DemoRG" -Location "West US"
   ```

### <a name="step-3-move-the-expressroute-circuit-to-the-resource-manager-deployment-model"></a>3단계: Resource Manager 배포 모델로 ExpressRoute 회로 이동

이제 클래식 배포 모델에서 Resource Manager 배포 모델로 ExpressRoute 회로를 이동할 준비가 되었습니다. 계속 진행하기 전에 [클래식에서 Resource Manager 배포 모델로 ExpressRoute 회로 이동](expressroute-move.md)에서 제공되는 정보를 검토합니다.

회로를 이동하려면 다음 코드 조각을 수정한 후 실행합니다.

```powershell
Move-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "DemoRG" -Location "West US" -ServiceKey "<Service-key>"
```

클래식 모드에서 ExpressRoute 회로에는 지역에 연결되는 개념이 없습니다. 그렇지만 Resource Manager에서 모든 리소스를 Azure 지역에 매핑해야 합니다. AzExpressRouteCircuit cmdlet에 지정 된 지역은 기술적으로 모든 지역이 될 수 있습니다. 조직 용도로 피어링 위치를 가장 잘 나타내는 지역을 선택할 수 있습니다.

> [!NOTE]
> 이동이 완료되면 이전 cmdlet에 나열된 새 이름을 사용하여 리소스 주소를 지정합니다. 회로는 기본적으로 이름이 바뀝니다.
> 

## <a name="modify-circuit-access"></a>회로 액세스 수정

### <a name="to-enable-expressroute-circuit-access-for-both-deployment-models"></a>두 배포 모델에 대한 ExpressRoute 회로 액세스를 사용하도록 설정하려면

클래식 ExpressRoute 회로를 Resource Manager 배포 모델로 이동한 후에 두 배포 모델에 모두 액세스할 수 있도록 설정할 수 있습니다. 두 배포 모델에 모두 액세스하려면 다음 cmdlet을 실행합니다.

1. 회로 세부 정보를 가져옵니다.

   ```powershell
   $ckt = Get-AzExpressRouteCircuit -Name "DemoCkt" -ResourceGroupName "DemoRG"
   ```

2. "Allow Classic Operations"을 TRUE로 설정합니다.

   ```powershell
   $ckt.AllowClassicOperations = $true
   ```

3. 회로를 업데이트합니다. 이 작업이 성공적으로 완료되면 클래식 배포 모델의 회로를 볼 수 있습니다.

   ```powershell
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

4. ExpressRoute 회로의 세부 정보를 가져오려면 다음 cmdlet을 실행합니다. 나열된 서비스 키를 볼 수 있어야 합니다.

   ```powershell
   get-azurededicatedcircuit
   ```

5. 이제 클래식 VNet에 대한 클래식 배포 모델 명령 및 Resource Manager VNet에 대한 Resource Manager 명령을 사용하여 ExpressRoute 회로에 대한 링크를 관리할 수 있습니다. 다음 문서에서는 ExpressRoute 회로에 대한 링크를 관리하는 방법을 안내합니다.

    * [Resource Manager 배포 모델에서 가상 네트워크를 ExpressRoute 회로에 연결](expressroute-howto-linkvnet-arm.md)
    * [클래식 배포 모델에서 가상 네트워크를 ExpressRoute 회로에 연결](expressroute-howto-linkvnet-classic.md)

### <a name="to-disable-expressroute-circuit-access-to-the-classic-deployment-model"></a>클래식 배포 모델에 대한 ExpressRoute 회로 액세스를 사용하지 않도록 설정하려면

클래식 배포 모델에 대한 액세스를 사용하지 않도록 설정하려면 다음 cmdlet을 실행합니다.

1. ExpressRoute 회로의 세부 정보를 가져옵니다.

   ```powershell
   $ckt = Get-AzExpressRouteCircuit -Name "DemoCkt" -ResourceGroupName "DemoRG"
   ```

2. "Allow Classic Operations"을 FALSE로 설정합니다.

   ```powershell
   $ckt.AllowClassicOperations = $false
   ```

3. 회로를 업데이트합니다. 이 작업이 성공적으로 완료되면 클래식 배포 모델의 회로를 볼 수 없습니다.

   ```powershell
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

## <a name="next-steps"></a>다음 단계

* [ExpressRoute 회로의 라우팅 만들기 및 수정](expressroute-howto-routing-arm.md)
* [가상 네트워크를 ExpressRoute 회로에 연결](expressroute-howto-linkvnet-arm.md)
