---
title: VNet 피어 링-다른 구독 만들기
titlesuffix: Azure Virtual Network
description: 리소스 관리자를 통해 서로 다른 Azure 구독에 존재하는 가상 네트워크 간에 가상 네트워크 피어링을 만드는 방법을 알아봅니다.
services: virtual-network
documentationcenter: ''
author: anavinahar
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/09/2019
ms.author: anavin
ms.openlocfilehash: d085279167b498b13cfb79b97703cfdff7d6dd8a
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77201767"
---
# <a name="create-a-virtual-network-peering---resource-manager-different-subscriptions"></a>가상 네트워크 피어링 만들기 - 리소스 관리자, 서로 다른 구독

이 자습서에서는 리소스 관리자를 통해 만든 가상 네트워크 간의 가상 네트워크 피어링을 만드는 방법을 알아봅니다. 가상 네트워크가 서로 다른 구독에 있습니다. 두 가상 네트워크를 피어링하면 서로 다른 가상 네트워크에 있는 리소스가 같은 가상 네트워크에 있는 리소스인 것처럼 같은 대역폭 및 대기 시간으로 서로 통신할 수 있습니다. [가상 네트워크 피어링](virtual-network-peering-overview.md)에 대해 자세히 알아보세요.

가상 네트워크 피어링을 만드는 단계는 가상 네트워크가 동일한 구독에 있는지 아니면 다른 구독에 있는지에 따라, 그리고 가상 네트워크가 어느 [Azure 배포 모델](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json)를 통해 생성되었는지에 따라 다릅니다. 다음 표에 나온 시나리오를 선택하여 다른 시나리오에서 가상 네트워크 피어링을 만드는 방법을 알아보세요.

|Azure 배포 모델  | Azure 구독  |
|--------- |---------|
|[둘 다 리소스 관리자](tutorial-connect-virtual-networks-portal.md) |동일|
|[하나는 리소스 관리자, 다른 하나는 클래식](create-peering-different-deployment-models.md) |동일|
|[하나는 리소스 관리자, 다른 하나는 클래식](create-peering-different-deployment-models-subscriptions.md) |다름|

클래식 배포 모델을 통해 배포된 두 가상 네트워크 간에는 가상 네트워크 피어링을 만들 수 없습니다. 둘 다 클래식 배포 모델을 통해 생성된 가상 네트워크를 연결해야 할 경우 Azure [VPN Gateway](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)를 사용하여 가상 네트워크를 연결할 수 있습니다.

