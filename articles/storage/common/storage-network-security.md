---
title: Azure Storage 방화벽 및 가상 네트워크 구성 | Microsoft Docs
description: 스토리지 계정에 대한 계층화된 네트워크 보안을 구성합니다.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: tamram
ms.reviewer: santoshc
ms.subservice: common
ms.openlocfilehash: 2c3b329aa767fbe9795c90ca236008210576fe12
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/22/2020
ms.locfileid: "76514734"
---
# <a name="configure-azure-storage-firewalls-and-virtual-networks"></a>Azure Storage 방화벽 및 가상 네트워크 구성

Azure Storage는 계층화된 보안 모델을 제공합니다. 이 모델을 사용 하면 사용 하는 네트워크의 유형 및 하위 집합에 따라 응용 프로그램 및 엔터프라이즈 환경에서 요구 하는 저장소 계정에 대 한 액세스 수준을 보호 하 고 제어할 수 있습니다. 네트워크 규칙이 구성 된 경우 지정 된 네트워크 집합을 통해 데이터를 요청 하는 응용 프로그램만 저장소 계정에 액세스할 수 있습니다. 저장소 계정에 대 한 액세스를 지정 된 IP 주소, IP 범위 또는 Azure Virtual Network (VNet)의 서브넷 목록에서 시작 되는 요청으로 제한할 수 있습니다.

저장소 계정에는 인터넷을 통해 액세스할 수 있는 공용 엔드포인트이 있습니다. [저장소 계정에 대 한 개인 엔드포인트](storage-private-endpoints.md)을 만들어 vnet에서 저장소 계정으로 개인 IP 주소를 할당 하 고 개인 링크를 통해 vnet과 저장소 계정 간의 모든 트래픽을 보호할 수도 있습니다. Azure storage 방화벽은 저장소 계정의 공용 엔드포인트에 대 한 액세스 제어 액세스를 제공 합니다. 또한 개인 엔드포인트을 사용 하는 경우 방화벽을 사용 하 여 공용 엔드포인트을 통해 모든 액세스를 차단할 수 있습니다. 저장소 방화벽 구성을 사용 하면 신뢰할 수 있는 Azure 플랫폼 서비스를 선택 하 여 저장소 계정에 안전 하 게 액세스할 수도 있습니다.

네트워크 규칙이 적용 되는 경우 저장소 계정에 액세스 하는 응용 프로그램은 여전히 요청에 대 한 적절 한 권한 부여가 필요 합니다. 권한 부여는 blob 및 큐에 대 한 Azure Active Directory (Azure AD) 자격 증명, 유효한 계정 액세스 키 또는 SAS 토큰을 사용 하 여 지원 됩니다.

