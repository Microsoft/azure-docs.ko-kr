---
title: '가상 네트워크 게이트웨이 삭제: Azure Portal: Resource Manager | Microsoft Docs'
description: Resource Manager 배포 모델에서 Azure Portal을 사용하여 가상 네트워크 게이트웨이를 삭제합니다.
services: vpn-gateway
documentationcenter: na
author: WenJason
ms.service: vpn-gateway
origin.date: 10/23/2018
ms.date: 03/04/2019
ms.author: v-jay
ms.openlocfilehash: 387b4e982772f22453876e1ea8b9e7c4039601c4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60845706"
---
# <a name="delete-a-virtual-network-gateway-using-the-portal"></a>포털을 사용하여 가상 네트워크 게이트웨이 삭제

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-delete-vnet-gateway-portal.md)
> * [PowerShell](vpn-gateway-delete-vnet-gateway-powershell.md)
> * [PowerShell(클래식)](vpn-gateway-delete-vnet-gateway-classic-powershell.md)

이 문서에서는 Resource Manager 배포 모델을 사용하여 배포된 Azure VPN 게이트웨이를 삭제하는 방법에 대해 설명합니다. VPN 게이트웨이 구성에 대한 가상 네트워크 게이트웨이 삭제하려는 경우에 몇 가지 다른 접근 방법을 사용할 수 있습니다.

- 테스트 환경의 경우처럼 모든 항목을 삭제하고 다시 시작하려면 전체 리소스 그룹을 삭제하면 됩니다. 리소스 그룹을 삭제하면 그룹 내의 모든 리소스가 삭제됩니다. 이 방법은 리소스 그룹에 리소스를 유지하지 않으려는 경우에만 권장됩니다. 이 방법을 사용할 때는 몇 가지 리소스만 선택적으로 삭제할 수가 없습니다.

- 리소스 그룹에 일부 리소스를 유지하려는 경우 가상 네트워크 게이트웨이를 삭제하는 작업이 좀 더 복잡해집니다. 가상 네트워크 게이트웨이를 삭제하려면 먼저 게이트웨이에 의존하는 모든 리소스를 삭제해야 합니다. 수행하는 단계는 만든 연결의 유형 및 각 연결에 대한 종속 리소스에 따라 달라집니다.

> [!IMPORTANT]
> 아래 지침에서는 Resource Manager 배포 모델을 사용하여 배포된 Azure VPN 게이트웨이를 삭제하는 방법에 대해 설명합니다. 클래식 배포 모델을 사용하여 배포된 VPN 게이트웨이를 삭제하려면 [여기](vpn-gateway-delete-vnet-gateway-classic-powershell.md)에 설명된 대로 Azure PowerShell을 사용하세요.


## <a name="delete-a-vpn-gateway"></a>VPN Gateway 삭제

가상 네트워크 게이트웨이를 삭제하려면 먼저 가상 네트워크 게이트웨이와 관련된 각 리소스를 삭제해야 합니다. 리소스는 종속성으로 인해 특정 순서로 삭제해야 합니다.

[!INCLUDE [delete gateway](../../includes/vpn-gateway-delete-vnet-gateway-portal-include.md)]

이때 가상 네트워크 게이트웨이가 삭제됩니다. 다음 단계는 더 이상 사용되지 않는 모든 리소스를 삭제하는 데 도움이 됩니다.

### <a name="to-delete-the-local-network-gateway"></a>로컬 네트워크 게이트웨이를 삭제하려면

1. **모든 리소스**에서 각 연결에 연결된 로컬 네트워크 게이트웨이를 찾습니다.
2. 로컬 네트워크 게이트웨이의 **개요** 블레이드에서 **삭제**를 클릭합니다.

### <a name="to-delete-the-public-ip-address-resource-for-the-gateway"></a>게이트웨이에 대한 공용 IP 주소 리소스를 삭제하려면

1. **모든 리소스**에서 게이트웨이와 연결된 공용 IP 주소 리소스를 찾습니다. 가상 네트워크 게이트웨이가 활성-활성인 경우 두 개의 공용 IP 주소가 표시됩니다. 
2. 공용 IP 주소에 대한 **개요** 페이지에서 **삭제**, **예**를 차례로 클릭하여 확인합니다.

### <a name="to-delete-the-gateway-subnet"></a>게이트웨이 서브넷을 삭제하려면

1. **모든 리소스**에서 가상 네트워크를 찾습니다. 
2. **서브넷** 블레이드에서 **게이트웨이 서브넷**, **삭제**를 차례로 클릭합니다. 
3. **예**를 클릭하여 게이트웨이 서브넷 삭제를 확인합니다.

## <a name="deleterg"></a>리소스 그룹을 삭제하여 VPN 게이트웨이 삭제

리소스 그룹에 리소스를 유지하지 않고 새로 시작하려는 경우 전체 리소스 그룹을 삭제하면 됩니다. 모든 항목을 제거하는 빠른 방법입니다. 다음 단계는 Resource Manager 배포 모델에만 적용됩니다.

1. **모든 리소스**에서 리소스 그룹을 찾고 클릭하여 블레이드를 엽니다.
2. **삭제**를 클릭합니다. 삭제 블레이드에서 영향을 받는 리소스를 봅니다. 해당 리소스를 모두 삭제할 것인지 확인합니다. 그렇지 않은 경우 이 문서의 맨 위에 있는 VPN Gateway 삭제 단계를 사용합니다.
3. 계속하려면 삭제할 리소스 그룹의 이름을 입력한 다음 **삭제**를 클릭합니다.

<!--Update_Description: wording update-->