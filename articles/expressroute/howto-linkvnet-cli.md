---
title: '가상 네트워크를 ExpressRoute 회로에 연결: CLI: Azure | Microsoft Docs'
description: 이 문서는 Resource Manager 배포 모델 및 CLI를 사용하여 VNet(가상 네트워크)을 ExpressRoute 회로에 연결하는 방법을 보여줍니다.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: anzaman,cherylmc
ms.custom: seodec18
ms.openlocfilehash: 5ddcfe14873d13384b043f7a977dc4f069dbe8dd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60883082"
---
# <a name="connect-a-virtual-network-to-an-expressroute-circuit-using-cli"></a>CLI를 사용하여 가상 네트워크를 ExpressRoute 회로에 연결

이 문서는 CLI를 사용하여 VNet(가상 네트워크)을 Azure ExpressRoute 회로에 연결하는 데 도움이 됩니다. Azure CLI를 사용하여 연결하려면 Resource Manager 배포 모델을 사용하여 가상 네트워크를 만들어야 합니다. 해당 항목은 같은 구독에 있을 수도 있고 다른 구독의 일부일 수도 있습니다. VNet을 ExpressRoute 회로에 연결하는 다른 방법을 사용하려는 경우 다음 목록에서 문서를 선택할 수 있습니다.

> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-linkvnet-arm.md)
> * [Azure CLI](howto-linkvnet-cli.md)
> * [비디오 - Azure Portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell(클래식)](expressroute-howto-linkvnet-classic.md)
> 

## <a name="configuration-prerequisites"></a>필수 구성 요소

