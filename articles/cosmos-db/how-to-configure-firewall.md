---
title: Azure Cosmos DB 계정에 대해 IP 방화벽 구성
description: Azure Cosmos 계정에서 방화벽을 지원하도록 IP 액세스 제어 정책을 구성하는 방법을 알아봅니다.
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.date: 03/03/2021
ms.author: mjbrown
ms.custom: devx-track-azurecli
ms.openlocfilehash: b94b30851a5206c2183d999a3c024351cf415c90
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105568242"
---
# <a name="configure-ip-firewall-in-azure-cosmos-db"></a>Azure Cosmos DB에서 IP 방화벽 구성
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

계정에 저장 된 데이터를 보호 하기 위해 Azure Cosmos DB은 강력한 HMAC (해시 기반 MAC(메시지 인증 코드))를 활용 하는 암호 기반 권한 부여 모델을 지원 합니다. 또한 Azure Cosmos DB는 인바운드 방화벽 지원을 위해 IP 기반 액세스 제어를 지원합니다. 이 모델은 기존 데이터베이스 시스템의 방화벽 규칙과 유사하며 사용자 계정에 추가 보안 수준을 제공합니다. 방화벽에서는 승인된 머신 및/또는 클라우드 서비스에서만 액세스할 수 있도록 Azure Cosmos 계정을 구성할 수 있습니다. 이러한 승인된 머신 및 서비스에서 Azure Cosmos 데이터베이스에 저장된 데이터에 액세스하려면 여전히 호출자가 유효한 권한 부여 토큰을 제공해야 합니다.

## <a name="ip-access-control"></a><a id="ip-access-control-overview"></a>IP 액세스 제어

기본적으로 요청에 유효한 권한 부여 토큰이 포함되어 있으면 인터넷에서 Azure Cosmos 계정에 액세스할 수 있습니다. IP 정책 기반 액세스 제어를 구성하려면 사용자가 지정된 Azure Cosmos 계정에 액세스하도록 허용된 클라이언트 IP 목록으로 포함할 IP 주소 또는 IP 주소 범위 집합을 CIDR(Classless Inter-Domain Routing) 형식으로 제공해야 합니다. 이 구성이 적용되면 이 허용되는 목록 이외의 머신에서 보내는 모든 요청은 403(금지됨) 응답을 받습니다. IP 방화벽을 사용하는 경우 계정에 액세스하려면 Azure Portal을 허용하는 것이 좋습니다. 데이터 탐색기를 사용하고 Azure Portal에 표시되는 계정에 대한 메트릭을 검색하려면 액세스 권한이 필요합니다. 데이터 탐색기를 사용 하 여 Azure Portal 계정에 액세스 하도록 허용 하는 것 외에도 방화벽 설정을 업데이트 하 여 현재 IP 주소를 방화벽 규칙에 추가 해야 합니다. 방화벽 변경 내용이 전파 되는 데 최대 15 분이 걸릴 수 있으며이 기간 동안에는 방화벽이 일관 되지 않은 동작을 나타낼 수 있습니다.

서브넷 및 VNET 액세스 제어를 사용하여 IP 기반 방화벽을 결합할 수 있습니다. 결합하면 공용 IP가 있는 모든 원본에 대한 액세스 및/또는 VNET 내 특정 서브넷으로부터의 액세스를 제한할 수 있습니다. 서브넷 및 VNET 기반 액세스 제어 사용에 대해 자세히 알아보려면 [가상 네트워크에서 Azure Cosmos DB 리소스에 액세스](./how-to-configure-vnet-service-endpoint.md)를 참조하세요.

요약하면, Azure Cosmos 계정에 액세스하려면 권한 부여 토큰이 항상 필요합니다. IP 방화벽 및 VNET ACL(액세스 제어 목록)을 설정하지 않은 경우 권한 부여 토큰을 사용하여 Azure Cosmos 계정에 액세스할 수 있습니다. Azure Cosmos 계정에서 IP 방화벽과 VNET ACL 중 하나 또는 모두 설정되면 지정한(및 권한 부여 토큰 사용) 원본에서 발생하는 요청만 유효한 응답을 가져옵니다. 

IP 방화벽을 사용하여 Azure Cosmos DB 계정에 저장된 데이터를 보호할 수 있습니다. Azure Cosmos DB는 인바운드 방화벽 지원을 위해 IP 기반 액세스 제어를 지원합니다. 다음 방법 중 하나를 사용하여 Azure Cosmos DB 계정에서 IP 방화벽을 설정할 수 있습니다.

