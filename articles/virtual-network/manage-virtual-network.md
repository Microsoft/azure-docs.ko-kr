---
title: Azure Virtual Network 만들기, 변경 또는 삭제
titlesuffix: Azure Virtual Network
description: 가상 네트워크를 만들고 삭제 하 고 기존 가상 네트워크의 DNS 서버 및 IP 주소 공간과 같은 설정을 변경 합니다.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/10/2019
ms.author: kumud
ms.openlocfilehash: 5581a4c43f0b78dc8c14c44bfb1ded371a925fd0
ms.sourcegitcommit: e0785ea4f2926f944ff4d65a96cee05b6dcdb792
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2020
ms.locfileid: "88706033"
---
# <a name="create-change-or-delete-a-virtual-network"></a>가상 네트워크 만들기, 변경 또는 삭제

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

가상 네트워크를 만들고 삭제하는 방법 및 기존 가상 네트워크의 DNS 서버 및 IP 주소 공간과 같은 설정을 변경하는 방법에 대해 알아봅니다. 가상 네트워크를 처음 사용하는 경우, [가상 네트워크 개요](virtual-networks-overview.md)를 참조하거나 [자습서](quick-create-portal.md)를 완료하여 자세히 알아볼 수 있습니다. 가상 네트워크에 서브넷이 포함됩니다. 서브넷을 만들고, 변경하고, 삭제하는 방법에 대해 알아보려면 [서브넷 관리](virtual-network-manage-subnet.md)를 참조하세요.

## <a name="before-you-begin"></a>시작하기 전에

이 문서에서 설명하는 모든 섹션의 단계를 수행하기 전에 다음 작업을 완료해야 합니다.

