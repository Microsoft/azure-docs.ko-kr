---
title: 'CLI: Azure CLI로 웹앱의 프라이빗 엔드포인트 배포'
description: Azure CLI를 사용하여 웹앱에 대한 프라이빗 엔드포인트를 배포하는 방법을 알아봅니다.
author: ericgre
ms.assetid: a56faf72-7237-41e7-85ce-da8346f2bcaa
ms.devlang: azurecli
ms.topic: sample
ms.date: 07/06/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.openlocfilehash: 08e1878adab680329a33bc44020b49dce38de4d6
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88959695"
---
# <a name="create-an-app-service-app-and-deploy-private-endpoint-using-azure-cli"></a>Azure CLI를 사용하여 App Service 앱 만들기 및 프라이빗 엔드포인트 배포

이 샘플 스크립트는 관련 리소스를 사용하여 App Service에서 앱을 만든 다음, 프라이빗 엔드포인트를 배포합니다.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

대신 Azure CLI를 로컬에 설치하고 사용하려는 경우 이 빠른 시작을 진행하려면 Azure CLI 버전 2.0.28 이상을 사용해야 합니다. 설치된 버전을 확인하려면 `az --version`을 실행합니다. 설치 또는 업그레이드 정보는 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

리소스를 만들려면 먼저 웹앱, 가상 네트워크 및 기타 네트워크 구성 요소를 호스트할 리소스 그룹을 만들어야 합니다. [az group create](/cli/azure/group)를 사용하여 리소스 그룹을 만듭니다. 다음 예제에서는 *francecentral* 위치에 *myResourceGroup*이라는 리소스 그룹을 만듭니다.

```azurecli-interactive
az group create --name myResourceGroup --location francecentral 
```

## <a name="create-an-app-service-plan"></a>App Service 계획 만들기

웹앱을 호스트할 App Service 계획을 만듭니다.
[az appservice plan create](/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create)를 사용하여 App Service 계획을 만듭니다.
다음 예제에서는 *P1V2* sku와 작업자를 하나만 사용하여 *francecentral* 위치에 *myAppServicePlan*이라는 App Service 계획을 만듭니다. 

```azurecli-interactive
az appservice plan create \
--name myAppServicePlan \
--resource-group myResourceGroup \
--location francecentral \
--sku P1V2 \
--number-of-workers 1
```

## <a name="create-a-web-app"></a>웹앱 만들기

App Service 계획이 있으면 웹앱을 배포할 수 있습니다.
[az appservice plan create](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create를 사용하여 웹앱을 만듭니다.
다음 예제에서는 *myAppServicePlan*이라는 계획에 *mySiteName*이라는 웹앱을 만듭니다.

```azurecli-interactive
az webapp create \
--name mySiteName \
--resource-group myResourceGroup \
--plan myAppServicePlan
```

## <a name="create-a-vnet"></a>VNet 만들기

[az network vnet create](/cli/azure/network/vnet)를 사용하여 Virtual Network를 만듭니다. 다음 예제에서는 *mySubnet*이라는 서브넷이 하나 있는 *myVNet*이라는 기본 가상 네트워크를 만듭니다.

```azurecli-interactive
az network vnet create \
--name myVNet \
--resource-group myResourceGroup \
--location francecentral \
--address-prefixes 10.8.0.0/16 \
--subnet-name mySubnet \
--subnet-prefixes 10.8.100.0/24
```

## <a name="configure-the-subnet"></a>서브넷 구성 

프라이빗 엔드포인트 네트워크 정책을 사용하지 않도록 설정하려면 서브넷을 업데이트해야 합니다. [az network vnet subnet update](https://docs.microsoft.com/cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-update)를 사용하여 *mySubnet*이라는 서브넷 구성을 업데이트합니다.

```azurecli-interactive
az network vnet subnet update \
--name mySubnet \
--resource-group myResourceGroup \
--vnet-name myVNet \
--disable-private-endpoint-network-policies true
```

## <a name="create-the-private-endpoint"></a>프라이빗 엔드포인트 만들기

[az network private-endpoint create](/cli/azure/network/private-endpoint)를 사용하여 웹앱의 프라이빗 엔드포인트를 만듭니다. 다음 예제에서는 웹앱 /subscriptions/SubscriptionID/resourceGroups/myResourceGroup/providers/Microsoft.Web/sites/myWebApp의 리소스 ID에 대해 *myConnectionName*이라는 연결을 사용하여 서브넷 *mySubnet*의 VNet인 *myVNet*에 *myPrivateEndpoint*라는 프라이빗 엔드포인트를 만듭니다. 그룹 매개 변수는 웹앱 유형의 *sites*입니다. 

```azurecli-interactive
az network private-endpoint create \
--name myPrivateEndpoint \
--resource-group myResourceGroup \
--vnet-name myVNet \
--subnet mySubnet \
--connection-name myConnectionName \
--private-connection-resource-id /subscriptions/SubscriptionID/resourceGroups/myResourceGroup/providers/Microsoft.Web/sites/myWebApp \
--group-id sites
```

## <a name="configure-the-private-zone"></a>프라이빗 영역 구성

마지막으로 VNet에 연결된 *privatelink.azurewebsites.net*이라는 프라이빗 DNS 영역을 만들어서 웹앱의 DNS 이름을 확인해야 합니다.


```azurecli-interactive
az network private-dns zone create \
--name privatelink.azurewebsites.net \
--resource-group myResourceGroup

az network private-dns link vnet create \
--name myDNSLink \
--resource-group myResourceGroup \
--registration-enabled false \
--virtual-network myVNet \
--zone-name privatelink.azurewebsites.net

az network private-endpoint dns-zone-group create \
--name myZoneGroup \
--resource-group myResourceGroup \
--endpoint-name myPrivateEndpoint \
--private-dns-zone privatelink.azurewebsites.net \
--zone-name privatelink.azurewebsites.net
```






[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]


## <a name="next-steps"></a>다음 단계

- Azure CLI에 대한 자세한 내용은 [Azure CLI 설명서](/cli/azure)를 참조하세요.
- 추가 App Service CLI 스크립트 샘플은 [Azure App Service 설명서](../samples-cli.md)에서 확인할 수 있습니다.