이 자습서는 동일한 지역에 가상 네트워크를 피어링합니다. 다른 [지원되는 지역](virtual-network-manage-peering.md#cross-region)에 있는 가상 네트워크를 피어링할 수도 있습니다. 가상 네트워크를 피어링하기 전에 [피어링 요구 사항 및 제약 조건](virtual-network-manage-peering.md#requirements-and-constraints)을 이해하는 것이 좋습니다.

[Azure Portal](#portal), Azure CLI([명령줄 인터페이스](#cli)), Azure [PowerShell](#powershell) 또는 [Azure Resource Manager 템플릿](#template)을 사용하여 가상 네트워크 피어링을 만들 수 있습니다. 앞의 도구 링크 중 원하는 도구 링크를 선택하여 원하는 도구를 사용하여 가상 네트워크 피어링을 만드는 단계로 바로 이동하세요.

가상 네트워크가 서로 다른 구독에 있고 구독이 다른 Azure Active Directory 테 넌 트와 연결 되어 있는 경우 계속 하기 전에 다음 단계를 완료 합니다.
1. 각 Active Directory 테넌트의 사용자를 다른 쪽 Azure Active Directory 테넌트의 [게스트 사용자](../active-directory/b2b/add-users-administrator.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-guest-users-to-the-directory)로 추가합니다.
1. 각 사용자는 반대 Azure Active Directory 테 넌 트의 게스트 사용자 초대를 수락 해야 합니다.

## <a name="portal"></a>피어링 만들기 - Azure Portal

다음 단계에서는 각 구독에 서로 다른 계정을 사용합니다. 두 구독 모두에 대해 권한이 있는 계정을 사용할 경우 모든 단계에 동일한 계정을 사용하고, 포털 로그아웃 절차와 가상 네트워크에 다른 사용자 권한을 할당하는 절차를 생략할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에 *UserA*로 로그인합니다. 로그인하는 데 사용하는 계정에 가상 네트워크 피어링을 만드는 데 필요한 권한이 있어야 합니다. 사용 권한 목록은 [가상 네트워크 피어링 사용 권한](virtual-network-manage-peering.md#permissions)을 참조하세요.
2. **+ 리소스 만들기**를 선택하고, **네트워킹**을 선택한 다음, **가상 네트워크**를 선택합니다.
3. 다음 설정에 다음 예제 값을 선택하거나 입력한 다음, **만들기**를 선택합니다.
    - **이름**: *myVnetA*
    - **주소 공간**: *10.0.0.0/16*
    - **서브넷 이름**: *기본값*
    - **서브넷 주소 범위**: *10.0.0.0/24*
    - **구독**: 구독 A를 선택합니다.
    - **리소스 그룹**: **새로 만들기**를 선택하고 *myResourceGroupA*를 입력합니다.
    - **위치**: *미국 동부*
4. 포털 위쪽에 있는 **리소스 검색** 상자에 *myVnetA*를 입력합니다. **myVnetA**가 검색 결과에 표시되면 선택합니다. 
5. 왼쪽에 있는 옵션의 세로 목록에서 **액세스 제어(IAM)** 를 선택합니다.
6. **myVnetA - 액세스 제어(IAM)** 에서 **+ 역할 할당 추가**를 선택합니다.
7. **역할** 상자에서 **네트워크 참가자**를 선택합니다.
8. **선택** 상자에서 *UserB*를 선택하거나 UserB의 이메일 주소를 입력하여 검색합니다.
9. **저장**을 선택합니다.
10. **myVnetA - 액세스 제어(IAM)** 아래의 왼쪽에 있는 세로 옵션 목록에서 **속성**을 선택합니다. 이후 단계에서 사용하는 **RESOURCE ID**를 복사합니다. 리소스 ID는 다음 예제와 유사 합니다. `/subscriptions/<Subscription Id>/resourceGroups/myResourceGroupA/providers/Microsoft.Network/virtualNetworks/myVnetA`.
11. 사용자 A를 포털에서 로그아웃한 다음 사용자 B로 로그인합니다.
12. 3단계에서 다음 값을 입력하거나 선택하여 2-3단계를 완료합니다.

    - **이름**: *myVnetB*
    - **주소 공간**: *10.1.0.0/16*
    - **서브넷 이름**: *기본값*
    - **서브넷 주소 범위**: *10.1.0.0/24*
    - **구독**: 구독 B를 선택합니다.
    - **리소스 그룹**: **새로 만들기**를 선택하고 *myResourceGroupB*를 입력합니다.
    - **위치**: *미국 동부*

13. 포털 위쪽에 있는 **리소스 검색** 상자에 *myVnetB*를 입력합니다. **myVnetB**가 검색 결과에 표시되면 선택합니다.
14. **myVnetB** 아래의 왼쪽에 있는 세로 옵션 목록에서 **속성**을 선택합니다. 이후 단계에서 사용하는 **RESOURCE ID**를 복사합니다. 리소스 ID는 다음 예제와 유사 합니다. `/subscriptions/<Subscription ID>/resourceGroups/myResourceGroupB/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB`.
15. **myVnetB**에서 **액세스 제어(IAM)** 를 선택한 다음, 8단계에서 **UserA**를 입력하여 myVnetB에 대해 5-10단계를 완료합니다.
16. 사용자 B를 포털에서 로그아웃한 다음 사용자 A로 로그인합니다.
17. 포털 위쪽에 있는 **리소스 검색** 상자에 *myVnetA*를 입력합니다. **myVnetA**가 검색 결과에 표시되면 선택합니다.
18. **myVnetA**를 선택합니다.
19. **설정**에서 **피어링**을 선택합니다.
20. **myVnetA - 피어링**에서 **+ 추가**를 선택합니다.
21. **피어링 추가**에서 다음 옵션을 입력하거나 선택한 다음, **확인**을 선택합니다.
     - **이름**: *myVnetAToMyVnetB*
     - **가상 네트워크 배포 모델**: **Resource Manager**를 선택합니다.
     - **리소스 ID를 알고 있음**: 이 확인란을 선택합니다.
     - **리소스 ID**: 14단계에서 구한 리소스 ID를 입력합니다.
     - **가상 네트워크 액세스 허용:** **사용**이 선택되어 있는지 확인합니다.
    이 자습서에서 다른 설정은 사용되지 않습니다. 모든 피어링 설정에 대해 알아보려면 [가상 네트워크 피어링 관리](virtual-network-manage-peering.md#create-a-peering)를 읽어보세요.
22. 이전 단계에서 **확인**을 선택한 후에 잠깐 기다리면 만든 피어링이 표시됩니다. 만든 **myVnetAToMyVnetB** 피어링에 대해 **PEERING STATUS** 열에 **Initiated**가 열거됩니다. myVnetA에서 myVnetB를 피어링했으며 이제 myVnetB에서 myVnetA를 피어링해야 합니다. 피어링은 양방향으로 만들어져야 가상 네트워크의 리소스가 서로 통신할 수 있습니다.
23. 사용자 A를 포털에서 로그아웃한 다음 사용자 B로 로그인합니다.
24. MyVnetB에 대해 17~21단계를 다시 완료합니다. 21단계에서 피어링의 이름을 *myVnetBToMyVnetA*로 지정하고 *가상 네트워크*에 대해 **myVnetA**를 선택하고 **리소스 ID** 상자에서 10단계에서 구한 ID를 입력합니다.
25. **확인**을 선택하여 myVnetB에 대한 피어링을 만든 후 몇 초가 지나면 방금 만든 **myVnetBToMyVnetA** 피어링이 **PEERING STATUS** 열에 **연결됨**과 함께 나열됩니다.
26. 사용자 B를 포털에서 로그아웃한 다음 사용자 A로 로그인합니다.
27. 17~19단계를 다시 완료합니다. 이제 **myVnetAToVNetB** 피어링의 **PEERING STATUS**도 **Connected**입니다. 피어링의 두 가상 네트워크에 대한 **피어링 상태** 열에 둘 다 **연결됨**이 표시되고 나면 피어링이 성공적으로 설정된 것입니다. 어느 쪽 가상 네트워크에서든 만든 모든 Azure 리소스는 이제 해당 IP 주소를 통해 서로 통신할 수 있습니다. 가상 네트워크에 대해 기본 Azure 이름 확인을 사용 중인 경우 가상 네트워크의 리소스가 가상 네트워크에서 이름을 확인할 수 없습니다. 피어링의 가상 네트워크에서 이름을 확인하려면 자체 DNS 서버를 만들어야 합니다. [자체 DNS 서버를 이용한 이름 확인](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) 설정 방법을 알아보세요.
28. **선택 사항**: 이 자습서에서 가상 머신을 만드는 내용은 다루지 않지만, 각 가상 네트워크에서 가상 머신을 만들고 한 가상 머신에서 다른 가상 머신으로 연결하여 연결의 유효성을 검사할 수 있습니다.
29. **선택 사항**: 이 자습서에서 만든 리소스를 삭제하려면 이 문서의 [리소스 삭제](#delete-portal) 섹션에서 설명하는 단계를 완료합니다.

## <a name="cli"></a>피어링 만들기 - Azure CLI

이 자습서에서는 각 구독에 대해 다른 계정을 사용합니다. 두 구독 모두에 대해 권한이 있는 계정을 사용할 경우 모든 단계에 동일한 계정을 사용하고, Azure 로그아웃 절차를 생략하며 사용자 역할 할당을 만드는 스크립트 줄을 제거할 수 있습니다. 다음 스크립트 전체에서 UserA@azure.com 및 UserB@azure.com은 사용자 A와 사용자 B에 사용하는 사용자 이름으로 바꿉니다. 

다음 스크립트:

- Azure CLI 버전 2.0.4 이상이 필요합니다. 버전을 확인하려면 `az --version`을 실행합니다. 업그레이드해야 하는 경우 [Azure CLI 설치](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json)를 참조하세요.
- Bash 셸에서 작동합니다. Windows 클라이언트에서 Azure CLI 스크립트 실행과 관련된 옵션은 [Windows에서 Azure CLI 설치](/cli/azure/install-azure-cli-windows)를 참조하세요.

CLI 및 해당 종속성을 설치하는 대신 Azure Cloud Shell을 사용할 수 있습니다. Azure Cloud Shell은 Azure Portal에서 직접 실행할 수 있는 평가판 Bash 셸입니다. Azure CLI가 사전 설치되어 계정에서 사용하도록 구성되어 있습니다. 다음 스크립트에서 **사용해보기** 단추를 선택하면 Azure 계정에 로그인할 수 있는 Cloud Shell이 호출됩니다.

1. CLI 세션을 열고 `azure login` 명령을 사용하여 사용자 A로 Azure에 로그인합니다. 로그인하는 데 사용하는 계정에 가상 네트워크 피어링을 만드는 데 필요한 권한이 있어야 합니다. 사용 권한 목록은 [가상 네트워크 피어링 사용 권한](virtual-network-manage-peering.md#permissions)을 참조하세요.
2. 다음 스크립트를 PC의 텍스트 편집기에 복사하고 `<SubscriptionA-Id>`는 구독 A의 ID로 교체한 다음 수정된 스크립트를 복사하여 CLI 세션에 붙여 넣고 `Enter`를 누릅니다. 구독 ID를 모르는 경우 `az account show` 명령을 입력합니다. 출력에 표시되는 **id** 값이 구독 ID입니다.

    ```azurecli-interactive
    # Create a resource group.
    az group create \
      --name myResourceGroupA \
      --location eastus

    # Create virtual network A.
    az network vnet create \
      --name myVnetA \
      --resource-group myResourceGroupA \
      --location eastus \
      --address-prefix 10.0.0.0/16

    # Assign UserB permissions to virtual network A.
    az role assignment create \
      --assignee UserB@azure.com \
      --role "Network Contributor" \
      --scope /subscriptions/<SubscriptionA-Id>/resourceGroups/myResourceGroupA/providers/Microsoft.Network/VirtualNetworks/myVnetA
    ```

3. `az logout` 명령을 사용하여 사용자 A로 Azure에서 로그아웃한 다음 사용자 B로 Azure에 로그인합니다. 로그인하는 데 사용하는 계정에 가상 네트워크 피어링을 만드는 데 필요한 권한이 있어야 합니다. 사용 권한 목록은 [가상 네트워크 피어링 사용 권한](virtual-network-manage-peering.md#permissions)을 참조하세요.
4. myVnetB를 만듭니다. 2 단계의 스크립트 내용을 PC의 텍스트 편집기에 복사합니다. `<SubscriptionA-Id>`를 구독 B의 ID로 바꿉니다. 10.0.0.0/16을 10.1.0.0/16으로 변경하고 모든 A를 B로, 모든 B를 A로 바꿉니다. 수정된 스크립트를 복사하여 CLI 세션에 붙여 넣은 다음 `Enter`를 누릅니다.
5. 사용자 B로 Azure에서 로그아웃하고 사용자 A로 Azure에 로그인합니다.
6. myVnetA에서 myVnetB로의 가상 네트워크 피어링을 만듭니다. PC의 텍스트 편집기에 다음 스크립트 내용을 복사합니다. `<SubscriptionB-Id>`를 구독 B의 ID로 바꿉니다. 스크립트를 실행하려면 수정된 스크립트를 복사하여 CLI 세션에 붙여 넣은 다음 Enter를 누릅니다.

    ```azurecli-interactive
        # Get the id for myVnetA.
        vnetAId=$(az network vnet show \
          --resource-group myResourceGroupA \
          --name myVnetA \
          --query id --out tsv)

        # Peer myVNetA to myVNetB.
        az network vnet peering create \
          --name myVnetAToMyVnetB \
          --resource-group myResourceGroupA \
          --vnet-name myVnetA \
          --remote-vnet-id /subscriptions/<SubscriptionB-Id>/resourceGroups/myResourceGroupB/providers/Microsoft.Network/VirtualNetworks/myVnetB \
          --allow-vnet-access
    ```

7. MyVnetA의 피어링 상태를 확인합니다.

    ```azurecli-interactive
    az network vnet peering list \
      --resource-group myResourceGroupA \
      --vnet-name myVnetA \
      --output table
    ```

    상태가 **Initiated**입니다. myVnetB로부터 myVnetA로의 피어링을 만든 뒤에 **Connected**로 바뀝니다.

8. 사용자 A로 Azure에서 로그아웃하고 사용자 B로 Azure에 로그인합니다.
9. myVnetB에서 myVnetA로의 피어링을 만듭니다. 6단계의 스크립트 내용을 PC의 텍스트 편집기에 복사합니다. `<SubscriptionB-Id>`를 구독 A의 ID로 바꾸고 모든 A를 B로, 모든 B를 A로 변경합니다. 변경을 마친 후 수정된 스크립트를 복사하여 CLI 세션에 붙여 넣고 `Enter`를 누릅니다.
10. MyVnetB의 피어링 상태를 확인합니다. 7단계의 스크립트 내용을 PC의 텍스트 편집기에 복사합니다. 리소스 그룹과 가상 네트워크 이름에 대해 A를 B로 변경하고, 스크립트를 복사하고, 수정된 스크립트를 CLI 세션에 붙여 넣은 다음 `Enter`를 누릅니다. 피어링 상태는 **Connected**입니다. myVnetB에서 myVnetA로의 피어링을 만든 후에는 MyVnetA의 피어링 상태가 **Connected**로 변경됩니다. 사용자 A를 다시 Azure에 로그인하고 7단계를 재수행하여 myVnetA의 피어링 상태를 확인할 수 있습니다. 

    > [!NOTE]
    > 두 가상 네트워크 모두에 대해 피어링 상태가 **Connected**가 될 때까지는 피어링이 설정되지 않습니다.

11. **선택 사항**: 이 자습서에서 가상 머신을 만드는 내용은 다루지 않지만, 각 가상 네트워크에서 가상 머신을 만들고 한 가상 머신에서 다른 가상 머신으로 연결하여 연결의 유효성을 검사할 수 있습니다.
12. **선택 사항**: 이 자습서에서 만든 리소스를 삭제하려면 이 문서의 [리소스 삭제](#delete-cli)에서 설명하는 단계를 완료합니다.

어느 쪽 가상 네트워크에서든 만든 모든 Azure 리소스는 이제 해당 IP 주소를 통해 서로 통신할 수 있습니다. 가상 네트워크에 대해 기본 Azure 이름 확인을 사용 중인 경우 가상 네트워크의 리소스가 가상 네트워크에서 이름을 확인할 수 없습니다. 피어링의 가상 네트워크에서 이름을 확인하려면 자체 DNS 서버를 만들어야 합니다. [자체 DNS 서버를 이용한 이름 확인](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) 설정 방법을 알아보세요.

## <a name="powershell"></a>피어링 만들기 - PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

이 자습서에서는 각 구독에 대해 다른 계정을 사용합니다. 두 구독 모두에 대해 권한이 있는 계정을 사용할 경우 모든 단계에 동일한 계정을 사용하고, Azure 로그아웃 절차를 생략하며 사용자 역할 할당을 만드는 스크립트 줄을 제거할 수 있습니다. 다음 스크립트 전체에서 UserA@azure.com 및 UserB@azure.com은 사용자 A와 사용자 B에 사용하는 사용자 이름으로 바꿉니다.

1. Azure PowerShell 버전 1.0.0 이상이 있는지 확인 합니다. `Get-Module -Name Az`를 실행 하 여이 작업을 수행할 수 있습니다. 최신 버전의 PowerShell [Az module](/powershell/azure/install-az-ps)을 설치 하는 것이 좋습니다. Azure PowerShell을 처음 사용하는 경우 [Azure PowerShell 개요](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json)를 참조하세요. 
2. PowerShell 세션을 시작합니다.
3. PowerShell에서 `Connect-AzAccount` 명령을 입력하여 Azure에 사용자 A로 로그인합니다. 로그인하는 데 사용하는 계정에 가상 네트워크 피어링을 만드는 데 필요한 권한이 있어야 합니다. 사용 권한 목록은 [가상 네트워크 피어링 사용 권한](virtual-network-manage-peering.md#permissions)을 참조하세요.
4. 리소스 그룹 및 가상 네트워크 A를 만듭니다. 다음 스크립트를 PC의 텍스트 편집기에 복사합니다. `<SubscriptionA-Id>`를 구독 A의 ID로 바꿉니다. 구독 ID를 모르는 경우 `Get-AzSubscription` 명령을 입력하여 확인합니다. 반환된 출력의 **ID** 값이 구독 ID입니다. 스크립트를 실행하려면 수정된 스크립트를 복사하여 PowerShell에 붙여 넣은 다음 `Enter`를 누릅니다.

    ```powershell
    # Create a resource group.
    New-AzResourceGroup `
      -Name MyResourceGroupA `
      -Location eastus

    # Create virtual network A.
    $vNetA = New-AzVirtualNetwork `
      -ResourceGroupName MyResourceGroupA `
      -Name 'myVnetA' `
      -AddressPrefix '10.0.0.0/16' `
      -Location eastus

    # Assign UserB permissions to myVnetA.
    New-AzRoleAssignment `
      -SignInName UserB@azure.com `
      -RoleDefinitionName "Network Contributor" `
      -Scope /subscriptions/<SubscriptionA-Id>/resourceGroups/myResourceGroupA/providers/Microsoft.Network/VirtualNetworks/myVnetA
    ```

5. 사용자 A로 Azure에서 로그아웃하고 사용자 B로 로그인합니다. 로그인하는 데 사용하는 계정에 가상 네트워크 피어링을 만드는 데 필요한 권한이 있어야 합니다. 사용 권한 목록은 [가상 네트워크 피어링 사용 권한](virtual-network-manage-peering.md#permissions)을 참조하세요.
6. 4단계의 스크립트 내용을 PC의 텍스트 편집기에 복사합니다. `<SubscriptionA-Id>`를 구독 B의 ID로 바꿉니다. 10.0.0.0/16을 10.1.0.0/16으로 변경합니다. 모든 A를 B로, 모든 B를 A로 변경합니다. 스크립트를 실행하려면 수정된 스크립트를 복사하여 PowerShell에 붙여 넣은 다음 `Enter`를 누릅니다.
7. 사용자 B로 Azure에서 로그아웃하고 사용자 A로 로그인합니다.
8. myVnetA에서 myVnetB로의 피어링을 만듭니다. PC의 텍스트 편집기에 다음 스크립트를 복사합니다. `<SubscriptionB-Id>`를 구독 B의 ID로 교체합니다. 스크립트를 실행하려면 수정된 스크립트를 복사하여 PowerShell에 붙여 넣은 다음 `Enter`를 누릅니다.

   ```powershell
   # Peer myVnetA to myVnetB.
   $vNetA=Get-AzVirtualNetwork -Name myVnetA -ResourceGroupName myResourceGroupA
   Add-AzVirtualNetworkPeering `
     -Name 'myVnetAToMyVnetB' `
     -VirtualNetwork $vNetA `
     -RemoteVirtualNetworkId "/subscriptions/<SubscriptionB-Id>/resourceGroups/myResourceGroupB/providers/Microsoft.Network/virtualNetworks/myVnetB"
   ```

9. MyVnetA의 피어링 상태를 확인합니다.

    ```powershell
    Get-AzVirtualNetworkPeering `
      -ResourceGroupName myResourceGroupA `
      -VirtualNetworkName myVnetA `
      | Format-Table VirtualNetworkName, PeeringState
    ```

    상태가 **Initiated**입니다. myVnetB로부터 myVnetA로의 피어링을 설정한 뒤 **Connected**로 바뀝니다.

10. 사용자 A로 Azure에서 로그아웃하고 사용자 B로 로그인합니다.
11. myVnetB에서 myVnetA로의 피어링을 만듭니다. 8단계의 스크립트 내용을 PC의 텍스트 편집기에 복사합니다. `<SubscriptionB-Id>`를 구독 B의 ID로 교체하고 모든 A를 B로, 모든 B를 A로 변경합니다. 스크립트를 실행하려면 수정된 스크립트를 복사하여 PowerShell에 붙여 넣은 다음 `Enter`를 누릅니다.
12. MyVnetB의 피어링 상태를 확인합니다. 9단계의 스크립트 내용을 PC의 텍스트 편집기에 복사합니다. 리소스 그룹 및 가상 네트워크 이름에 대해 A를 B로 변경합니다. 스크립트를 실행하려면 수정된 스크립트를 PowerShell에 붙여 넣은 다음 `Enter`를 누릅니다. 상태는 **Connected**입니다. **myVnetB**에서 **myVnetA**로의 피어링을 만든 후에는 **myVnetA**의 피어링 상태가 **Connected**로 변경됩니다. 사용자 A를 다시 Azure에 로그인하고 9단계를 재수행하여 myVnetA의 피어링 상태를 확인할 수 있습니다.

    > [!NOTE]
    > 두 가상 네트워크 모두에 대해 피어링 상태가 **Connected**가 될 때까지는 피어링이 설정되지 않습니다.

    어느 쪽 가상 네트워크에서든 만든 모든 Azure 리소스는 이제 해당 IP 주소를 통해 서로 통신할 수 있습니다. 가상 네트워크에 대해 기본 Azure 이름 확인을 사용 중인 경우 가상 네트워크의 리소스가 가상 네트워크에서 이름을 확인할 수 없습니다. 피어링의 가상 네트워크에서 이름을 확인하려면 자체 DNS 서버를 만들어야 합니다. [자체 DNS 서버를 이용한 이름 확인](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) 설정 방법을 알아보세요.

13. **선택 사항**: 이 자습서에서 가상 머신을 만드는 내용은 다루지 않지만, 각 가상 네트워크에서 가상 머신을 만들고 한 가상 머신에서 다른 가상 머신으로 연결하여 연결의 유효성을 검사할 수 있습니다.
14. **선택 사항**: 이 자습서에서 만든 리소스를 삭제하려면 이 문서의 [리소스 삭제](#delete-powershell)에서 설명하는 단계를 완료합니다.

## <a name="template"></a>피어링 만들기 - Resource Manager 템플릿

1. 가상 네트워크를 만들고 적절한 [사용 권한](virtual-network-manage-peering.md#permissions)을 할당하려면 이 문서의 [포털](#portal), [Azure CLI](#cli) 또는 [PowerShell](#powershell) 섹션에 나오는 단계를 완료합니다.
2. 다음에 나오는 텍스트를 로컬 컴퓨터의 파일에 저장합니다. `<subscription ID>`를 사용자 A의 구독 ID로 바꿉니다. 예를 들어 파일을 vnetpeeringA.json으로 저장할 수 있습니다.

   ```json
   {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        },
        "variables": {
        },
    "resources": [
            {
            "apiVersion": "2016-06-01",
            "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",
            "name": "myVnetA/myVnetAToMyVnetB",
            "location": "[resourceGroup().location]",
            "properties": {
            "allowVirtualNetworkAccess": true,
            "allowForwardedTraffic": false,
            "allowGatewayTransit": false,
            "useRemoteGateways": false,
                "remoteVirtualNetwork": {
                "id": "/subscriptions/<subscription ID>/resourceGroups/PeeringTest/providers/Microsoft.Network/virtualNetworks/myVnetB"
                }
            }
            }
        ]
   }
   ```

3. UserA로 Azure에 로그인하고 [포털](../azure-resource-manager/templates/deploy-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-resources-from-custom-template), [PowerShell](../azure-resource-manager/templates/deploy-powershell.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-local-template) 또는 [Azure CLI](../azure-resource-manager/templates/deploy-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-local-template)를 사용하여 템플릿을 배포합니다. 2단계에서 예제 json 텍스트를 저장했던 파일 이름을 지정합니다.
4. 2단계의 예제 json 파일을 컴퓨터에 복사하고 다음으로 시작하는 줄을 변경합니다.
   - **name**: *myVnetA/myVnetAToMyVnetB*를 *myVnetB/myVnetBToMyVnetA*로 변경합니다.
   - **id**: `<subscription ID>`를 사용자 B의 구독 ID로 바꾸고 *myVnetB*를 *myVnetA*로 변경합니다.
5. 3단계를 다시 완료하고 UserB로 Azure에 로그인합니다.
6. **선택 사항**: 이 자습서에서 가상 머신을 만드는 내용은 다루지 않지만, 각 가상 네트워크에서 가상 머신을 만들고 한 가상 머신에서 다른 가상 머신으로 연결하여 연결의 유효성을 검사할 수 있습니다.
7. **선택 사항**: 이 자습서에서 만든 리소스를 삭제하려면 Azure Portal, PowerShell 또는 Azure CLI를 사용하여 이 문서의 [리소스 삭제](#delete) 섹션에서 설명하는 단계를 완료합니다.

## <a name="delete"></a>리소스 삭제
이 자습서를 마친 경우 사용 요금이 발생하지 않도록 자습서에서 만든 리소스를 삭제하려고 할 것입니다. 리소스 그룹을 삭제하면 리소스 그룹에 있는 리소스도 모두 삭제됩니다.

### <a name="delete-portal"></a>Azure Portal

1. Azure Portal에 사용자 A로 로그인합니다.
2. 포털 검색 상자에 **myResourceGroupA**를 입력합니다. 검색 결과에서 **myResourceGroupA**를 선택합니다.
3. **삭제**를 선택합니다.
4. 삭제를 확인하려면 **TYPE THE RESOURCE GROUP NAME** 상자에 **myResourceGroupA**를 입력한 다음, **삭제**를 선택합니다.
5. 사용자 A를 포털에서 로그아웃한 다음 사용자 B로 로그인합니다.
6. MyResourceGroupB에 대해 2-4단계를 완료합니다.

### <a name="delete-cli"></a>Azure CLI

1. 사용자 A로 Azure에 로그인하고 다음 명령을 실행합니다.

   ```azurecli-interactive
   az group delete --name myResourceGroupA --yes
   ```

2. 사용자 A로 Azure에서 로그아웃한 다음 사용자 B로 로그인합니다.
3. 다음 명령을 실행하십시오.

   ```azurecli-interactive
   az group delete --name myResourceGroupB --yes
   ```

### <a name="delete-powershell"></a>PowerShell

1. 사용자 A로 Azure에 로그인하고 다음 명령을 실행합니다.

   ```powershell
   Remove-AzResourceGroup -Name myResourceGroupA -force
   ```

2. 사용자 A로 Azure에서 로그아웃한 다음 사용자 B로 로그인합니다.
3. 다음 명령을 실행하십시오.

   ```powershell
   Remove-AzResourceGroup -Name myResourceGroupB -force
   ```

## <a name="next-steps"></a>다음 단계

- 프로덕션 환경에 사용하기 위한 가상 네트워크 피어링을 만들기 전에 먼저 중요한 [가상 네트워크 피어링 제약 조건 및 동작](virtual-network-manage-peering.md#requirements-and-constraints)에 철저하게 익숙해집니다.
- 모든 [가상 네트워크 피어링 설정](virtual-network-manage-peering.md#create-a-peering)에 대해 알아봅니다.
- 가상 네트워크 피어링을 통해 [허브 및 스포크 네트워크 토폴로지를 만드는](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke#virtual-network-peering) 방법을 알아봅니다.
