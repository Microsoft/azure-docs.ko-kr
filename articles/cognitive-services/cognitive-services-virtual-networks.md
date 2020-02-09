---
title: Virtual Network
titleSuffix: Azure Cognitive Services
description: Cognitive Services 리소스에 대해 계층화 된 네트워크 보안을 구성 합니다.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: dapine
ms.openlocfilehash: 1ae3caa2d1f90bbbae1070d95d676eb206a361a0
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73647343"
---
# <a name="configure-azure-cognitive-services-virtual-networks"></a>Azure Cognitive Services 가상 네트워크 구성

Azure Cognitive Services는 계층화 된 보안 모델을 제공 합니다. 이 모델을 사용 하 여 Cognitive Services 계정을 특정 네트워크 하위 집합으로 보호할 수 있습니다. 네트워크 규칙이 구성 된 경우 지정 된 네트워크 집합을 통해 데이터를 요청 하는 응용 프로그램만 계정에 액세스할 수 있습니다. 요청 필터링을 사용 하 여 리소스에 대한 액세스를 제한할 수 있습니다. 지정 된 IP 주소, IP 범위 또는 [Azure Virtual network](../virtual-network/virtual-networks-overview.md)의 서브넷 목록에서 시작 되는 요청만 허용 합니다. 이 제품에 관심이 있는 경우 [미리 보기 액세스를 요청](https://aka.ms/cog-svc-vnet-signup)해야 합니다.

네트워크 규칙이 적용 되는 경우 Cognitive Services 리소스에 액세스 하는 응용 프로그램은 권한 부여가 필요 합니다. 권한 부여는 [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) (Azure AD) 자격 증명 또는 유효한 API 키로 지원 됩니다.

> [!IMPORTANT]
> Cognitive Services 계정에 대한 방화벽 규칙을 설정 하면 기본적으로 들어오는 데이터에 대한 요청이 차단 됩니다. 에서 요청을 허용 하려면 다음 조건 중 하나를 충족 해야 합니다.
> * 요청은 대상 Cognitive Services 계정의 허용 된 서브넷 목록에 있는 Azure Virtual Network (VNet) 내에서 작동 하는 서비스에서 시작 해야 합니다. VNet에서 시작 된 요청의 엔드포인트은 Cognitive Services 계정의 [사용자 지정 하위 도메인](cognitive-services-custom-subdomains.md) 으로 설정 해야 합니다.
> * 또는 요청이 허용 되는 IP 주소 목록에서 시작 되어야 합니다.
>
> 차단되는 요청에는 다른 Azure 서비스, Azure Portal, 로깅 및 메트릭 서비스 등이 포함됩니다.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="scenarios"></a>시나리오

Cognitive Services 리소스를 보호 하려면 먼저 기본적으로 모든 네트워크 (인터넷 트래픽 포함)의 트래픽에 대한 액세스를 거부 하는 규칙을 구성 해야 합니다. 그런 다음 특정 Vnet 트래픽에 대한 액세스 권한을 부여 하는 규칙을 구성 해야 합니다. 이 구성을 사용하면 애플리케이션에 대한 보안 네트워크 경계를 구축할 수 있습니다. 또한 특정 인터넷 또는 온-프레미스 클라이언트에서 연결을 사용 하도록 설정 하 여 공용 인터넷 IP 주소 범위 선택에서 트래픽에 대한 액세스를 허용 하도록 규칙을 구성할 수 있습니다.

네트워크 규칙은 REST 및 WebSocket을 포함 하 여 Azure Cognitive Services에 대한 모든 네트워크 프로토콜에 적용 됩니다. Azure 테스트 콘솔과 같은 도구를 사용 하 여 데이터에 액세스 하려면 명시적 네트워크 규칙을 구성 해야 합니다. 기존 Cognitive Services 리소스에 네트워크 규칙을 적용 하거나 새 Cognitive Services 리소스를 만들 때 사용할 수 있습니다. 네트워크 규칙이 적용되면 모든 요청에 적용됩니다.

## <a name="supported-regions-and-service-offerings"></a>지원 되는 지역 및 서비스 제공

Cognitive Services에 대한 가상 네트워크 지원은 *카나리아 중부 미국 EUAP*, *유럽 서부*및 *미국 서 부 2* Azure 지역으로 제한 됩니다. 또한 모든 인식 서비스 제공이 가상 네트워크를 지원 하지는 않습니다. 다음 인식 서비스 제공을 통해 가상 네트워크를 사용할 수 있습니다. 서비스 제공이 여기에 나열 되어 있지 않으면 가상 네트워크를 지원 하지 않습니다.

> [!div class="checklist"]
> * [변칙 탐지기](./anomaly-detector/index.yml)
> * [Computer Vision](./computer-vision/index.yml)
> * [Content Moderator](./content-moderator/index.yml)
> * [Custom Vision](./custom-vision-service/index.yml)
> * [Face](./face/index.yml)
> * [폼 인식기](./form-recognizer/index.yml)
> * [LUIS](./luis/index.yml)
> * [Personalizer](./personalizer/index.yml)
> * [텍스트 분석](./text-analytics/index.yml)

## <a name="change-the-default-network-access-rule"></a>기본 네트워크 액세스 규칙 변경

기본적으로 Cognitive Services 리소스는 네트워크에 있는 클라이언트의 연결을 허용 합니다. 선택한 네트워크에 대한 액세스를 제한하려면 먼저 기본 동작을 변경해야 합니다.

> [!WARNING]
> 네트워크 규칙을 변경 하면 응용 프로그램에서 Azure Cognitive Services에 연결 하는 기능에 영향을 줄 수 있습니다. 액세스 **권한을 부여** 하는 특정 네트워크 규칙도 적용 되지 않는 한 기본 네트워크 규칙을 **deny** 로 설정 하면 데이터에 대한 모든 액세스가 차단 됩니다. 액세스를 거부하도록 기본 규칙을 변경하기 전에 네트워크 규칙을 사용하여 허용된 모든 네트워크에 대한 액세스를 허가해야 합니다. 온-프레미스 네트워크에 대한 IP 주소를 나열 하도록 허용 하는 경우 온-프레미스 네트워크에서 가능한 모든 나가는 공용 IP 주소를 추가 해야 합니다.

### <a name="managing-default-network-access-rules"></a>기본 네트워크 액세스 규칙 관리

Azure Portal, PowerShell 또는 Azure CLI를 통해 Cognitive Services 리소스에 대한 기본 네트워크 액세스 규칙을 관리할 수 있습니다.

# <a name="azure-portaltabportal"></a>[Azure Portal](#tab/portal)

1. 보안을 유지 하려는 Cognitive Services 리소스로 이동 합니다.

1. **가상 네트워크**라는 **리소스 관리** 메뉴를 선택 합니다.

   ![가상 네트워크 옵션](media/vnet/virtual-network-blade.png)

1. 기본적으로 액세스를 거부하려면 **선택한 네트워크**에서 액세스를 허용하도록 선택합니다. **선택한 네트워크** 설정 만으로 구성 된 **가상 네트워크** 또는 **주소 범위** 를 함께 사용 하지 않으면 모든 액세스가 효과적으로 거부 됩니다. 모든 액세스가 거부 되 면 Cognitive Services 리소스를 사용 하려고 시도 하는 요청은 허용 되지 않습니다. Azure Portal Azure PowerShell 또는 Azure CLI를 사용 하 여 Cognitive Services 리소스를 구성할 수 있습니다.
1. 모든 네트워크의 트래픽을 허용하려면 **모든 네트워크**에서 액세스를 허용하도록 선택합니다.

   ![가상 네트워크 거부](media/vnet/virtual-network-deny.png)

1. **저장**을 선택하여 변경 내용을 적용합니다.

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

1. [Azure PowerShell](/powershell/azure/install-az-ps) 를 설치 하 고 [로그인](/powershell/azure/authenticate-azureps)하거나 **체험**을 선택 합니다.

1. Cognitive Services 리소스에 대한 기본 규칙의 상태를 표시 합니다.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
    }
    (Get-AzCognitiveServicesAccountNetworkRuleSet @parameters).DefaultAction
    ```

1. 기본적으로 네트워크 액세스를 거부하도록 기본 규칙을 설정합니다.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -DefaultAction Deny
    }
    Update-AzCognitiveServicesAccountNetworkRuleSet @parameters
    ```