- 아직 Azure 계정이 없으면 [평가판 계정](https://azure.microsoft.com/free)에 등록합니다.
- 포털을 사용하는 경우 https://portal.azure.com을 열고 Azure 계정으로 로그인합니다.
- 이 문서의 작업을 완료하기 위해 PowerShell 명령을 사용하는 경우 [Azure Cloud Shell](https://shell.azure.com/powershell)에서 명령을 실행하거나 컴퓨터에서 PowerShell을 실행합니다. Azure Cloud Shell은 이 항목의 단계를 실행하는 데 무료로 사용할 수 있는 대화형 셸입니다. 공용 Azure 도구가 사전 설치되어 계정에서 사용하도록 구성되어 있습니다. 이 자습서에는 Azure PowerShell 모듈 버전 1.0.0 이상이 필요합니다. 설치되어 있는 버전을 확인하려면 `Get-Module -ListAvailable Az`을 실행합니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-az-ps)를 참조하세요. 또한 PowerShell을 로컬로 실행하는 경우 `Connect-AzAccount`를 실행하여 Azure와 연결해야 합니다.
- 이 문서의 작업을 완료하기 위해 Azure CLI(명령줄 인터페이스)를 사용하는 경우 [Azure Cloud Shell](https://shell.azure.com/bash)에서 명령을 실행하거나 컴퓨터에서 CLI를 실행합니다. 이 자습서에는 Azure CLI 버전 2.0.31 이상이 필요합니다. 설치되어 있는 버전을 확인하려면 `az --version`을 실행합니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요. 또한 Azure CLI를 로컬로 실행하는 경우 `az login`를 실행하여 Azure와 연결해야 합니다.
- 를 사용 하 여 로그인 하거나 Azure에 연결 하는 계정은 [네트워크 참가자](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) 역할 또는 [사용 권한](#permissions)에 나열 된 적절 한 작업에 할당 된 [사용자 지정 역할](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 에 할당 되어야 합니다.

## <a name="create-a-virtual-network"></a>가상 네트워크 만들기

1. **+ 리소스 만들기**  >  **네트워킹**  >  **가상 네트워크**를 선택 합니다.
2. 다음 설정에 대 한 값을 입력 하거나 선택한 다음, **만들기**를 선택 합니다.
   - **이름**: 가상 네트워크를 만들려고 선택하는 [리소스 그룹](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group)에서 고유해야 합니다. 가상 네트워크를 만든 후 이름을 변경할 수 없습니다. 시간이 지남에 따라 여러 가상 네트워크를 만들 수 있습니다. 명명 제안에 대해서는 [명명 규칙](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#naming-and-tagging-resources)을 참조하세요. 명명 규칙을 따르면 여러 가상 네트워크를 보다 쉽게 관리할 수 있습니다.
   - **주소 공간**: 가상 네트워크의 주소 공간은 CIDR 표기법으로 지정된 하나 이상의 겹치지 않는 주소 범위로 구성됩니다. 정의하는 주소 범위는 공용 또는 프라이빗 주소가 될 수 있습니다(RFC 1918). 주소 범위를 공용으로 정의하든 프라이빗으로 정의하든 가상 네트워크 내, 상호 연결된 가상 네트워크 및 가상 네트워크에 연결된 모든 온-프레미스 네트워크에서만 주소 범위에 연결할 수 있습니다. 다음 주소 범위는 추가할 수 없습니다.
     - 224.0.0.0/4(멀티캐스트)
     - 255.255.255.255/32(브로드캐스트)
     - 127.0.0.0/8(루프백)
     - 169.254.0.0/16(링크-로컬)
     - 168.63.129.16/32(내부 DNS, DHCP 및 Azure Load Balancer [상태 프로브](../load-balancer/load-balancer-custom-probe-overview.md#probesource))

     포털에서 가상 네트워크를 만들 때 주소 범위를 하나만 정의할 수 있지만 가상 네트워크를 만든 후 주소 공간에 주소 범위를 더 추가할 수 있습니다. 기존 가상 네트워크에 주소 범위를 추가하는 방법은 [주소 범위 추가 또는 제거](#add-or-remove-an-address-range)를 참조하세요.

     > [!WARNING]
     > 가상 네트워크에 다른 가상 네트워크 또는 온-프레미스 네트워크와 겹치는 주소 범위가 있는 경우 두 네트워크를 연결할 수 없습니다. 주소 범위를 정의하기 전에 나중에 가상 네트워크를 다른 가상 네트워크 또는 온-프레미스 네트워크에 연결할지 고려해야 합니다. 조직에서 소유 하는 개인 주소 공간 또는 공용 주소 공간을 사용 하 여 가상 네트워크 주소 범위를 구성 하는 것이 좋습니다.
     >

     - **서브넷 이름**: 서브넷 이름은 가상 네트워크 내에서 고유해야 합니다. 서브넷을 만든 후 서브넷 이름을 변경할 수 없습니다. 가상 네트워크에 서브넷이 필요하지는 않지만 포털에서는 가상 네트워크를 만들 때 하나의 서브넷을 정의해야 합니다. 포털에서 가상 네트워크를 만들 때 서브넷을 하나만 정의할 수 있습니다. 가상 네트워크를 만든 후 나중에 서브넷을 추가할 수 있습니다. 가상 네트워크에 서브넷을 추가하려면 [서브넷 관리](virtual-network-manage-subnet.md)를 참조하세요. Azure CLI 또는 PowerShell을 사용하여 여러 서브넷이 있는 가상 네트워크를 만들 수 있습니다.

       >[!TIP]
       >경우에 따라 관리자는 여러 서브넷을 만들어 서브넷 간의 트래픽 라우팅을 필터링하거나 제어합니다. 서브넷을 정의하기 전에 서브넷 간에 트래픽을 필터링하고 라우팅하는 방법을 고려하는 것이 좋습니다. 서브넷 간의 트래픽을 필터링하는 방법에 대한 자세한 내용은 [네트워크 보안 그룹](security-overview.md)을 참조하세요. Azure에서는 서브넷 간에 트래픽을 자동으로 라우팅하지만 Azure 기본 경로를 재정의할 수 있습니다. Azures 기본 서브넷 트래픽 라우팅에 대한 자세한 내용은 [라우팅 개요](virtual-networks-udr-overview.md)를 참조하세요.
       >

     - **서브넷 주소 범위:** 범위는 가상 네트워크에 대해 입력한 주소 공간 내에 있어야 합니다. 지정할 수 있는 최소 범위는 /29이며, 서브넷에 대해 8개의 IP 주소를 제공합니다. Azure는 프로토콜 준수를 위해 각 서브넷의 첫 번째 및 마지막 주소를 예약합니다. 세 개의 추가 주소가 Azure 서비스를 사용하기 위해 예약되어 있습니다. 결과적으로 서브넷 주소 범위가 /29인 가상 네트워크에는 세 개의 IP 주소만 사용할 수 있습니다. 가상 네트워크를 VPN Gateway에 연결하려면 게이트웨이 서브넷을 만들어야 합니다. [게이트웨이 서브넷에 대한 특정 주소 범위 고려 사항](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub)에서 대해 자세히 알아보세요. 특정 조건에서 서브넷을 만든 후에 주소 범위를 변경할 수 있습니다. 서브넷 주소 범위를 변경하는 방법에 대한 자세한 내용은 [서브넷 관리](virtual-network-manage-subnet.md)를 참조하세요.
     - **구독**: [구독](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription)을 선택 합니다. 둘 이상의 Azure 구독에서 동일한 가상 네트워크를 사용할 수 없습니다. 그러나 단일 구독의 가상 네트워크를 [가상 네트워크 피어링](virtual-network-peering-overview.md)이 있는 다른 구독의 가상 네트워크에 연결할 수 있습니다. 가상 네트워크에 연결하는 모든 Azure 리소스는 가상 네트워크와 동일한 구독에 있어야 합니다.
     - **리소스 그룹**: 기존 리소스 그룹을 선택 하거나 새 [리소스 그룹](../azure-resource-manager/management/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-groups) 을 만듭니다. 가상 네트워크에 연결하는 Azure 리소스는 가상 네트워크와 동일한 리소스 그룹 또는 다른 리소스 그룹에 있을 수 있습니다.
     - **위치:** 지역이라고도 하는 Azure [위치](https://azure.microsoft.com/regions/)를 선택합니다. 가상 네트워크는 하나의 Azure 위치에만 있을 수 있습니다. 그러나 VPN Gateway를 사용하여 서로 다른 위치에 있는 가상 네트워크를 연결할 수 있습니다. 가상 네트워크에 연결하는 모든 Azure 리소스는 가상 네트워크와 동일한 위치에 있어야 합니다.

**명령**

- Azure CLI: [az network vnet create](/cli/azure/network/vnet)
- PowerShell: [AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork)

## <a name="view-virtual-networks-and-settings"></a>가상 네트워크 및 설정 보기

1. 포털 맨 위에 있는 검색 상자에 *가상 네트워크*를 입력합니다. 검색 결과에 **가상 네트워크**가 표시되면 이를 선택합니다.
2. 가상 네트워크 목록에서 설정을 확인할 가상 네트워크를 선택합니다.
3. 선택한 가상 네트워크에 대해 다음 설정이 나열됩니다.
   - **개요**: 주소 공간 및 DNS 서버와 같은 가상 네트워크에 대한 정보를 제공합니다. 다음 스크린샷에서는 **MyVNet**이라는 가상 네트워크에 대한 개요 설정을 보여 줍니다.

     ![네트워크 인터페이스 개요](./media/manage-virtual-network/vnet-overview.png)

     **리소스 그룹** 또는 **구독 이름** 옆의 **변경**을 선택하여 다른 구독 또는 리소스 그룹으로 가상 네트워크를 이동할 수 있습니다. 가상 네트워크를 이동하는 방법은 [다른 리소스 그룹 또는 구독으로 리소스 이동](../azure-resource-manager/management/move-resource-group-and-subscription.md?toc=%2fazure%2fvirtual-network%2ftoc.json)을 참조하세요. 필수 조건과 Azure Portal, PowerShell 및 Azure CLI를 사용하여 리소스를 이동하는 방법을 나열하고 있습니다. 가상 네트워크에 연결된 모든 리소스는 가상 네트워크와 함께 이동해야 합니다.
   - **주소 공간**: 가상 네트워크에 할당된 주소 공간이 나열됩니다. 주소 범위를 주소 공간에 추가 및 제거하는 방법을 알아보려면 이 문서의 [주소 범위 추가 또는 제거](#add-or-remove-an-address-range)에서 설명하는 단계를 완료합니다.
   - **연결된 디바이스**: 가상 네트워크에 연결된 모든 리소스가 나열됩니다. 이전 스크린샷에는 세 개의 네트워크 인터페이스와 하나의 부하 분산 장치가 가상 네트워크에 연결되어 있습니다. 만들고 가상 네트워크에 연결한 새 리소스가 모두 나열됩니다. 가상 네트워크에 연결된 리소스를 삭제하면 더 이상 목록에 표시되지 않습니다.
   - **서브넷:** 가상 네트워크 내에 있는 서브넷 목록이 표시됩니다. 서브넷을 추가하고 제거하는 방법에 대한 자세한 내용은 [서브넷 관리](virtual-network-manage-subnet.md)를 참조하세요.
   - **DNS 서버**: Azure 내부 DNS 서버 또는 사용자 지정 DNS 서버에서 가상 네트워크에 연결된 디바이스에 대한 이름 확인을 제공하는지 여부를 지정할 수 있습니다. Azure Portal을 사용하여 가상 네트워크를 만들 때 기본적으로 Azure의 DNS 서버가 가상 네트워크 내에서 이름을 확인하는 데 사용됩니다. DNS 서버를 수정하려면 이 문서의 [DNS 서버 변경](#change-dns-servers)에서 설명하는 단계를 완료합니다.
   - **피어링**: 구독에 기존 피어링이 있는 경우 여기에 나열됩니다. 기존 피어링에 대한 설정을 보거나, 피어링을 만들거나 변경하거나 삭제할 수 있습니다. 피어링에 대한 자세히 내용은 [가상 네트워크 피어링](virtual-network-peering-overview.md)을 참조하세요.
   - **속성:** 가상 네트워크의 리소스 ID 및 해당 Azure 구독을 포함하여 가상 네트워크에 대한 설정을 표시합니다.
   - **다이어그램:** 가상 네트워크에 연결된 모든 디바이스를 시각적으로 보여줍니다. 다이어그램에는 디바이스에 대한 몇 가지 중요한 정보가 있습니다. 이 보기에서 디바이스를 관리하려면 다이어그램에서 디바이스를 선택합니다.
   - **일반적인 Azure 설정**: 일반적인 Azure 설정에 대한 자세한 내용은 다음 정보를 참조하세요.
     - [활동 로그](../azure-monitor/platform/platform-logs-overview.md)
     - [액세스 제어(IAM)](../role-based-access-control/overview.md)
     - [태그](../azure-resource-manager/management/tag-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
     - [잠금](../azure-resource-manager/management/lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
     - [자동화 스크립트](../azure-resource-manager/management/manage-resource-groups-portal.md#export-resource-groups-to-templates)

**명령**

- Azure CLI: [az network vnet show](/cli/azure/network/vnet)
- PowerShell: [AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork)

## <a name="add-or-remove-an-address-range"></a>주소 범위 추가 또는 제거

가상 네트워크에 대한 주소 범위를 추가 및 제거할 수 있습니다. 주소 범위는 CIDR 표기법으로 지정해야 하며, 동일한 가상 네트워크 내에서 다른 주소 범위와 겹칠 수 없습니다. 정의하는 주소 범위는 공용 또는 프라이빗 주소가 될 수 있습니다(RFC 1918). 주소 범위를 공용으로 정의하든 프라이빗으로 정의하든 가상 네트워크 내, 상호 연결된 가상 네트워크 및 가상 네트워크에 연결된 모든 온-프레미스 네트워크에서만 주소 범위에 연결할 수 있습니다. 

연결 된 서브넷의 범위를 포함 하는 가상 네트워크의 주소 범위를 줄일 수 있습니다. 또한 주소 범위를 확장할 수 있습니다. 예를 들어/16을/8로 변경할 수 있습니다. 

<!-- the above statement has been edited to reflect the most recent comments on the reopened issue: https://github.com/MicrosoftDocs/azure-docs/issues/20572 -->

다음 주소 범위는 추가할 수 없습니다.

- 224.0.0.0/4(멀티캐스트)
- 255.255.255.255/32(브로드캐스트)
- 127.0.0.0/8(루프백)
- 169.254.0.0/16(링크-로컬)
- 168.63.129.16/32(내부 DNS, DHCP 및 Azure Load Balancer [상태 프로브](../load-balancer/load-balancer-custom-probe-overview.md#probesource))

주소 범위를 추가 또는 제거하려면:

1. 포털 맨 위에 있는 검색 상자에 *가상 네트워크*를 입력합니다. 검색 결과에 **가상 네트워크**가 표시되면 이를 선택합니다.
2. 가상 네트워크 목록에서 주소 범위를 추가하거나 제거할 가상 네트워크를 선택합니다.
3. **설정**에서 **주소 공간**을 선택합니다.
4. 다음 옵션 중 하나를 완료합니다.
    - **주소 범위 추가**: 새 주소 범위를 입력합니다. 주소 범위는 가상 네트워크에 대해 정의된 기존 주소 범위와 겹칠 수 없습니다.
    - **주소 범위 제거**: 제거할 주소 범위의 오른쪽에 있는 **...** 를 선택한 다음 **제거**를 선택합니다. 주소 범위에 서브넷이 있으면 해당 주소 범위를 제거할 수 없습니다. 주소 범위를 제거하려면 먼저 주소 범위에 있는 모든 서브넷(및 서브넷의 모든 리소스)을 삭제해야 합니다.
5. **저장**을 선택합니다.

**명령**

- Azure CLI: [az network vnet update](/cli/azure/network/vnet)
- PowerShell: [AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork)

## <a name="change-dns-servers"></a>DNS 서버 변경

가상 네트워크에 연결된 모든 VM은 가상 네트워크에 대해 지정한 DNS 서버에 등록됩니다. 또한 지정된 DNS 서버를 이름 확인에 사용합니다. VM의 각 NIC(네트워크 인터페이스)에는 자체의 DNS 서버 설정이 있을 수 있습니다. NIC에 자체의 DNS 서버 설정이 있는 경우 가상 네트워크에 대한 DNS 서버 설정이 이 설정으로 재정의됩니다. NIC DNS 설정에 대해 자세히 알아보려면 [네트워크 인터페이스 작업 및 설정](virtual-network-network-interface.md#change-dns-servers)을 참조하세요. VM 및 Azure Cloud Services 역할 인스턴스의 이름 확인에 대해 자세히 알아보려면 [VM 및 역할 인스턴스에 대한 이름 확인](virtual-networks-name-resolution-for-vms-and-role-instances.md)을 참조하세요. DNS 서버를 추가, 변경 또는 제거하려면

1. 포털 맨 위에 있는 검색 상자에 *가상 네트워크*를 입력합니다. 검색 결과에 **가상 네트워크**가 표시되면 이를 선택합니다.
2. 가상 네트워크 목록에서 DNS 서버를 변경할 가상 네트워크를 선택합니다.
3. **설정**아래에서 **DNS 서버**를 선택 합니다.
4. 다음 옵션 중 하나를 선택합니다.
   - **기본값 (Azure에서 제공)**: 모든 리소스 이름 및 개인 IP 주소는 Azure DNS 서버에 자동으로 등록 됩니다. 동일한 가상 네트워크에 연결된 모든 리소스 간에 이름을 확인할 수 있습니다. 이 옵션을 사용하면 가상 네트워크에서 이름을 확인할 수 없습니다. 가상 네트워크 전체에서 이름을 확인하려면 사용자 지정 DNS 서버를 사용해야 합니다.
   - **사용자 지정:** 가상 네트워크에 대한 Azure 제한까지 하나 이상의 서버를 추가할 수 있습니다. DNS 서버 제한에 대해 자세히 알아보려면 [Azure 제한](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual-networking-limits-classic)을 참조하세요. 다음과 같은 옵션이 있습니다.
   - **주소 추가:** 가상 네트워크 DNS 서버 목록에 서버를 추가하고, 해당 DNS 서버를 Azure에 등록합니다. Azure에 DNS 서버를 이미 등록한 경우 목록에서 DNS 서버를 선택할 수 있습니다.
   - **주소 제거:** 제거하려는 서버 옆에 있는 **...** 를 선택한 다음 **제거**를 선택합니다. 서버를 삭제하면 이 가상 네트워크 목록에서만 서버가 제거됩니다. DNS 서버는 다른 가상 네트워크에서 사용할 수 있도록 Azure에 등록된 상태로 유지됩니다.
   - **DNS 서버 주소 다시 정렬**: 사용자 환경에 맞게 올바른 순서로 DNS 서버를 나열하는지 확인하는 것이 중요합니다. DNS 서버 목록은 지정된 순서로 사용됩니다. 라운드 로빈 설정으로 작동하지 않습니다. 목록의 첫 번째 DNS 서버에 연결할 수 있으면 DNS 서버가 제대로 작동하는지 여부와 관계없이 클라이언트에서 해당 DNS 서버를 사용합니다. 나열된 모든 DNS 서버를 제거한 다음 원하는 순서로 다시 추가합니다.
   - **주소 변경**: 목록에서 DNS 서버를 강조 표시한 다음 새 주소를 입력합니다.
5. **저장**을 선택합니다.
6. 가상 네트워크에 연결된 VM을 다시 시작하여 새 DNS 서버 설정을 할당합니다. VM이 다시 시작될 때까지 현재 DNS 설정을 계속 사용합니다.

**명령**

- Azure CLI: [az network vnet update](/cli/azure/network/vnet)
- PowerShell: [AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork)

## <a name="delete-a-virtual-network"></a>가상 네트워크 삭제

가상 네트워크에 연결된 리소스가 없는 경우에만 해당 VNet을 삭제할 수 있습니다. 가상 네트워크 내의 서브넷에 연결된 리소스가 있는 경우 먼저 가상 네트워크 내의 모든 서브넷에 연결된 리소스를 삭제해야 합니다. 리소스 삭제를 수행하는 단계는 리소스에 따라 다릅니다. 서브넷에 연결된 리소스를 삭제하는 방법에 대해 알아보려면 삭제하려는 각 리소스 종류에 대한 설명서를 참조하세요. 가상 네트워크를 삭제하려면

1. 포털 맨 위에 있는 검색 상자에 *가상 네트워크*를 입력합니다. 검색 결과에 **가상 네트워크**가 표시되면 이를 선택합니다.
2. 가상 네트워크 목록에서 삭제할 가상 네트워크를 선택합니다.
3. **설정**에서 **연결된 디바이스**를 선택하여 가상 네트워크에 연결된 디바이스가 없는지 확인합니다. 연결된 디바이스가 있으면 가상 네트워크를 삭제하기 전에 먼저 이러한 디바이스를 삭제해야 합니다. 연결된 디바이스가 없으면 **개요**를 선택합니다.
4. **삭제**를 선택합니다.
5. 가상 네트워크 삭제를 확인하려면 **예**를 선택합니다.

**명령**

- Azure CLI: [azure network vnet delete](/cli/azure/network/vnet)
- PowerShell: [AzVirtualNetwork](/powershell/module/az.network/remove-azvirtualnetwork)

## <a name="permissions"></a>사용 권한

가상 네트워크에서 작업을 수행하려면 다음 표에 나열된 적절한 작업이 할당된 [네트워크 기여자](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) 역할 또는 [사용자 지정](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 역할에 계정을 할당해야 합니다.

| 작업                                  |   이름                                |
|---------------------------------------- |   --------------------------------    |
|Microsoft.Network/virtualNetworks/read   |   가상 네트워크 읽기              |
|Microsoft.Network/virtualNetworks/write  |   가상 네트워크 만들기 또는 업데이트  |
|Microsoft.Network/virtualNetworks/delete |   가상 네트워크 삭제            |

## <a name="next-steps"></a>다음 단계

- [PowerShell](powershell-samples.md) 또는 [Azure CLI](cli-samples.md) 샘플 스크립트를 사용하거나 Azure [Resource Manager 템플릿](template-samples.md)을 사용하여 가상 네트워크를 만듭니다.
- 가상 네트워크에 대 한 [정의 Azure Policy](policy-samples.md) 만들기 및 할당
