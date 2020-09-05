---
title: 'Azure Express 경로: 회로 수정: PowerShell: 클래식'
description: 이 문서에서는 ExpressRoute 클래식 배포 모델 회로 상태를 확인하고, 업데이트 또는 삭제하고, 프로비전을 해제하는 단계를 설명합니다.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: how-to
ms.date: 11/05/2019
ms.author: cherylmc
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 801032e3df9dcb778fa4e591ef37eabc317e997a
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89073338"
---
# <a name="modify-an-expressroute-circuit-using-powershell-classic"></a>PowerShell을 사용하여 ExpressRoute 회로 수정(클래식)

> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-circuit-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-circuit-arm.md)
> * [Azure CLI](howto-circuit-cli.md)
> * [Azure Resource Manager 템플릿](expressroute-howto-circuit-resource-manager-template.md)
> * [비디오 - Azure Portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (클래식)](expressroute-howto-circuit-classic.md)
>

이 문서에서는 ExpressRoute 클래식 배포 모델 회로 상태를 확인하고, 업데이트 또는 삭제하고, 프로비전을 해제하는 단계를 설명합니다. 이 문서는 클래식 배포 모델에 적용됩니다.

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**Azure 배포 모델 정보**

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="before-you-begin"></a>시작하기 전에

[!INCLUDE [classic powershell install instructions](../../includes/expressroute-poweshell-classic-install-include.md)]

## <a name="get-the-status-of-a-circuit"></a>회로 상태 가져오기

`Get-AzureCircuit` cmdlet을 사용하여 이 정보를 언제든지 검색할 수 있습니다. 매개 변수 없이 호출을 수행하면 모든 회로가 표시됩니다.

```powershell
Get-AzureDedicatedCircuit

Bandwidth                        : 200
CircuitName                      : MyTestCircuit
Location                         : Silicon Valley
ServiceKey                       : *********************************
ServiceProviderName              : equinix
ServiceProviderProvisioningState : Provisioned
Sku                              : Standard
Status                           : Enabled

Bandwidth                        : 1000
CircuitName                      : MyAsiaCircuit
Location                         : Singapore
ServiceKey                       : #################################
ServiceProviderName              : equinix
ServiceProviderProvisioningState : Provisioned
Sku                              : Standard
Status                           : Enabled
```

서비스 키를 매개 변수 형태로 호출에 전달하면 특정 ExpressRoute 회로에 대한 정보를 가져올 수 있습니다.

```powershell
Get-AzureDedicatedCircuit -ServiceKey "*********************************"

Bandwidth                        : 200
CircuitName                      : MyTestCircuit
Location                         : Silicon Valley
ServiceKey                       : *********************************
ServiceProviderName              : equinix
ServiceProviderProvisioningState : Provisioned
Sku                              : Standard
Status                           : Enabled
```

다음 예제를 실행하여 모든 매개 변수에 대한 자세한 설명을 볼 수 있습니다.

```powershell
get-help get-azurededicatedcircuit -detailed
```

## <a name="modify-a-circuit"></a>회로 수정

연결에 미치는 영향 없이 ExpressRoute 회로의 특정 속성을 수정할 수 있습니다.

가동 중지 시간 없이 다음 작업을 수행할 수 있습니다.

* ExpressRoute 회로에 대해 ExpressRoute 프리미엄 추가 기능을 사용하거나 사용하지 않을 수 있습니다.
* 포트에 사용 가능한 용량이 있는 경우 ExpressRoute 회로의 대역폭을 증가시킵니다. 회로 대역폭 다운그레이드는 지원되지 않습니다.
* 요금제를 데이터 요금에서 무제한 데이터 요금으로 변경합니다. 요금제를 무제한 데이터 요금에서 데이터 요금으로 변경하는 것은 지원되지 않습니다.
* *Allow Classic Operations*을 활성화하거나 비활성화할 수 있습니다.

제한 및 제한 사항에 대한 자세한 내용은 [ExpressRoute FAQ](expressroute-faqs.md) 를 참조하세요.

### <a name="enable-the-expressroute-premium-add-on"></a>ExpressRoute Premium 추가 기능 활성화

다음 PowerShell cmdlet을 사용하여 기존 회로에 대해 ExpressRoute 프리미엄 추가 기능을 활성화할 수 있습니다.

```powershell
Set-AzureDedicatedCircuitProperties -ServiceKey "*********************************" -Sku Premium

Bandwidth                        : 1000
CircuitName                      : TestCircuit
Location                         : Silicon Valley
ServiceKey                       : *********************************
ServiceProviderName              : equinix
ServiceProviderProvisioningState : Provisioned
Sku                              : Premium
Status                           : Enabled
```

이제 ExpressRoute 프리미엄 추가 기능을 사용할 수 있게 됩니다. 명령이 성공적으로 실행되는 즉시 프리미엄 추가 기능에 대한 대금 청구가 시작됩니다.

### <a name="disable-the-expressroute-premium-add-on"></a>ExpressRoute 프리미엄 추가 기능 비활성화

> [!IMPORTANT]
> 표준 회로에 허용된 것보다 많은 리소스를 사용할 경우 이 작업이 실패할 수 있습니다.
>
>

#### <a name="considerations"></a>고려 사항