* 최신 버전의 CLI(명령줄 인터페이스)가 필요합니다. 자세한 내용은 [Azure CLI 설치](https://docs.microsoft.com/cli/azure/install-azure-cli)를 참조하세요.

* 구성을 시작하기 전에 [필수 조건](expressroute-prerequisites.md), [라우팅 요구 사항](expressroute-routing.md) 및 [워크플로](expressroute-workflows.md)를 검토해야 합니다.

* 활성화된 ExpressRoute 회로가 있어야 합니다. 
  * 지침을 수행하여 [ExpressRoute 회로를 만들고](howto-circuit-cli.md) 연결 공급자를 통해 회로를 사용하도록 설정합니다. 
  * 회로에 구성된 Azure 개인 피어링이 있는지 확인합니다. 라우팅 지침에 대한 문서는 [라우팅 구성](howto-routing-cli.md) 을 참조하세요. 
  * Azure 개인 피어이링 구성되어 있는지 확인합니다. 네트워크와 Microsoft 간의 BGP 피어링이 종단 간 연결을 사용하도록 작동 중이어야 합니다.
  * 가상 네트워크 및 가상 네트워크 게이트웨이를 만들어서 완전히 프로비전해야 합니다. 지침에 따라 [ExpressRoute에 대한 가상 네트워크 게이트웨이를 구성합니다](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli). `--gateway-type ExpressRoute`를 사용해야 합니다.

* 최대 10개의 가상 네트워크를 표준 ExpressRoute 회로에 연결할 수 있습니다. 표준 ExpressRoute 회로를 사용하는 경우 모든 가상 네트워크는 동일한 지역에 있어야 합니다. 

* 단일 VNet을 최대 4개의 ExpressRoute 회로에 연결할 수 있습니다. 아래 프로세스를 사용하여 각 ExpressRoute 회로에 대한 새 연결 개체를 만듭니다. ExpressRoute 회로는 동일한 구독, 서로 다른 구독 또는 두 가지가 혼합된 상태로 존재할 수 있습니다.

* ExpressRoute 프리미엄 추가 기능을 사용하도록 설정하면 ExpressRoute 회로의 지역 외부에서 가상 네트워크를 연결하거나 ExpressRoute 회로에 많은 수의 가상 네트워크를 연결할 수 있습니다. Premium 추가 기능에 대한 자세한 내용은 [FAQ](expressroute-faqs.md)를 참조하세요.

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>동일한 구독에 있는 가상 네트워크를 회로에 연결

다음 예제를 사용하여 ExpressRoute 회로에 가상 네트워크 게이트웨이를 연결할 수 있습니다. 명령을 실행하기 전에 가상 네트워크 게이트웨이가 연결을 위해 생성되고 준비되었는지 확인합니다.

```azurecli
az network vpn-connection create --name ERConnection --resource-group ExpressRouteResourceGroup --vnet-gateway1 VNet1GW --express-route-circuit2 MyCircuit
```

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>다른 구독에 있는 가상 네트워크를 회로에 연결

여러 구독에서 ExpressRoute 회로를 공유할 수 있습니다. 아래 그림에는 여러 구독에서 ExpressRoute 회로에 대한 작업을 공유하는 방법의 간단한 계통도가 나와 있습니다.

큰 구름 안에 있는 각각의 작은 구름은 한 조직 내의 여러 부서에 속하는 구독을 나타내는 데 사용됩니다. 조직 내의 각 부서는 자체 구독을 사용하여 서비스를 배포하되, 단일 ExpressRoute 회로를 공유하여 온-프레미스 네트워크로 다시 연결할 수 있습니다. 단일 부서(이 예제에서: IT)는 ExpressRoute 회로를 소유할 수 있습니다. 조직 내의 기타 구독도 ExpressRoute 회로를 사용할 수 있습니다.

> [!NOTE]
> 전용 회로에 대한 연결 및 대역폭 요금은 ExpressRoute 회로 소유자에게 적용됩니다. 모든 가상 네트워크는 동일한 대역폭을 공유합니다.
> 
> 

![구독 간 연결](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)

### <a name="administration---circuit-owners-and-circuit-users"></a>관리 - 회로 소유자 및 회로 사용자

'회로 소유자'는 ExpressRoute 회로 리소스의 인증된 고급 사용자입니다. 회로 소유자는 '회로 사용자'가 사용할 수 있는 권한 부여를 만들 수 있습니다. 회로 사용자는 ExpressRoute 회로와 동일한 구독 내에 있지 않은 가상 네트워크 게이트웨이의 소유자입니다. 회로 사용자는 가상 네트워크당 하나의 권한 부여를 사용할 수 있습니다.

회로 소유자는 언제든지 부여된 권한을 수정하고 해지할 수 있습니다. 권한 부여를 해지하면 액세스가 해지된 구독에서 모든 링크 연결이 삭제됩니다.

### <a name="circuit-owner-operations"></a>회로 소유자 작업

**권한 부여를 만들려면**

회로 소유자가 권한 부여를 만들면 ExpressRoute 회로에 해당 가상 네트워크 게이트웨이를 연결하는 회로 사용자에 의해 사용할 수 있는 권한 부여 키가 만들어집니다. 권한 부여는 하나의 연결에만 유효합니다.

다음 예제에서는 권한 부여를 만드는 방법을 보여줍니다.

```azurecli
az network express-route auth create --circuit-name MyCircuit -g ExpressRouteResourceGroup -n MyAuthorization
```

응답에는 권한 부여 키와 상태가 포함됩니다.

```azurecli
"authorizationKey": "0a7f3020-541f-4b4b-844a-5fb43472e3d7",
"authorizationUseStatus": "Available",
"etag": "W/\"010353d4-8955-4984-807a-585c21a22ae0\"",
"id": "/subscriptions/81ab786c-56eb-4a4d-bb5f-f60329772466/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit/authorizations/MyAuthorization1",
"name": "MyAuthorization1",
"provisioningState": "Succeeded",
"resourceGroup": "ExpressRouteResourceGroup"
```

**권한 부여를 검토하려면**

회로 소유자는 다음 예제를 실행하여 특정 회로에 발급한 모든 권한 부여를 검토할 수 있습니다.

```azurecli
az network express-route auth list --circuit-name MyCircuit -g ExpressRouteResourceGroup
```

**권한 부여를 추가하려면**

회로 소유자는 다음 예제를 사용하여 권한 부여를 추가할 수 있습니다.

```azurecli
az network express-route auth create --circuit-name MyCircuit -g ExpressRouteResourceGroup -n MyAuthorization1
```

**권한 부여를 삭제하려면**

회로 소유자는 다음 예제를 실행하여 권한 부여를 취소/삭제할 수 있습니다.

```azurecli
az network express-route auth delete --circuit-name MyCircuit -g ExpressRouteResourceGroup -n MyAuthorization1
```

### <a name="circuit-user-operations"></a>회로 사용자 작업

회로 사용자는 회로 소유자의 피어 ID 및 권한 부여 키가 필요합니다. 권한 부여 키는 GUID입니다.

```azurecli
Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
```

**연결 권한 부여를 사용하려면**

회로 사용자는 다음 예제를 실행하여 링크 권한 부여를 사용할 수 있습니다.

```azurecli
az network vpn-connection create --name ERConnection --resource-group ExpressRouteResourceGroup --vnet-gateway1 VNet1GW --express-route-circuit2 MyCircuit --authorization-key "^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^"
```

**연결 권한 부여를 해제하려면**

ExpressRoute 회로와 가상 네트워크의 연결을 삭제하여 권한 부여를 해제할 수 있습니다.

## <a name="next-steps"></a>다음 단계

ExpressRoute에 대한 자세한 내용은 [ExpressRoute FAQ](expressroute-faqs.md)를 참조하세요.