> [!IMPORTANT]
> 저장소 계정에 대 한 방화벽 규칙을 켜면 요청이 Azure Virtual Network (VNet) 내에서 또는 허용 되는 공용 IP 주소에서 시작 하는 서비스에서 시작 되는 경우를 제외 하 고 기본적으로 들어오는 데이터에 대 한 요청이 차단 됩니다. 차단되는 요청에는 다른 Azure 서비스, Azure Portal, 로깅 및 메트릭 서비스 등이 포함됩니다.
>
> 서비스 인스턴스를 호스팅하는 서브넷의 트래픽을 허용 하 여 VNet 내에서 작동 하는 Azure 서비스에 대 한 액세스 권한을 부여할 수 있습니다. 또한 아래에 설명 된 [예외](#exceptions) 메커니즘을 통해 제한 된 수의 시나리오를 사용할 수 있습니다. Azure Portal를 통해 저장소 계정의 데이터에 액세스 하려면 사용자가 설정 하는 신뢰할 수 있는 경계 (IP 또는 VNet) 내의 컴퓨터에 있어야 합니다.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="scenarios"></a>시나리오

저장소 계정을 보호 하려면 먼저 공용 엔드포인트에서 모든 네트워크 (인터넷 트래픽 포함)의 트래픽에 대 한 액세스를 거부 하는 규칙을 먼저 구성 해야 합니다. 그런 다음 특정 Vnet 트래픽에 대 한 액세스 권한을 부여 하는 규칙을 구성 해야 합니다. 또한 특정 인터넷 또는 온-프레미스 클라이언트에서 연결을 사용 하도록 설정 하 여 공용 인터넷 IP 주소 범위 선택에서 트래픽에 대 한 액세스를 허용 하도록 규칙을 구성할 수 있습니다. 이 구성을 사용하면 애플리케이션에 대한 보안 네트워크 경계를 구축할 수 있습니다.

특정 가상 네트워크 및 공용 IP 주소 범위에서 동일한 저장소 계정에 대 한 액세스를 허용 하는 방화벽 규칙을 결합할 수 있습니다. 저장소 방화벽 규칙은 기존 저장소 계정에 적용 하거나 새 저장소 계정을 만들 때 적용할 수 있습니다.

저장소 방화벽 규칙은 저장소 계정의 공용 엔드포인트에 적용 됩니다. 저장소 계정의 개인 엔드포인트에 대 한 트래픽을 허용 하는 방화벽 액세스 규칙은 필요 하지 않습니다. 개인 엔드포인트의 생성을 승인 하는 프로세스는 개인 엔드포인트을 호스트 하는 서브넷의 트래픽에 대 한 암시적 액세스를 부여 합니다.

네트워크 규칙은 REST 및 SMB를 포함하여 Azure Storage에 대한 모든 네트워크 프로토콜에 적용됩니다. Azure Portal, Storage 탐색기 및 AZCopy와 같은 도구를 사용 하 여 데이터에 액세스 하려면 명시적 네트워크 규칙을 구성 해야 합니다.

네트워크 규칙이 적용되면 모든 요청에 적용됩니다. 특정 IP 주소에 대한 액세스 권한을 부여하는 SAS 토큰은 토큰 소유자의 액세스를 제한하지만, 구성된 네트워크 규칙 이외의 새 액세스 권한은 부여하지 않습니다.

가상 머신 디스크 트래픽(탑재 및 분리 작업 및 디스크 IO 포함)은 네트워크 규칙의 영향을 받지 않습니다. 페이지 Blob에 대한 REST 액세스는 네트워크 규칙에 의해 보호됩니다.

클래식 스토리지 계정은 방화벽 및 가상 네트워크를 지원하지 않습니다.

예외를 만들어 VM 백업 및 복원에 적용되는 네트워크 규칙이 있는 스토리지 계정에서 관리되지 않는 디스크를 사용할 수 있습니다. 이 프로세스는 이 문서의 [예외](#exceptions) 섹션에서 설명하고 있습니다. 방화벽 예외는 Azure에서 이미 관리되고 있으므로 관리 디스크에는 적용되지 않습니다.

## <a name="change-the-default-network-access-rule"></a>기본 네트워크 액세스 규칙 변경

기본적으로 스토리지 계정은 네트워크에 있는 모든 클라이언트로부터의 연결을 허용합니다. 선택한 네트워크에 대한 액세스를 제한하려면 먼저 기본 동작을 변경해야 합니다.

> [!WARNING]
> 네트워크 규칙을 변경하면 Azure Storage에 연결하는 애플리케이션의 기능에 영향을 미칠 수 있습니다. 액세스 **권한을 부여** 하는 특정 네트워크 규칙도 적용 되지 않는 한 기본 네트워크 규칙을 **deny** 로 설정 하면 데이터에 대 한 모든 액세스가 차단 됩니다. 액세스를 거부하도록 기본 규칙을 변경하기 전에 네트워크 규칙을 사용하여 허용된 모든 네트워크에 대한 액세스를 허가해야 합니다.

### <a name="managing-default-network-access-rules"></a>기본 네트워크 액세스 규칙 관리

스토리지 계정에 대한 기본 네트워크 액세스 규칙은 Azure Portal, PowerShell 또는 CLIv2를 통해 관리할 수 있습니다.

#### <a name="azure-portal"></a>Azure Portal

1. 보호하려는 스토리지 계정으로 이동합니다.

1. **Firewall 및 Virtual Network**이라는 설정 메뉴를 클릭합니다.

1. 기본적으로 액세스를 거부하려면 **선택한 네트워크**에서 액세스를 허용하도록 선택합니다. 모든 네트워크의 트래픽을 허용하려면 **모든 네트워크**에서 액세스를 허용하도록 선택합니다.

1. **저장**을 클릭하여 변경 내용을 적용합니다.

#### <a name="powershell"></a>PowerShell

1. [Azure PowerShell](/powershell/azure/install-Az-ps)을 설치하고 [로그인](/powershell/azure/authenticate-azureps)합니다.

1. 스토리지 계정에 대한 기본 규칙의 상태를 표시합니다.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").DefaultAction
    ```

1. 기본적으로 네트워크 액세스를 거부하도록 기본 규칙을 설정합니다.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Deny
    ```

1. 기본적으로 네트워크 액세스를 허용하도록 기본 규칙을 설정합니다.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Allow
    ```

#### <a name="cliv2"></a>CLIv2

1. [Azure CLI](/cli/azure/install-azure-cli)를 설치하고 [로그인](/cli/azure/authenticate-azure-cli)합니다.

1. 스토리지 계정에 대한 기본 규칙의 상태를 표시합니다.

    ```azurecli
    az storage account show --resource-group "myresourcegroup" --name "mystorageaccount" --query networkRuleSet.defaultAction
    ```

1. 기본적으로 네트워크 액세스를 거부하도록 기본 규칙을 설정합니다.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --default-action Deny
    ```

1. 기본적으로 네트워크 액세스를 허용하도록 기본 규칙을 설정합니다.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --default-action Allow
    ```

## <a name="grant-access-from-a-virtual-network"></a>가상 네트워크의 액세스 허가

특정 서브넷 에서만 액세스를 허용 하도록 저장소 계정을 구성할 수 있습니다. 허용 되는 서브넷은 동일한 구독의 VNet 또는 다른 Azure Active Directory 테넌트에 속한 구독을 포함 하 여 다른 구독의 VNet에 속할 수 있습니다.

VNet 내의 Azure Storage에 대해 [서비스 엔드포인트](/azure/virtual-network/virtual-network-service-endpoints-overview)를 사용하도록 설정합니다. 서비스 엔드포인트은 VNet에서 Azure Storage 서비스에 대 한 최적의 경로를 통해 트래픽을 라우팅합니다. 서브넷 및 가상 네트워크의 id도 각 요청과 함께 전송 됩니다. 그런 다음 관리자는 VNet의 특정 서브넷에서 요청을 받을 수 있도록 저장소 계정에 대 한 네트워크 규칙을 구성할 수 있습니다. 이러한 네트워크 규칙을 통해 액세스가 허용되는 클라이언트에서 데이터에 액세스하려면 스토리지 계정의 권한 부여 요구 사항을 계속 충족해야 합니다.

각 스토리지 계정은 [IP 네트워크 규칙](#grant-access-from-an-internet-ip-range)과 결합될 수 있는 최대 100개의 가상 네트워크 규칙을 지원합니다.

### <a name="available-virtual-network-regions"></a>사용 가능한 가상 네트워크 지역

일반적으로 서비스 엔드포인트는 동일한 Azure 지역의 가상 네트워크와 서비스 인스턴스 간에 작동합니다. Azure Storage에서 서비스 엔드포인트를 사용하는 경우 이 범위에는 [쌍을 이루는 지역](/azure/best-practices-availability-paired-regions)이 포함됩니다. 서비스 엔드포인트를 사용하면 지역별 장애 조치 및 읽기 전용 RA-GRS(지역 중복 스토리지) 인스턴스에 대한 액세스 중에 연속성을 유지할 수 있습니다. 가상 네트워크에서 스토리지 계정으로의 액세스를 허가하는 네트워크 규칙은 모든 RA-GRS 인스턴스에 대한 액세스도 허가합니다.

지역 가동 중단 시 재해 복구를 계획하는 경우 쌍을 이루는 지역에 VNet을 미리 만들어야 합니다. 이러한 대체 가상 네트워크에서 액세스할 수 있도록 허용하는 네트워크 규칙을 사용하여 Azure Storage에 대한 서비스 엔드포인트를 사용하도록 설정합니다. 그런 다음, 이러한 규칙을 지역 중복 스토리지 계정에 적용합니다.

> [!NOTE]
> 서비스 엔드포인트는 가상 네트워크 지역 및 지정된 지역 쌍 외부의 트래픽에는 적용되지 않습니다. 가상 네트워크에서 액세스할 수 있도록 허용하는 네트워크 규칙만 스토리지 계정의 주 지역 또는 쌍을 이루는 지정된 지역의 스토리지 계정에 적용할 수 있습니다.

### <a name="required-permissions"></a>필요한 사용 권한

가상 네트워크 규칙을 스토리지 계정에 적용하려면 추가되는 서브넷에 대한 적절한 권한이 사용자에게 있어야 합니다. 필요한 권한은 *서브넷에 서비스 조인*이며, *스토리지 계정 기여자* 기본 제공 역할에 포함됩니다. 사용자 지정 역할 정의에 추가할 수도 있습니다.

액세스 권한이 부여 된 저장소 계정 및 가상 네트워크는 다른 Azure AD 테넌트의 일부인 구독을 포함 하 여 다른 구독에 있을 수 있습니다.

> [!NOTE]
> 다른 Azure Active Directory 테넌트에 속한 가상 네트워크의 서브넷에 대 한 액세스 권한을 부여 하는 규칙 구성은 현재 Powershell, CLI 및 REST Api를 통해서만 지원 됩니다. 이러한 규칙은 포털에서 볼 수 있지만 Azure Portal를 통해 구성할 수 없습니다.

### <a name="managing-virtual-network-rules"></a>가상 네트워크 규칙 관리

Azure Portal, PowerShell 또는 CLIv2를 통해 스토리지 계정에 대한 가상 네트워크 규칙을 관리할 수 있습니다.

#### <a name="azure-portal"></a>Azure Portal

1. 보호하려는 스토리지 계정으로 이동합니다.

1. **Firewall 및 Virtual Network**이라는 설정 메뉴를 클릭합니다.

1. **선택한 네트워크**에서 액세스를 허용하도록 선택했는지 확인합니다.

1. 새 네트워크 규칙을 사용하여 가상 네트워크에 대한 액세스 권한을 부여하려면 **가상 네트워크** 아래에서 **기존 가상 네트워크 추가**를 클릭하고, **가상 네트워크** 및 **서브넷** 옵션을 선택한 다음, **추가**를 클릭합니다. 새 가상 네트워크를 만들고 액세스 권한을 부여하려면 **새 가상 네트워크 추가**를 클릭합니다. 새 가상 네트워크를 만드는 데 필요한 정보를 제공한 다음, **만들기**를 클릭합니다.

    > [!NOTE]
    > 이전에 Azure Storage에 대한 서비스 엔드포인트가 선택한 가상 네트워크 및 서브넷에 구성되지 않은 경우 이 작업의 일환으로 구성할 수 있습니다.
    >
    > 현재는 규칙을 만드는 동안 동일한 Azure Active Directory 테넌트에 속한 가상 네트워크만 선택할 수 있도록 표시 됩니다. 다른 테넌트에 속한 가상 네트워크의 서브넷에 대 한 액세스 권한을 부여 하려면 Powershell, CLI 또는 REST Api를 사용 하세요.

1. 가상 네트워크 또는 서브넷 규칙을 제거하려면 **...** 를 클릭하여 가상 네트워크 또는 서브넷에 대한 상황에 맞는 메뉴를 열고 **제거**를 클릭합니다.

1. **저장**을 클릭하여 변경 내용을 적용합니다.

#### <a name="powershell"></a>PowerShell

1. [Azure PowerShell](/powershell/azure/install-Az-ps)을 설치하고 [로그인](/powershell/azure/authenticate-azureps)합니다.

1. 가상 네트워크 규칙을 나열합니다.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").VirtualNetworkRules
    ```

1. 기존 가상 네트워크 및 서브넷에서 Azure Storage에 대한 서비스 엔드포인트를 사용하도록 설정합니다.

    ```powershell
    Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Set-AzVirtualNetworkSubnetConfig -Name "mysubnet" -AddressPrefix "10.0.0.0/24" -ServiceEndpoint "Microsoft.Storage" | Set-AzVirtualNetwork
    ```

1. 가상 네트워크 및 서브넷에 대한 네트워크 규칙을 추가합니다.

    ```powershell
    $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -VirtualNetworkResourceId $subnet.Id
    ```

    > [!TIP]
    > 다른 Azure AD 테넌트에 속한 VNet의 서브넷에 대 한 네트워크 규칙을 추가 하려면 "/subscriptions/subscription-ID/resourceGroups/resourceGroup-Name/providers/Microsoft.Network/virtualNetworks/vNet-name/subnets/subnet-name" 형식으로 정규화 된 **VirtualNetworkResourceId** 매개 변수를 사용 합니다.

1. 가상 네트워크 및 서브넷에 대한 네트워크 규칙을 제거합니다.

    ```powershell
    $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -VirtualNetworkResourceId $subnet.Id
    ```

> [!IMPORTANT]
> 반드시 [기본 규칙](#change-the-default-network-access-rule)을 **거부**로 설정해야 합니다. 그렇지 않으면 네트워크 규칙이 적용되지 않습니다.

#### <a name="cliv2"></a>CLIv2

1. [Azure CLI](/cli/azure/install-azure-cli)를 설치하고 [로그인](/cli/azure/authenticate-azure-cli)합니다.

1. 가상 네트워크 규칙을 나열합니다.

    ```azurecli
    az storage account network-rule list --resource-group "myresourcegroup" --account-name "mystorageaccount" --query virtualNetworkRules
    ```

1. 기존 가상 네트워크 및 서브넷에서 Azure Storage에 대한 서비스 엔드포인트를 사용하도록 설정합니다.

    ```azurecli
    az network vnet subnet update --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --service-endpoints "Microsoft.Storage"
    ```

1. 가상 네트워크 및 서브넷에 대한 네트워크 규칙을 추가합니다.

    ```azurecli
    $subnetid=(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --subnet $subnetid
    ```

    > [!TIP]
    > 다른 Azure AD 테넌트에 속한 VNet의 서브넷에 대 한 규칙을 추가 하려면 "/subscriptions/\<subscription-ID\>/Wsourceg/\<resourceGroup-Name\>/providers/Microsoft.Network/virtualNetworks/\<vNet-name\>/subnets/\<" 형식으로 정규화 된 서브넷 ID를 사용 합니다.
    >
    > **Subscription** 매개 변수를 사용 하 여 다른 Azure AD 테넌트에 속한 VNet의 서브넷 ID를 검색할 수 있습니다.

1. 가상 네트워크 및 서브넷에 대한 네트워크 규칙을 제거합니다.

    ```azurecli
    $subnetid=(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --subnet $subnetid
    ```

> [!IMPORTANT]
> 반드시 [기본 규칙](#change-the-default-network-access-rule)을 **거부**로 설정해야 합니다. 그렇지 않으면 네트워크 규칙이 적용되지 않습니다.

## <a name="grant-access-from-an-internet-ip-range"></a>인터넷 IP 범위의 액세스 허가

특정 공용 인터넷 IP 주소 범위에서 액세스할 수 있도록 스토리지 계정을 구성할 수 있습니다. 이 구성은 특정 인터넷 기반 서비스와 온-프레미스 네트워크에 대한 액세스 권한을 부여하고 일반 인터넷 트래픽을 차단합니다.

*16.17.18.0/24* 형식의 [CIDR 표기법](https://tools.ietf.org/html/rfc4632)을 사용하거나 개별 IP 주소(예: *16.17.18.19*)를 사용하여 허용된 인터넷 주소 범위를 제공합니다.

   > [!NOTE]
   > "/31" 또는 "/32" 접두사 크기를 사용하는 작은 주소 범위는 지원되지 않습니다. 이러한 범위는 개별 IP 주소 규칙을 사용하여 구성해야 합니다.

IP 네트워크 규칙은 **공용 인터넷** IP 주소에 대해서만 허용됩니다. 사설망에 예약된 IP 주소 범위([RFC 1918](https://tools.ietf.org/html/rfc1918#section-3)에 정의된 대로)는 IP 규칙에서 허용되지 않습니다. 사설망에는 _10.*_ , _172.16.*_  - _172.31.*_ 및 _192.168.*_ 로 시작하는 주소가 포함됩니다.

   > [!NOTE]
   > IP 네트워크 규칙은 스토리지 계정과 동일한 Azure 지역에서 시작된 요청에 영향을 주지 않습니다. 동일한 지역 요청을 허용하려면 [가상 네트워크 규칙](#grant-access-from-a-virtual-network)을 사용하세요.

  > [!NOTE]
  > 저장소 계정과 동일한 지역에 배포 된 서비스는 통신을 위해 개인 Azure IP 주소를 사용 합니다. 따라서 공용 인바운드 IP 주소 범위에 따라 특정 Azure 서비스에 대 한 액세스를 제한할 수 없습니다.

저장소 방화벽 규칙의 구성에는 IPV4 주소만 지원 됩니다.

각 저장소 계정은 최대 100 IP 네트워크 규칙을 지원 합니다.

### <a name="configuring-access-from-on-premises-networks"></a>온-프레미스 네트워크에서의 액세스 구성

IP 네트워크 규칙을 사용하여 온-프레미스 네트워크에서 스토리지 계정으로의 액세스 권한을 부여하려면 네트워크에서 사용되는 인터넷 연결 IP 주소를 식별해야 합니다. 네트워크 관리자에게 도움을 요청합니다.

공용 피어링 또는 Microsoft 피어링을 위해 온-프레미스에서 [ExpressRoute](/azure/expressroute/expressroute-introduction)를 사용하는 경우 사용되는 NAT IP 주소를 식별해야 합니다. 공용 피어링의 경우 기본적으로 각 ExpressRoute 회로는 트래픽이 Microsoft Azure 네트워크 백본으로 들어갈 때 Azure 서비스 트래픽에 적용되는 두 개의 NAT IP 주소를 사용합니다. Microsoft 피어 링의 경우 사용 되는 NAT IP 주소는 고객 제공 이거나 서비스 공급자가 제공 합니다. 서비스 리소스에 대한 액세스를 허용하려면 리소스 IP 방화벽 설정에서 이러한 공용 IP 주소를 허용해야 합니다. ExpressRoute 회로 IP 주소를 찾으려면 Azure Portal을 통해 [ExpressRoute에서 지원 티켓을 엽니다](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview). [ExpressRoute 공용 및 Microsoft 피어링을 위한 NAT](/azure/expressroute/expressroute-nat#nat-requirements-for-azure-public-peering)에 대해 자세히 알아보세요.

### <a name="managing-ip-network-rules"></a>IP 네트워크 규칙 관리

Azure Portal, PowerShell 또는 CLIv2를 통해 스토리지 계정에 대한 IP 네트워크 규칙을 관리할 수 있습니다.

#### <a name="azure-portal"></a>Azure Portal

1. 보호하려는 스토리지 계정으로 이동합니다.

1. **Firewall 및 Virtual Network**이라는 설정 메뉴를 클릭합니다.

1. **선택한 네트워크**에서 액세스를 허용하도록 선택했는지 확인합니다.

1. 인터넷 IP 범위에 대한 액세스 권한을 부여하려면 **방화벽** > **주소 범위** 아래에서 IP 주소 또는 주소 범위(CIDR 형식)를 입력합니다.

1. IP 네트워크 규칙을 제거하려면 주소 범위 옆에 있는 휴지통 아이콘을 클릭합니다.

1. **저장**을 클릭하여 변경 내용을 적용합니다.

#### <a name="powershell"></a>PowerShell

1. [Azure PowerShell](/powershell/azure/install-Az-ps)을 설치하고 [로그인](/powershell/azure/authenticate-azureps)합니다.

1. IP 네트워크 규칙을 나열합니다.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").IPRules
    ```

1. 개별 IP 주소에 대한 네트워크 규칙을 추가합니다.

    ```powershell
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19"
    ```

1. IP 주소 범위에 대한 네트워크 규칙을 추가합니다.

    ```powershell
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.0/24"
    ```

1. 개별 IP 주소에 대한 네트워크 규칙을 제거합니다.

    ```powershell
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19"
    ```

1. IP 주소 범위에 대한 네트워크 규칙을 제거합니다.

    ```powershell
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.0/24"
    ```

> [!IMPORTANT]
> 반드시 [기본 규칙](#change-the-default-network-access-rule)을 **거부**로 설정해야 합니다. 그렇지 않으면 네트워크 규칙이 적용되지 않습니다.

#### <a name="cliv2"></a>CLIv2

1. [Azure CLI](/cli/azure/install-azure-cli)를 설치하고 [로그인](/cli/azure/authenticate-azure-cli)합니다.

1. IP 네트워크 규칙을 나열합니다.

    ```azurecli
    az storage account network-rule list --resource-group "myresourcegroup" --account-name "mystorageaccount" --query ipRules
    ```

1. 개별 IP 주소에 대한 네트워크 규칙을 추가합니다.

    ```azurecli
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.19"
    ```

1. IP 주소 범위에 대한 네트워크 규칙을 추가합니다.

    ```azurecli
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.0/24"
    ```

1. 개별 IP 주소에 대한 네트워크 규칙을 제거합니다.

    ```azurecli
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.19"
    ```

1. IP 주소 범위에 대한 네트워크 규칙을 제거합니다.

    ```azurecli
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.0/24"
    ```

> [!IMPORTANT]
> 반드시 [기본 규칙](#change-the-default-network-access-rule)을 **거부**로 설정해야 합니다. 그렇지 않으면 네트워크 규칙이 적용되지 않습니다.

## <a name="exceptions"></a>예외

네트워크 규칙은 대부분의 시나리오에서 응용 프로그램과 데이터 간의 연결에 대 한 보안 환경을 만드는 데 도움이 됩니다. 그러나 일부 응용 프로그램은 가상 네트워크 또는 IP 주소 규칙을 통해 고유 하 게 격리 될 수 없는 Azure 서비스에 종속 됩니다. 그러나 전체 응용 프로그램 기능을 사용 하려면 이러한 서비스를 저장소에 부여 해야 합니다. 이러한 경우 신뢰할 수 있는 ***Microsoft 서비스 허용*** ... 설정을 사용 하 여 이러한 서비스에서 데이터, 로그 또는 분석에 액세스 하도록 할 수 있습니다.

### <a name="trusted-microsoft-services"></a>신뢰할 수 있는 Microsoft 서비스

일부 Microsoft 서비스는 네트워크 규칙에 포함 될 수 없는 네트워크에서 작동 합니다. 이러한 신뢰할 수 있는 Microsoft 서비스의 하위 집합에 저장소 계정에 대 한 액세스 권한을 부여 하는 동시에 다른 앱에 대 한 네트워크 규칙을 유지할 수 있습니다. 이러한 신뢰할 수 있는 서비스는 강력한 인증을 사용 하 여 저장소 계정에 안전 하 게 연결 합니다. Microsoft 서비스에 대 한 두 가지 신뢰 된 액세스 모드를 사용 하도록 설정 했습니다.

- **구독에 등록 된**일부 서비스의 리소스는 로그 또는 백업 작성과 같은 선택 작업에 대해 **동일한 구독에서** 저장소 계정에 액세스할 수 있습니다.
- 일부 서비스의 리소스에는 시스템 할당 관리 id에 **RBAC 역할을 할당** 하 여 저장소 계정에 대 한 명시적 액세스 권한을 부여할 수 있습니다.


**신뢰할 수 있는 Microsoft 서비스 허용** ... 설정을 사용 하도록 설정 하면 저장소 계정과 동일한 구독에 등록 된 다음 서비스의 리소스에 설명 된 대로 제한 된 작업 집합에 대 한 액세스 권한이 부여 됩니다.

| 서비스                  | 리소스 공급자 이름     | 허용되는 연산                 |
|:------------------------ |:-------------------------- |:---------------------------------- |
| Azure Backup             | Microsoft.RecoveryServices | IAAS 가상 머신에서 관리되지 않는 디스크의 백업 및 복원을 실행합니다. (관리되는 디스크에 필요 없음). [자세히 알아보기](/azure/backup/backup-introduction-to-azure-backup). |
| Azure Data Box           | Microsoft.DataBox          | Data Box를 사용 하 여 Azure로 데이터를 가져올 수 있습니다. [자세히 알아보기](/azure/databox/data-box-overview). |
| Azure DevTest Lab       | Microsoft.DevTestLab       | 사용자 지정 이미지 만들기 및 아티팩트 설치. [자세히 알아보기](/azure/devtest-lab/devtest-lab-overview). |
| Azure Event Grid         | Microsoft.EventGrid        | Blob Storage 이벤트 게시를 사용하도록 설정하고 Event Grid가 스토리지 큐에 게시하도록 허용합니다. [Blob Storage 이벤트](/azure/event-grid/event-sources)와 [큐에 게시](/azure/event-grid/event-handlers)에 대해 알아봅니다. |
| Azure Event Hubs         | Microsoft.EventHub         | Event Hubs 캡처로 데이터를 보관합니다. [자세한 정보](/azure/event-hubs/event-hubs-capture-overview). |
| Azure 파일 동기화          | Microsoft.StorageSync      | Azure 파일 공유를 위해 온-프레미스 파일 서버를 캐시로 변환할 수 있습니다. 다중 사이트 동기화, 빠른 재해 복구 및 클라우드 쪽 백업을 허용 합니다. [자세히 알아보기](../files/storage-sync-files-planning.md) |
| Azure HDInsight          | Microsoft.HDInsight        | 새 HDInsight 클러스터에 대 한 기본 파일 시스템의 초기 콘텐츠를 프로 비전 합니다. [자세히 알아보기](/azure/hdinsight/hdinsight-hadoop-use-blob-storage). |
| Azure 가져오기 내보내기      | Microsoft.ImportExport     | 가져오기/내보내기 서비스를 사용 하 여 azure로 데이터를 가져오고 Azure에서 데이터를 내보낼 수 있습니다. [자세히 알아보기](/azure/storage/common/storage-import-export-service).  |
| Azure Monitor            | Microsoft.Insights         | 리소스 진단 로그, Azure Active Directory 로그인 및 감사 로그 및 Microsoft Intune 로그를 포함 하 여 보안 저장소 계정에 모니터링 데이터를 쓸 수 있습니다. [자세히 알아보기](/azure/monitoring-and-diagnostics/monitoring-roles-permissions-security). |
| Azure 네트워킹         | Microsoft.Network          | 네트워크 트래픽 로그를 저장 및 분석합니다. [자세히 알아보기](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview). |
| Azure Site Recovery      | Microsoft.SiteRecovery     | 방화벽 사용 캐시, 원본 또는 대상 저장소 계정을 사용 하는 경우 Azure IaaS 가상 컴퓨터의 재해 복구에 대 한 복제를 사용 하도록 설정 합니다.  [자세히 알아보기](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication). |

신뢰할 수 있는 **Microsoft 서비스 허용** ... 설정을 사용 하면 해당 리소스 인스턴스에 대 한 [시스템 할당 관리 id](../../active-directory/managed-identities-azure-resources/overview.md) 에 [RBAC 역할](storage-auth-aad.md#assign-rbac-roles-for-access-rights) 을 명시적으로 할당 하는 경우 아래 서비스의 특정 인스턴스에서 저장소 계정에 액세스할 수도 있습니다. 이 경우 인스턴스에 대 한 액세스 범위는 관리 id에 할당 된 RBAC 역할에 해당 합니다.

| 서비스                        | 리소스 공급자 이름          | 용도            |
| :----------------------------- | :------------------------------------- | :---------- |
| Azure Container Registry 작업 | Microsoft.ContainerRegistry/registries | ACR 작업은 컨테이너 이미지를 빌드할 때 저장소 계정에 액세스할 수 있습니다. |
| Azure Data Factory             | Microsoft.DataFactory/factories        | ADF 런타임을 통해 저장소 계정에 대 한 액세스를 허용 합니다. |
| Azure Logic Apps               | Microsoft.Logic/workflows              | 논리 앱이 저장소 계정에 액세스할 수 있도록 합니다. [자세히 알아보기](../../logic-apps/create-managed-service-identity.md#authenticate-access-with-managed-identity). |
| Azure Machine Learning | Microsoft.MachineLearningServices      | 권한 있는 Azure Machine Learning 작업 영역은 실험 출력, 모델 및 로그를 Blob 저장소에 기록 합니다. [자세히 알아보기](/azure/machine-learning/service/how-to-enable-virtual-network#use-a-storage-account-for-your-workspace). |
| Azure SQL Data Warehouse       | Microsoft.Sql                          | PolyBase를 사용 하 여 특정 SQL Database 인스턴스에서 데이터를 가져오고 내보낼 수 있습니다. [자세히 알아보기](/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview). |
| Azure Stream Analytics         | Microsoft.StreamAnalytics             | 스트리밍 작업의 데이터를 Blob 저장소에 쓸 수 있습니다. 이 기능은 현재 미리 보기로 제공됩니다. [자세히 알아보기](/azure/stream-analytics/blob-output-managed-identity). |
| Azure Synapse Analytics        | Synapse/작업 영역          | Synapse Analytics에서 Azure Storage의 데이터에 액세스할 수 있습니다. |


### <a name="storage-analytics-data-access"></a>스토리지 분석 데이터 액세스

경우에 따라 네트워크 경계 밖에서 진단 로그 및 메트릭을 읽을 수 있는 권한이 필요합니다. 저장소 계정에 대 한 신뢰할 수 있는 서비스 액세스를 구성 하는 경우 로그 파일, 메트릭 테이블 또는 둘 다에 대해 읽기 액세스를 허용할 수 있습니다. [스토리지 분석 작업에 대한 자세한 정보](/azure/storage/storage-analytics)

### <a name="managing-exceptions"></a>예외 관리

Azure Portal, PowerShell 또는 Azure CLI v2를 통해 네트워크 규칙 예외를 관리할 수 있습니다.

#### <a name="azure-portal"></a>Azure Portal

1. 보호하려는 스토리지 계정으로 이동합니다.

1. **Firewall 및 Virtual Network**이라는 설정 메뉴를 클릭합니다.

1. **선택한 네트워크**에서 액세스를 허용하도록 선택했는지 확인합니다.

1. **예외** 아래에서 허용하려는 예외를 선택합니다.

1. **저장**을 클릭하여 변경 내용을 적용합니다.

#### <a name="powershell"></a>PowerShell

1. [Azure PowerShell](/powershell/azure/install-Az-ps)을 설치하고 [로그인](/powershell/azure/authenticate-azureps)합니다.

1. 스토리지 계정 네트워크 규칙에 대한 예외를 표시합니다.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount").Bypass
    ```

1. 스토리지 계정 네트워크 규칙에 대한 예외를 구성합니다.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -Bypass AzureServices,Metrics,Logging
    ```

1. 스토리지 계정 네트워크 규칙에 대한 예외를 제거합니다.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -Bypass None
    ```

> [!IMPORTANT]
> 반드시 [기본 규칙](#change-the-default-network-access-rule)을 **거부**로 설정해야 합니다. 그렇지 않으면 예외를 제거해도 효과가 없습니다.

#### <a name="cliv2"></a>CLIv2

1. [Azure CLI](/cli/azure/install-azure-cli)를 설치하고 [로그인](/cli/azure/authenticate-azure-cli)합니다.

1. 스토리지 계정 네트워크 규칙에 대한 예외를 표시합니다.

    ```azurecli
    az storage account show --resource-group "myresourcegroup" --name "mystorageaccount" --query networkRuleSet.bypass
    ```

1. 스토리지 계정 네트워크 규칙에 대한 예외를 구성합니다.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --bypass Logging Metrics AzureServices
    ```

1. 스토리지 계정 네트워크 규칙에 대한 예외를 제거합니다.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --bypass None
    ```

> [!IMPORTANT]
> 반드시 [기본 규칙](#change-the-default-network-access-rule)을 **거부**로 설정해야 합니다. 그렇지 않으면 예외를 제거해도 효과가 없습니다.

## <a name="next-steps"></a>다음 단계

[서비스 엔드포인트](/azure/virtual-network/virtual-network-service-endpoints-overview)에서 Azure 네트워크 서비스 엔드포인트에 대해 자세히 알아봅니다.

[Azure Storage 보안 가이드](../blobs/security-recommendations.md)에서 Azure Storage 보안에 대해 자세히 살펴봅니다.