1. 기본적으로 네트워크 액세스를 허용하도록 기본 규칙을 설정합니다.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -DefaultAction Allow
    }
    Update-AzCognitiveServicesAccountNetworkRuleSet @parameters
    ```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

1. [Azure CLI](/cli/azure/install-azure-cli) 를 설치 하 고 [로그인](/cli/azure/authenticate-azure-cli)하거나 **체험**을 선택 합니다.

1. Cognitive Services 리소스에 대한 기본 규칙의 상태를 표시 합니다.

    ```azurecli-interactive
    az cognitiveservices account show \
        -g "myresourcegroup" -n "myaccount" \
        --query networkRuleSet.defaultAction
    ```

1. 기본적으로 네트워크 액세스를 거부하도록 기본 규칙을 설정합니다.

    ```azurecli-interactive
    az cognitiveservices account update \
        -g "myresourcegroup" -n "myaccount" \
        --default-action Deny
    ```

1. 기본적으로 네트워크 액세스를 허용하도록 기본 규칙을 설정합니다.

    ```azurecli-interactive
    az cognitiveservices account update \
        -g "myresourcegroup" -n "myaccount" \
        --default-action Allow
    ```

***

## <a name="grant-access-from-a-virtual-network"></a>가상 네트워크의 액세스 허가

특정 서브넷 에서만 액세스할 수 있도록 Cognitive Services 리소스를 구성할 수 있습니다. 허용 되는 서브넷은 동일한 구독의 VNet 또는 다른 Azure Active Directory 테넌트에 속한 구독을 포함 하 여 다른 구독에 속할 수 있습니다.

VNet 내에서 Azure Cognitive Services에 대한 [서비스 엔드포인트](../virtual-network/virtual-network-service-endpoints-overview.md) 을 사용 하도록 설정 합니다. 서비스 엔드포인트은 VNet에서 Azure Cognitive Services 서비스에 대한 최적의 경로를 통해 트래픽을 라우팅합니다. 서브넷 및 가상 네트워크의 id도 각 요청과 함께 전송 됩니다. 그런 다음 관리자는 VNet의 특정 서브넷에서 요청을 받을 수 있도록 하는 Cognitive Services 리소스에 대한 네트워크 규칙을 구성할 수 있습니다. 이러한 네트워크 규칙을 통해 액세스 권한을 부여 받은 클라이언트는 데이터에 액세스 하기 위해 Cognitive Services 리소스의 권한 부여 요구 사항을 계속 충족 해야 합니다.

각 Cognitive Services 리소스는 [IP 네트워크 규칙과](#grant-access-from-an-internet-ip-range)결합 될 수 있는 최대 100 가상 네트워크 규칙을 지원 합니다.

### <a name="required-permissions"></a>필요한 사용 권한

Cognitive Services 리소스에 가상 네트워크 규칙을 적용 하려면 추가할 서브넷에 대한 적절 한 권한이 사용자에 게 있어야 합니다. 필요한 권한은 기본 *참가자* 역할 또는 *Cognitive Services 기여자* 역할입니다. 필요한 사용 권한을 사용자 지정 역할 정의에 추가할 수도 있습니다.

다른 Azure AD 테넌트의 일부인 구독을 포함 하 여, Cognitive Services 리소스 및 액세스 권한이 부여 된 가상 네트워크가 서로 다른 구독에 있을 수 있습니다.

> [!NOTE]
> 다른 Azure Active Directory 테넌트에 속한 가상 네트워크의 서브넷에 대한 액세스 권한을 부여 하는 규칙 구성은 현재 Powershell, CLI 및 REST Api를 통해서만 지원 됩니다. 이러한 규칙은 포털에서 볼 수 있지만 Azure Portal를 통해 구성할 수 없습니다.

### <a name="managing-virtual-network-rules"></a>가상 네트워크 규칙 관리

Azure Portal, PowerShell 또는 Azure CLI를 통해 Cognitive Services 리소스에 대한 가상 네트워크 규칙을 관리할 수 있습니다.

# <a name="azure-portaltabportal"></a>[Azure Portal](#tab/portal)

1. 보안을 유지 하려는 Cognitive Services 리소스로 이동 합니다.

1. **가상 네트워크**라는 **리소스 관리** 메뉴를 선택 합니다.

1. **선택한 네트워크**에서 액세스를 허용하도록 선택했는지 확인합니다.

1. 기존 네트워크 규칙을 사용 하 여 가상 네트워크에 대한 액세스 권한을 부여 하려면 **가상**네트워크에서 **기존 가상 네트워크 추가**를 선택 합니다.

   ![기존 vNet 추가](media/vnet/virtual-network-add-existing.png)

1. **가상 네트워크** 및 **서브넷** 옵션을 선택한 다음 **사용**을 선택 합니다.

   ![기존 vNet 세부 정보 추가](media/vnet/virtual-network-add-existing-details.png)

1. 새 가상 네트워크를 만들고 액세스 권한을 부여 하려면 **새 가상 네트워크 추가**를 선택 합니다.

   ![새 vNet 추가](media/vnet/virtual-network-add-new.png)

1. 새 가상 네트워크를 만드는 데 필요한 정보를 제공 하 고 **만들기**를 선택 합니다.

   ![VNet 만들기](media/vnet/virtual-network-create.png)

    > [!NOTE]
    > Azure Cognitive Services에 대한 서비스 엔드포인트이 선택한 가상 네트워크 및 서브넷에 대해 이전에 구성 되지 않은 경우이 작업의 일부로 구성할 수 있습니다.
    >
    > 현재는 규칙을 만드는 동안 동일한 Azure Active Directory 테넌트에 속한 가상 네트워크만 선택할 수 있도록 표시 됩니다. 다른 테넌트에 속한 가상 네트워크의 서브넷에 대한 액세스 권한을 부여 하려면 Powershell, CLI 또는 REST Api를 사용 하세요.

1. 가상 네트워크 또는 서브넷 규칙을 제거 **하려면 ...을 선택 하** 여 가상 네트워크 또는 서브넷에 대한 상황에 맞는 메뉴를 열고 **제거**를 선택 합니다.

   ![VNet 제거](media/vnet/virtual-network-remove.png)

1. **저장**을 선택하여 변경 내용을 적용합니다.

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

1. [Azure PowerShell](/powershell/azure/install-az-ps) 를 설치 하 고 [로그인](/powershell/azure/authenticate-azureps)하거나 **체험**을 선택 합니다.

1. 가상 네트워크 규칙을 나열합니다.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
    }
    (Get-AzCognitiveServicesAccountNetworkRuleSet @parameters).VirtualNetworkRules
    ```

