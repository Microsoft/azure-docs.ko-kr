---
title: Azure Virtual Network 피어링 만들기, 변경 또는 삭제 | Microsoft Docs
description: 가상 네트워크 피어링을 만들고 변경하거나 삭제하는 방법을 알아봅니다.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/01/2019
ms.author: anavin
ms.openlocfilehash: 4103930e0d089f5f7c17586f22616431c8aa11d9
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/15/2020
ms.locfileid: "75978350"
---
# <a name="create-change-or-delete-a-virtual-network-peering"></a>가상 네트워크 피어링 만들기, 변경 또는 삭제

가상 네트워크 피어링을 만들고 변경하거나 삭제하는 방법을 알아봅니다. 가상 네트워크 피어링은 Azure 백본 네트워크를 통해 동일한 지역 및 여러 지역(글로벌 VNet 피어링)에 있는 네트워크를 연결할 수 있습니다. 피어링된 후 가상 네트워크는 여전히 별도의 리소스로 관리됩니다. 가상 네트워크 피어링을 처음 접하는 경우 [가상 네트워크 피어링 개요](virtual-network-peering-overview.md)를 참조하거나 [자습서](tutorial-connect-virtual-networks-portal.md)를 완료하여 자세히 알아볼 수 있습니다.

## <a name="before-you-begin"></a>시작하기 전에

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

이 문서에서 설명하는 모든 섹션의 단계를 수행하기 전에 다음 작업을 완료해야 합니다.