* 프리미엄을 표준으로 다운그레이드하기 전에 회로에 연결된 가상 네트워크 수가 10개 미만인지 확인합니다. 그렇게 하지 않으면 업데이트 요청이 실패하고, 프리미엄 요금이 청구됩니다.
* 다른 지리적 위치의 모든 가상 네트워크를 연결 해제해야 합니다. 그렇지 않으면 업데이트 요청이 실패하고, 프리미엄 요금이 청구됩니다.
* 프라이빗 피어링을 위해서는 경로 테이블의 경로가 4000개 미만이어야 합니다. 경로 테이블 크기가 4000개 경로 이상이면 BGP 세션이 폐기되고 게시된 프리픽스 수가 4000개 미만이 될 때까지 다시 활성화되지 않습니다.

#### <a name="to-disable-the-premium-add-on"></a>프리미엄 추가 기능을 비활성화하려면

다음 PowerShell cmdlet을 사용하여 기존 회로에 대해 ExpressRoute 프리미엄 추가 기능을 사용하지 않도록 설정할 수 있습니다.

```powershell

Set-AzureDedicatedCircuitProperties -ServiceKey "*********************************" -Sku Standard

Bandwidth                        : 1000
CircuitName                      : TestCircuit
Location                         : Silicon Valley
ServiceKey                       : *********************************
ServiceProviderName              : equinix
ServiceProviderProvisioningState : Provisioned
Sku                              : Standard
Status                           : Enabled
```

### <a name="update-the-expressroute-circuit-bandwidth"></a>ExpressRoute 회로 대역폭 업데이트

공급자에 대해 지원되는 대역폭 옵션은 [ExpressRoute FAQ](expressroute-faqs.md)를 확인하세요. 물리적 포트(회로가 생성되어 있는)에 허용되는 한 기존 회로보다 크다면 어떤 크기든 선택할 수 있습니다.

> [!IMPORTANT]
> 기존 포트에 적절한 용량이 없는 경우 ExpressRoute 회로를 다시 만들어야 할 수 있습니다. 해당 위치에서 사용 가능한 추가 용량이 없는 경우 해당 회로를 업그레이드할 수 없습니다.
>
> 그러나 중단 없이 ExpressRoute 회로의 대역폭을 줄일 수는 없습니다. 대역폭을 다운그레이드하려면 ExpressRoute 회로의 프로비전을 해제하고 새 ExpressRoute 회로를 다시 프로비전해야 합니다.
>
>

#### <a name="resize-a-circuit"></a>회로 크기 조정

필요한 크기를 선택한 후에, 다음 명령을 사용하여 회로 크기를 조정할 수 있습니다.

```powershell
Set-AzureDedicatedCircuitProperties -ServiceKey ********************************* -Bandwidth 1000

Bandwidth                        : 1000
CircuitName                      : TestCircuit
Location                         : Silicon Valley
ServiceKey                       : *********************************
ServiceProviderName              : equinix
ServiceProviderProvisioningState : Provisioned
Sku                              : Standard
Status                           : Enabled
```

Microsoft 쪽에서 회로의 크기를 확장하면 연결 공급자에게 연락하여 변경 사항에 맞게 구성을 업데이트해야 합니다. 이 지점에서 업데이트된 대역폭 옵션에 청구를 시작합니다.

회로 대역폭을 증가시킬 때 다음과 같은 오류가 표시되면 기존 회로가 생성된 물리적 포트에 남아있는 대역폭이 충분하지 않다는 의미입니다. 이 회로를 삭제하고 필요한 크기의 회로를 새로 만들어야 합니다.

```powershell
Set-AzureDedicatedCircuitProperties : InvalidOperation : Insufficient bandwidth available to perform this circuit
update operation
At line:1 char:1
+ Set-AzureDedicatedCircuitProperties -ServiceKey ********************* ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
  + CategoryInfo          : CloseError: (:) [Set-AzureDedicatedCircuitProperties], CloudException
  + FullyQualifiedErrorId : Microsoft.WindowsAzure.Commands.ExpressRoute.SetAzureDedicatedCircuitPropertiesCommand
```

## <a name="deprovision-and-delete-a-circuit"></a>회로 프로비전 해제 및 삭제

### <a name="considerations"></a>고려 사항

* 이 작업이 성공하려면 모든 가상 네트워크를 ExpressRoute 회로에서 연결 해제해야 합니다. 이 작업이 실패할 경우 회로에 연결된 가상 네트워크가 있는지 확인하십시오.
* Express 경로 회로 서비스 공급자 프로 비전 상태가 **프로 비전** 또는 **프로 비전** 된 경우 서비스 공급자와 협력 하 여 회로를 프로 비전 해제 해야 합니다. 서비스 공급자가 회로의 프로비전을 해제한 다음 통지를 보낼 때까지 리소스가 계속 예약되며 요금이 청구됩니다.
* 서비스 공급자가 회로 프로비전을 해제하여 서비스 공급자 프로비전 상태가 **프로비전되지 않음**이 되면 회로를 삭제할 수 있습니다. 그러면 회로에 대한 요금 청구가 중지됩니다.

#### <a name="delete-a-circuit"></a>회로 삭제

다음 명령을 실행하여 ExpressRoute 회로를 삭제할 수 있습니다.

```powershell
Remove-AzureDedicatedCircuit -ServiceKey "*********************************"
```