* Azure Portal에서
* Azure Resource Manager 템플릿을 사용하여 선언적으로
* **IpRangeFilter** 속성을 업데이트 하 여 Azure CLI 또는 Azure PowerShell를 통해 프로그래밍 방식으로

## <a name="configure-an-ip-firewall-by-using-the-azure-portal"></a><a id="configure-ip-policy"></a> Azure Portal을 사용하여 IP 방화벽 구성

Azure Portal에서 IP 액세스 제어 정책을 설정하려면 Azure Cosmos DB 계정 페이지로 이동하고 탐색 메뉴에서 **방화벽 및 가상 네트워크** 를 선택합니다. **다음에서 액세스 허용** 값을 **선택한 네트워크** 로 변경한 다음, **저장** 을 선택합니다.

![Azure Portal에서 방화벽 페이지를 여는 방법을 보여 주는 스크린샷](./media/how-to-configure-firewall/azure-portal-firewall.png)

IP 액세스 제어가 켜지면 Azure Portal에서는 IP 주소, IP 주소 범위 및 스위치를 지정하는 기능을 제공합니다. 스위치를 사용하면 다른 Azure 서비스 및 Azure Portal에 액세스할 수 있습니다. 이러한 스위치에 대한 세부 정보는 다음 섹션에 나와 있습니다.

> [!NOTE]
> Azure Cosmos DB 계정에 대해 IP 액세스 제어 정책을 사용하도록 설정한 후에는 허용되는 IP 주소 범위 목록 이외의 머신에서 시도하는 Azure Cosmos DB 계정에 대한 모든 요청이 거부됩니다. 액세스 제어의 무결성을 보장하기 위해 포털에서 Azure Cosmos DB 리소스를 찾아보는 기능도 차단됩니다.

### <a name="allow-requests-from-the-azure-portal"></a>Azure Portal의 요청 허용

프로그래밍 방식으로 IP 액세스 제어 정책을 사용하도록 설정할 경우 Azure Portal의 IP 주소를 **ipRangeFilter** 속성에 추가해야 액세스가 유지됩니다. 포털 IP 주소는 다음과 같습니다.

|지역|IP 주소|
|------|----------|
|독일|51.4.229.218|
|중국|139.217.8.252|
|US Gov|52.244.48.71|
|다른 모든 하위 지역|104.42.195.92,40.76.54.131,52.176.6.30,52.169.50.45,52.187.184.26|

다음 스크린샷에 표시 된 것 처럼 **Azure Portal에서 액세스 허용** 옵션을 선택 하 여 Azure Portal에 액세스 하도록 요청할 수 있습니다.

![Azure Portal 액세스를 사용하도록 설정하는 방법을 보여 주는 스크린샷](./media/how-to-configure-firewall/enable-azure-portal.png)

### <a name="allow-requests-from-global-azure-datacenters-or-other-sources-within-azure"></a>글로벌 Azure 데이터 센터 또는 Azure 내 다른 원본의 요청 허용

고정 IP를 제공하지 않는 서비스(예: Azure Stream Analytics 및 Azure Functions)에서 Azure Cosmos DB 계정에 액세스하는 경우 IP 방화벽을 사용하여 액세스를 제한할 수 있습니다. 다음 스크린샷에 표시 된 것 처럼 **azure 데이터 센터 내에서 연결 허용** 옵션을 선택 하 여 azure 내의 다른 원본에서 액세스를 사용 하도록 설정할 수 있습니다.

![Azure 데이터 센터에서 연결을 허용 하는 방법을 보여 주는 스크린샷](./media/how-to-configure-firewall/enable-azure-services.png)

이 옵션을 사용 하도록 설정 하면 허용 되는 `0.0.0.0` ip 주소 목록에 ip 주소가 추가 됩니다. `0.0.0.0`Ip 주소는 Azure 데이터 센터 ip 범위에서 Azure Cosmos DB 계정에 대 한 요청을 제한 합니다. 이 설정은 Azure Cosmos DB 계정에 대해 다른 IP 범위의 액세스를 허용하지 않습니다.

> [!NOTE]
> 이 옵션은 Azure에 배포된 다른 고객 구독의 요청을 비롯한 Azure의 모든 요청을 허용하도록 방화벽을 구성합니다. 이 옵션에서 허용된 IP 목록은 광범위하므로 방화벽 정책의 효율성을 제한합니다. 요청이 정적 IP 또는 VNET의 서브넷에서 발생하지 않는 경우에만 이 옵션을 사용합니다. Azure Portal은 Azure에 배포되기 때문에 이 옵션을 선택하면 자동으로 Azure Portal에서 액세스하도록 허용합니다.