- 아직 Azure 계정이 없으면 [평가판 계정](https://azure.microsoft.com/free)에 등록합니다.
- 포털을 사용하는 경우 https://portal.azure.com 을 열고, 피어링을 사용하는 데 [필요한 권한](#permissions)이 있는 계정으로 로그인합니다.
- 이 문서의 작업을 완료하기 위해 PowerShell 명령을 사용하는 경우 [Azure Cloud Shell](https://shell.azure.com/powershell)에서 명령을 실행하거나 컴퓨터에서 PowerShell을 실행합니다. Azure Cloud Shell은 이 항목의 단계를 실행하는 데 무료로 사용할 수 있는 대화형 셸입니다. 공용 Azure 도구가 사전 설치되어 계정에서 사용하도록 구성되어 있습니다. 이 자습서에는 Azure PowerShell 모듈 버전 1.0.0 이상이 필요합니다. 설치되어 있는 버전을 확인하려면 `Get-Module -ListAvailable Az`을 실행합니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-az-ps)를 참조하세요. PowerShell을 로컬로 실행 중인 경우 피어링을 사용하고 Azure와 연결을 만드는 데 [필요한 권한](#permissions)을 가진 계정으로 `Connect-AzAccount`도 실행해야 합니다.
- 이 문서의 작업을 완료하기 위해 Azure CLI(명령줄 인터페이스)를 사용하는 경우 [Azure Cloud Shell](https://shell.azure.com/bash)에서 명령을 실행하거나 컴퓨터에서 CLI를 실행합니다. 이 자습서에는 Azure CLI 버전 2.0.31 이상이 필요합니다. 설치되어 있는 버전을 확인하려면 `az --version`을 실행합니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요. Azure CLI를 로컬로 실행 중인 경우 피어링을 사용하고 Azure와 연결을 만드는 데 [필요한 권한](#permissions)을 가진 계정으로 `az login`도 실행해야 합니다.

Azure에 로그인하거나 연결할 때 사용하는 계정이 [권한](#permissions)에 나열된 적절한 작업이 할당된 [사용자 지정 역할](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)이나 [네트워크 기여자](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) 역할에 할당되어야 합니다.

## <a name="create-a-peering"></a>피어링 만들기

피어링을 만들려면 먼저 요구 사항, 제약 조건 및 [필요한 권한](#permissions)을 숙지하세요.

1. Azure Portal 맨 위에 있는 검색 상자에 *가상 네트워크*를 입력합니다. 검색 결과에 **가상 네트워크**가 표시되면 이를 선택합니다. 클래식 배포 모델을 통해 배포된 가상 네트워크에서는 피어링을 만들 수 없으므로 **가상 네트워크(클래식)** 가 목록에 나타나더라도 선택하지 않습니다.
2. 목록에서 피어링을 만들 가상 네트워크를 선택합니다.
3. **설정**에서 **피어링**을 선택합니다.
4. **+추가**를 선택합니다. 
5. <a name="add-peering"></a>다음 설정에 대한 값을 입력하거나 선택합니다.
    - **이름:** 피어링의 이름은 가상 네트워크 내에서 고유해야 합니다.
    - **가상 네트워크 배포 모델:** 피어링하려는 가상 네트워크를 배포한 배포 모델을 선택합니다.
    - **리소스 ID를 알고 있음:** 피어링하려는 가상 네트워크에 대한 읽기 권한이 있는 경우 이 확인란을 선택 취소된 상태로 둡니다. 피어링하려는 가상 네트워크 또는 구독에 대한 읽기 권한이 없는 경우 이 확인란을 선택합니다. 확인란을 선택할 때 나타난 **리소스 ID** 상자에 피어링하려는 가상 네트워크의 전체 리소스 ID를 입력합니다. 입력하는 리소스 ID는 이 가상 네트워크와 동일한 또는 [지원되는 다른](#requirements-and-constraints) Azure [지역](https://azure.microsoft.com/regions)에 있는 가상 네트워크의 리소스 ID여야 합니다. 전체 리소스 ID는 `/subscriptions/<Id>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/virtualNetworks/<virtual-network-name>`유사 합니다. 가상 네트워크의 속성을 확인하여 가상 네트워크의 리소스 ID를 알 수 있습니다. 가상 네트워크의 속성을 확인하는 방법을 알아보려면 [가상 네트워크 관리](manage-virtual-network.md#view-virtual-networks-and-settings)를 참조하세요. 구독이 피어링을 만드는 가상 네트워크가 포함된 구독과 다른 Azure Active Directory 테넌트에 연결되어 있는 경우 먼저 각 테넌트의 사용자를 [게스트 사용자](../active-directory/b2b/add-users-administrator.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-guest-users-to-the-directory)로 상대 테넌트에 추가합니다.
    - **구독:** 피어링하려는 가상 네트워크의 [구독](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription)을 선택합니다. 계정이 읽기 권한이 있는 구독 수에 따라 하나 이상의 구독이 나열됩니다. **리소스 ID** 확인란을 선택한 경우 이 설정을 사용할 수 없습니다.
    - **가상 네트워크:** 피어링하려는 가상 네트워크를 선택합니다. 두 Azure 배포 모델 중 하나를 통해 만든 가상 네트워크를 선택할 수 있습니다. 다른 지역에서 가상 네트워크를 선택하려는 경우 [지원되는 영역](#cross-region)에서 가상 네트워크를 선택해야 합니다. 목록에 가상 네트워크가 표시되게 하려면 가상 네트워크에 대한 읽기 권한이 있어야 합니다. 가상 네트워크가 나열되지만, 회색으로 표시된 경우 가상 네트워크의 주소 공간이 이 가상 네트워크의 주소 공간과 겹치기 때문일 수 있습니다. 가상 네트워크 주소 공간이 겹치면 피어링할 수 없습니다. **리소스 ID** 확인란을 선택한 경우 이 설정을 사용할 수 없습니다.
    - **가상 네트워크 액세스 허용:** 두 가상 네트워크 간 통신을 사용하도록 설정하려면 **사용**(기본값)을 선택합니다. 가상 네트워크 간 통신을 사용하도록 설정하면 어느 쪽 가상 네트워크에든 연결된 리소스가 같은 가상 네트워크에 연결된 것처럼 같은 대역폭 및 대기 시간으로 서로 통신할 수 있습니다. 두 가상 네트워크의 리소스 간 모든 통신은 Azure 프라이빗 네트워크를 통해 이루어집니다. 네트워크 보안 그룹에 대한 **VirtualNetwork** 서비스 태그는 가상 네트워크와 피어링된 가상 네트워크를 포함합니다. 네트워크 보안 그룹 서비스 태그에 대한 자세한 내용은 [네트워크 보안 그룹 개요](security-overview.md#service-tags)를 참조하세요. 트래픽이 피어링된 가상 네트워크로 흐르지 않게 하려면 **사용 안 함**을 선택합니다. 가상 네트워크를 다른 가상 네트워크와 피어링했지만, 종종 두 가상 네트워크 간 트래픽 흐름을 비활성화하려는 경우에도 **사용 안 함**을 선택할 수 있습니다. 피어링을 삭제하고 다시 만드는 것보다 사용/사용 안 함을 설정하는 것이 더 편리함을 알 수 있습니다. 이 설정을 사용하지 않도록 설정하면 피어링된 가상 네트워크 간에 트래픽이 흐르지 않습니다.
    - **전달된 트래픽 허용:** 가상 네트워크의 네트워크 가상 어플라이언스에서 *전달된* 트래픽이(가상 네트워크에서 발생하지 않은) 피어링을 통한 이 가상 네트워크로 흐를 수 있도록 허용하려면 이 확인란을 선택합니다. 예를 들어 Spoke1, Spoke2 및 Hub라는 3개의 가상 네트워크를 가정합니다. 피어링은 각 스포크 가상 네트워크와 허브 가상 네트워크 간에 존재하지만 피어링은 스포크 가상 네트워크 간에 존재하지 않습니다. 네트워크 가상 어플라이언스는 허브 가상 네트워크에 배포되고 사용자 정의 경로는 네트워크 가상 어플라이언스를 통해 서브넷 간에 트래픽을 라우팅하는 각 스포크 가상 네트워크에 적용됩니다. 각 스포크 가상 네트워크와 허브 가상 네트워크 간의 피어 링에 대해이 확인란을 선택 하지 않으면 허브는 가상 네트워크 간에 트래픽을 전달 하지 않으므로 스포크 가상 네트워크 간에 트래픽이 전달 되지 않습니다. 이 기능을 사용하도록 설정하여 피어링을 통해 전달된 트래픽을 허용하는 동안에는 사용자 정의 경로나 네트워크 가상 어플라이언스가 만들어지지 않습니다. 사용자 정의 경로와 네트워크 가상 어플라이언스는 개별적으로 만들어집니다. [사용자 정의 경로](virtual-networks-udr-overview.md#user-defined)에 대해 자세히 알아보세요. Azure VPN Gateway를 통해 가상 네트워크 간에 트래픽이 전달될 경우 이 설정을 선택할 필요가 없습니다.
    - **게이트웨이 전송 허용:** 가상 네트워크 게이트웨이를 이 가상 네트워크에 연결했고 피어링된 가상 네트워크의 트래픽이 게이트웨이를 통해 흐르도록 하려면 이 상자를 선택합니다. 예를 들어 가상 네트워크 게이트웨이를 통해 이 가상 네트워크를 온-프레미스 네트워크에 연결할 수 있습니다. 게이트웨이는 ExpressRoute 또는 VPN 게이트웨이가 될 수 있습니다. 이 상자를 선택하면 피어링된 가상 네트워크에서 이 가상 네트워크에 연결된 게이트웨이를 통해 온-프레미스 네트워크로 트래픽이 흐를 수 있습니다. 이 상자를 선택하면 피어링된 가상 네트워크에 대한 게이트웨이를 구성할 수 없습니다. 다른 가상 네트워크에서 이 가상 네트워크로의 피어링을 설정할 때 피어링된 가상 네트워크에 대해 **원격 게이트웨이 사용** 확인란을 선택해야 합니다. 이 상자를 선택 취소된 상태로 두면(기본값), 피어링된 가상 네트워크에서 이 가상 네트워크로 여전히 트래픽이 흐르지만 이 가상 네트워크에 연결된 가상 네트워크 게이트웨이를 통해서는 흐를 수 없습니다. 피어링이 가상 네트워크(Resource Manager)와 가상 네트워크(클래식) 사이에 있는 경우 게이트웨이는 가상 네트워크(Resource Manager)에 있어야 합니다.

       온-프레미스 네트워크로 트래픽을 전달하는 것 외에도 VPN 게이트웨이는 가상 네트워크가 서로 피어링될 필요 없이 게이트웨이가 있는 가상 네트워크로 피어링된 가상 네트워크 간에 네트워크 트래픽을 전달할 수 있습니다. 트래픽을 전달하기 위해 VPN 게이트웨이를 사용하면 허브(**전달된 트래픽 허용**에 대해 설명한 허브 및 스포크 예제 참조) 가상 네트워크에서 VPN 게이트웨이를 사용하여 서로 피어링되지 않은 스포크 가상 네트워크 간에 트래픽을 라우팅하려는 경우 유용합니다. 전송을 위한 게이트웨이 사용 허용에 관해 자세히 알아보려면 [가상 네트워크 피어링에서 전송을 위한 VPN 게이트웨이 구성](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json)을 참조하세요. 이 시나리오는 다음 홉 형식으로 가상 네트워크 게이트웨이를 지정하는 사용자 정의 경로를 구현해야 합니다. [사용자 정의 경로](virtual-networks-udr-overview.md#user-defined)에 대해 자세히 알아보세요. 사용자 지정 경로에서 다음 홉 형식으로 VPN 게이트웨이만 지정할 수 있습니다. 사용자 지정 경로에서 다음 홉 형식으로 ExpressRoute 게이트웨이를 지정할 수 없습니다.

    - **원격 게이트웨이 사용:** 이 가상 네트워크의 트래픽이 피어링 중인 가상 네트워크에 연결된 가상 네트워크 게이트웨이를 통해 흐를 수 있도록 허용하려면 이 상자를 선택합니다. 예를 들어 피어링 중인 가상 네트워크에 온-프레미스 네트워크에 대한 통신을 가능하게 하는 VPN Gateway가 연결되어 있습니다.  이 상자를 선택하면 이 가상 네트워크에서 피어링된 가상 네트워크에 연결된 VPN Gateway를 통해 트래픽이 흐를 수 있습니다. 이 상자를 선택하는 경우 피어링된 가상 네트워크에 가상 네트워크 게이트웨이가 연결되어 있어야 하며 **게이트웨이 전송 허용** 확인란이 선택되어 있어야 합니다. 이 상자를 선택 취소된 상태로 두면(기본값), 피어링된 가상 네트워크에서 이 가상 네트워크로 여전히 트래픽이 흐를 수 있지만 이 가상 네트워크에 연결된 가상 네트워크 게이트웨이를 통해서는 흐를 수 없습니다.
    이 가상 네트워크에 대한 하나의 피어링만 이 설정을 활성화할 수 있습니다.

        가상 네트워크에 구성된 게이트웨이가 이미 있는 경우 원격 게이트웨이를 사용할 수 없습니다. 전송을 위한 게이트웨이 사용에 관해 자세히 알아보려면 [가상 네트워크 피어링에서 전송을 위한 VPN 게이트웨이 구성](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json)을 참조

6. **확인**을 선택하여 선택한 가상 네트워크에 피어링을 추가합니다.

서로 다른 구독의 가상 네트워크와 배포 모델 간의 피어링을 구현하기 위한 단계별 지침은 [다음 단계](#next-steps)를 참조하세요.

### <a name="commands"></a>명령

- **Azure CLI**: [az network vnet peering create](/cli/azure/network/vnet/peering)
- **PowerShell**: [AzVirtualNetworkPeering](/powershell/module/az.network/add-azvirtualnetworkpeering)

## <a name="view-or-change-peering-settings"></a>피어링 설정 보기 또는 변경

피어링을 변경하려면 먼저 요구 사항, 제약 조건 및 [필요한 권한](#permissions)을 숙지하세요.

1. 포털 맨 위에 있는 검색 상자에 *가상 네트워크*를 입력합니다. 검색 결과에 **가상 네트워크**가 표시되면 이를 선택합니다. 클래식 배포 모델을 통해 배포된 가상 네트워크에서는 피어링을 만들 수 없으므로 **가상 네트워크(클래식)** 가 목록에 나타나더라도 선택하지 않습니다.
2. 목록에서 피어링 설정을 변경할 가상 네트워크를 선택합니다.
3. **설정**에서 **피어링**을 선택합니다.
4. 설정을 보거나 변경할 피어링을 선택합니다.
5. 해당 설정을 변경합니다. 피어 링 만들기의 [5 단계](#add-peering) 에서 각 설정에 대 한 옵션을 읽습니다.
6. **저장**을 선택합니다.

**명령**

- **Azure CLI**: [az network vnet peering list](/cli/azure/network/vnet/peering): 가상 네트워크에 대한 피어링을 나열함, [az network vnet peering show](/cli/azure/network/vnet/peering): 특정 피어링에 대한 설정을 표시함, [az network vnet peering update](/cli/azure/network/vnet/peering): 피어링 설정을 변경함
- **PowerShell**: [AzVirtualNetworkPeering](/powershell/module/az.network/get-azvirtualnetworkpeering) 뷰 피어 링 설정을 검색 하 고 설정을 변경 하려면 [AzVirtualNetworkPeering](/powershell/module/az.network/set-azvirtualnetworkpeering) 를 설정 합니다.

## <a name="delete-a-peering"></a>피어링 삭제

피어링을 삭제하기 전에 계정에 [필요한 권한](#permissions)이 있는지 확인합니다.

피어링이 삭제되면 더 이상 가상 네트워크에서 피어링된 가상 네트워크로 트래픽이 흐르지 않습니다. Resource Manager를 통해 배포된 가상 네트워크가 피어링되면 각 가상 네트워크에 다른 가상 네트워크에 대한 피어링이 있습니다. 한 가상 네트워크에서 피어링을 삭제하여 가상 네트워크 간 통신을 사용하지 않도록 설정하더라도 다른 가상 네트워크에서 피어링이 삭제되지는 않습니다. 다른 가상 네트워크에 있는 피어링의 피어링 상태는 **연결 끊김**입니다. 첫 번째 가상 네트워크에서 피어링을 다시 만들고 두 가상 네트워크의 피어링 상태가 모두 *연결됨*으로 변경될 때까지는 피어링을 다시 만들 수 없습니다.

가상 네트워크가 가끔 통신하되 항상 통신하지는 않게 하려면 피어링을 삭제하는 대신 **가상 네트워크 액세스 허용** 설정을 **사용 안 함**으로 설정할 수 있습니다. 방법을 알아보려면이 문서의 피어 링 만들기 섹션에 있는 6 단계를 참조 하세요. 피어링을 삭제하고 다시 만드는 것보다 네트워크 액세스를 사용 안 함/사용 설정하는 것이 더 쉬움을 알 수 있습니다.

1. 포털 맨 위에 있는 검색 상자에 *가상 네트워크*를 입력합니다. 검색 결과에 **가상 네트워크**가 표시되면 이를 선택합니다. 클래식 배포 모델을 통해 배포된 가상 네트워크에서는 피어링을 만들 수 없으므로 **가상 네트워크(클래식)** 가 목록에 나타나더라도 선택하지 않습니다.
2. 목록에서 피어링을 삭제할 가상 네트워크를 선택합니다.
3. **설정**에서 **피어링**을 선택합니다.
4. 삭제할 피어링의 오른쪽에서 **...** , **삭제**, **예**를 차례로 선택하여 첫 번째 가상 네트워크에서 피어링을 삭제합니다.
5. 이전 단계를 완료하여 피어링의 다른 가상 네트워크에서 피어링을 삭제합니다.

**명령**

- **Azure CLI**: [az network vnet peering delete](/cli/azure/network/vnet/peering)
- **PowerShell**: [AzVirtualNetworkPeering](/powershell/module/az.network/remove-azvirtualnetworkpeering)

## <a name="requirements-and-constraints"></a>요구 사항 및 제약 조건

- <a name="cross-region"></a>동일한 또는 다른 지역에 있는 가상 네트워크를 피어링할 수 있습니다. 다른 지역의 가상 네트워크 피어 링은 *글로벌 VNet 피어 링*이 라고도 합니다. 
- 전역 피어 링을 만들 때 피어 링 가상 네트워크는 Azure 공용 클라우드 지역 또는 중국 클라우드 지역 또는 정부 클라우드 지역에 있을 수 있습니다. 클라우드 간에 피어 링 할 수 없습니다. 예를 들어 azure 공용 클라우드의 VNet은 Azure 중국 클라우드의 VNet에 피어 링 수 없습니다.
- 하나의 가상 네트워크의 리소스는 전역적으로 피어링된 가상 네트워크에 있는 기본 내부 부하 분산 장치의 프런트 엔드 IP 주소와 통신할 수 없습니다. 기본 Load Balancer에 대한 지원은 동일한 지역 내에서만 가능합니다. 표준 Load Balancer에 대한 지원은 VNet Peering 및 글로벌 VNet Peering에서 모두 가능합니다. 글로벌 VNet 피어 링을 통해 작동 하지 않는 기본 부하 분산 장치를 사용 하는 서비스는 여기에 설명 되어 [있습니다.](virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers)
- 원격 게이트웨이를 사용 하거나 글로벌 피어 링 가상 네트워크 및 로컬 피어 링 가상 네트워크에서 게이트웨이 전송을 허용할 수 있습니다.
- 가상 네트워크는 같은 구독에 있을 수도 있고 다른 구독에 있을 수도 있습니다. 다른 구독에서 가상 네트워크를 피어링하는 경우 두 구독이 같은 Azure Active Directory 테넌트에 연결되어 있을 수도 있고 다른 테넌트에 연결되어 있을 수도 있습니다. AD 테넌트가 아직 없는 경우 [새로 만들](../active-directory/develop/quickstart-create-new-tenant.md?toc=%2fazure%2fvirtual-network%2ftoc.json-a-new-azure-ad-tenant)수 있습니다. Portal에서는 다른 Azure Active Directory 테넌트에 연결된 구독의 가상 네트워크에 대한 피어링이 지원되지 않습니다. CLI, PowerShell 또는 템플릿을 사용할 수는 있습니다.
- 피어링하는 가상 네트워크에 겹치지 않는 IP 주소 공간이 있어야 합니다.
- 가상 네트워크가 다른 가상 네트워크와 피어링되면 가상 네트워크에 주소 범위를 추가하거나 가상 네트워크에서 주소 범위를 삭제할 수 없습니다. 주소 범위를 추가하거나 제거하려면 피어링을 삭제하고 주소 범위를 추가하거나 제거한 다음 피어링을 다시 만듭니다. 가상 네트워크에 주소 범위를 추가하거나 가상 네트워크에서 주소 범위를 제거하려면 [가상 네트워크 관리](manage-virtual-network.md)를 참조하세요.
- Resource Manager를 통해 배포된 두 가상 네트워크 또는 Resource Manager를 통해 배포된 가상 네트워크와 클래식 배포 모델을 통해 배포된 가상 네트워크를 피어링할 수 있습니다. 클래식 배포 모델을 통해 만든 두 가상 네트워크를 피어링할 수는 없습니다. Azure 배포 모델에 익숙하지 않은 경우 [Azure 배포 모델 이해](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 문서를 읽어보세요. [VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#V2V)를 사용하여 클래식 배포 모델을 통해 만든 두 가상 네트워크는 연결할 수 없습니다.
- Resource Manager를 통해 만든 두 가상 네트워크를 피어링할 때는 피어링의 각 가상 네트워크에 대해 피어링을 구성해야 합니다. 피어링 상태에 대한 다음 유형 중 하나가 표시됩니다. 
  - *시작됨:* 첫 번째 가상 네트워크에서 두 번째 가상 네트워크로의 피어링을 만들면 피어링 상태가 *시작됨*입니다. 
  - *연결됨:* 두 번째 가상 네트워크에서 첫 번째 가상 네트워크로의 피어링을 만들면 피어링 상태가 *연결됨*입니다. 첫 번째 가상 네트워크의 피어링 상태를 보면 해당 상태가 *시작됨*에서 *연결됨*으로 변경된 것을 알 수 있습니다. 두 가상 네트워크 피어링의 피어링 상태가 *연결됨*이 될 때까지는 피어링이 설정되지 않습니다.
- Resource Manager를 통해 만든 가상 네트워크를 클래식 배포 모델을 통해 만든 가상 네트워크와 피어링하는 경우 Resource Manager를 통해 배포된 가상 네트워크에 대한 피어링만 구성합니다. 가상 네트워크(클래식)에 대해서나 클래식 배포 모델을 통해 배포된 두 가상 네트워크 간에는 피어링을 구성할 수 없습니다. 가상 네트워크(Resource Manager)에서 가상 네트워크(클래식)로의 피어링을 만들면 피어링 상태가 *업데이트 중*이 되었다가 곧 *연결됨*으로 변경됩니다.
- 두 가상 네트워크 간에 피어링이 설정됩니다. 피어링은 전이적이지 않습니다. 다음 사이에 피어링을 만들면:
  - VirtualNetwork1 및 VirtualNetwork2
  - VirtualNetwork2 및 VirtualNetwork3

  VirtualNetwork2를 통한 VirtualNetwork1과 VirtualNetwork3 사이의 피어링은 없습니다. VirtualNetwork1과 VirtualNetwork3 사이에 가상 네트워크 피어링을 만들려는 경우 VirtualNetwork1과 VirtualNetwork3 사이에 피어링을 만들어야 합니다.
- 기본 Azure 이름 확인을 사용하여 피어링된 가상 네트워크에서 이름을 확인할 수 없습니다. 다른 가상 네트워크에서 이름을 확인하려면 [프라이빗 도메인용 Azure DNS](../dns/private-dns-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 또는 사용자 지정 DNS 서버를 사용해야 합니다. 자체 DNS 서버를 설정하는 방법을 알아보려면 [자체 DNS 서버를 이용한 이름 확인](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)을 참조하세요.
- 동일한 지역의 피어링된 가상 네트워크에 있는 리소스는 같은 가상 네트워크에 있는 것처럼 같은 대역폭 및 대기 시간으로 서로 통신할 수 있습니다. 그러나 각 가상 머신 크기에는 고유한 최대 네트워크 대역폭이 있습니다. 다양한 가상 머신 크기의 최대 네트워크 대역폭에 대한 자세한 내용을 알아보려면 [Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 또는 [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 가상 머신 크기를 참조하세요.
- 가상 네트워크를 다른 가상 네트워크에 피어링할 수 있으며, Azure Virtual Network 게이트웨이를 통해 다른 가상 네트워크에 연결할 수도 있습니다. 가상 네트워크가 피어링 및 게이트웨이를 통해 연결된 경우 가상 네트워크 간 트래픽은 게이트웨이가 아니라 피어링 구성을 통해 흐릅니다.
- 가상 네트워크 피어링이 성공적으로 구성된 후 지점 및 사이트 간 VPN 클라이언트를 다시 다운로드하여 새 경로가 클라이언트에 다운로드되는지 확인해야 합니다.
- 가상 네트워크 피어링을 활용하는 수신 및 송신 트래픽에 대한 명목 요금이 부과됩니다. 자세한 내용은 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/virtual-network)를 참조하세요.

## <a name="permissions"></a>권한

가상 네트워크 피어링 작업에 사용하는 계정을 다음 역할에 할당해야 합니다.

- [네트워크 기여자](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor): Resource Manager를 통해 배포된 가상 네트워크에 해당합니다.
- [클래식 네트워크 기여자](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor): 클래식 배포 모델을 통해 배포된 가상 네트워크에 해당합니다.

계정이 이전 역할 중 하나에 할당되지 않은 경우 다음 표에서 필요한 작업이 할당된 [사용자 지정 역할](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)에 할당되어야 합니다.

| 실행력                                                          | 이름 |
|---                                                              |---   |
| Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write  | 가상 네트워크 A에서 가상 네트워크 B로의 피어링을 만들어야 합니다. 가상 네트워크 A는 가상 네트워크(Resource Manager)이어야 함          |
| Microsoft.Network/virtualNetworks/peer/action                   | 가상 네트워크 B(Resource Manager)에서 가상 네트워크 A로의 피어링을 만들어야 함                                                       |
| Microsoft.ClassicNetwork/virtualNetworks/peer/action                   | 가상 네트워크 B(클래식)에서 가상 네트워크 A로의 피어링을 만들어야 함                                                                |
| Microsoft.Network/virtualNetworks/virtualNetworkPeerings/read   | 가상 네트워크 피어링 읽기   |
| Microsoft.Network/virtualNetworks/virtualNetworkPeerings/delete | 가상 네트워크 피어링 삭제 |

## <a name="next-steps"></a>다음 단계

- 가상 네트워크 피어링은 동일하거나 다른 구독에 존재하는 동일하거나 다른 배포 모델을 통해 만든 가상 네트워크 간에 만들어집니다. 다음 시나리오 중 하나에 대한 자습서를 완료합니다.

  |Azure 배포 모델             | Subscription  |
  |---------                          |---------|
  |둘 다 Resource Manager              |[동일](tutorial-connect-virtual-networks-portal.md)|
  |                                   |[다름](create-peering-different-subscriptions.md)|
  |하나는 Resource Manager, 다른 하나는 클래식  |[동일](create-peering-different-deployment-models.md)|
  |                                   |[다름](create-peering-different-deployment-models-subscriptions.md)|

- [허브 및 스포크 네트워크 토폴로지](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json)를 만드는 방법을 알아봅니다.
- [PowerShell](powershell-samples.md) 또는 [Azure CLI](cli-samples.md) 샘플 스크립트를 사용하거나 Azure [Resource Manager 템플릿](template-samples.md)을 사용하여 가상 네트워크 피어링을 만듬
- 가상 네트워크에 대한 [Azure 정책](policy-samples.md) 만들기 및 적용