1. 기존 가상 네트워크 및 서브넷에서 Azure Cognitive Services에 대한 서비스 엔드포인트을 사용 하도록 설정 합니다.

    ```azurepowershell-interactive
    Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" `
        -Name "myvnet" | Set-AzVirtualNetworkSubnetConfig -Name "mysubnet" `
        -AddressPrefix "10.0.0.0/24" `
        -ServiceEndpoint "Microsoft.CognitiveServices" | Set-AzVirtualNetwork
    ```

1. 가상 네트워크 및 서브넷에 대한 네트워크 규칙을 추가합니다.

    ```azurepowershell-interactive
    $subParameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myvnet"
    }
    $subnet = Get-AzVirtualNetwork @subParameters | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"

    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -VirtualNetworkResourceId $subnet.Id
    }
    Add-AzCognitiveServicesAccountNetworkRule @parameters
    ```

    > [!TIP]
    > 다른 Azure AD 테넌트에 속한 VNet의 서브넷에 대한 네트워크 규칙을 추가 하려면 "/subscriptions/subscription-ID/resourceGroups/resourceGroup-Name/providers/Microsoft.Network/virtualNetworks/vNet-name/subnets/subnet-name" 형식으로 정규화 된 **VirtualNetworkResourceId** 매개 변수를 사용 합니다.

1. 가상 네트워크 및 서브넷에 대한 네트워크 규칙을 제거합니다.

    ```azurepowershell-interactive
    $subParameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myvnet"
    }
    $subnet = Get-AzVirtualNetwork @subParameters | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"

    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -VirtualNetworkResourceId $subnet.Id
    }
    Remove-AzCognitiveServicesAccountNetworkRule @parameters
    ```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

1. [Azure CLI](/cli/azure/install-azure-cli) 를 설치 하 고 [로그인](/cli/azure/authenticate-azure-cli)하거나 **체험**을 선택 합니다.

1. 가상 네트워크 규칙을 나열합니다.

    ```azurecli-interactive
    az cognitiveservices account network-rule list \
        -g "myresourcegroup" -n "myaccount" \
        --query virtualNetworkRules
    ```

1. 기존 가상 네트워크 및 서브넷에서 Azure Cognitive Services에 대한 서비스 엔드포인트을 사용 하도록 설정 합니다.

    ```azurecli-interactive
    az network vnet subnet update -g "myresourcegroup" -n "mysubnet" \
    --vnet-name "myvnet" --service-endpoints "Microsoft.CognitiveServices"
    ```

1. 가상 네트워크 및 서브넷에 대한 네트워크 규칙을 추가합니다.

    ```azurecli-interactive
    $subnetid=(az network vnet subnet show \
        -g "myresourcegroup" -n "mysubnet" --vnet-name "myvnet" \
        --query id --output tsv)

    # Use the captured subnet identifier as an argument to the network rule addition
    az cognitiveservices account network-rule add \
        -g "myresourcegroup" -n "myaccount" \
        --subnet $subnetid
    ```

    > [!TIP]
    > 다른 Azure AD 테넌트에 속한 VNet의 서브넷에 대한 규칙을 추가 하려면 "/subscriptions/subscription-ID/resourceGroups/resourceGroup-Name/providers/Microsoft.Network/virtualNetworks/vNet-name/subnets/subnet-name" 형식으로 정규화 된 서브넷 ID를 사용 합니다.
    > 
    > **Subscription** 매개 변수를 사용 하 여 다른 Azure AD 테넌트에 속한 VNet의 서브넷 ID를 검색할 수 있습니다.

1. 가상 네트워크 및 서브넷에 대한 네트워크 규칙을 제거합니다.

    ```azurecli-interactive
    $subnetid=(az network vnet subnet show \
        -g "myresourcegroup" -n "mysubnet" --vnet-name "myvnet" \
        --query id --output tsv)

    # Use the captured subnet identifier as an argument to the network rule removal
    az cognitiveservices account network-rule remove \
        -g "myresourcegroup" -n "myaccount" \
        --subnet $subnetid
    ```
***

> [!IMPORTANT]
> 반드시 [기본 규칙](#change-the-default-network-access-rule)을 **거부**로 설정해야 합니다. 그렇지 않으면 네트워크 규칙이 적용되지 않습니다.

## <a name="grant-access-from-an-internet-ip-range"></a>인터넷 IP 범위의 액세스 허가

특정 공용 인터넷 IP 주소 범위에서 액세스할 수 있도록 Cognitive Services 리소스를 구성할 수 있습니다. 이 구성은 특정 서비스 및 온-프레미스 네트워크에 대한 액세스 권한을 부여 하 여 일반 인터넷 트래픽을 효과적으로 차단 합니다.

`16.17.18.0/24` 형식으로 [CIDR 표기법](https://tools.ietf.org/html/rfc4632) 을 사용 하 여 허용 되는 인터넷 주소 범위를 제공 하거나 `16.17.18.19`와 같은 개별 IP 주소를 제공 합니다.

   > [!Tip]
   > "/31" 또는 "/32" 접두사 크기를 사용하는 작은 주소 범위는 지원되지 않습니다. 이러한 범위는 개별 IP 주소 규칙을 사용하여 구성해야 합니다.

IP 네트워크 규칙은 **공용 인터넷** IP 주소에 대해서만 허용됩니다. 사설망에 예약된 IP 주소 범위([RFC 1918](https://tools.ietf.org/html/rfc1918#section-3)에 정의된 대로)는 IP 규칙에서 허용되지 않습니다. 개인 네트워크에는 `10.*`, `172.16.*` - `172.31.*`및 `192.168.*`으로 시작 하는 주소가 포함 됩니다.

   > [!NOTE]
   > IP 네트워크 규칙은 Cognitive Services 리소스와 동일한 Azure 지역에서 시작 되는 요청에 영향을 주지 않습니다. 동일한 지역 요청을 허용하려면 [가상 네트워크 규칙](#grant-access-from-a-virtual-network)을 사용하세요.

현재 IPv4 주소만 지원됩니다. 각 Cognitive Services 리소스는 [가상 네트워크 규칙과](#grant-access-from-a-virtual-network)결합 될 수 있는 최대 100 IP 네트워크 규칙을 지원 합니다.

### <a name="configuring-access-from-on-premises-networks"></a>온-프레미스 네트워크에서의 액세스 구성

IP 네트워크 규칙을 사용 하 여 온-프레미스 네트워크에서 Cognitive Services 리소스에 대한 액세스 권한을 부여 하려면 네트워크에서 사용 하는 인터넷 연결 IP 주소를 식별 해야 합니다. 네트워크 관리자에게 도움을 요청합니다.

공용 피어 링 또는 Microsoft 피어 링에 대해 [express](../expressroute/expressroute-introduction.md) 경로를 온-프레미스에서 사용 하는 경우에는 NAT IP 주소를 식별 해야 합니다. 공용 피어 링의 경우 기본적으로 각 Express 경로 회로는 두 개의 NAT IP 주소를 사용 합니다. 트래픽이 Microsoft Azure 네트워크 백본으로 들어갈 때 Azure 서비스 트래픽에 적용 됩니다. Microsoft 피어 링의 경우 사용 되는 NAT IP 주소는 고객 제공 이거나 서비스 공급자가 제공 합니다. 서비스 리소스에 대한 액세스를 허용하려면 리소스 IP 방화벽 설정에서 이러한 공용 IP 주소를 허용해야 합니다. ExpressRoute 회로 IP 주소를 찾으려면 Azure Portal을 통해 [ExpressRoute에서 지원 티켓을 엽니다](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview). [ExpressRoute 공용 및 Microsoft 피어링을 위한 NAT](../expressroute/expressroute-nat.md#nat-requirements-for-azure-public-peering)에 대해 자세히 알아보세요.

### <a name="managing-ip-network-rules"></a>IP 네트워크 규칙 관리

Azure Portal, PowerShell 또는 Azure CLI를 통해 Cognitive Services 리소스에 대한 IP 네트워크 규칙을 관리할 수 있습니다.

# <a name="azure-portaltabportal"></a>[Azure Portal](#tab/portal)

1. 보안을 유지 하려는 Cognitive Services 리소스로 이동 합니다.

1. **가상 네트워크**라는 **리소스 관리** 메뉴를 선택 합니다.

1. **선택한 네트워크**에서 액세스를 허용하도록 선택했는지 확인합니다.

1. 인터넷 IP 범위에 대한 액세스 권한을 부여 하려면 **방화벽** > **주소 범위**아래에 IP 주소 또는 주소 범위 ( [CIDR 형식](https://tools.ietf.org/html/rfc4632))를 입력 합니다. 유효한 공용 IP (예약 되지 않은) 주소만 허용 됩니다.

   ![IP 범위 추가](media/vnet/virtual-network-add-ip-range.png)

1. IP 네트워크 규칙을 제거 하려면 주소 범위 옆에 있는 <span class="docon docon-delete x-hidden-focus"></span> 휴지통 아이콘을 선택 합니다.

   ![IP 범위 삭제](media/vnet/virtual-network-delete-ip-range.png)

1. **저장**을 선택하여 변경 내용을 적용합니다.

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

1. [Azure PowerShell](/powershell/azure/install-az-ps) 를 설치 하 고 [로그인](/powershell/azure/authenticate-azureps)하거나 **체험**을 선택 합니다.

1. IP 네트워크 규칙을 나열합니다.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
    }
    (Get-AzCognitiveServicesAccountNetworkRuleSet @parameters).IPRules
    ```