### <a name="requests-from-your-current-ip"></a>현재 IP의 요청

개발을 간소화하기 위해 Azure Portal을 통해 클라이언트 머신의 IP를 식별하여 허용된 목록에 추가할 수 있습니다. 그러면 머신에서 실행되는 앱은 Azure Cosmos DB 계정에 액세스할 수 있습니다.

포털은 클라이언트 IP 주소를 자동으로 검색합니다. 이 주소는 머신의 클라이언트 IP 주소이거나 네트워크 게이트웨이의 IP 주소일 수 있습니다. 프로덕션에 대한 워크로드를 수행하기 전에 이 IP 주소를 제거해야 합니다.

현재 IP를 IP 목록에 추가하려면 **내 현재 IP 추가** 를 선택합니다. 그런 다음 **저장** 을 선택합니다.

:::image type="content" source="./media/how-to-configure-firewall/enable-current-ip.png" alt-text="현재 IP의 방화벽 설정을 구성하는 방법을 보여주는 스크린샷":::

### <a name="requests-from-cloud-services"></a>클라우드 서비스의 요청

Azure에서 클라우드 서비스는 Azure Cosmos DB를 사용하여 중간 계층 서비스 논리를 호스팅하는 일반적인 방법입니다. 클라우드 서비스에서 Azure Cosmos DB 계정에 액세스할 수 있게 하려면 [IP 액세스 제어 정책을 구성](#configure-ip-policy)하여 클라우드 서비스의 공용 IP 주소를 Azure Cosmos DB 계정에 연결된 허용된 IP 주소 목록에 추가해야 합니다. 이렇게 하면 클라우드 서비스의 모든 역할 인스턴스가 Azure Cosmos DB 계정에 액세스할 수 있습니다.

다음 스크린샷처럼 Azure Portal에서 클라우드 서비스의 IP 주소를 검색할 수 있습니다.

:::image type="content" source="./media/how-to-configure-firewall/public-ip-addresses.png" alt-text="Azure Portal에 표시된 클라우드 서비스의 공용 IP 주소를 보여주는 스크린샷":::

역할 인스턴스를 추가하여 클라우드 서비스를 스케일 아웃할 때 이러한 새 인스턴스는 동일한 클라우드 서비스에 포함되므로 자동으로 Azure Cosmos DB 계정에 대한 액세스 권한을 갖습니다.

### <a name="requests-from-virtual-machines"></a>가상 머신의 요청

Azure Cosmos DB를 사용하여 중간 계층 서비스를 호스트하는 데 [가상 머신](https://azure.microsoft.com/services/virtual-machines/) 또는 [가상 머신 확장 집합](../virtual-machine-scale-sets/overview.md)을 사용할 수도 있습니다. 가상 머신에서 액세스할 수 있도록 Cosmos DB 계정을 구성하려면 [IP 액세스 제어 정책을 구성](#configure-ip-policy)하여 가상 머신 및/또는 가상 머신 확장 집합의 공용 IP 주소를 Azure Cosmos DB 계정에 허용되는 IP 주소 중 하나로 구성해야 합니다.

다음 스크린샷처럼 Azure Portal에서 가상 머신의 IP 주소를 검색할 수 있습니다.

:::image type="content" source="./media/how-to-configure-firewall/public-ip-addresses-dns.png" alt-text="Azure Portal에 표시된 가상 머신의 공용 IP 주소를 보여주는 스크린샷":::

그룹에 가상 머신 인스턴스를 추가하면 Azure Cosmos DB 계정에 대한 액세스 권한을 자동으로 부여합니다.

### <a name="requests-from-the-internet"></a>인터넷의 요청

인터넷에 있는 컴퓨터에서 Azure Cosmos DB 계정에 액세스하는 경우 머신의 클라이언트 IP 주소 또는 IP 주소 범위를 사용자 계정의 허용되는 IP 주소 목록에 추가해야 합니다.

### <a name="add-outbound-rules-to-the-firewall"></a>방화벽에 아웃바운드 규칙 추가

방화벽 설정에 추가할 아웃 바운드 IP 범위의 현재 목록에 액세스 하려면 [AZURE IP 범위 및 서비스 태그 다운로드](https://www.microsoft.com/download/details.aspx?id=56519)를 참조 하세요.

목록을 자동화 하려면 [서비스 태그 검색 API (공개 미리 보기) 사용](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api-public-preview)을 참조 하세요.

## <a name="configure-an-ip-firewall-by-using-a-resource-manager-template"></a><a id="configure-ip-firewall-arm"></a>Resource Manager 템플릿을 사용하여 IP 방화벽 구성

Azure Cosmos DB 계정에 대 한 액세스 제어를 구성 하려면 리소스 관리자 템플릿이 허용 되는 IP 범위의 배열이 포함 된 **ipRules** 속성을 지정 하는지 확인 합니다. 이미 배포된 Cosmos 계정에 IP 방화벽을 구성하는 경우 `locations` 배열이 현재 배포된 것과 일치하는지 확인합니다. `locations` 배열과 기타 속성을 동시에 수정할 수 없습니다. Azure Cosmos DB에 대 한 Azure Resource Manager 템플릿에 대 한 자세한 내용 및 예제는 [Azure Resource Manager 템플릿](./templates-samples-sql.md) 을 참조 하세요 Azure Cosmos DB

> [!IMPORTANT]
> **IpRules** 속성은 API 버전 2020-04-01에서 도입 되었습니다. 이전 버전에서는 쉼표로 구분 된 IP 주소 목록 인 **ipRangeFilter** 속성을 대신 제공 했습니다.

아래 예제에서는 API 버전 2020-04-01 이상에서 **ipRules** 속성을 노출 하는 방법을 보여 줍니다.

```json
{
  "type": "Microsoft.DocumentDB/databaseAccounts",
  "name": "[variables('accountName')]",
  "apiVersion": "2020-04-01",
  "location": "[parameters('location')]",
  "kind": "GlobalDocumentDB",
  "properties": {
    "consistencyPolicy": "[variables('consistencyPolicy')[parameters('defaultConsistencyLevel')]]",
    "locations": "[variables('locations')]",
    "databaseAccountOfferType": "Standard",
    "enableAutomaticFailover": "[parameters('automaticFailover')]",
    "ipRules": [
      {
        "ipAddressOrRange": "40.76.54.131"
      },
      {
        "ipAddressOrRange": "52.176.6.30"
      },
      {
        "ipAddressOrRange": "52.169.50.45"
      },
      {
        "ipAddressOrRange": "52.187.184.26"
      }
    ]
  }
}
```

2020-04-01 이전의 모든 API 버전에 대 한 동일한 예제는 다음과 같습니다.

```json
{
  "type": "Microsoft.DocumentDB/databaseAccounts",
  "name": "[variables('accountName')]",
  "apiVersion": "2019-08-01",
  "location": "[parameters('location')]",
  "kind": "GlobalDocumentDB",
  "properties": {
    "consistencyPolicy": "[variables('consistencyPolicy')[parameters('defaultConsistencyLevel')]]",
    "locations": "[variables('locations')]",
    "databaseAccountOfferType": "Standard",
    "enableAutomaticFailover": "[parameters('automaticFailover')]",
    "ipRangeFilter":"40.76.54.131,52.176.6.30,52.169.50.45,52.187.184.26"
  }
}
```

## <a name="configure-an-ip-access-control-policy-by-using-the-azure-cli"></a><a id="configure-ip-firewall-cli"></a>Azure CLI를 사용하여 IP 액세스 제어 정책 구성

다음 명령은 IP 액세스 제어를 사용하여 Azure Cosmos DB 계정을 만드는 방법을 보여줍니다.

```azurecli-interactive
# Create a Cosmos DB account with default values and IP Firewall enabled
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
ipRangeFilter='192.168.221.17,183.240.196.255,40.76.54.131'

# Make sure there are no spaces in the comma-delimited list of IP addresses or CIDR ranges.
az cosmosdb create \
    -n $accountName \
    -g $resourceGroupName \
    --locations regionName='West US 2' failoverPriority=0 isZoneRedundant=False \
    --locations regionName='East US 2' failoverPriority=1 isZoneRedundant=False \
    --ip-range-filter $ipRangeFilter
```

## <a name="configure-an-ip-access-control-policy-by-using-powershell"></a><a id="configure-ip-firewall-ps"></a>PowerShell을 사용하여 IP 액세스 제어 정책 구성

다음 스크립트는 IP 액세스 제어를 사용하여 Azure Cosmos DB 계정을 만드는 방법을 보여줍니다.

```azurepowershell-interactive
# Create a Cosmos DB account with default values and IP Firewall enabled
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$ipRules = @("192.168.221.17","183.240.196.255","40.76.54.131")

$locations = @(
    @{ "locationName"="West US 2"; "failoverPriority"=0; "isZoneRedundant"=False },
    @{ "locationName"="East US 2"; "failoverPriority"=1, "isZoneRedundant"=False }
)

# Make sure there are no spaces in the comma-delimited list of IP addresses or CIDR ranges.
$CosmosDBProperties = @{
    "databaseAccountOfferType"="Standard";
    "locations"=$locations;
    "ipRules"=$ipRules
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2020-04-01" -ResourceGroupName $resourceGroupName `
    -Name $accountName -PropertyObject $CosmosDBProperties
```

## <a name="troubleshoot-issues-with-an-ip-access-control-policy"></a><a id="troubleshoot-ip-firewall"></a>IP 액세스 제어 정책 관련 문제 해결

다음 옵션을 사용하여 IP 액세스 제어 정책 관련 문제를 해결할 수 있습니다.

### <a name="azure-portal"></a>Azure portal

Azure Cosmos DB 계정에 대해 IP 액세스 제어 정책을 사용하도록 설정하면 허용되는 IP 주소 범위 목록 이외의 머신에서 시도하는 계정에 대한 모든 요청이 차단됩니다. 컨테이너 탐색 및 문서 쿼리와 같은 포털 데이터 평면 작업을 사용하도록 설정하려면 포털의 **방화벽** 창을 사용하여 Azure Portal 액세스를 명시적으로 허용해야 합니다.

### <a name="sdks"></a>SDK

허용 목록에 없는 머신에서 SDK를 사용하여 Azure Cosmos DB 리소스에 액세스하는 경우 일반적인 **403 사용 권한 없음** 응답이 추가 정보 없이 반환됩니다. 계정에 대해 허용된 IP 목록을 확인하고 Azure Cosmos DB 계정에 올바른 정책 구성이 적용되는지 확인합니다.

### <a name="source-ips-in-blocked-requests"></a>차단된 요청의 원본 IP

Azure Cosmos DB 계정에 진단 로깅을 사용하도록 설정합니다. 이러한 로그는 각 요청 및 응답을 보여줍니다. 방화벽 관련 메시지가 403 반환 코드와 함께 로깅됩니다. 이러한 메시지를 필터링하면 차단된 요청에 대한 원본 IP를 확인할 수 있습니다. [Azure Cosmos DB 진단 로깅](./monitor-cosmos-db.md)을 확인합니다.

### <a name="requests-from-a-subnet-with-a-service-endpoint-for-azure-cosmos-db-enabled"></a>Azure Cosmos DB에 대해 서비스 엔드포인트를 설정한 서브넷의 요청

Azure Cosmos DB에 대한 서비스 엔드포인트가 활성화된 가상 네트워크에 위치한 서브넷의 요청은 가상 네트워크 및 서브넷 ID를 Azure Cosmos DB 계정에 전송합니다. 이러한 요청에는 원본의 공용 IP가 없으므로 IP 필터에 의해 거부됩니다. 가상 네트워크에 있는 특정 서브넷의 액세스를 허용하려면 [Azure Cosmos DB 계정의 가상 네트워크 및 서브넷 기반 액세스를 구성하는 방법](how-to-configure-vnet-service-endpoint.md)에 설명된 대로 액세스 제어 목록을 추가합니다. 방화벽 규칙을 적용 하는 데 최대 15 분이 걸릴 수 있으며이 기간 동안에는 방화벽에서 일관 되지 않은 동작이 발생할 수 있습니다.

### <a name="private-ip-addresses-in-list-of-allowed-addresses"></a>허용 된 주소 목록의 개인 IP 주소

개인 IP 주소를 포함 하는 허용 된 주소 목록을 사용 하 여 Azure Cosmos 계정을 만들거나 업데이트 하지 못합니다. 목록에 개인 IP 주소가 지정 되어 있지 않은지 확인 합니다.

## <a name="next-steps"></a>다음 단계

Azure Cosmos DB 계정에 대한 가상 네트워크 서비스 엔드포인트를 구성하려면 다음 문서를 참조하세요.

* [Azure Cosmos DB 계정에 대한 가상 네트워크 및 서브넷 액세스 제어](how-to-configure-vnet-service-endpoint.md)
* [Azure Cosmos DB 계정에 대한 가상 네트워크 및 서브넷 기반 액세스 구성](how-to-configure-vnet-service-endpoint.md)