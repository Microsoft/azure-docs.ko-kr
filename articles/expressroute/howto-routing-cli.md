---
title: 'Azure Express 경로: 피어 링 구성: CLI'
description: 이 문서는 ExpressRoute 회로의 프라이빗, 공용 및 Microsoft 피어링을 만들고 프로비저닝하는 데 도움이 됩니다. 또한 회로의 상태를 확인하고 업데이트 또는 삭제하는 방법을 보여줍니다.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: dbda73e022ebaad283641ce2f54a5962aeb4cb60
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75436834"
---
# <a name="create-and-modify-peering-for-an-expressroute-circuit-using-cli"></a>CLI를 사용하여 ExpressRoute 회로의 피어링 만들기 및 수정

이 문서는 CLI를 사용하여 Resource Manager 배포 모델에서 ExpressRoute 회로에 라우팅 구성/피어링을 만들고 관리하는 데 도움이 됩니다. ExpressRoute 회로에 대한 피어링의 상태를 확인, 업데이트 또는 삭제 및 프로비전 해제를 수행할 수도 있습니다. 회로를 사용하는 다른 메서드를 사용하려는 경우 다음 목록에서 문서를 선택합니다.

> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-routing-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-routing-arm.md)
> * [Azure CLI](howto-routing-cli.md)
> * [공용 피어 링](about-public-peering.md)
> * [비디오 - 프라이빗 피어링](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [비디오 - Microsoft 피어링](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> * [PowerShell(클래식)](expressroute-howto-routing-classic.md)
> 

## <a name="configuration-prerequisites"></a>필수 구성 요소

* 시작하기 전에 최신 버전의 CLI 명령(2.0 이상)을 설치합니다. CLI 명령을 설치하는 방법에 대한 자세한 내용은 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.
* 구성을 시작하기 전에 [필수 조건](expressroute-prerequisites.md), [라우팅 요구 사항](expressroute-routing.md) 및 [워크플로](expressroute-workflows.md) 페이지를 검토했는지 확인합니다.
* 활성화된 ExpressRoute 회로가 있어야 합니다. 지침을 수행하여 [ExpressRoute 회로를 만들고](howto-circuit-cli.md) 진행하기 전에 연결 공급자를 통해 회로를 사용하도록 설정합니다. ExpressRoute 회로는 이 문서에서 명령을 실행할 수 있도록 프로비전되고 활성화된 상태여야 합니다.

이 지침은 2계층 연결 서비스를 제공하는 서비스 공급자를 사용하여 만든 회로에만 적용됩니다. 관리된 3계층 서비스(일반적으로 MPLS와 같은 IPVPN)를 제공하는 서비스 공급자를 사용하는 경우 연결 공급자는 사용자를 위해 라우팅을 구성하고 관리합니다.

Express 경로 회로에 대해 개인 피어 링 및 Microsoft 피어 링을 구성할 수 있습니다 (Azure 공용 피어 링은 새 회로에서 사용 되지 않음). 피어 링은 선택 하는 순서에 관계 없이 구성할 수 있습니다. 그러나 각 피어링의 구성을 한 번에 하나 씩 완료하도록 해야 합니다. 라우팅 도메인 및 피어링에 대한 자세한 내용은 [ExpressRoute 라우팅 도메인](expressroute-circuit-peerings.md)을 참조하세요. 공용 피어 링에 대한 자세한 내용은 [express 경로 공용 피어 링](about-public-peering.md)을 참조 하세요.

## <a name="msft"></a>Microsoft 피어링

이 섹션은 ExpressRoute 회로에 Microsoft 피어링 구성을 만들고 가져오며 업데이트하고 삭제하는 데 도움이 됩니다.

> [!IMPORTANT]
> 경로 필터를 정의하지 않은 경우에도 2017년 8월 1일 이전에 구성된 ExpressRoute 회로의 Microsoft 피어링에는 Microsoft 피어링을 통해 보급된 모든 서비스 접두사가 포함됩니다. 2017년 8월 1일 이후에 구성되는 ExpressRoute 회로의 Microsoft 피어링에는 경로 필터를 회로에 연결할 때까지 접두사가 보급되지 않습니다. 자세한 내용은 [Microsoft 피어링에 경로 필터 구성](how-to-routefilter-powershell.md)을 참조하세요.
> 
> 

### <a name="to-create-microsoft-peering"></a>Microsoft 피어링을 만들려면

1. Azure CLI 최신 버전 설치 최신 버전의 Azure CLI(명령줄 인터페이스)를 사용합니다. *구성을 시작하기 전에 [필수 구성 요소](expressroute-prerequisites.md) 및 [워크플로](expressroute-workflows.md)를 검토합니다.

   ```azurecli-interactive
   az login
   ```

   ExpressRoute 회로를 만들려는 구독을 선택합니다.

   ```azurecli-interactive
   az account set --subscription "<subscription ID>"
   ```
2. ExpressRoute 회로를 만듭니다. 지침에 따라 [ExpressRoute 회로](howto-circuit-cli.md) 를 만들고 연결 공급자를 통해 프로비전합니다. 연결 공급자가 관리된 3계층 서비스를 제공하는 경우 연결 공급자를 요청하여 Microsoft 피어링을 사용하도록 할 수 있습니다. 이 경우에 다음 섹션에 나열된 지침에 따를 필요가 없습니다. 그러나 회로를 만든 후에 연결 공급자가 라우팅을 관리하지 않는 경우 다음 단계를 사용하여 구성을 계속합니다. 

3. 먼저 ExpressRoute 회로가 프로비전되고 사용 가능한지 확인합니다. 다음 예제를 사용합니다.

   ```azurecli-interactive
   az network express-route list
   ```

   응답은 다음 예제와 유사합니다.

   ```azurecli
   "allowClassicOperations": false,
   "authorizations": [],
   "circuitProvisioningState": "Enabled",
   "etag": "W/\"1262c492-ffef-4a63-95a8-a6002736b8c4\"",
   "gatewayManagerEtag": null,
   "id": "/subscriptions/81ab786c-56eb-4a4d-bb5f-f60329772466/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit",
   "location": "westus",
   "name": "MyCircuit",
   "peerings": [],
   "provisioningState": "Succeeded",
   "resourceGroup": "ExpressRouteResourceGroup",
   "serviceKey": "1d05cf70-1db5-419f-ad86-1ca62c3c125b",
   "serviceProviderNotes": null,
   "serviceProviderProperties": {
    "bandwidthInMbps": 200,
    "peeringLocation": "Silicon Valley",
    "serviceProviderName": "Equinix"
   },
   "serviceProviderProvisioningState": "Provisioned",
   "sku": {
    "family": "UnlimitedData",
    "name": "Standard_MeteredData",
    "tier": "Standard"
   },
   "tags": null,
   "type": "Microsoft.Network/expressRouteCircuits]
   ```

4. 회로에 Microsoft 피어링을 구성합니다. 진행하기 전에 다음 정보가 있는지 확인합니다.

   * 기본 링크에 대한 /30 서브넷입니다. 사용자가 소유하고 RIR/IRR에 등록된 유효한 공용 IPv4 접두사여야 합니다.
   * 보조 링크에 대한 /30 서브넷입니다. 사용자가 소유하고 RIR/IRR에 등록된 유효한 공용 IPv4 접두사여야 합니다.
   * 피어링을 설정할 유효한 VLAN ID입니다. 회로에 다른 피어링이 동일한 VLAN ID를 사용하지 않는지 확인합니다.
   * 피어링에 대한 AS 숫자입니다. 2바이트 및 4바이트 AS 번호를 모두 사용할 수 있습니다.
   * 보급된 접두사: BGP 세션을 통해 보급하려는 모든 접두사 목록을 제공해야 합니다. 공용 IP 주소 접두사만 수락됩니다. 접두사 집합을 보내려는 경우 쉼표로 구분된 목록을 보낼 수 있습니다. 이 접두사는 RIR/IRR에 등록되어야 합니다.
   * **선택 사항 -** 고객 ASN: 피어링 AS 숫자에 등록되지 않은 광고 접두사인 경우 등록된 AS 번호를 지정할 수 있습니다.
   * 라우팅 레지스트리 이름: AS 번호 및 접두사가 등록된 RIR/ IRR를 지정할 수 있습니다.
   * **선택 사항 -** 사용하기로 선택한 경우 MD5 해시를 사용합니다.

   다음 예제를 실행하여 회로에 Microsoft 피어링을 구성합니다.

   ```azurecli-interactive
   az network express-route peering create --circuit-name MyCircuit --peer-asn 100 --primary-peer-subnet 123.0.0.0/30 -g ExpressRouteResourceGroup --secondary-peer-subnet 123.0.0.4/30 --vlan-id 300 --peering-type MicrosoftPeering --advertised-public-prefixes 123.1.0.0/24
   ```

### <a name="getmsft"></a>Microsoft 피어링 세부 정보를 보려면

다음 예제를 사용하여 구성 세부 정보를 가져올 수 있습니다.

```azurecli-interactive
az network express-route peering show -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzureMicrosoftPeering
```
> [!IMPORTANT]
> Microsoft는 지정 된 ' 보급 된 공용 접두사 ' 및 ' 피어 ASN ' (또는 ' Customer ASN ')이 인터넷 라우팅 레지스트리에서 할당 되었는지 확인 합니다. 다른 엔터티에서 공용 접두사를 가져오는 경우 및 라우팅이 라우팅 레지스트리를 사용 하 여 기록 되지 않으면 자동 유효성 검사가 완료 되지 않으며 수동 유효성 검사를 요구 합니다. 자동 유효성 검사에 실패 하면 위의 명령 출력에 ' AdvertisedPublicPrefixesState '이 ' 유효성 검사 필요 '로 표시 됩니다. 
> 
> ' 유효성 검사 필요 ' 메시지가 표시 되 면 라우팅 레지스트리에서 접두사의 소유자로 표시 된 엔터티가 조직에 할당 된 공용 접두사를 표시 하는 문서를 수집 하 고 다음을 통해 수동으로 유효성 검사를 위해 이러한 문서를 제출 합니다. 아래와 같이 지원 티켓을 엽니다. 
> 
>

다음 예제와 유사하게 출력됩니다.

```azurecli
{
  "azureAsn": 12076,
  "etag": "W/\"2e97be83-a684-4f29-bf3c-96191e270666\"",
  "gatewayManagerEtag": "18",
  "id": "/subscriptions/9a0c2943-e0c2-4608-876c-e0ddffd1211b/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit/peerings/AzureMicrosoftPeering",
  "lastModifiedBy": "Customer",
  "microsoftPeeringConfig": {
    "advertisedPublicPrefixes": [
        ""
      ],
     "advertisedPublicPrefixesState": "",
     "customerASN": ,
     "routingRegistryName": ""
  }
  "name": "AzureMicrosoftPeering",
  "peerAsn": ,
  "peeringType": "AzureMicrosoftPeering",
  "primaryAzurePort": "",
  "primaryPeerAddressPrefix": "",
  "provisioningState": "Succeeded",
  "resourceGroup": "ExpressRouteResourceGroup",
  "routeFilter": null,
  "secondaryAzurePort": "",
  "secondaryPeerAddressPrefix": "",
  "sharedKey": null,
  "state": "Enabled",
  "stats": null,
  "vlanId": 100
}
```

### <a name="updatemsft"></a>Microsoft 피어링 구성을 업데이트하려면

구성의 일부를 업데이트할 수 있습니다. 다음 예제에서 회로의 보급된 접두사는 123.1.0.0/24에서 124.1.0.0/24로 업데이트됩니다.

```azurecli-interactive
az network express-route peering update --circuit-name MyCircuit -g ExpressRouteResourceGroup --peering-type MicrosoftPeering --advertised-public-prefixes 124.1.0.0/24
```

### <a name="addIPv6msft"></a>기존 IPv4 구성에 IPv6 Microsoft 피어링 설정을 추가하려면

```azurecli-interactive
az network express-route peering update -g ExpressRouteResourceGroup --circuit-name MyCircuit --peering-type MicrosoftPeering --ip-version ipv6 --primary-peer-subnet 2002:db00::/126 --secondary-peer-subnet 2003:db00::/126 --advertised-public-prefixes 2002:db00::/126
```

### <a name="deletemsft"></a>Microsoft 피어링을 삭제하려면

다음 예제를 실행하여 피어링 구성을 제거할 수 있습니다.

```azurecli-interactive
az network express-route peering delete -g ExpressRouteResourceGroup --circuit-name MyCircuit --name MicrosoftPeering
```

## <a name="private"></a>Azure 프라이빗 피어링

이 섹션은 ExpressRoute 회로에 Azure 프라이빗 피어링 구성을 만들고 가져오며 업데이트하고 삭제하는 데 도움이 됩니다.

### <a name="to-create-azure-private-peering"></a>Azure 프라이빗 피어링을 만들려면

1. Azure CLI 최신 버전 설치 최신 버전의 Azure CLI(명령줄 인터페이스)를 사용해야 합니다. *구성을 시작하기 전에 [필수 구성 요소](expressroute-prerequisites.md) 및 [워크플로](expressroute-workflows.md)를 검토합니다.

   ```azurecli-interactive
   az login
   ```

   ExpressRoute 회로를 만들려는 구독을 선택합니다.

   ```azurecli-interactive
   az account set --subscription "<subscription ID>"
   ```
2. ExpressRoute 회로를 만듭니다. 지침에 따라 [ExpressRoute 회로](howto-circuit-cli.md) 를 만들고 연결 공급자를 통해 프로비전합니다. 연결 공급자가 관리된 3계층 서비스를 제공하는 경우 연결 공급자를 요청하여 Azure 프라이빗 피어링을 사용하도록 할 수 있습니다. 이 경우에 다음 섹션에 나열된 지침에 따를 필요가 없습니다. 그러나 회로를 만든 후에 연결 공급자가 라우팅을 관리하지 않는 경우 다음 단계를 사용하여 구성을 계속합니다.

3. 먼저 ExpressRoute 회로가 프로비전되고 사용 가능한지 확인합니다. 다음 예제를 사용합니다.

   ```azurecli-interactive
   az network express-route show --resource-group ExpressRouteResourceGroup --name MyCircuit
   ```

   응답은 다음 예제와 유사합니다.

   ```azurecli
   "allowClassicOperations": false,
   "authorizations": [],
   "circuitProvisioningState": "Enabled",
   "etag": "W/\"1262c492-ffef-4a63-95a8-a6002736b8c4\"",
   "gatewayManagerEtag": null,
   "id": "/subscriptions/81ab786c-56eb-4a4d-bb5f-f60329772466/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit",
   "location": "westus",
   "name": "MyCircuit",
   "peerings": [],
   "provisioningState": "Succeeded",
   "resourceGroup": "ExpressRouteResourceGroup",
   "serviceKey": "1d05cf70-1db5-419f-ad86-1ca62c3c125b",
   "serviceProviderNotes": null,
   "serviceProviderProperties": {
   "bandwidthInMbps": 200,
   "peeringLocation": "Silicon Valley",
   "serviceProviderName": "Equinix"
   },
   "serviceProviderProvisioningState": "Provisioned",
   "sku": {
    "family": "UnlimitedData",
    "name": "Standard_MeteredData",
    "tier": "Standard"
   },
   "tags": null,
   "type": "Microsoft.Network/expressRouteCircuits]
   ```

4. 회로에 Azure 프라이빗 피어링을 구성합니다. 다음 단계를 계속 진행하기 전에 다음 항목이 있는지 확인합니다.

   * 기본 링크에 대한 /30 서브넷입니다. 서브넷은 가상 네트워크에 예약된 주소 공간의 일부가 아니어야 합니다.
   * 보조 링크에 대한 /30 서브넷입니다. 서브넷은 가상 네트워크에 예약된 주소 공간의 일부가 아니어야 합니다.
   * 피어링을 설정할 유효한 VLAN ID입니다. 회로에 다른 피어링이 동일한 VLAN ID를 사용하지 않는지 확인합니다.
   * 피어링에 대한 AS 숫자입니다. 2바이트 및 4바이트 AS 번호를 모두 사용할 수 있습니다. 이 피어링에 프라이빗 AS 숫자를 사용할 수 있습니다. 65515를 사용하지 않는지 확인합니다.
   * **선택 사항 -** 사용하기로 선택한 경우 MD5 해시를 사용합니다.

   다음 예제를 사용하여 회로에 Azure 프라이빗 피어링을 구성합니다.

   ```azurecli-interactive
   az network express-route peering create --circuit-name MyCircuit --peer-asn 100 --primary-peer-subnet 10.0.0.0/30 -g ExpressRouteResourceGroup --secondary-peer-subnet 10.0.0.4/30 --vlan-id 200 --peering-type AzurePrivatePeering
   ```

   MD5 해시를 사용하려는 경우 다음 예제를 사용합니다.

   ```azurecli-interactive
   az network express-route peering create --circuit-name MyCircuit --peer-asn 100 --primary-peer-subnet 10.0.0.0/30 -g ExpressRouteResourceGroup --secondary-peer-subnet 10.0.0.4/30 --vlan-id 200 --peering-type AzurePrivatePeering --SharedKey "A1B2C3D4"
   ```

   > [!IMPORTANT]
   > 고객 ASN이 아닌 피어링 ASN로 AS 번호를 지정했는지 확인합니다.
   > 
   > 

### <a name="getprivate"></a>Azure 프라이빗 피어링 세부 정보를 보려면

다음 예제를 사용하여 구성 세부 정보를 가져올 수 있습니다.

```azurecli-interactive
az network express-route peering show -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePrivatePeering
```

다음 예제와 유사하게 출력됩니다.

```azurecli
{
  "azureAsn": 12076,
  "etag": "W/\"2e97be83-a684-4f29-bf3c-96191e270666\"",
  "gatewayManagerEtag": "18",
  "id": "/subscriptions/9a0c2943-e0c2-4608-876c-e0ddffd1211b/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit/peerings/AzurePrivatePeering",
  "ipv6PeeringConfig": null,
  "lastModifiedBy": "Customer",
  "microsoftPeeringConfig": null,
  "name": "AzurePrivatePeering",
  "peerAsn": 7671,
  "peeringType": "AzurePrivatePeering",
  "primaryAzurePort": "",
  "primaryPeerAddressPrefix": "",
  "provisioningState": "Succeeded",
  "resourceGroup": "ExpressRouteResourceGroup",
  "routeFilter": null,
  "secondaryAzurePort": "",
  "secondaryPeerAddressPrefix": "",
  "sharedKey": null,
  "state": "Enabled",
  "stats": null,
  "vlanId": 100
}
```

### <a name="updateprivate"></a>Azure 프라이빗 피어링 구성을 업데이트하려면

다음 예제를 사용하여 구성의 일부를 업데이트할 수 있습니다. 이 예제에서는 회로의 VLAN ID를 100개에서 500개로 업데이트됩니다.

```azurecli-interactive
az network express-route peering update --vlan-id 500 -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePrivatePeering
```

### <a name="deleteprivate"></a>Azure 프라이빗 피어링을 삭제하려면

다음 예제를 실행하여 피어링 구성을 제거할 수 있습니다.

> [!WARNING]
> 이 예제를 실행하기 전에 모든 가상 네트워크 및 ExpressRoute Global Reach 연결을 제거해야 합니다. 
> 
> 

```azurecli-interactive
az network express-route peering delete -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePrivatePeering
```


## <a name="next-steps"></a>다음 단계

다음 단계에서는 [VNet을 ExpressRoute 회로에 연결](howto-linkvnet-cli.md)합니다.

* ExpressRoute 워크플로에 대한 자세한 내용은 [ExpressRoute 워크플로](expressroute-workflows.md)를 참조하세요.
* 회로 피어링에 대한 자세한 내용은 [ExpressRoute 회로 및 라우팅 도메인](expressroute-circuit-peerings.md)을 참조하세요.
* 가상 네트워크 작업에 대한 자세한 내용은 [가상 네트워크 개요](../virtual-network/virtual-networks-overview.md)를 참조하세요.