1. 개별 IP 주소에 대한 네트워크 규칙을 추가합니다.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -IPAddressOrRange "16.17.18.19"
    }
    Add-AzCognitiveServicesAccountNetworkRule @parameters
    ```

1. IP 주소 범위에 대한 네트워크 규칙을 추가합니다.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -IPAddressOrRange "16.17.18.0/24"
    }
    Add-AzCognitiveServicesAccountNetworkRule @parameters
    ```

1. 개별 IP 주소에 대한 네트워크 규칙을 제거합니다.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -IPAddressOrRange "16.17.18.19"
    }
    Remove-AzCognitiveServicesAccountNetworkRule @parameters
    ```

1. IP 주소 범위에 대한 네트워크 규칙을 제거합니다.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -IPAddressOrRange "16.17.18.0/24"
    }
    Remove-AzCognitiveServicesAccountNetworkRule @parameters
    ```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

1. [Azure CLI](/cli/azure/install-azure-cli) 를 설치 하 고 [로그인](/cli/azure/authenticate-azure-cli)하거나 **체험**을 선택 합니다.

1. IP 네트워크 규칙을 나열합니다.

    ```azurecli-interactive
    az cognitiveservices account network-rule list \
        -g "myresourcegroup" -n "myaccount" --query ipRules
    ```

1. 개별 IP 주소에 대한 네트워크 규칙을 추가합니다.

    ```azurecli-interactive
    az cognitiveservices account network-rule add \
        -g "myresourcegroup" -n "myaccount" \
        --ip-address "16.17.18.19"
    ```

1. IP 주소 범위에 대한 네트워크 규칙을 추가합니다.

    ```azurecli-interactive
    az cognitiveservices account network-rule add \
        -g "myresourcegroup" -n "myaccount" \
        --ip-address "16.17.18.0/24"
    ```

1. 개별 IP 주소에 대한 네트워크 규칙을 제거합니다.

    ```azurecli-interactive
    az cognitiveservices account network-rule remove \
        -g "myresourcegroup" -n "myaccount" \
        --ip-address "16.17.18.19"
    ```

1. IP 주소 범위에 대한 네트워크 규칙을 제거합니다.

    ```azurecli-interactive
    az cognitiveservices account network-rule remove \
        -g "myresourcegroup" -n "myaccount" \
        --ip-address "16.17.18.0/24"
    ```

***

> [!IMPORTANT]
> 반드시 [기본 규칙](#change-the-default-network-access-rule)을 **거부**로 설정해야 합니다. 그렇지 않으면 네트워크 규칙이 적용되지 않습니다.

## <a name="next-steps"></a>다음 단계

* 다양 한 [Azure Cognitive Services](welcome.md) 살펴보기
* [Azure Virtual Network 서비스 엔드포인트](../virtual-network/virtual-network-service-endpoints-overview.md) 에 대한 자세한 정보